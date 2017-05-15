---
title: HGV-Schnittstelle für den Einheitskontenplan (EKP)
permalink: /
---

#  HGV-Schnittstelle für den Einheitskontenplan (EKP)

## Wie erfolgt die Datenpflege

Die Pflege des Einheitskontenplanes unterteilt sich in folgende drei Bereiche:

1. Pflege der Stammdaten
1. Pflege der Monatsdaten
1. Übertragung der Buchhaltungsdaten (der eigentliche EKP)

### Pflege der Stammdaten

Bei den Stammdaten handelt es sich um Angaben, welche für der Kategorisierung des Betriebes dienen.  
Dies sind Angaben zum Betrieb, wie z.B. um was für eine Betriebsart (Schankbetrieb, Beherbergungsbetrieb, Bar, Mischbetrieb, ...) es sich handelt, ob vereinfachte Buchhaltungsform, ob im Eigentum, wieviele Sterne, welche Gemeinde und Tourismusverband.

Die Stammdaten werden über eine Weboberfläche gepflegt.  
Diese Weboberfläche wird vom HGV zur Verfügung gestellt. Pro Steuerberatungsbüro ist ein Login vorgesehen, wo sie ihre jeweiligen Kunden pflegen können.

### Pflege der Monatsdaten

Unter Monatsdaten verstehen sich Angaben zu den Betten, Nächtigungen, Offenhaltetagen, Sitzplätze, Mitarbeiter.

Die Monatsdaten werden über eine Weboberfläche gepflegt.

Da sich diese Angaben (vor allem Nächtigungen und Offenhaltetage) laufend ändern ist alternativ ist ein automatischer periodischer Import als CSV-Datei möglich um die manuelle Pflege so gering wie möglich zu halten. Diese Schnittstelle wird im Detail weiter unten erklärt.

### Pflege der Buchhaltungsdaten

Die Daten werden im CSV-Format über eine Schnittstelle übertragen. Diese Schnittstelle wird im Detail nachfolgend erklärt.

## Schnittstelle zum HGV

Der Datenupload der CSV-Dateien erfolgt über eine gesicherte `HTTPS`-Webschnittstelle.  
Dia Authentisierung erfolgt über [Basic-Authentication](https://de.wikipedia.org/wiki/HTTP-Authentifizierung#Basic_Authentication).  
Nur vom HGV zertifizierte Partner dürfen die Daten an diese Webschnittstelle schicken.

#### Daten welche über die Schnittstelle gepflegt werden, werden im [CSV-Format](https://de.wikipedia.org/wiki/CSV_%28Dateiformat%29) übertragen.

1. Zur Zeichenkodierung wird `UTF-8` verwendet, damit Sonderzeichen korrekt übertragen werden.  
   **Achtung!** Diese Encodierung ist nicht das standardmäßig von Excel verwendete Format, ist jedoch das im Web gängigere und portablere Format.
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

> Eine Überlegung ist eine alternative Schnittstelle zu schaffen, bei welcher die Daten vom HGV abgeholt werden (FTP, SCP, HTTP). Bei erfolgreichem Import werden die Daten anschließend gelöscht.

## Definition der Monatsdaten

### Felder

| ​Englisch                         | ​Deutsch                              | Format          | ​Anmerkungen ​                                                                                                     |
|----------------------------------|--------------------------------------|-----------------|------------------------------------------------------------------------------------------------------------------|
| ​SenderID                         | ​SenderID                             | ​Ganzzahl        | ​wird vom HGV vergeben                                                                                            |
| ​CompanyID        ​                | ​BetriebsID                           | ​anonymisiert    | ​die Betriebs-ID muss so behandelt werden, dass nur der   Steuerberater weiß, um welchen Betrieb es sich handelt. |
| ​Year                             | ​Jahr                                 | ​####            | ​                                                                                                                 |
| ​Month                            | ​Monat                                | ​1-12            | ​                                                                                                                 |
| ​StandardBeds                     | ​AnzahlErwBetten                      | ​Ganzzahl        | ​                                                                                                                 |
| ​ExtraBeds                        | ​AnzahlZusatzBetten                   | ​Ganzzahl        | ​                                                                                                                 |
| ​AdultStays                       | ​NaechtErw                            | ​Ganzzahl        | ​                                                                                                                 |
| ​ChildStays                       | ​NaechtKinder                         | ​Ganzzahl        | ​                                                                                                                 |
| ​BusinessDays                     | ​Offenhaltetage                       | ​Ganzzahl        | ​                                                                                                                 |
| ​HotelBusinessDays                | ​OffenhaltetageHotel                  | ​Ganzzahl        | ​                                                                                                                 |
| ​RestaurantBusinessDays           | ​OffenhaltetageRestaurant             | ​Ganzzahl        | ​                                                                                                                 |
| ​RestaurantSeats                  | ​SitzplaetzeRest                      | ​Ganzzahl        | ​                                                                                                                 |
| ​CampingSeats                     | ​SitzplaetzteCamping                  | ​Ganzzahl        | ​                                                                                                                 |
| ​RentedPitches                    | ​VermieteteStellplaetzeCamping        | ​Ganzzahl        | ​                                                                                                                 |
| ​Employees                        | ​Mitarbeiter                          | ​Ganzzahl ​       | ​inklusive Familienmitglieder, die letzten beiden   Stellen definierten die 2 Nachkommastellen = Wert * 100       |
| ​EmployedFamilyMembers            | ​mitarbeitende   Fam.mitgl.           | ​Ganzzahl        | ​die letzten beiden Stellen definierten die 2   Nachkommastellen = Wert des Kontos * 100​                          |

### Beispiel

```csv
CompanyID;Year;Month;Beds;ExtraBeds
1;2016;1;32;1
1;2016;2;32;3​
```

## Definition der Buchhaltungsdaten

### Felder

| Englisch ​    | ​Deutsch       | ​Format          | ​Beschreibung                                                                                                                         |
|--------------|---------------|-----------------|--------------------------------------------------------------------------------------------------------------------------------------|
| ​SenderID     | ​SenderID      | ​Ganzzahl        | ​wird vom HGV vergeben​                                                                                                                |
| ​CompanyID    | ​BetriebsID    | ​anonymisiert    | ​die ​Betriebs-ID muss so behandelt werden, dass nur   der Steuerberater weiß, um welchen Betrieb es sich handelt                      |
| ​Year        ​ | ​Jahr          | ​####            | ​                                                                                                                                     |
| ​Month        | ​Monat         | ​1-12            | ​                                                                                                                                     |
| ​Account      | ​Konto         | ​Ganzzahl        | ​                                                                                                                                     |
| ​Value        | ​Wert         ​ | ​Ganzzahl        | ​die letzten beiden Stellen die 2 Nachkommastellen   definieren = Wert des Kontos* 100, falls negativ dann mit negativem   Vorzeichen​ |

### Beispiel

```csv
SenderID;CompanyID;Year;Month;Account;Value
1;1;2016;1;61101;3210000
1;1;2016;1;61102;1320000
1;2;2016;1;61101;220000
```





