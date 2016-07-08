# Objekte in der DMP

## Ordner
Ordner sind Strukturierungselemente für Dateien.

Auf Ordnern sind folgende Aktionen erlaubt:
- Rename
- Move
- Delete


## Dateien
Dateien sind die primären Datenobjekte in der DMP. Genau wie in typischen Dateisystemen enthalten sie beliebige Projektdaten.

Auf Dateien sind folgende Aktionen erlaubt:
- [alle Ordneroperationen]
- Group
- Publish
- Qualify
- Define Schema (für CSV-Dateien)

## Dateigruppen
Dateigruppen fassen innerhalb von Ordnern mehrere Dateien in einer Gruppe zusammen. Gruppennamen werden in der DMP auf einer Ebene mit Dateien visualisiert. Unterhalb der Gruppenebene erscheinen die einzelnen Dateien eingerückt und in grauem Font

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

# Aktionen in der DMP

## Group
Die Group-Aktion fasst mehrere Dateien zu einer Gruppe zusammen.

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert mehrere bisher ungruppierte Dateien
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
c) Einzeldateien und Dateien aus mehreren Gruppen in einer neuen Gruppe zusammen

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

ENDE: Datei ist aus der Gruppe gelöst und erscheint wieder eigenständig
```

## Publish
Die Publish-Aktion veröffentlicht Dateien oder Dateigruppen im CKAN

```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert eine Datei oder Gruppe
2. [Publish-Symbol erscheint in der Ansicht]
3. Nutzer klickt Publish-Symbol
4. DMP startet Publish-Prozess
   - die DMP prüft intern, ob das zu publizierende Objekt korrekt qualifiziert ist
     - es erscheint eine Warnung [ggf. Fehlermeldung] bei mangelhafter / ungenügender Qualifizierung

ENDE: Publish-Prozess ist beendet, normale Dateibrowser-Ansicht wird wieder hergestellt
```

## Qualify

Der Qualify-Prozess sorgt für eine Qualifizierung von Dateien und Gruppen vor der Publikation. Ein Assistent prüft
a) das Vorhandensein notwendiger und optionaler ISO-Metadaten
b) das Vorhandensein eines Schema-Descriptors für CSV-Dateien und Gruppen von CSV-Dateien

##### Einzelaktion - via GUI
```
START: Nutzer befindet sich im Dateibrowser

1. Nutzer markiert eine Datei oder Gruppe
2. [Qualify-Symbol erscheint in der Ansicht]
3. Nutzer klickt Qualify-Symbol
   - Bei CSV-Dateien und -gruppen wird die Existenz eines Schema-Descriptors geprüft
     - Falls dieser fehlt: Abbruch mit Hinweis. [alternativ: Start des Qualify-Prozesses]
   - DMP startet den Qualify-Prozess

ENDE: Publish-Prozess ist beendet, normale Dateibrowser-Ansicht wird wieder hergestellt
```

#####  Qualify-Prozess (Datei|Gruppe, Properties) - als Parametrisierter Teilprozess
```
START
1. 

ENDE
```

## Define-Schema
