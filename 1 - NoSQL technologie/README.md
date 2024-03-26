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
Databáze obsahují kolekce a v kolekcích jsou uloženy dokumenty (které jsou podobné tabulkám v relačních databázích, ale nemají žádnou danou strukturu), což je exkluzivní pro databáze dokumentového typu, na rozdíl od Neo4j, Cassandra a Redis, ale struktura je velmi podobná struktuře relačních databází. Mezi některými pojmy můžeme vidět podobnosti v účelu, jako Collections (in SQL - tables), Documents (in SQL - rows) a Fields (in SQL columns).

Samotné dokumenty se skládají z pojmenovaných polí (fieldů). V rámci dokumentů existuje několik omezení, například dokument musí splňovat syntaxi JSON, délka indexovaného klíče by neměla přesáhnout 1024 bytů a maximální zanoření JSON je 100 (100 subklíčů).

MongoDB podporuje sharding. Sharding umožňuje rozdělit data mezi mnoho serverů, což zvyšuje výkon a umožňuje horizontální škálování. Samotná data jsou rozdělena na několik shardů na základě sharding klíčů, ke kterým mohou rychle získat přístup klientské mongos (spojení mezi nimi konfigurují Config servery), čímž je realizována klient-server architektura. Celkově sharding v MongoDB umožňuje škálovat databázi pro zpracování zvýšené zátěže téměř bez omezení.

Má nativní podporu replikace, což umožňuje odolnost proti výpadkům a zvýšenou dostupnost dat vytvořením více kopií dat (nazývaných replica-set) a jejich distribucí mezi více uzly. Na začátku jeden blok má roli Primary a ostatní jsou Secondary. Pokud Primary blok přestane být k dispozici, náhodný ze zbývajících Secondary bloků je nastaven jako Primary a už teď se používá on.

Volba distribuce dat závisí na účelu použití MongoDB, takže neexistuje žádný „doporučený“ způsob. Použití Indexovaní, Relikace, Sharding poskytuje pro určité účely různé výhody (popsané výše), proto je nutné zvolit správnou kombinaci těchto technologií pro nejlepší účinnost.

### CAP teorém
MongoDB se řídí CP (Consistency and Partition Tolerance) v rámci CAP teorému. To znamená, že v případě sítě může obětovat dostupnost ve prospěch konzistence a odolnosti vůči partitioningu. 

Pro naše řešení jsou tyto garance dostačující, protože preferujeme datovou konzistenci před absolutní dostupností a tolerancí vůči partitioningu. V našem kontextu je klíčové zajistit spolehlivost a konzistenci dat, což MongoDB poskytuje prostřednictvím replikace a konfigurace shardingu podle potřeby.

### Architektura

### Zabezpečení

### Výhody a nevýhody

### Případy užití

## Popis vlastního datasetu

## Závěr
