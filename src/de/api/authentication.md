# Authentifizierung

Die Kodex-API kann verschiedene Authentifizierungsverfahren verwenden und ist mit mehreren externen Authentifizierungsanbietern kompatibel. Die SaaS-Version der API, die unter `api.kiprotect.com` verfügbar ist, verwendet eine tokenbasierte Authentifizierung.

## Rollenbasierte Zugriffsverwaltung

Die Kodex API bietet rollenbasierte Zugriffskontrollen für alle Objekte.
Ein **API-Benutzer** gehört zu einer oder mehreren **Organisationen** und hat eine oder mehrere **Rollen** in diesen Organisationen.
Ein **API-Objekt**, z.B. ein Projekt, ein Stream oder eine Datenquelle, gehört entweder zu einem **Rollenobjekt** oder ist ein eigenes **Rollenobjekt**.
Rollenobjekte bilden bestimmte Objektrollen (die z.B. Schreib- oder Leserechte für ein Objekt gewähren) auf Organisationsrollen ab.
Daher können Benutzer auf der Grundlage der ihnen zugewiesenen Organisationsrollen auf Objekte zugreifen und mit ihnen arbeiten.

