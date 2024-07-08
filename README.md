# SKR42-dolibarr

inspiriert von: https://github.com/hanneshier/dolibarr-skr49

Kontenrahmen SKR42 für Vereine, Stiftungen und gGmbH - zur Gewinnermittlung gem. § 4 Abs. 3 EStG (EÜR)\
Quelle: https://www.datev.de/web/de/datev-shop/material/12902-datev-kontenrahmen-skr-42-vereine-stiftungen-ggmbh-4-abs-3-estg/

- als plain extrahierte [CSV]((skr42_extract.csv) (Spalten "Konto";"Kontoname") zum Weiterverarbeten für alle weiteren Einsatzszenarien
- als imprtfähige CSV und XLSX zum Import in Dolibarr (folgt noch)

## Modifikation der Import-Regel

Bei einem Import der Kontenrahmen in Dolibarr ist die Zeichenlänge für das Feld "Kontenklasse" auf 20 Zeichenbechränkt. Das ist für die Bezeichnung der Konetenklassen laut SKR 42 teilweise zu wenig. Durch eine Manipulation der Datei "/htdocs/core/modules
/modAccounting.class.php" lässt sich das beheben. Ein RegEx-Wert wird wie folgt so angepasst, dass anstatt 20 Zeichen anschließend 99 Zeichen erlaubt sind:

	'$this->import_regex_array[$r] = array('aa.fk_pcg_version'=>'pcg_version@'.MAIN_DB_PREFIX.'accounting_system', 'aa.account_number'=>'^.{1,32}$', 'aa.label'=>'^.{1,255}$', 'aa.account_parent'=>'^.{0,32}$', 'aa.fk_accounting_category'=>'rowid@'.MAIN_DB_PREFIX.'c_accounting_category', ** 'aa.pcg_type'=>'^.{1,20}$', ** 'aa.active'=>'^0|1$', 'aa.datec'=>'^\d{4}-\d{2}-\d{2}$');'
    '$this->import_regex_array[$r] = array('aa.fk_pcg_version'=>'pcg_version@'.MAIN_DB_PREFIX.'accounting_system', 'aa.account_number'=>'^.{1,32}$', 'aa.label'=>'^.{1,255}$', 'aa.account_parent'=>'^.{0,32}$', 'aa.fk_accounting_category'=>'rowid@'.MAIN_DB_PREFIX.'c_accounting_category', ** 'aa.pcg_type'=>'^.{1,99}$', ** 'aa.active'=>'^0|1$', 'aa.datec'=>'^\d{4}-\d{2}-\d{2}$');'

