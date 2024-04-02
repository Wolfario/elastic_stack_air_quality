# MongoDB

## Popis technologie
*MongoDB* je zdrojově dostupná multiplatformní NoSQL databáze orientovaná na dokumenty, která používá JSON-like (JSON, BSON or XML) dokumenty.

### Obecné chování
*MongoDB*, na rozdíl od tří zbývajících databází, ze kterých si můžů vybrat, je typem NoSQL databáze **zaměřené na dokumenty**. Jednotlivé dokumenty jsou **standardně kódovány v datovém formátu JSON** (ukládané na disk už ve formátu BSON).

Pro interakci s databází **disponuje vlastním dotazovacím jazykem** (případně vlastní programátorské API), čímž se významně liší od relačních databází.

Ve srovnání s *Redisem*, *Apache Cassandrou* a *Neo4j* umožňuje *MongoDB* **provádět složitější dotazy** a **podporuje indexování** pro rychlé a efektivní vyhledávání dat v databázi.

Je velmi flexibilní, což umožňuje ukládat různé typy dat, včetně textu, čísel, obrázků a dokumentů (dokumenty se mapují na objekty).

*MongoDB* **není omezena na konkrétní databázový model** a u každého objektu není třeba striktně vlastnit každou z položek na rozdíl od relačních databází. Na rozdíl od databází klíč-hodnota **lze objekty vyhledávat podle hodnot v dotazech**.

Na rozdíl od *MongoDB* nemají jiné databáze (*Redis*, *Apache Cassandra* a *Neo4j*) **nativní podporu pro sharding** tak, jak jej implementuje MongoDB (i když *Apache Cassandra* má sharding, v MongoDB provádíme sharding na základě sharding key, nikoli systém partitions-partition key).

### Základní principy
Databáze obsahují **kolekce** a v kolekcích jsou uloženy **dokumenty** (které jsou podobné tabulkám v relačních databázích, ale nemají žádnou danou strukturu), což je exkluzivní pro databáze dokumentového typu, na rozdíl od *Neo4j*, *Cassandra* a *Redis*, ale struktura je velmi podobná struktuře relačních databází. Mezi některými pojmy můžeme vidět podobnosti v účelu, jako Collections (in SQL - tables), Documents (in SQL - rows) a Fields (in SQL columns).

Samotné **dokumenty se skládají z pojmenovaných polí** (fieldů). V rámci dokumentů existuje několik omezení, například dokument musí splňovat syntaxi JSON, délka indexovaného klíče by neměla přesáhnout 1024 bytů a maximální zanoření JSON je 100 (100 subklíčů).

*MongoDB* **podporuje sharding**. Sharding umožňuje rozdělit data mezi mnoho serverů, což **zvyšuje výkon a umožňuje horizontální škálování**. Samotná data jsou rozdělena na několik shardů na základě sharding klíčů, ke kterým mohou rychle získat přístup klientské mongos (spojení mezi nimi konfigurují Config servery), čímž je realizována klient-server architektura. Celkově sharding v *MongoDB* umožňuje **škálovat databázi pro zpracování zvýšené zátěže téměř bez omezení**.

**Má nativní podporu replikace**, což umožňuje **odolnost proti výpadkům** a **zvýšenou dostupnost dat** vytvořením více kopií dat (nazývaných replica-set) a jejich distribucí mezi více uzly. Na začátku jeden blok má roli `Primary` a ostatní jsou `Secondary`. Pokud `Primary` blok přestane být k dispozici, náhodný ze zbývajících `Secondary` bloků je nastaven jako `Primary` a už teď se používá on.

Volba distribuce dat závisí na účelu použití *MongoDB*, takže neexistuje žádný *doporučený* způsob. Použití Indexovaní, Relikace, Sharding poskytuje pro určité účely různé výhody (popsané výše), proto je nutné zvolit správnou kombinaci těchto technologií pro nejlepší účinnost.

### CAP teorém
***MongoDB* **se řídí CP**** (Consistency and Partition Tolerance) v rámci CAP teorému. To znamená, že v případě sítě může obětovat dostupnost ve prospěch konzistence a odolnosti vůči partitioningu. 

Pro naše řešení jsou tyto garance dostačující, protože **preferujeme datovou konzistenci před absolutní dostupností** a **tolerancí vůči partitioningu**. V našem kontextu je klíčové zajistit spolehlivost a konzistenci dat, což MongoDB poskytuje prostřednictvím replikace a konfigurace shardingu podle potřeby.

### Architektura
Architektura řešení je založena na replikační sadě, což je standardním způsobem, jak dosáhnout vysoké dostupnosti v *MongoDB*. Vytvořil jsem replikační skupinu s názvem `rs0`, kde každý uzel odpovídá jednomu kontejneru: `mongo1`, `mongo2` a `mongo3`. V *Docker Compose* jsem nastavil sítě `mongoCluster` pro komunikaci mezi uzly. Data jsou ukládána do oddělených adresářů pro každý uzel pomocí volumes v *Docker Compose*. Využívám replikaci pro zajištění vysoké dostupnosti a odolnosti vůči selhání. Když jeden uzel selže, ostatní uzly v replikační sadě mohou pokračovat v práci a klienti se nemusí starat o výpadek.    

**Nevyužívám sharding** protože aplikace zatím nepotřebuje horizontální škálování pro zvládnutí velkého objemu dat nebo vysokého provozu. Sharding je obvykle potřebný, když jediný server není schopen zvládnout zátěž nebo když data přesahují kapacitu úložiště, ale moje data jsou příliš malá, takže jsem považoval za moudré sharding nepoužívat.

Moje databáze pracuje s dvěma hlavními kolekcemi: `animals` a `dishes`.

V kolekci `animals` jsou ukládána data o zvířatech. Každý dokument obsahuje informace jako `id`, `species` (*druh zvířete*), `name` (*jméno*), `color` (*barva zvířeti*), `gender` (*pohlaví zvířeti*), `zoo_address` (*adresa zoo*), `approx_bith` (*přibližné datum narození*), `favorite_dish` (*Pole obzahující oblíbéná jídla zvířeti*), `price` (*cena*). Data jsou strukturována v podobě objektu, kde každá položka má specifický datový typ (například: `int`, `string`, `ISO date`, `array`, `double`).

V kolekci `dishes` jsou data o jednotlivých jídlech. Každý dokument obsahuje informace jako `name` (*název jídla*), `health_influence` (*vlív na zdráví*) a `price` (*cena*).

**Pro kontrolu integritu dat jsem nastavil validaci pomocí JSON schématu**. Například, zajišťuji, že povinná pole jsou vyplněna, že cena je číslo větší než nula, a oblíbená jídla jsou uchovávána v poli s minimálně jedním prvkem.

Nevybral jsem další datové struktury, protože aktuální schémata pro `animals` a `dishes` odpovídají potřebám mé aplikace a usnadňují strukturované ukládání a dotazování dat.

Pracoval jsem s relativně malým množstvím dat, a proto jsem zatím nezvolil sharding. Volba neshardingování byla motivována aktuální velikostí dat, která není dostatečně velká na to, aby vyžadovala horizontální škálování. Přesto jsem si vědom toho, že s nárůstem objemu dat může dojít k potřebě distribuovat data napříč více uzly pomocí shardingu.

Data jsem ručně vygeneroval pomocí služby [Mockaroo](https://www.mockaroo.com/)

### Perzistence

### Zabezpečení

### Výhody a nevýhody

### Případy užití

## Popis vlastního datasetu

## Závěr
