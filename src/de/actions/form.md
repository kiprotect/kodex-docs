# Formular Aktion

Die Aktion `form` implementiert unsere [Open-Source-Formularvalidierungs- und Transformationsbibliothek](https://github.com/kiprotect/go-helpers), mit der Sie komplexe, hierarchische Daten analysieren, validieren und transformieren können.

## Erste Schritte

Ein **Formular** besteht aus einer Anzahl von **Feldern**. Jedes Feld kann einen oder mehrere **Validatoren** definieren, die den Inhalt des Feldes validieren und transformieren können. Wenn die Formularüberprüfung erfolgreich ist, gibt die Aktion die überprüften Felder zurück. Wenn nicht, wird ein Fehler ausgegeben. Hier ist ein Beispiel für eine Formularaktionsspezifikation:

```yaml
type: form
config:
  fields:
    - name: date
      validators:
        - type: IsString
        - type: IsTime
          config:
            format: rfc3339
    - name: count
      validators:
        - type: IsInteger
          config:
            hasMin: true
            min: 0
            hasMax: true
            max: 1000
```

Diese Aktion stellt sicher, dass jedes Element ein `date` Feld hat, das eine RFC-3339-formatierte Zeichenkette enthält, und ein `count` Feld, das einen Integer-Wert zwischen 0 und 1000 enthält. Es sind noch viele andere Validator-Typen verfügbar, hier ist die vollständige Liste:

* **CanBeAnything** : Stellt nur sicher, dass das Feld vorhanden ist, führt aber keine weitere Überprüfung dafür durch.
* **IstBoolean** : Stellt sicher, dass das Feld einen booleschen Wert enthält, d.h. `true` oder `false`.
* **IstBytes** : Stellt sicher, dass das Feld ein Byte-Array mit einer bestimmten `encoding`, z. B. `hex`, `base64` oder `base64-url` kodierten Daten enthält.
* **IstFließend** : Stellt sicher, dass das Feld einen Float-Wert enthält. Optional, wenn `hasMin` oder `hasMax` `true` ist, definieren die Konfigurationsoptionen `min` oder `max` den Bereich des Float-Wertes.
* **IstHex** : Stellt sicher, dass das Feld einen Hex-Wert enthält. Wenn `convert` `true` ist, wird der Wert in ein Byte-Array konvertiert.
* **IstIn** : Stellt sicher, dass das Feld eines der angegebenen `choices` enthält.
* **IstGanzzahl** : Stellt sicher, dass das Feld einen Integer-Wert enthält. Optional, wenn `hasMin` oder `hasMax` `true` ist, definieren die Konfigurationsoptionen `min` oder `max` den Bereich des Integer-Wertes.
* **IstListe** : Stellt sicher, dass das Feld eine Liste von Werten enthält. Optional wird eine Liste von `validators` auf jedes Listenelement angewendet.
* **IsNotIn** : Wie **IsIn**, stellt aber sicher, dass der Wert des Feldes **nicht** in der angegebenen `choices` ist.
* **IstOptional** : Überspringt die anderen Prüfer, wenn das Feld undefiniert ist, so dass es optional ist.
* **IstString** : Stellt sicher, dass das Feld einen String-Wert enthält. Optional geben `minLength` und `maxLength` die minimale und maximale Länge der Zeichenkette an.
* **IstStringList** : Stellt sicher, dass das Feld eine Liste von Strings enthält. Optional wird eine Liste von `validators` auf jedes Listenelement angewendet.
* **IstStringMap** : Stellt sicher, dass das Feld eine String-basierte Hash-Map / ein Wörterbuch enthält. Optional kann eine `form` angegeben werden, die zur Validierung des Wertes verwendet wird.
* **IsTime** : Stellt sicher, dass das Feld ein datetime/time-Objekt im angegebenen `format` enthält, entweder `rfc3339`, `rfc3339-date`, `unix`, `unix-nano` oder `unix-milli`. Wenn `raw` wahr ist, wird die Zeichenkette nicht in ein `Time` Objekt konvertiert. Wenn `toUTC` wahr ist, wird das resultierende `Time` Objekt in `UTC` umgewandelt.
* **IsUUID** : Stellt sicher, dass das Feld einen UUID-Wert enthält.
* **EntsprichtRegex** : Stellt sicher, dass das Feld mit dem angegebenen regulären Ausdruck übereinstimmt. Geht davon aus, dass das Feld einen String-Wert enthält, der vor der Verwendung des `IsString` Validators überprüft werden sollte.
* **Oder** : Stellt sicher, dass das Feld einen von mehreren möglichen Werten enthält, wie durch eine Liste von `options` angegeben, die wiederum eine Liste von Überprüfern enthält. Hält bei der ersten Option an, die den Wert erfolgreich validiert.
* **Wechseln** : Validiert das Feld gegen eine Liste von Validatoren, die in einer `cases` String-Map definiert sind, wobei der gewählte Fall vom Wert eines anderen Feldes abhängt, dessen Name durch `key` gegeben ist.

## Aktionen innerhalb von Formularen

Darüber hinaus unterstützt der Formularmechanismus auch alle anderen Aktionen durch den `IsAction` Validator, der eine Aktionsspezifikation als Konfiguration annimmt. Beispiel-Validator:

```yaml
type: IsAction
config:
  type: pseudonymize
  config:
    method: merengue
```

Diese führt eine Pseudonymisierung des Wertes mit der Methode `merengue` durch. Parameter von eingebetteten Aktionen werden automatisch von der Formularaktion verwaltet.
