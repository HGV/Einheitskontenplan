---
permalink:
title: HGV-Schnittstelle für den Einheitskontenplan (EKP)
---

## Wie erfolgt die Übertragung?

Es gibt drei verschiedene Endpunkte der Datenübertragung:

1. Stammdaten
1. Buchhaltungsdaten (EKP)
1. Monatsdaten

### Pflege der Stammdaten

Die Stammdaten werden über eine Weboberfläche gepflegt.

### Pflege der Monatsdaten

Die Monatsdaten werden über eine Weboberfläche gepflegt.

Alternativ ist ein automatischer periodischer Import als CSV-Date möglich.

### Pflege der Buchhaltungsdaten

Buchhaltungsdaten werden im [CSV-Format](https://de.wikipedia.org/wiki/CSV_%28Dateiformat%29) übertragen.   

> Das Trennzeichen ist ein Semikolon (`;`)
> Die Encodierung ist `UTF8`

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


## Schnittstelle zum HGV

Der Datenupload der CSV-Dateien erfolgt über eine gesicherte Webschnittstelle.  
Dia Authentisierung erfolgt über OAuth2.  
Nur vom HGV zertifizierte Partner dürfen die Daten an diese Webschnittstelle schicken.

### EKP

Die CSV-Datei wird an den enpunkt `/ekp` per `POST` geschickt.
Content-Type ist `text/csv`

* Bei **erfolgreichem Import** gibt die Schnittstelle den HTTP-Status Code `201 Created` zurück.
* Ist das **CSV-Format fehlerhaft** oder liegt ein sonstiger Fehler im übertragenen Dokument vor git die Schnittstelle den Status Code `400 Bad Request` zurück. Im Body steht die Ursache, wieso die Übertragung nicht erfolgreich war.
* War der Import **aufgrund eines Fehlers beim HGV** nicht erfolgreich gibt die Schnittstelle den Status Code `500 Internal Server Error` zurück. Im Body steht die Fehlerursache.

Es ist zu beachten dass die Übertragung einige Zeit dauern kann, sei es aufgrund dessen, dass die CSV-Datei sehr groß sein kann (bei einer Übertragung mehrerer Betriebe) oder aber der Import der Daten beim HGV länger dauert, da Sanitätschecks gemacht werden müssen und die Daten zusammengeführt werden müssen.

> Eine Überlegung ist eine alternative Schnittstelle zu schaffen, bei welcher die Daten vom HGV abgeholt werden (FTP, SCP, HTTP). Bei erfolgreichem Import werden die Daten einfach gelöscht.