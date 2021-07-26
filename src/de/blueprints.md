# Blaupausen

Blaupausen sind Konfigurationen, die festlegen, wie Kodex Daten lesen, analysieren, transformieren und schreiben soll.

Privacy & Security Engineering ist komplex: In einem typischen Szenario möchten wir vielleicht Daten aus einer Vielzahl von Quellen (z.B. einer Datenbank, Datei oder API) lesen und für verschiedene Zwecke (z.B. Analyse oder Anomalieerkennung) verarbeiten, die oft unterschiedliche Datentransformationen erfordern. Möglicherweise möchten wir die Daten auch an verschiedene Ziele senden (z.B. an eine Datenbank oder Nachrichtenwarteschlange). Und schließlich möchten wir all dies vielleicht kontinuierlich tun, während die Daten produziert werden.

Um dies so einfach wie möglich zu machen, haben wir eine deklarative und ausdrucksstarke Konfigurationssprache auf der Basis von YAML-Dateien implementiert. Andere Systeme wie Kubernetes oder Ansible haben gezeigt, dass Sammlungen von YAML-Dateien leistungsfähige Werkzeuge für das Konfigurationsmanagement sein können. Außerdem sind die Dateien einfach zu handhaben und können wie jede andere Konfigurationsdatei versioniert werden.

## Grundlagen der Blaupause

<aside>
    Die Grundstruktur einer Blaupause. Dieses Beispiel würde Daten aus einer SQL-Datenbank lesen, sie pseudonymisieren und die pseudonymisierten Daten an eine HTTP-API weiterleiten.

    <div class="highlight">
        {%filter highlight(strip=True, language='yaml')%}
            actions: # different actions we want to apply to our data items
              - name: pseudonymize-name
                type: pseudonymize
                ...
            sources: # sources we want to load data items from
              - name: production-db
                type: sql
                ...
            destinations: # destinations we want to send data items to
              - name: audit-api
                type: http
                ...
            streams: # streams of data items that we want to process
              - name: default
                description: |
                  Pseudonymize incoming data before sending it to the audit API.
                sources: # sources of data items for this stream
                  - production-db
                configs: # configurations for this stream
                  - name: default
                    actions: # actions in this configuration
                      - pseudonymize-name
                    destinations: # destinations that these items should be sent to
                      - name: audit-api
        {%endfilter%}
    </div>

</aside>

Eine Blaupause enthält im Wesentlichen mindestens vier verschiedene Abschnitte: Aktionen, Quellen, Ziele und Streams. Jeder Stream bezieht sich auf eine oder mehrere Quellen und enthält eine oder mehrere Stream-Konfigurationen. Jede Stream-Konfiguration bezieht sich auf eine oder mehrere Aktionen sowie auf ein oder mehrere Ziele. Zusammen sagen diese Primitive dem Kodex, wie Daten durch ihn fließen sollen und wie sie analysiert und transformiert werden sollen.

## Ausführen von Blaupausen

Sie können eine bestimmte Blaupause mit dem Befehl `run` ausführen:

```
kodex run [blueprint name]
```


Wenn Sie den `[blueprint name]` weglassen, sucht Kodex nach einer Datei mit dem Namen `.blueprint.yml` im aktuellen Verzeichnis, um sie auszuführen. Andernfalls prüft Kodex zunächst, ob Sie einen Dateispeicherort angegeben haben, und lädt den Blueprint von dort, falls ja. Andernfalls geht Kodex alle Verzeichnisse für Blaupausen durch (standardmäßig `~/.kodex/blueprints`) und versucht, die von Ihnen angegebene Blaupause zu finden. Wenn wir zum Beispiel Folgendes ausführen

```
kodex run pseudonymization/examples/data-types/pseudonymize
```


Kodex wird versuchen, eine Blaupause mit dem Namen `pseudonymize.yml` in einem Unterordner `pseudonymization/examples/data-types` innerhalb eines der angegebenen Blaupausenpfade zu finden. Es kann vorkommen, dass Sie verschiedene Versionen eines Blueprints installiert haben. Standardmäßig lädt Kodex die neueste Version, die es finden kann. Wenn Sie das nicht möchten, können Sie mit dem Flag `--version` eine Version angeben:

```
kodex run my-blueprint --version 0.4.1
```


Die Versionsnummern folgen der [semantischen Versionsspezifikation (2.0](https://semver.org/lang/de/)).

## Blueprint-Repositorien

Mit Kodex können Sie Blaupausen aus so genannten Repositories herunterladen und installieren. Um zum Beispiel unser offizielles Blaupausen-Repository zu erhalten, führen Sie einfach

```
kodex blueprints download
```


Dadurch wird der neueste Schnappschuss des Hauptzweigs unseres [Blueprints-Repositorys](https://github.com/kiprotect/blueprints) heruntergeladen und in ein lokales Verzeichnis installiert. Wenn Sie die Blaupausen von einer anderen URL herunterladen möchten, geben Sie sie einfach an:

```
kodex blueprints download https://my.blueprints/repo.zip
```


Kodex sucht im ZIP-Archiv nach einem Verzeichnis mit einer `.blueprints.yml` -Datei und entpackt dieses Verzeichnis in den Hauptpfad für Blaupausen. Sie können natürlich auch eigene lokale Blueprint-Repositories erstellen. Stellen Sie nur sicher, dass Sie diese in einem Unterordner eines Blueprints-Pfads ablegen und eine `.blueprints.yml` -Datei mit dem folgenden Inhalt erstellen:

<div class="highlight">
    {%filter highlight(strip=True, language='yaml')%}
    package: [your-package-name] # e.g. my-blueprints
    version: [your version] # e.g. 1.5.2
    {%endfilter%}
</div>


