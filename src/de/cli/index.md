# Befehlszeilenschnittstelle (CLI)

Die Kodex-Befehlszeilenschnittstelle (CLI) ist der einfachste Weg, um mit dem Datenschutz und der Sicherheitstechnik zu beginnen. Sie ermöglicht es Ihnen, eine breite Palette von datenschutz- und sicherheitsfördernden Transformationen sowie Analysen Ihrer strukturierten Daten durchzuführen.

## Installieren der Kodex CLI

Zunächst müssen Sie das Kodex CLI-Tool herunterladen oder erstellen. Auf unserer [Website]({{'urls.download'|translate}}) können Sie vorkompilierte Binärdateien für verschiedene Plattformen herunterladen. Alternativ können Sie das Tool aus dem Quellcode erstellen, indem Sie den Anweisungen auf unserer [Github-Seite](https://github.com/kiprotect/kodex) folgen.

## Erste Schritte

Standardmäßig steuern wir das CLI-Tool über so genannte **Blueprints**. Ein Blueprint ist eine Konfigurationsdatei (oder eine Sammlung davon), die beschreibt, wie Kodex strukturierte Daten lesen, analysieren, transformieren und schreiben soll.

Um eine Blaupause auszuführen, führen Sie einfach `kodex run [blueprint name]`. Der Kodex wird mit einer kostenlosen, öffentlichen Sammlung von Beispiel-Entwürfen geliefert, die uns den Einstieg erleichtern. Wir können sie auch über die Befehlszeile herunterladen und installieren:

```
kodex blueprints download
```


Dadurch wird unser öffentliches [Blaupausen-Repository](https://github.com/kiprotect/blueprints) heruntergeladen und in einem lokalen Verzeichnis gespeichert (standardmäßig `~/.kodex/blueprints`). Sie können dann jede Blaupause ausführen, indem Sie einfach ihren Pfad relativ zum Blaupausen-Verzeichnis angeben. Lassen Sie uns also ein einfaches Beispiel ausführen, das zeigt, wie Kodex verschiedene Datentypen pseudonymisieren kann:

```
kodex run pseudonymization/examples/data-types/pseudonymize
```


Dadurch wird die Konfiguration aus der Blaupausendatei (`pseudonymize.yml`) geladen. Diese Datei gibt an, woher die Daten gelesen werden sollen (in diesem Fall eine JSON-Datei), wie die Daten transformiert werden sollen (in diesem Fall unter Verwendung einer Pseudonymisierung) und wohin die resultierenden Ausgabedaten gesendet werden sollen (wieder eine JSON-Datei).

<aside>
    Die Elemente in der <code>input.json</code> Datei, die wir pseudonymisieren möchten.

    <div class="highlight">
        {%filter highlight(strip=True, language='json')%}
            {
            "name": "test",
            "date": "2020-06-04",
            "ip": "42.34.122.112/32",
            "count": 4354
            }
            {
            "name": "another test",
            "date": "2019-07-02",
            "ip": "42.34.122.114/32",
            "count": 214
            }
        {%endfilter%}
    </div>

</aside>

Der von uns gewählte Beispiel-Entwurf liest Datenelemente aus einer `input.json` Datei, die sich im gleichen Verzeichnis wie der Entwurf befindet, pseudonymisiert alle Attribute jedes Elements unter Verwendung verschiedener anwendbarer Pseudonymisierungsmethoden und schreibt die pseudonymisierten Daten in eine JSON-Datei (`pseudonymized.json`) im aktuellen Verzeichnis. So sieht die Ausgabe aus:

<div class="highlight">
    {%filter highlight(strip=True, language='json')%}
    {
    "_kip": "278ba5f7db26ca661b4e64b1eb6abb3d4e7d1aa15e55155b3a1f7626424f679c",
    "count": 7643,
    "date": "2021-09-11",
    "ip": "150.39.196.226/32",
    "name": "3YdcJQ=="
    }
    {
    "_kip": "278ba5f7db26ca661b4e64b1eb6abb3d4e7d1aa15e55155b3a1f7626424f679c",
    "count": 46,
    "date": "2015-07-19",
    "ip": "150.39.196.8/32",
    "name": "gKGhSKShnHg/vny6"
    }
    {%endfilter%}

</div>

Wie Sie sehen können, pseudonymisierte Kodex jedes Attribut in jedem Datenelement und fügte auch ein neues Attribut, `_kip`, zu den Elementen hinzu. Der Wert dieses Attributs bezieht sich auf einen Parametersatz, der die kryptografischen Schlüssel enthält, die zur Transformation der Daten verwendet wurden. Die eigentlichen Schlüssel werden in einem so genannten Parameterspeicher gespeichert. Wenn Sie das nicht wünschen, können Sie die Schlüssel auch selbst verwalten: Die `pseudonymize-with-key` Blaupause im gleichen Verzeichnis tut dies, indem sie Sie zunächst zur Eingabe eines Schlüssels auffordert und diesen Schlüssel dann verwendet, um weitere Verschlüsselungsschlüssel für die einzelnen Pseudonymisierungsoperationen abzuleiten. Die Verwaltung von Schlüsseln und Parametern ist an sich schon ein komplexes Thema, denn jetzt können Sie sicher sein, dass Kodex sich um die schmutzigen Details für Sie kümmert.

### Depseudonymisierung von Daten

Irgendwann möchten Sie Ihre Daten vielleicht tatsächlich wieder depseudonymisieren. Kodex macht dies einfach, indem er eine `undo` Aktion zur Verfügung stellt, die auf reversible Transformationen wie die oben beschriebene kryptografische Pseudonymisierung angewendet werden kann. Um also die oben genannten Daten zu depseudonymisieren, können wir einfach eine Blaupause ausführen, die eine solche `undo` Aktion enthält:

```
kodex run pseudonymization/examples/data-types/depseudonymize
```


die die depseudonymisierten Daten (die genau mit den Eingabedaten übereinstimmen sollten) ausdrucken wird. Wenn Sie selbst einen Pseudonymisierungsschlüssel mit der Taste `pseudonymize-with-key`, angegeben haben, können Sie stattdessen `depseudonymize-with-key` blueprint ausführen, das Sie zur Eingabe des Pseudonymisierungsschlüssels auffordert. Ist das nicht einfach?

## Nächste Schritte

Jetzt, da Sie die Grundlagen des Kodex CLI-Tools kennen, können Sie mehr ins Detail gehen. Hier ist, was Sie tun können:

* Stöbern Sie im [Aktionskatalog]({{'kodex.actions.index'|href}}), um sich über die verschiedenen Maßnahmen zur Verbesserung von Privatsphäre und Sicherheit zu informieren, die der Kodex anbietet.
* Schauen Sie das [Blaupausen-Repository](https://github.com/kiprotect/blueprints) durch, um zu sehen, wie ein bestimmtes Problem mit dem Kodex angegangen werden kann.
* Schauen Sie sich die [Dokumentation zu den Blaupausen]({{'kodex.blueprints'|href}}) an, um zu erfahren, wie Sie mit Kodex Ihre eigenen Blaupausen zum Thema Datenschutz und Sicherheit schreiben können.

## Haben Sie Fragen?

Haben Sie Fragen zum Kodex? Sie können Folgendes tun:

* [Öffnen Sie ein Github <tr-ignore>Issue</tr-ignore>](https://github.com/kiprotect/kodex), um unsere großartige Open-Source-Gemeinschaft um Hilfe zu bitten.
* [Schreiben Sie uns eine E-Mail](mailto:cli@kiprotect.com) mit Ihrer Frage.
