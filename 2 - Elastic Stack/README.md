# Elastic Stack

## Kvalita vzduchu v různých oblastech světa

### Popis datasetu
Tři datové sady, které jsem získal z webové služby [Kaggle](https://www.kaggle.com/). Obsahují informace o obsahu různých chemických prvků ve vzduchu a další tematicky související ukazatele, na základě čehož lze usuzovat o kvalitě vzduchu. Liší se lokací, kde byla provedena měření, a lehcé strukturou obsažených informací. Samotné datové sady jsou:
#### Dataset [india_air_pollution_daily](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india)
- 16 sloupců
- 29531 záznamů

Jedná se o poměrně velký dataset (ale je nejmenší tady) o měření ukazatelů vzduchu v **Indii**, který obsahuje jako první dva sloupce `City`, což znamená město v Indii, a `Date`, datum měření ukazatelů. Měření byla provedena v letech 2015 až 2020. Ostatní sloupce kromě posledních dvou, tj. `PM2.5`, `PM10`, `NO`, `NO2`, `NOx`, `NH3`, `CO`, `SO2`, `O3`, `Benzene`, `Toluene` a `Xylene` jsou čísla ve formátu `float`, které znamenají obsah těchto chemických prvků ve vzduchu. Jednotky měření látek se liší, a rád bych o tom diskutoval později, až najdeme sloupce se společnými hodnotami ve všech třech datových sadách. Zbývající dva sloupce jsou `AQI` (Air Quality Index) je speciální index kvality vzduchu vypočítaný na základě měření, a `AQI_Bucket`, slovní charakteristika indexu `AQI` (například: `Poor`, `Moderate`, `Severe`).
Bohužel datový soubor obsahuje dostatečné množství `NaN` (nebo `null`) hodnot pro některé záznamy, což pro nás celkově není tak kritické, protože datový soubor obsahuje více než 29 tisíc záznamů, což nám bude dostatečné pro vizualizaci a analýzu.


#### Dataset [mexico_air_pollution_daily](https://www.kaggle.com/datasets/elianaj/mexico-air-quality-dataset?resource=download&select=stations_daily.csv)
- 27 sloupců
- 231592 záznamů

Dataset o měření ukazatelů vzduchu v **Mexiku**. První dva sloupce představují `datetime`, což je *timestamp* měření a `station_id`, což je identifikační číslo stanice, na níž bylo měření provedeno (bohužel nemáme podrobnější informace o stanicích, ale hodnoty `station_id` se pohybují v rozmezí od 32 do 426, což naznačuje poměrně široké pokrytí území statu). Měření byla provedena v letech 2000 až 2021. Ostatní sloupce, tentokrát až do konce (protože `AQI` a `AQI_Bucket` již nebyly přepočítány), stejně jako v minulém datovém souboru obsahují informace o obsahu chemických látek a dalších drobnostech o vzduchu. Konkrétně se jedná o sloupce `PM2.5`, `PM10`, `NOx`, `O3`, `CO`, `HR` (relativní vlhkost), `NO`, `NO2`, `TMP`, `BEN`, `CH4`, `CN`, `CO2`, `H2S`, `HCNM`, `HCT`, `HRI` (interní relativní vlhkost), `IUV` (index ultrafialového záření), `PB` (atmosférický tlak), `PP` (srážky v mm), `PST` (celkově suspendované částice), `RS` (sluneční záření), `TMPI`, `UVA` (ultrafialové záření), `XIL`. Upozorním, že `TMP` je obecná teplota v °C a `TMPI` je interní teplota, také v °C. Sloupce `PST`, `HCT`, `H2S` a `CO2` neobsahují žádné záznamy (budou odstraněny). 
Stejně jako v předchozím datovém souboru je zde mnoho hodnot `null` a `NaN` v záznamech, což nám však moc nebrání při vizualizaci a analýze.

#### Dataset [seoul_air_pollution_hourly](https://www.kaggle.com/datasets/bappekim/air-pollution-in-seoul)
- 11 sloupců
- 647511 záznamů

Dataset měření různých ukazatelů ve vzduchu v jihokorejském hlavním městě Soulu. Tento dataset je největší podle počtu záznamů ze všech tří ostatních, nicméně obsahuje méně sloupců a tudíž informací o různých ukazatelích ve vzduchu. Měření byla provedena v období mezi lety 2017 a 2019 ve 25 oblastech Soulu.
První sloupec `Measurement date` obsahuje hodinový *timestamp* měření. `Station code` a `Address` jsou informace o umístění měření, konkrétně, identifikační číslo od 101 do 125 a plná adresa (`string`) měřicí stanice. `Latitude` a `Longitude` jsou výška a šířka umístění stanice, což se může hodit pro naložení dat na mapu. Ostatní sloupce jsou již známé, obsahují informace o ukazatelích měření vzduchu: `SO2`, `NO2`, `O3`, `CO`, `PM10`, `PM2.5`.
Tato databáze je vysoce kvalitní a neobsahuje žádná chybějící data. Každý záznam měření obsahuje informace o každém sloupci.

#### Propojení
Společné sloupce ve všech třech datových sadách:
- `timestamp`. Všechny datasety se překrývají v časových rozměrech mezi lety 2017 a 2019, což umožňuje jejich spojení v tomto datovém intervalu pro společnou analýzu a vizualizace.
- `PM10` a `PM2.5`. PM - particulate matter (polétavý prach). Polétavý prach je definován jako počet částic s průměrem 2,5 (`PM2.5`) resp. 10 (`PM10`) mikronů nebo méně. Ve všech třech datových sadách jsou tyto hodnoty měřeny v $\frac{μg}{m^3}$.
- `NO2`, `O3` a `CO`. V mexické datové sadě jsou podobné hodnoty měřeny v PPM (částic na milion) zatímco v indické jsou v $\frac{μg}{m^3}$ (`CO` v $\frac{mg}{m^3}$), a informace o jednotkách měření v Soulském datasetu chybí. 
**Při snaze o srovnání těchto sloupců bude třeba převodu PPM na $\frac{μg}{m^3}$ nebo naopak**.

### Zdroje
- Služba s datovými sadami **Kaggle** - https://www.kaggle.com/

### Formát dat
- Dataset [india_air_pollution_daily](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india)
    - `city` - `string` název města v Indii.
    - `timestamp_filter` - `date` formatu `yyyy-MM-dd`.
    - `pm25` - `float` particulate matter (polétavý prach) pro 2.5${μm}$ v $\frac{μg}{m^3}$.
    - `pm10`- `float` particulate matter (olétavý prach) pro 10${μm}$ v $\frac{μg}{m^3}$.
    - `no` - `float` oxid dusnatý v $\frac{μg}{m^3}$.
    - `no2` - `float` dioxid dusnatý v $\frac{μg}{m^3}$.
    - `nox` - `float` jakýkoli nitric x-oxid v PPB (parts per billion).
    - `nh3` - `float` amoniak v $\frac{μg}{m^3}$.
    - `co`- `float` kysličník uhelnatý v $\frac{mg}{m^3}$.
    - `so2`- `float` kysličník siřičitý v $\frac{μg}{m^3}$.
    - `o3`- `float` ozón v $\frac{μg}{m^3}$.
    - `benzene`- `float` benzen v $\frac{μg}{m^3}$.
    - `toluene`- `float` toluen v $\frac{μg}{m^3}$.
    - `xylene`- `float` xylen v $\frac{μg}{m^3}$.
    - `aqi` - `float` Air Quality Index.
    - `aqi_bucket` - `string` slovní popis AQI.
- Dataset [mexico_air_pollution_daily](https://www.kaggle.com/datasets/elianaj/mexico-air-quality-dataset?resource=download&select=stations_daily.csv)
    > Sloupce, které neobsahovaly žádné záznamy, zde nejsou!
    - `timestamp_filter` - `date` formatu `yyyy-MM-dd`.
    - `station_id` - `integer` identifikační číslo měřicí stanice.
    - `pm25` - `float` particulate matter (polétavý prach) pro 2.5${μm}$ v $\frac{μg}{m^3}$.
    - `pm10` - `float` particulate matter (olétavý prach) pro 10${μm}$ v $\frac{μg}{m^3}$.
    - `nox` - `float` jakýkoli nitric x-oxid v PPB (parts per billion).
    - `o3` - `float` ozón v PPM (parts per million).
    - `co` - `float` kysličník uhelnatý v PPM (parts per million).
    - `hr` - `float` relativní vlhkost v procentech.
    - `no` - `float` oxid dusnatý v PPM.
    - `no2` - `float` dioxid dusnatý v PPM.
    - `tmp` - `float` templota v °C.
    - `benzene` - `float` benzen v PPM.
    - `ch4` - `float` metan v PPM.
    - `cn` - `float` černý uhlík v PPM.
    - `hcnm` - `float` nemethanové uhlovodíky v PPM.
    - `hri` - `float` relativní interní vlhkost v procentech.
    - `iuv` - `float` ultrafialový index.
    - `pb` - `float` barometrický tlak v ${mmHg}$.
    - `pp` - `float` srážky v ${mm}$
    - `rs` - `float` solární radiace v $\frac{W}{m^2}$.
    - `tmpi` - `float` relativní interní teplota v °C.
    - `uva` - `float` ultrafialová radiace v $\frac{mW}{m^2}$.
    - `xylene` - `float` xylen v PPM.


- Dataset [seoul_air_pollution_hourly](https://www.kaggle.com/datasets/bappekim/air-pollution-in-seoul)
    - `timestamp_filter` - `date` formatu `yyyy-MM-dd HH:mm`.
    - `station_code` - `integer` identifikační číslo měřicí stanice.
    - `address` - `string` úplná adresa měřicí stanice.
    - `location` - `geo_point` (v době vytvoření je stále ve formátu `array` protože nefunguje přeložení do `geo_point`) který obsahuje zeměpisnou šířku a délku (`latitude` a `longitude` jsou ve `float` formátu) a jsou souřadnicemi umístění stanice.
    - `so2` - `float` kysličník siřičitý v $\frac{μg}{m^3}$.
    - `no2` - `float` dioxid dusnatý v $\frac{μg}{m^3}$.
    - `o3` - `float` ozón v $\frac{μg}{m^3}$.
    - `co` - `float` kysličník uhelnatý v $\frac{μg}{m^3}$.
    - `pm10` - `float` particulate matter (olétavý prach) pro 10${μm}$ v $\frac{μg}{m^3}$.
    - `pm25` - `float` particulate matter (polétavý prach) pro 2.5${μm}$ v $\frac{μg}{m^3}$.


### Provedené úpravy dat

## Závěr
