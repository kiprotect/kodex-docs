# Kodex Community Edition (CE)

Um die Community-Edition von Kodex zu installieren, führen Sie einfach

<div class="highlight">
    {%filter highlight(strip=True, language='bash')%}
		git clone https://github.com/kiprotect/kodex
		cd kodex
		make
		make install
	{% endfilter %}
</div>


Das war's! Sie können jetzt Blaupausen mit dem CLI-Befehl `kodex` bearbeiten.

# Kodex Enterprise Edition (EE)

Um die Enterprise-Edition von Kodex (Kodex-EE) zu installieren, laden Sie zunächst `kodex-ee` von unserem [Webportal](https://app.kiprotect.com) herunter. Die Binärdatei enthält alles, was Sie für die ersten Schritte benötigen. Insbesondere bietet es einen `install` Befehl, der die Installation von Kodex-EE für verschiedene Umgebungen (größtenteils) automatisiert. Um zum Beispiel eine Docker-Compose-Konfiguration mit allen Kodex-Komponenten und den erforderlichen externen Diensten zu erstellen, führen Sie einfach aus

<div class="highlight">
    {%filter highlight(strip=True, language='bash')%}
		kodex-ee install --method docker-compose
		cd docker-compose
		docker-compose up
	{% endfilter %}
</div>


Damit sollten Sie ein voll funktionsfähiges Testsystem mit dem Kodex-Frontend erhalten, das auf Port 4242 läuft. Die folgenden Abschnitte erklären im Detail, wie Sie Kodex-EE auf Ihrer Infrastruktur dauerhaft zum Laufen bringen.

## Bereitstellungsoptionen

Kodex-EE wird als eine Reihe von zustandslosen Linux-Diensten eingesetzt, die mit externen Datenbanken und Systemen interagieren, wie oben beschrieben. Eine vollständige Bereitstellung besteht aus mindestens einem **Daemon** und einer **API-Instanz**. Diese beiden Dienste sind zustandslos und können mit einer einfachen Einstellungsdatei konfiguriert werden. Die API ist für die Verwendung hinter einem Reverse-Proxy konzipiert, der TLS und Lastausgleich handhabt (Beispielkonfigurationen für Server wie **nginx** oder **caddy** sind verfügbar).

## Anforderungen

Kodex-EE benötigt mehrere externe Dienste, um zu funktionieren:

* Eine **PostgreSQL-Datenbank** (vorzugsweise Version 9.6 oder neuer), die Kodex-EE zum Speichern von Metadaten über Datenströme, Aktionen und Parameter verwendet. Bestimmte Kodex-EE-Plugins (z. B. Klaro, Konsens) verwenden ihre eigenen PostgreSQL-Schemas für die Datenspeicherung. Aus Stabilitäts- und Leistungsgründen ist es ratsam, für diese separate PostgreSQL-Instanzen bereitzustellen.  
* Ein **RabbitMQ-Server** (vorzugsweise Version 3.8 oder neuer) oder ein **Kafka-Server** (vorzugsweise Version 2.7 oder neuer), den Kodex-EE verwendet, um während der Verarbeitung Datenelemente zwischenzuspeichern.
* Ein **Authentifizierungsdienst auf Basis von SSO, SAML, OpenID-Connect oder LDAP**, den Kodex-EE zur Authentifizierung von Benutzern verwenden kann. Der Dienst muss eine Zuordnung von Benutzern zu Organisationen und Rollen innerhalb dieser Organisationen bieten.
* Eine **Redis-Datenbank** (vorzugsweise Version 5.0 oder neuer), die Kodex-EE verwendet, um z.B. API- und Datenverarbeitungsmetriken sowie für die Ratenbegrenzung notwendige Daten zu speichern.
<md-list v=""*&gt;Optional**, entweder ein **persistentes Verzeichnis/Volume**, auf das von API- und Daemon-Knoten aus zugegriffen werden kann, oder ein **"Blob"-Speicherdienst** mit einer S3-kompatiblen API. Kodex-EE verwendet diese, um Dateidaten zu speichern, die für bestimmte Hintergrundaufgaben erzeugt werden.</md-list>

Die angegebenen Mindestversionen von Diensten sind die, mit denen Kodex-EE getestet und eingesetzt wird. Es kann möglich sein, dass es auch mit älteren Versionen läuft, obwohl wir keine Garantien geben können. Das Gleiche gilt für die Verwendung neuerer Versionen dieser Dienste.

### PostgreSQL-Datenbank(en)

Kodex-EE verwendet ein primäres Datenbankschema, um Metadaten über Dinge wie Datenumwandlungen, Quellen und Ziele zu speichern. Bei regelmäßiger Verwendung wird nur eine kleine Anzahl von Tabelleneinträgen erzeugt, und das primäre Datenbankschema wächst selten über einige GB hinaus. Bestimmte Kodex-EE-Plugins (z.B. Klaro oder Konsens) verwenden sekundäre Datenbankschemata zum Speichern von Datenergebnissen, die proportional zu den verarbeiteten Daten wachsen. Kodex-EE kann einen Datenaufbewahrungsplan implementieren und löscht alte Daten, dennoch hängt der benötigte Speicherplatz von der Menge der verarbeiteten Daten ab. Bitte beachten Sie, dass die Leistung auch abnehmen kann, wenn sehr große Datenmengen verarbeitet werden. Alle Datenschemata von Kodex-EE sind jedoch so ausgelegt, dass sie Milliarden von Datenzeilen ohne nennenswerte Probleme verarbeiten können, vorausgesetzt, es wurde eine angemessene Hardware für die Datenbanken bereitgestellt.

### Redis-Datenbank(en)

Kodex-EE verwendet Redis für die Sammlung von Metriken und die Aufbewahrung des internen Zustands während der Verarbeitung (z. B. für die Anonymisierung). Metriken werden über eine hierarchische Zeitspanne (Stunden, Tage, Wochen, Monate, Jahre) erzeugt und einzelne Datenelemente laufen automatisch ab. Der Speicherbedarf für den internen Status hängt von der Art der Datenverarbeitung und der Anzahl der verarbeiteten Elemente ab.

### Nachrichten-Warteschlange(n) (RabbitMQ oder Kafka)

Kodex-EE speichert verarbeitete Daten in einer Nachrichtenwarteschlange zur internen Verarbeitung und Pufferung. Die Menge der in diesen Warteschlangen gespeicherten Daten hängt von vielen Faktoren ab, insbesondere von der Anzahl der eingehenden Daten, der Anzahl der Daemon-Knoten und der Lese-/Schreibkapazität der Datenquellen und -ziele. Kodex-EE wendet automatisch Backpressure an, wenn Schreibvorgänge in die interne Warteschlange unmöglich werden.

## Daemon & API-Anforderungen

Kodex EE kann auf Systemen mit minimalen Ressourcen wie einem Embedded System oder SoC laufen.

### Betriebssystem

Kodex-EE wird als statisch kompiliertes X64/AMD64-Binary geliefert, das mit allen gängigen Linux-Distributionen kompatibel ist. Der Betrieb von Kodex-EE unter Windows, MacOS und OpenBSD sollte möglich sein, ist aber derzeit ungetestet und wird nicht unterstützt.

### Speicher

Wir empfehlen mindestens 512 MB verfügbaren RAM sowohl für die **API** als auch für den **Daemon**. Die tatsächliche Speichernutzung kann je nach Arbeitsbelastung variieren, aber Kodex EE wird versuchen, die Datenverarbeitung automatisch zu begrenzen, um nicht den gesamten verfügbaren Speicher auszuschöpfen.

### CPU

Sowohl der Daemon als auch die API verarbeiten Daten gleichzeitig in parallelen Threads, daher können sie effektiv mehrere CPU-Kerne / Verarbeitungs-Threads nutzen.

### Lagerung

Abgesehen von der oben erwähnten aufgabenspezifischen Speicherung benötigen weder der Daemon noch die API eine persistente Datenspeicherung auf der Festplatte.

### Netzwerkbetrieb

Kodex-EE muss eine Verbindung zu Datenquellen und -zielen haben, von denen es Daten lesen oder in die es Daten schreiben soll. Die Verarbeitungsgeschwindigkeit wird durch die verfügbare Netzwerk-Bandbreite begrenzt.

# Betrieb

Die folgenden Abschnitte beschreiben betriebsbezogene Aspekte von Kodex-EE.

## Metriken

Kodex-EE sammelt verschiedene Metriken, die über die REST-API offengelegt werden, und unterstützt die Instrumentierung für Prometheus.

## Loggen

Kodex-EE verfügt über ein internes, pegelbasiertes Protokollierungssystem, das mit **Syslog** kompatibel ist.

