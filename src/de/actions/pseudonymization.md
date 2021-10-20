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

