# Objekte in der DMP

## Ordner
Ordner sind Strukturierungselemente für Dateien.

Auf Ordnern sind folgende Aktionen erlaubt:

- Rename
- Move
- Delete
- Edit Properties

## Dateien
Dateien sind die primären Datenobjekte in der DMP. Genau wie in typischen Dateisystemen enthalten sie beliebige Projektdaten.

Auf Dateien sind folgende Aktionen erlaubt:

-  [alle Ordneroperationen]
-  Group
- Publish
- Qualify
- Define Schema (für CSV-Dateien)

## Dateigruppen
Dateigruppen fassen innerhalb von Ordnern mehrere Dateien in einer Gruppe zusammen. Gruppennamen werden in der DMP auf einer Ebene mit Dateien visualisiert. Unterhalb der Gruppenebene erscheinen die einzelnen Dateien eingerückt und in grauem Font.

NB: Eine Datei kann nicht mehreren Gruppen zugeordnet werden. Sollte dies benötigt werden, so ist eine Kopie das Datenobjekts anzulegen.

Auf Gruppen sind folgende Aktionen erlaubt:

- [alle Dateioperationen]
- Define Schema (für Gruppen von CSV-Dateien)
- Ungroup -> Gruppenverband auflösen

Auf Dateien innerhalb einer Gruppe sind folgende Aktionen erlaubt:

- [alle Ordneroperationen]
- Remove from Group

Folgende Sondertypen von Gruppen werden unterschieden (aktuelle Iteration!):

- Shapefiles
- CSV-Dateien

## Properties
Properties sind Multi-Sets von Key-Value-Pairs, die Ordner, Dateien und Dateigruppen näher beschreiben. Properties können grundsätzlich mehrfach definiert werden (z.B. für Datensatzautoren, Keywords, ...) (Alternative: Wir erlauben Wertlisten als Values)

Die Editieraktionen für Properties sind:

- Anlegen
- Ändern
- Löschen

# Aktionen in der DMP

## Delete
Die Delete-Aktion entfernt Objekte aus der DMP. Löschvorgänge kaskadieren in den CKAN, d.h. publizierte Datensätze werden mit Hilfe des Publishing-Logs auch aus dem CKAN gelöscht. Nach Löschung beider Objekte wird auch der entsprechende Eintrag aus dem Publishing-Log entfernt.

## Group
Die Group-Aktion fasst mehrere Dateien zu einer Gruppe zusammen.

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert mehrere bisher ungruppierte Dateien (durch Shift-Klick im einfachsten Fall)
2. [Group-Symbol erscheint in der Ansicht]
3. Nutzer klickt Gruppierungssymbol
4. DMP fasst Dateien zu einer Gruppe zusammen
   - Gruppenobjekt wird erstellt
   - Eventuell vorhandene Properties der Einzeldateien werden in das Gruppenobjekt gemerged
   - Merge Regeln müssen noch mit Anwendern überprüft werden, für die erste Iteration gilt:
     - Non-Null-Elemente schlagen Null-Elemente
     - neue Non-Null-Elemente ersetzen alte Non-Null-Elemente (upsert)

ENDE: Dateien werden in der Ansicht als Gruppe dargestellt
```

## Add-to-Group
Die Add-to-Group-Aktion fügt

a) Dateien zu einer bestehenden Gruppe hinzu
b) Dateien aus mehreren Gruppen in einer neuen Gruppe zusammen (alte Gruppenverbände werden aufgelöst)
c) Einzeldateien und Dateien aus mehreren Gruppen in einer neuen Gruppe zusammen (alte Gruppenverbände werden aufgelöst und ein neuer erzeugt)

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert mehrere Elemente der Ansicht
   - Eine Gruppe, eine oder mehrere Dateien
   - Mehrere Gruppen
   - Mehrere Gruppen und mehrere Dateien
2. [Add-to-Group-Symbol erscheint in der Ansicht]
3. Nutzer klickt Add-to-Group-Symbol
4. DMP fügt alle Dateien zu einer neuen Gruppe zusammen
   - Gruppenobjekt wird erstellt
   - Eventuell vorhandene Properties der Einzeldateien und -gruppen werden in das Gruppenobjekt gemerged
   - Merge Regeln müssen noch mit Anwendern überprüft werden, für die erste Iteration gilt:
     - Non-Null-Elemente schlagen Null-Elemente
     - neue Non-Null-Elemente ersetzen alte Non-Null-Elemente (upsert)
     - Non-Null-Elemente der Gruppen schlagen Non-Null-Elemente der Dateien
   - Einzeldateien behalten ihre ursprünglichen Properties

ENDE: Dateien werden in der Ansicht als eine gemeinsame Gruppe dargestellt
```

## Ungroup
Die Ungroup-Aktion löst den Gruppenverband von Dateien auf.

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert eine Gruppe
2. [Ungroup-Symbol erscheint in der Ansicht]
3. Nutzer klickt Ungroup-Symbol
4. DMP löst alle Dateiobjekte aus der Gruppe
   [Dateien behalten nur ihre ursprünglichen Properties]

ENDE: Alle Dateien sind aus der Gruppe gelöst und erscheinen wieder eigenständig. Das Gruppenelement existiert nicht mehr.
```

## Remove-from-Group
Die Remove-from-Group-Aktion löst einzelne Dateien aus dem Gruppenverband heraus.

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert datei innerhal einer Gruppe
2. [Remove-from-Group-Symbol erscheint in der Ansicht]
3. Nutzer klickt Remove-from-Group-Symbol
4. DMP löst das Dateiobjekte aus der Gruppe
   [Datei behält nur ihre ursprünglichen Properties]

ENDE: Datei ist aus der Gruppe gelöst und erscheint wieder eigenständig **parallel zum Gruppenelement**
```

## Publish
Die Publish-Aktion veröffentlicht Dateien oder Dateigruppen im CKAN. Um später publizierte Datensätze im CKAN aktualisieren zu können, muss die DMP ein Publishing-Log führen.

Verfügt das zu publizierende Objekt über einen Schema-Descriptor, so wird im Rahmen des Publikationsvorgangs zusätzlich ein angepasster Schema-Descriptor generiert, der die internen DMP-IDs gegen CKAN-IDs substituiert. Dieser zusätzliche Descriptor wird nur im Publishing-Log gespeichert.

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert eine Datei oder Gruppe
2. [Publish-Symbol erscheint in der Ansicht]
3. Nutzer klickt Publish-Symbol
4. DMP startet Publish-Prozess
   4.1. Die DMP prüft intern, ob das zu publizierende Objekt korrekt qualifiziert ist
        - es erscheint eine Warnung [ggf. Fehlermeldung] bei mangelhafter / ungenügender Qualifizierung
   4.2. Die DMP prüft, ob das zu publizierende Objekt bereits publiziert wurde
        - JA: Hat sich der Schema Descriptor verändert?
          - JA: die alten Ressourcen werden aus dem CKAN gelöscht und es wird ein neuer Datensatz im CKAN publiziert (s. nächster Punkt)
          - NEIN: es werden nur die Daten (unter Zuhilfenahme der CKAN-IDs aus dem Publishing-Log) und die ISO-Metadaten aktualisiert
        - NEIN: Es wird ein neuer Datensatz im CKAN publiziert
          - Anpassung des Schema Descriptors: Austausch der DMP-IDs durch CKAN-IDs
          - Übertragung von Daten, ISO-Metadaten und angepasstem Schema Descriptor
          - Speicherung des Publikationsvorgangs im Publishing-Log
        - N/A: (kein Schema Descriptor vorhanden)
          - es werden nur die Daten (unter Zuhilfenahme der CKAN-IDs aus dem Publishing-Log) und die ISO-Metadaten aktualisiert
   4.3. Das Datum der letzten Veröffentlichung wird im Publishing-Log aktualisiert
ENDE: Publish-Prozess ist beendet, normale Dateibrowser-Ansicht wird wieder hergestellt
```

##### Publishing-Log
Im Publishing-Log werden zu jedem Publikationsobjekt folgende Eigenschaften gespeichert:

| Name | Definition | Datentyp / Wert | Kardinalität | Verwendung |
|---|---|---|---|---|
| ObjectID | ID des publizierten Objekts (Datei oder Gruppe)  | DMP-ID | [1] | PFLICHT |
| ID-LUT | Lookup Table DMP-IDs -> CKAN-IDs  | Map(String,String) | [1] | PFLICHT |
| Date | Datum der letzten Publikation | ISO Date | [1] | PFLICHT |
| CKAN-SchemaDescriptor | Angepasster Schema Descriptor f.d. CKAN (mit CKAN-IDs) | SchemaDescriptor | [0..1] | PFLICHT wenn vorhanden |

Objekte im Publishing-Log leben so lange, wie die Objekte in der DMP leben. 

## Qualify

Der Qualify-Prozess sorgt für eine Qualifizierung von Dateien und Gruppen vor der Publikation. Ein Assistent prüft
a) das Vorhandensein notwendiger und optionaler ISO-Metadaten
b) das Vorhandensein eines Schema-Descriptors für CSV-Dateien und Gruppen von CSV-Dateien

NB: Für nicht-georeferenzierte CSV-Dateien bzw. CSV-Dateien ohne Schema Descriptor sollte trotzdem eine Veröffentlichung möglich sein. In diesem Fall erfolgt die Publikation ohne Schema Descriptor und die Daten erscheinen nicht in SOS/REST-API.

##### Einzelaktion - via GUI

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert eine Datei oder Gruppe
2. [Qualify-Symbol erscheint in der Ansicht]
3. Nutzer klickt Qualify-Symbol
   - Bei CSV-Dateien und -gruppen wird die Existenz eines Schema-Descriptors geprüft
     - Falls dieser fehlt:
       -  Abbruch | Qualify | Warnung übergehen 
   - DMP startet den Qualify-Prozess

ENDE: Publish-Prozess ist beendet, normale Dateibrowser-Ansicht wird wieder hergestellt
```

##### Qualify-Prozess (Datei|Gruppe, Properties) - als parametrisierbarer Teilprozess

```
START
1. Laden der Publication-Properties
2. Vorhandene Property-Elemente um fehlende Publication Properties ergänzen
3. Fehlende Property-Werte aus Datensatz ableiten [Algorithmik kann sukzessive verfeinert werden]
4. Validierung der Property-Werte:
   - Korrekter Wert: Property GRÜN markieren
   - Falscher Wert: Property ROT markieren
   - Fehlender Pflichwerte: Property ROT markieren
   - Fehlender empfohlener Werte: Property GELB markieren
   - Sonstiger fehlender Wert: Property GRAU darstellen
5. Nutzer ergänzt Eingaben

ENDE: Properties speichern;
      Rückgabewert:
        OK (kein GELB oder ROT)
        WARN (ein oder mehrere GELB, keines ROT)
        ERROR (ein oder mehrere ROT)
```

##### Publication-Properties

| Name | Definition | Datentyp / Wert | Kardinalität | Verwendung |
|---|---|---|---|---|
| Title | Titel des Datensatzes  | Text ohne Zeilenumbruch | [1] | PFLICHT |
| Abstract | Kurzbeschreibung des Datensatz | Text mit Zeilenumbruch | [0..1] | EMPFOHLEN |
| Date | Datum der Erzeugung, Veröffentlichung oder Aktualisierung | ISO Date | [1] | PFLICHT |
| Author | Author des Datensatzes | Text ohne Zeilenumbruch | [0..1] | EMPFOHLEN |
| Organisation | Zuständige Organisation  | Text ohne Zeilenumbruch | [1] | PFLICHT |
| Email | Kontakt-Email | Mailadresse [REGEX](http://emailregex.com/) | [1] | PFLICHT |
| Phone | Kontakt-Telefon | [REGEX](https://www.safaribooksonline.com/library/view/regular-expressions-cookbook/9781449327453/ch04s03.html) | [0..1] | EMPFOHLEN |
| Spatial extent | Räumliche Ausdehnung, BBOX in WGS84 lat/lon | BBOX String, Bsp: (50.7,7.1,50.8,7.25) | [0..1] | EMPFOHLEN sofern vorhanden |
| Temporal extent | Zeitliche Ausdehnung, Start / Ende | Anfangs- und Enddatum (ISO-String) | [0..1] | EMPFOHLEN sofern vorhanden |
| Sprache | Sprache d. Datensatzes (Attribute etc.) | ISO Language Code | [0..1] | OPTIONAL |
| Keyword | Schlüsselwort für den Datensatz | Text ohne Zeilenumbruch | [0..*] | EMPFOHLEN |
| Theme | Thematik d. Datensatzes | Text ohne Zeilenumbruch | [0..*] | EMPFOHLEN |
| License | Nutzungs- und Weitergabelizenz | Text ohne Zeilenumbruch ODER Creative Commons | [0..1] | EMPFOHLEN |
| Spatial Resolution | Räumliche Auflösung der Daten | Meter oder Grad | [0..1] | EMPFOHLEN sofern vorhanden |
| Temporal Resolution | Zeitliche Auflösung der Daten | ISO-Intervall | [0..1] | EMPFOHLEN sofern vorhanden |
| Source | Herkunft des Datensatz | Text mit Zeilenumbruch | [0..1] | OPTIONAL |


## Define-Schema
Die Define-Schema-Aktion erzeugt einen Schema-Descriptor für CSV-Dateien und Gruppen von CSV-Dateien. Alle Dateireferenzen werden zunächst über DMP-IDs der jeweiligen Objekte abgebildet. Die "Übersetzung" in CKAN-IDs erfolgt erst im Rahmen des Publikationsvorgangs.
