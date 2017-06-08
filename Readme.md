---
title: HGV-Benchmark
permalink: /
---

#  HGV-Benchmark

## Wie erfolgt die Datenpflege

Die Pflege des Einheitskontenplanes unterteilt sich in folgende drei Bereiche:

1. [Pflege der Stammdaten](#pflege-der-stammdaten)
1. [Pflege der Monatsdaten](#pflege-der-monatsdaten)
1. [Pflege der Buchhaltungsdaten (EKP)](#pflege-der-buchhaltungsdaten)

### Pflege der Stammdaten

Bei den Stammdaten handelt es sich um Angaben, welche der Kategorisierung des Betriebes dienen.  
Dies sind Angaben zum Betrieb, wie z.B. um was für eine Betriebsart (Schankbetrieb, Beherbergungsbetrieb, Bar, Mischbetrieb, ...) es sich handelt, ob vereinfachte Buchhaltungsform, ob im Eigentum, wieviele Sterne, welche Gemeinde und Tourismusverband...

Die Stammdaten werden über eine Weboberfläche gepflegt.  
Diese Weboberfläche wird vom HGV zur Verfügung gestellt. Pro Partner ist ein Login vorgesehen, wo ihre jeweiligen Kunden gepflegt werden können.

### Pflege der Monatsdaten

Unter Monatsdaten verstehen sich Angaben zu den Betten, Nächtigungen, Offenhaltetagen, Sitzplätze, Mitarbeiter.

Die Monatsdaten werden über eine Weboberfläche gepflegt.

### Pflege der Buchhaltungsdaten

Die Daten werden im CSV-Format über die [HGV-Schnittstelle](#schnittstelle-zum-hgv) übertragen.

-----------------------------------

## Schnittstelle zum HGV

Der Datenupload der CSV-Dateien erfolgt über eine gesicherte `HTTPS`-Webschnittstelle.  
Die Authentisierung erfolgt über [Basic-Authentication](https://de.wikipedia.org/wiki/HTTP-Authentifizierung#Basic_Authentication).  
Nur vom HGV zertifizierte Partner dürfen Daten an diese Webschnittstelle schicken.

#### Daten welche über die Schnittstelle gepflegt werden, werden im [CSV-Format](https://de.wikipedia.org/wiki/CSV_%28Dateiformat%29) übertragen.

1. Zur Zeichenkodierung wird `UTF-8` verwendet, damit Sonderzeichen korrekt übertragen werden.  
   **Achtung!** Diese Zeichenkodierung ist nicht das standardmäßig von Excel verwendete Format, ist jedoch das im Web gängigere und portablere Format.
1. Zeilenumbrüche werden mittels `CRLF` angegeben.
1. Das Trennzeichen ist ein Semikolon (`;`)
1. Falls der Text einer Spalte ein Semikolon oder einen Zeilenumbruch beinhaltet ist dieser Text mit doppelten Anführungszeichen (`"`) zu umschließen.
1. Falls der Text einer Spalte ein Anführungszeichen (ausgenommen sind die beiden umschließenden Anführungszeichen) beinhaltet ist diesem Anführungszeichen ein "Backslash" `\` voranzustellen.

### Servicebeschreibung

Die CSV-Datei wird an den Endpunkt `/ekp` per HTTP-`POST` geschickt.

1. Die Monatsdaten werden an folgenden Endpunkt gesendet: `/ekp/monthlyData`
2. Die Buchhaltungsdaten werden an folgenden Endpunkt gesendet: `/ekp/accounting`

Content-Type ist `text/csv`

* Bei **erfolgreichem Import** gibt die Schnittstelle den HTTP-Status Code `201 Created` zurück.
* Ist das **CSV-Format fehlerhaft** oder liegt ein sonstiger Fehler im übertragenen Dokument vor gibt die Schnittstelle den Status Code `400 Bad Request` zurück. Im Textkörper steht die Ursache, wieso die Übertragung nicht erfolgreich war.
* War der Import **aufgrund eines Fehlers beim HGV** nicht erfolgreich gibt die Schnittstelle den Status Code `500 Internal Server Error` zurück. Im Textkörper steht die Fehlerursache.

Es ist zu beachten dass die Übertragung einige Zeit dauern kann, sei es aufgrund dessen, dass die CSV-Datei sehr groß sein kann (bei Übertragung mehrerer Betriebe/Monate) oder aber der Import der Daten beim HGV länger dauert, da Sanitätschecks gemacht werden und die Daten mit bestehenden Daten zusammengeführt werden müssen.

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
SenderID;CompanyID;Year;Month;Account;Value
1;1;2016;1;61101;3210000
1;1;2016;1;61102;1320000
1;2;2016;1;61101;220000
```
