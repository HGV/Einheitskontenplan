---
title: HGV-Benchmark
permalink: /
---

#  HGV-Benchmark

## Wie erfolgt die Datenpflege

Die Pflege des HGV-Benchmarks unterteilt sich in folgende drei Bereiche:

1. [Pflege der Stammdaten](#pflege-der-stammdaten)
1. [Pflege der Monatsdaten](#pflege-der-monatsdaten)
1. [Pflege der Buchhaltungsdaten (EKP)](#pflege-der-buchhaltungsdaten)

### Pflege der Stammdaten

Bei den Stammdaten handelt es sich um Angaben, welche die Eigenschaften des Betriebes definieren.  
Dies sind Angaben zum Betrieb, wie z.B. um was für eine Betriebsart (Schankbetrieb, Beherbergungsbetrieb, Bar, Mischbetrieb, ...) es sich handelt, ob vereinfachte Buchhaltungsform, ob im Eigentum, wieviele Sterne, welche Gemeinde und Tourismusverband.

Die Stammdaten werden über eine Weboberfläche gepflegt.  
Diese Weboberfläche wird vom HGV zur Verfügung gestellt. Pro Partner ist ein Login vorgesehen, wo ihre jeweiligen Kunden gepflegt werden können.

### Pflege der Monatsdaten

Unter Monatsdaten verstehen sich Angaben zu den Betten, Nächtigungen, Offenhaltetagen, Sitzplätze, Mitarbeiter.

Die Monatsdaten werden über eine Weboberfläche gepflegt.

### Pflege der Buchhaltungsdaten

Die Daten werden im [CSV-Format](#definition-der-buchhaltungsdaten) über einen [Datei-Upload](#datei-upload) übertragen.

-----------------------------------

## Definition der Buchhaltungsdaten

### Felder

| Name ​    | ​Beschreibung       | ​Format          | ​Beschreibung                                                                                                                         |
|--------------|---------------|-----------------|--------------------------------------------------------------------------------------------------------------------------------------|
| ​CompanyID    | ​Dient zur Identifikation des Betriebes    | Text    | ​die ​Betriebs-ID muss so behandelt werden, dass nur   der Steuerberater weiß, um welchen Betrieb es sich handelt                      |
| ​Year        ​ | ​Jahr          | ​Ganzzahl         | ​4-stellig                                                                                                                                     |
| ​Month        | ​Monat         | Ganzzahl             | ​​1-12                                                                                                                                     |
| ​Account      | ​Konto         | ​Ganzzahl        | ​                                                                                                                                     |
| ​Value        | ​Wert         ​ | ​Ganzzahl        | ​die letzten beiden Stellen die 2 Nachkommastellen   definieren = Wert des Kontos* 100, falls negativ dann mit negativem   Vorzeichen​ |

### Beispiel

```csv
CompanyID;Year;Month;Account;Value
1;2016;1;61101;3210000
1;2016;1;61102;1320000
2;2016;1;61101;220000
```

-----------------------------------

## Datei-Upload

Der Datenupload der CSV-Dateien erfolgt über eine gesicherte `HTTPS`-Webschnittstelle.  
Die Authentisierung erfolgt über [Basic-Authentication](https://de.wikipedia.org/wiki/HTTP-Authentifizierung#Basic_Authentication).  
Nur vom HGV zertifizierte Partner dürfen Daten an diese Webschnittstelle schicken.

#### Daten welche über den Upload gepflegt werden, werden im [CSV-Format](https://de.wikipedia.org/wiki/CSV_%28Dateiformat%29) übertragen.

1. Zur Zeichenkodierung wird `UTF-8` verwendet, damit Sonderzeichen korrekt übertragen werden.  
   **Achtung!** Diese Zeichenkodierung ist nicht das standardmäßig von Excel verwendete Format, ist jedoch das im Web gängigere und portablere Format.
1. Zeilenumbrüche werden mittels `CRLF` angegeben.
1. Das Trennzeichen ist ein Semikolon (`;`)
1. Falls der Text einer Spalte ein Semikolon oder einen Zeilenumbruch beinhaltet, ist dieser Text mit doppelten Anführungszeichen (`"`) zu umschließen.
1. Falls der Text einer Spalte ein Anführungszeichen (ausgenommen sind die beiden umschließenden Anführungszeichen) beinhaltet, ist diesem Anführungszeichen ein "Backslash" `\` voranzustellen.

Ein Fortschrittsbalken informiert über den Fortschritt der Übertragung. Im Fehlerfall wird dem Benutzer eine Fehlermeldung mit dem Grund der nicht erfolgreichen Übertragung angezeigt.
