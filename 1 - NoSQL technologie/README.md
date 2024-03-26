# MongoDB

## Popis technologie
MongoDB je zdrojově dostupná multiplatformní NoSQL databáze orientovaná na dokumenty, která používá JSON-like (JSON, BSON or XML) dokumenty.

### Obecné chování
MongoDB, na rozdíl od tří zbývajících databází, ze kterých si můžů vybrat, je typem NoSQL databáze zaměřené na dokumenty. Jednotlivé dokumenty jsou standardně kódovány v datovém formátu JSON (ukládané na disk už ve formátu BSON).

Pro interakci s databází disponuje vlastním dotazovacím jazykem (případně vlastní programátorské API), čímž se významně liší od relačních databází.

Ve srovnání s Redisem, Apache Cassandrou a Neo4j umožňuje MongoDB provádět složitější dotazy a podporuje indexování pro rychlé a efektivní vyhledávání dat v databázi.

Je velmi flexibilní, což umožňuje ukládat různé typy dat, včetně textu, čísel, obrázků a dokumentů (dokumenty se mapují na objekty).

MongoDB není omezena na konkrétní databázový model a u každého objektu není třeba striktně vlastnit každou z položek na rozdíl od relačních databází. Na rozdíl od databází klíč-hodnota lze objekty vyhledávat podle hodnot v dotazech.

Na rozdíl od MongoDB nemají jiné databáze (Redis, Apache Cassandra a Neo4j) nativní podporu pro sharding tak, jak jej implementuje MongoDB (i když Apache Cassandra má sharding, v MongoDB provádíme sharding na základě sharding key, nikoli systém partitions-partition key).

### Základní principy

### CAP teorém

### Architektura

### Zabezpečení

### Výhody a nevýhody

### Případy užití

## Popis vlastního datasetu

## Závěr
