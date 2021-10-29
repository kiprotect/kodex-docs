# Pseudonymisierung

Pseudonymisierung ist eine **Technologie zur Verbesserung der Privatsphäre (PET)**, die direkte oder indirekte Identifikatoren in einem Datenpunkt durch andere, "pseudonyme" Werte ersetzt.
Dies schützt die Identität der Person(en), zu der/denen die Daten gehören, da ein Angreifer oder ein Nutzer der Daten keine Beziehung mehr zu einer bestimmten Person herstellen kann.

Pseudonymisierungsmethoden können auf der Grundlage ihrer Eigenschaften klassifiziert werden. **Reversible Pseudonymisierungsmethoden** ermöglichen die De-Pseudonymisierung von Daten, d.h. die Rückkehr zum ursprünglichen Identifikator aus dem Pseudonym mit Hilfe zusätzlicher Daten wie einem kryptographischen Schlüssel oder einer Zuordnungstabelle.
Bei **nicht umkehrbaren Pseudonymisierungsmethoden** ist dies nicht möglich. **Deterministische Pseudonymisierungsmethoden** erzeugen immer das gleiche Pseudonym, wenn sie zweimal auf denselben Identifikator angewendet werden.
**Nicht-deterministische Methoden** erzeugen zufällige Pseudonyme, selbst wenn sie mehrfach auf denselben Identifikator angewendet werden.
**Strukturerhaltende Pseudonymisierungsmethoden bewahren** die spezifische interne Struktur von Identifikatoren.
Zum Beispiel ist eine deterministische Pseudonymisierungsmethode auch strukturerhaltend in Bezug auf den Gleichheitsoperator:
Wenn die Pseudonyme für zwei Identifikatoren gleich sind, müssen auch die ursprünglichen Identifikatoren identisch gewesen sein.
Dieses Prinzip lässt sich auf weitergehende strukturelle Eigenschaften ausdehnen, wie im Folgenden erläutert wird.
**Formatbewahrende Pseudonymisierungsmethoden bewahren** das ursprüngliche Format der Daten.
Beispielsweise könnte eine formatschonende Methode ein gültiges Postleitzahlen-Pseudonym aus einer Original-Postleitzahl erzeugen.

## Umkehrbare Methoden

Kodex unterstützt schlüsselbasierte, umkehrbare Pseudonymisierungsmethoden, die auf format-erhaltender Verschlüsselung basieren.
Die resultierenden Pseudonyme können mit dem Originalschlüssel entschlüsselt/depseudonymisiert werden. Die Pseudonymisierungsmethode `merengue` beispielsweise arbeitet mit beliebigen Binärdaten und erzeugt binäre Pseudonyme, die zusätzlich die Präfixstruktur der Daten erhalten können:

```yaml
- name: pseudonymize-name
  type: pseudonymize
  config:
    key: name
    method: merengue
```

### Strukturerhaltende Methoden

Basierend auf der Methode `merengue` bietet Kodex verschiedene strukturerhaltende Pseudonymisierungsmethoden, die mit Datentypen wie Zeitstempeln, IP-Adressen und Zahlen arbeiten können. 
Diese Methoden bewahren das Format der Originaldaten und können auch strukturelle Informationen bewahren.
Die folgenden Aktionen führen zum Beispiel eine struktur- und formaterhaltende Pseudonymisierung der oben genannten Datentypen durch:


```yaml
- name: pseudonymize-date
  type: pseudonymize
  config:
    key: date
    method: structured
    type: date
    format: "%(2010-2030)Y-%m-%d"
- name: pseudonymize-ip
  type: pseudonymize
  config:
    key: ip
    method: structured
    type: ipv4
    preserve-prefixes: true
- name: pseudonymize-count
  type: pseudonymize
  config:
    key: count
    method: structured
    type: integer
    preserve-prefixes: true
    type-params:
      min: 0
      max: 10000
```

## Nicht-umkehrbare Methoden

Kodex unterstützt auch nicht umkehrbare Pseudonymisierungsmethoden, insbesondere die auf Keyed Hash basierende Pseudonymisierung, die auf Keyed-Hash Message Authentication Codes (HMAC) basiert, sowie die nicht auf Keyed Hash basierende Pseudonymisierung (von der wir dringend abraten). Die folgende Aktion erzeugt zum Beispiel ein HMAC-basiertes Pseudonym:

```yaml
- name: pseudonymize-id
  type: pseudonymize
  config:
    key: id
    method: hmac
```

## Referenz

Wie alle Aktionen erfordert die Methode `pseudonymize` den Parameter `config`, der wiederum einen Parameter `key` erfordert, der das Attribut angibt, das pseudonymisiert werden soll, sowie einen Parameter `method`, der die Pseudonymisierungsmethode angibt. Derzeit werden die folgenden Methoden unterstützt:

* `merengue`: Nicht-struktur- und nicht-formaterhaltende, reversible, verschlüsselungsbasierte Pseudonymisierung.
* `structured`: Struktur- & Format-erhaltende, umkehrbare, verschlüsselungsbasierte Pseudonymisierung.
* `hmac`: Nicht-struktur- und nicht-formaterhaltende, nicht umkehrbare, authentifizierte Hash-basierte Pseudonymisierung.

Je nach gewählter Methode sind zusätzliche Konfigurationsparameter erforderlich, wie unten beschrieben.

### Merengue Pseudonymisierungs-Parameter

Die Methode `merengue` akzeptiert einen Parameter `encode`, der die Kodierung der resultierenden Byte-Zeichenfolge angibt. Derzeit ist der einzig mögliche Wert (und die Vorgabe) `base64`.


### Strukturierte Pseudonymisierungsparameter

Die Methode `structured` akzeptiert die folgenden Parameter:

*  <md-it>`preserve-prefixes`* : Bei true * bleiben die Präfixe eines strukturierten Datenwerts erhalten. Wenn Sie beispielsweise das Format `date` verwenden, werden Datumsangaben mit einem gemeinsamen Präfix (z.B. das gleiche Jahr und der gleiche Monat) Pseudonymen zugeordnet, die ebenfalls ein Präfix der gleichen Länge haben. Standardmäßig auf * false*.
* `type`: Gibt den zu pseudonymisierenden Datentyp an. Muss einer von `ip`, `date`, `integer`, `ipv4` oder `ipv6` sein.
* `type-params`: Gibt je nach gewähltem Typ zusätzliche Typparameter an. Derzeit erfordert nur der Typ `integer` die obligatorischen Parameter `min` und `max`, die seinen Bereich angeben.
* `format`: Gibt das typabhängige Format der zu pseudonymisierenden Daten an (falls zutreffend). Derzeit unterstützt nur der Typ `data` den Parameter `format`, wie unten beschrieben.

#### Datum Format

Der Typ `date` kann Datumsangaben in verschiedenen Formaten analysieren und erzeugen. Der `format` Spezifizierer ist dem Standard-Unix-Zeitformat nachempfunden und verwendet `%` Zeichen, gefolgt von einem Formatcode. Die folgenden Codes werden unterstützt:

* `Y`: Das Jahr als vierstellige Zahl (z.B. 2021)
* `m`: Der Monat als zweistellige Zahl (z.B. 01 oder 12)
* `d`: Der Tag des Monats in Form einer zweistelligen Zahl (z.B. 03 oder 30)
* `H`: Die Stunde der Uhrzeit als zweistellige Zahl (z.B. 11 oder 07)
* `M`: Die Minute der Uhrzeit als zweistellige Zahl (z.B. 01 oder 59)
* `S`: Die Sekunde der Uhrzeit als zweistellige Zahl (z.B. 01 oder 59)
* `n`: Die Nanosekunden der Zeit in Form einer neunstelligen Zahl (z.B. 000000001)
* `z`: Die Zeitzone der Uhrzeit als vierstellige Zahl mit Vorzeichen (z.B. +0400 oder - 0730)

Zusätzlich zu der üblichen Syntax erlaubt das Format auch die Angabe von Bereichen, die in Klammern nach dem Zeichen `%` angegeben werden müssen. Zum Beispiel begrenzt `%Y(2001-2022)` das Jahr auf Werte zwischen 2001 und 2022. Die Angabe von Bereichen kann nützlich sein, um sicherzustellen, dass pseudonymisierte Zeitstempel innerhalb eines erwarteten Bereichs liegen. Dies setzt jedoch voraus, dass alle Eingabewerte ebenfalls in diesem Bereich liegen.
