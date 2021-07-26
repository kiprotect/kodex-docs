# Aktionen

Aktionen sind die Art und Weise, wie Kodex Daten transformiert oder analysiert. Aktionen können einzeln oder als Sequenz aufgerufen werden, wobei jede Aktion das Ergebnis der vorhergehenden Aktion erhält. Die folgenden Abschnitte beschreiben die verschiedenen Aktionstypen, die derzeit von Kodex unterstützt werden.

## Generische Validierung & Transformation

Oft sind Daten komplex & hierarchisch, was es schwierig macht, einfache Transformationen auf sie anzuwenden. Für diesen Fall unterstützt Kodex eine generische formularbasierte Validierung und Transformation von Daten. Damit können Sie komplexe, hierarchische Daten wie JSON-Dokumente einfach parsen, validieren und transformieren. Sie können mehr über diese Art der Transformation in der [Formular-Aktionsdokumentation]({{'actions.form'|href}}) lesen.

## Pseudonymisierung

Die Pseudonymisierung erzeugt Daten, die nicht mehr direkt einer bestimmten Person zugeordnet werden können. Die Verwendung pseudonymisierter Daten senkt das Risiko der Datenverarbeitung für Einzelpersonen und verringert die Auswirkungen von Datenverlust oder Datendiebstahl. Sie kann sowohl auf direkte oder indirekte Identifikatoren als auch auf eine Vielzahl von strukturierten Datentypen wie Zahlen, Daten, Namen oder IP-Adressen angewandt werden. Einige Pseudonymisierungsmethoden basieren auf einer reversiblen Verschlüsselung, die es ermöglicht, die Daten bei Kenntnis des Schlüssels wieder zu de-pseudonymisieren. Andere Methoden wie Hashing sind nicht umkehrbar.

## Verschlüsselung

Die Verschlüsselung erzeugt Daten, die statistisch nicht von zufälligem Rauschen unterscheidbar sind und die nur mit Kenntnis eines geheimen Verschlüsselungsschlüssels entschlüsselt werden können. Kodex setzt standardmäßige symmetrische und asymmetrische Verschlüsselungstechniken ein.

Bitte beachten Sie, dass wir Verschlüsselungsmethoden, die bei identischen Eingabedaten und Verschlüsselungsschlüssel(n) den gleichen Geheimtext erzeugen, als Pseudonymisierungsmethoden betrachten, da die resultierenden Daten (absichtlich) nicht den modernen Sicherheitsstandards für Verschlüsselungsmethoden entsprechen. Daher werden formaterhaltende Verschlüsselungsmethoden, die ohne zufällige Initialisierungsvektoren (IV) arbeiten, bei diesem Ansatz ebenfalls als Pseudonymisierungsmethoden betrachtet.

## Anonymisierung

Anonymisierungsmaßnahmen verwenden statistische Techniken, um Daten zu erzeugen, aus denen es nicht möglich sein sollte, bestimmte Personen zu identifizieren oder auf nicht-triviale Informationen über diese Personen zu schließen. Kodex stützt sich auf Randomisierung und Aggregation als Anonymitätsmechanismen und produziert anonyme Daten, die modernen Anonymitätsstandards wie dem "differentiellen Datenschutz" entsprechen.

## Entdeckung

Discovery-Aktionen ermöglichen es Ihnen, verschiedene Arten von persönlichen oder sensiblen Informationen in Ihren strukturierten und unstrukturierten Datenelementen aufzuspüren.

## Identifizierung und Identitätsmanagement

Identifizierungs- und Identitätsmanagement-Aktionen ermöglichen es Ihnen, Personen anhand verschiedener direkter oder indirekter Identifikatoren zu identifizieren und jeder Person eine dauerhafte pseudonyme ID zuzuordnen. So können Sie z.B. Datenelemente aus verschiedenen Quellen einer einzigen Person zuordnen und die pseudonyme ID dieser Person verwenden, um z.B. anonyme oder pseudonyme Daten zu erzeugen oder die Zustimmung dieser Person zu einem bestimmten Datenverarbeitungszweck zu überprüfen.

## Verwaltung von Genehmigungen

Mit Aktionen zur Verwaltung der Einwilligung können Sie überprüfen, ob eine bestimmte Person ihre Einwilligung zu einem bestimmten Datenverarbeitungszweck gegeben hat. Zusammen mit den Identifizierungs- und Identitätsmanagement-Aktionen ermöglichen sie es Ihnen, konforme Datenverarbeitungs-Workflows aufzubauen und z.B. Daten von Personen, die ihre Einwilligung zu einem bestimmten Verarbeitungszweck nicht gegeben oder zurückgezogen haben, zu unterdrücken.

## Audit-Protokollierung

Audit-Protokollierungsaktionen ermöglichen es Ihnen, den Datenfluss von Einzelpersonen durch Ihre gesamte Datenverarbeitungsinfrastruktur zu verfolgen. Sie erzeugen pseudonyme, durchsuchbare Informationen darüber, wie Daten, die zu einer bestimmten Person gehören, verarbeitet wurden und wohin sie gesendet wurden, so dass Sie diese Daten z.B. abrufen, ändern oder löschen können.
