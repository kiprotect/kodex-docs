# Befehlszeilenschnittstelle (CLI)

Die Kodex-Befehlszeilenschnittstelle (CLI) ist der einfachste Weg, um mit dem Datenschutz und der Sicherheitstechnik zu beginnen. Sie ermöglicht es Ihnen, eine breite Palette von datenschutz- und sicherheitsfördernden Transformationen sowie Analysen Ihrer strukturierten Daten durchzuführen.

## Installieren der Kodex CLI

Zunächst müssen Sie das Kodex CLI-Tool herunterladen oder erstellen. Auf unserer [Website]({{'urls.download'|translate}}) können Sie vorkompilierte Binärdateien für verschiedene Plattformen herunterladen. Alternativ können Sie das Tool aus dem Quellcode erstellen, indem Sie den Anweisungen auf unserer [Github-Seite](https://github.com/kiprotect/kodex) folgen.

## Erste Schritte

Standardmäßig steuern wir das CLI-Tool über so genannte **Blueprints**. Ein Blueprint ist eine Konfigurationsdatei (oder eine Sammlung davon), die beschreibt, wie Kodex strukturierte Daten lesen, analysieren, transformieren und schreiben soll.

Um einen Blueprint auszuführen, führen Sie einfach `kodex run [blueprint name]` aus. Kodex kommt mit einem kostenlosen, öffentlichen Repository von Beispiel-Blaupausen, die uns den Einstieg erleichtern. Wir können sie auch über die Kommandozeile herunterladen und installieren:

```
kodex blueprints download
```


Dies lädt unser öffentliches [Blueprints-Repository](https://github.com/kiprotect/blueprints) herunter und speichert es in einem lokalen Verzeichnis (standardmäßig `~/.kodex/blueprints`). Sie können dann jeden Blueprint ausführen, indem Sie einfach seinen Pfad relativ zum Blueprints-Verzeichnis angeben. Lassen Sie uns also ein einfaches Beispiel ausführen, das zeigt, wie Kodex verschiedene Datentypen pseudonymisieren kann:

```
kodex run pseudonymization/examples/data-types/pseudonymize
```


Dadurch wird die Konfiguration aus der Blueprint-Datei (`pseudonymize.yml`) geladen. Diese Datei gibt an, woher Daten gelesen werden sollen (in diesem Fall eine JSON-Datei), wie die Daten umgewandelt werden sollen (in diesem Fall unter Verwendung einer Pseudonymisierung) und wohin die resultierenden Ausgabedaten gesendet werden sollen (wieder eine JSON-Datei).

<aside>
    Die Elemente in der <code>input.json</code> Datei, die
wir pseudonymisieren möchten.

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

Der Beispiel-Blaupause, die wir ausgewählt haben, liest Datenelemente aus einer `input.json` Datei, die sich im gleichen Verzeichnis wie die Blaupause befindet, pseudonymisiert alle Attribute jedes Elements mit verschiedenen anwendbaren Pseudonymisierungsmethoden und schreibt die pseudonymisierten Daten in eine JSON-Datei (`pseudonymized.json`) im aktuellen Verzeichnis. So sieht die Ausgabe aus:

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

Wie Sie sehen können, hat Kodex jedes Attribut in jedem Datenelement pseudonymisiert und außerdem ein neues Attribut, `_kip`, zu den Elementen hinzugefügt. Der Wert dieses Attributs verweist auf einen Parametersatz, der die kryptografischen Schlüssel enthält, die zur Transformation der Daten verwendet wurden. Die eigentlichen Schlüssel werden in einem sogenannten Parameterspeicher abgelegt. Wenn Sie das nicht möchten, können Sie die Schlüssel auch selbst verwalten: Der Blueprint `pseudonymize-with-key` im gleichen Verzeichnis tut dies, indem er Sie zunächst zur Eingabe eines Schlüssels auffordert und dann aus diesem Schlüssel weitere Verschlüsselungsschlüssel für die einzelnen Pseudonymisierungsoperationen ableitet. Die Verwaltung von Schlüsseln und Parametern ist ein komplexes Thema für sich, seien Sie einfach versichert, dass Kodex sich um die unübersichtlichen Details für Sie kümmert.

### Depseudonymisierung von Daten

Irgendwann möchten Sie Ihre Daten vielleicht sogar wieder depseudonymisieren. Kodex macht dies einfach, indem es eine `undo` Aktion bereitstellt, die auf umkehrbare Transformationen wie die kryptografische Pseudonymisierung oben angewendet werden kann. Um also die obigen Daten zu depseudonymisieren, können wir einfach einen Blueprint ausführen, der eine solche `undo` Aktion enthält:

```
kodex run pseudonymization/examples/data-types/depseudonymize
```


was die depseudonymisierten Daten ausgibt (die genau mit den Eingabedaten übereinstimmen sollten). Wenn Sie selbst einen Pseudonymisierungsschlüssel bereitgestellt haben, indem Sie die `pseudonymize-with-key` verwenden, können Sie stattdessen `depseudonymize-with-key` blueprint ausführen, das Sie zur Eingabe des Pseudonymisierungsschlüssels auffordert. Ist das nicht einfach?

## Nächste Schritte

Jetzt, da Sie die Grundlagen des Kodex CLI-Tools kennen, können Sie mehr ins Detail gehen. Hier ist, was Sie tun können:

* Stöbern Sie im [Aktionskatalog]({{'kodex.actions.index'|href}}), um sich über die verschiedenen Maßnahmen zur Verbesserung von Privatsphäre und Sicherheit zu informieren, die der Kodex anbietet.
* Schauen Sie das [Blaupausen-Repository](https://github.com/kiprotect/blueprints) durch, um zu sehen, wie ein bestimmtes Problem mit dem Kodex angegangen werden kann.
* Schauen Sie sich die [Dokumentation zu den Blaupausen]({{'kodex.blueprints'|href}}) an, um zu erfahren, wie Sie mit Kodex Ihre eigenen Blaupausen zum Thema Datenschutz und Sicherheit schreiben können.

## Haben Sie Fragen?

Haben Sie Fragen zum Kodex? Sie können Folgendes tun:

* [Öffnen Sie ein Github <tr-ignore>Issue</tr-ignore>](https://github.com/kiprotect/kodex), um unsere großartige Open-Source-Gemeinschaft um Hilfe zu bitten.
* [Schreiben Sie uns eine E-Mail](mailto:cli@kiprotect.com) mit Ihrer Frage.
