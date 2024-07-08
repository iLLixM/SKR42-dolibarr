# SKR42-dolibarr

inspiriert von: https://github.com/hanneshier/dolibarr-skr49

Kontenrahmen SKR42 für Vereine, Stiftungen und gGmbH - zur Gewinnermittlung gem. § 4 Abs. 3 EStG (EÜR)\
Quelle: https://www.datev.de/web/de/datev-shop/material/12902-datev-kontenrahmen-skr-42-vereine-stiftungen-ggmbh-4-abs-3-estg/

- als plain extrahierte [CSV](skr42_extract.csv) (Spalten "Konto";"Kontoname") zum Weiterverarbeten für alle weiteren Einsatzszenarien
- als direkt [importfähige CSV](SKR42_EuR.csv) in Dolibarr
- als [XLSX](SKR42_EuR.xlsx) zum Bearbeiten, Filtern, etc. 

## Modifikation der Import-Regel

Bei einem Import der Kontenrahmen in Dolibarr ist die Zeichenlänge für das Feld "Kontenklasse" auf 20 Zeichen beschränkt. Das ist für die Bezeichnung der Kontenklassen laut SKR 42 teilweise zu wenig. Durch eine Manipulation der Datei `/htdocs/core/modules/modAccounting.class.php` lässt sich das beheben. Ein RegEx-Wert wird wie folgt so angepasst, dass anstatt 20 Zeichen anschließend 99 Zeichen erlaubt sind:

	ORG: '$this->import_regex_array[$r] = array('aa.fk_pcg_version'=>'pcg_version@'.MAIN_DB_PREFIX.'accounting_system', 'aa.account_number'=>'^.{1,32}$', 'aa.label'=>'^.{1,255}$', 'aa.account_parent'=>'^.{0,32}$', 'aa.fk_accounting_category'=>'rowid@'.MAIN_DB_PREFIX.'c_accounting_category', ** 'aa.pcg_type'=>'^.{1,20}$', ** 'aa.active'=>'^0|1$', 'aa.datec'=>'^\d{4}-\d{2}-\d{2}$');'
    NEW: '$this->import_regex_array[$r] = array('aa.fk_pcg_version'=>'pcg_version@'.MAIN_DB_PREFIX.'accounting_system', 'aa.account_number'=>'^.{1,32}$', 'aa.label'=>'^.{1,255}$', 'aa.account_parent'=>'^.{0,32}$', 'aa.fk_accounting_category'=>'rowid@'.MAIN_DB_PREFIX.'c_accounting_category', ** 'aa.pcg_type'=>'^.{1,99}$', ** 'aa.active'=>'^0|1$', 'aa.datec'=>'^\d{4}-\d{2}-\d{2}$');'

## Import des Kontenplans in Dolibarr

Unter Buchhaltung - Einstellungen - Allgemein sollte die Funktion "Beibehalten der Nullen am Ende eines Buchungskontos ("1200")" aktiviert werden.

Der Import des Kontenplan funktioniert in Dolibarr über Tools - Import-Assistent - Neuer Import - Buchhaltung (erweitert) / Kontenplan. Dort unter csv die Datei [SKR42_EuR.csv](SKR42_EuR.csv) hochladen. Im 4. Schritt müssen jeweils die Spalten der Tabelle den (in der Frontend-Übersetzung) gleichnamigen Datenbankfeldern zugeordnet werden.\
**Wichtig:**  Damit die Referenzen jeweils auf "Übergeordnetes Konto" eingetragen werden können, ist ein zweistufiger Import von Nöten (ansonstem kommt es zu Fehlermeldungen beim Import). Beim ersten Import ist die Zuorndung der Spalte "Übergeordnetes Konto" leer zu lassen.\
Der Import wird anschließend mit der gleichen Datei erneut ausgeführt. In diesem wird nun auch die Spalte "Übergeordnetes Konto" zugeordnet. In Schritt 5 ist dabei allerdings beim Punkt "Schlüssel (Spalte), der zum Aktualisieren der vorhandenen Daten von verwendet wird" der Eintrag "Buchungskonto" zu aktiveren.

Wenn der Kontenplan erneut (bspw. mit Änderungen) importiert werden soll, ist in Schritt 5 einer oder mehrere Werte in der Option "Schlüssel (Spalte), der zum Aktualisieren der vorhandenen Daten von verwendet wird" auszuwählen.

Standardmäßig sind alle Konten deaktiviert! Die benötigten Konten müssen also zuerst aktiviert werden, bevor sie genutzt werden können.
