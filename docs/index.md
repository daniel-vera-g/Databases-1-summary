- [Relationale Datenbanken](#relationale-datenbanken)
  * [Beziehungen](#beziehungen)
- [Database management system](#database-management-system)
- [SQL](#sql)
  * [Begriffe](#begriffe)
  * [Datentypen](#datentypen)
  * [Operatoren](#operatoren)
  * [Datenabfragen](#datenabfragen)
  * [Unterabfragen](#unterabfragen)
    + [Θ Theta Operatoren](#%CE%B8-theta-operatoren)
    + [Funktionen](#funktionen)
      - [Skalare Funktionen](#skalare-funktionen)
      - [Aggregatfunktionen](#aggregatfunktionen)
    + [Gruppierungen](#gruppierungen)
      - [GROUP BY auf Aggregatfunktionen](#group-by-auf-aggregatfunktionen)
      - [HAVING](#having)
      - [Reihenfolge Ausführung der Anweisungen](#reihenfolge-ausfuhrung-der-anweisungen)

# Relationale Datenbanken

<!-- TODO beschr. der Aufgaben des dbms  ? -->

![Basics](./img/basics.png)
![Basics 2](./img/basics2.png)

Ubersicht:

![Overview](./img/overview.png)

* Grad n: Anzahl der Domänen, über denen Tupel in Relation definiert sind(Relativ fest).
* Kardinalität: Anzahl der Tupel in einer Relation(Sehr dynamisch).

## Beziehungen

> Gegenseitige Referenzierung durch Schluessel & Fremdschluessel

1. Kandidatenschluessel: Zusammenstellung Attributwerte, bei der Werte der Attribute Tupel eindeutig identifizieren

* Relation kann aus einen oder mehrere Attributennamen bestehen.
* Umfasst Werte von so vielen Attributennamen wie nötig und so wenig wie möglich.

2. Primärschlüssel: Spezieller Kandidatenschluessel, der zum Primärschlüssel erklärt wird

* Werte mehrerer Attribute umfassen, oder als neues Attribut(**id**) speziell zu diesem Zweck eingeführt.
* Für Fremdschluessel Bezeichnung verwendet.
* Manchmal `unterstrichen` dargestellt

3. Surrogatschluessel: Primärschlüssel als neues Attribut(Nicht aus Daten in Tabelle)

* Als __UUID__

4. Fremdschluessel: Relationen miteinander zu verknüpfen und die referenzielle Integrität der DB zu gewährleisten.

* Immmer einen existierenden Primärschlüssel einer anderen Relation referenzieren
* Meist mit vorangestellten `#` notiert.

# Database management system

![DBMS](./img/dbms.png)

<!-- TODO beschr. der Einzelnen Schichten  ? -->

# SQL

## Begriffe

1. Katalog:

* DB besitzt mehrere Kataloge.
* Jeder Katalog besitzt mehrere Schemata.

2. Schema:

* In jeder DB mind. 1 Schema.
* In jedem Schema kann es Tabellen, Sichten, ... mit eindeutigen Namen geben.

## Datentypen

![Datentypen](./img/datentypen1.png)
![Datentypen](./img/datentypen2.png)

## Operatoren

![Operatoren](./img/operatoren.png)

## Datenabfragen

Syntax:

```SQL
SELECT <expression>
  FROM <table-name>
    [WHERE <search-condition>]
    [GROUP BY...[HAVING...]]
```

Hilfreiche SQL commands:

* `DISTINCT`: Keine Duplikate
* `AS`: Umbenennung der Ergebnisspalten
* `ORDER BY`: Sortierung des Ergebnisses(`DESC` oder `ASC`)
* `BETWEEN <x> AND <y>`: Restriktionen mit Bereich
* `AND`: Restriktionen mit mehreren Bedingungen
* `LIKE`: Restriktionen mit Pattern matching:
  * `%` ~ `*`: Beliebige Zeichenfolge
  * `_`  ~ `?`: Beliebiges Zeichen
  * `\`: Escape für obere Zeichen

## Unterabfragen

> In Klammern gesetzte "neue" Abfragen, welche die Hauptaufgabe mit Werten versorgt

1. Unterabfragen, die **genau** einen Wert liefern.
2. Unterabfragen, **mehrere** Werte liefern.

In `WHERE` Hauptabfrage Mengenoperationen nutzen, wenn in Unterabfragen mehrere Ergebniszeile liefert:

1. `IN`: Wert mind. einmal in Ergebnis der Unterabfragen enthalten
2. `EXISTS`: Wenigstens eine Zeile der Unterabfrage eine Bedingung erfüllt
3. `Θ ANY` oder `Θ SOME`: Irgendeine Zeile eine Bedingung der Operatoren für Mengenvergleiche(=,<>, <=, >=... => Theta-Operatoren Θ) erfüllt.
4. `ALL`: Alle Zeilen den Operator Θ erfüllen.
5. `NOT`: Negation. Nutzbar in Kombination mit Bspw. `EXISTS`

### Θ Theta Operatoren

Vorgang:

1. Kreuzprodukt bilden(Bspw. mit doppelten Werten)
2. Anwendung eines der Theta-Operatoren(`=,<>,...`) auf Kreuzprodukt -> Alle Zeilen, die Bedingung erfüllen werden ausgewählt.

* **Korrelierte Unterabfrage**: Unterabfrage nimmt direkten Bezug auf Hauptabfrage
* Mit **AS** Schluesselwort den Tabellennamen abkürzen

### Funktionen

1. _Skalare Funktionen_ = Beziehen sich auf **ein Attribut**
2. _Aggregatfunktionen_ = Beziehen sich auf Menge von Zeilen

* `NULL`-Werte werden außer bei `COUNT(*)` nicht mit betrachtet.

#### Skalare Funktionen

* Mathematische Funkt.: `ABS, ACOS, ASIN, ATAN, ...`
* Funktionen für Zeichenketten: `CHARINDEX/INSTR, LEFT, LEN(GTH), LOWER, ...`
* Funktionen zur Zeit- & Datumsdokumentation:`CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP, ...`

#### Aggregatfunktionen

> Dienen zur Berechnung von Summe, Maximum, etc. eines Attributs über mehrere Zeilen hinweg

Beispiele: `AVG`, `MAX`, `MIN`, `SUM`, `STDDEV`(Standardabweichung der Werte), `COUNT`(Anzahl Werte)

### Gruppierungen

#### GROUP BY auf Aggregatfunktionen

Durch Schluesselwort `GROUP BY` lassen sich Aggregatfunktionen auch auf Teilmengen von Zeilen anwenden.

Bspw.:

* `SELECT lecturer_account AS acc, COUNT(grade) FROM applications GROUP BY lecturer_account` => Gruppiere identische Accounts & Zähle Noten je Gruppe(Wie viele Arbeiten hat jeder Dozent betreut?).
* Es wird erst das `GROUP BY` ausgeführt und dann, die Aggregatfunktion.
* Die angegeben Spaltenliste bestimmt Zeilen, die gruppiert werden.
* Alle Spalten des SELECT's **müssen** in der GROUP BY-Klausel gebunden werden, außer jene Spalten des SELECT's die Aggregatfunktionen sind!

#### HAVING

> Teilmengen, die durch Aggregatfunktionen minimiert werden **einschränken**

1. `WHERE` schränkt *einzelne Zeilen* ein
2. `HAVING` schränkt *ganze Gruppen von Zeilen* ein

* Vorgang: Filterung der Einzelzeilen(WHERE) -> Gruppierung(GROUP BY) -> Einschränkung(HAVING)
* Verwendet oft Aggregatfunktionen, da es sich oft auf Gruppen bezieht

#### Reihenfolge Ausführung der Anweisungen

1. *Kreuzprodukt* über alle Tabellen, die FROM spezifiziert
2. *Restriktion*: Zeilen bestimmen, welche die WHERE-Bedingung erfüllen
3. *Projektion*: Spalten entnehmen, die SELECT Klausel entstprechen & ggf. umbenennen
4. *Gruppenbildung*: Nach GROUP BY
5. *Grupper-Restriktion*: Nach HAVING
6. *Mengenbildung* mit anderen SELECT's(Bspw. UNION)
7. *Sortieren* nach ORDER BY

###

<!-- WIP Slide XX -->
