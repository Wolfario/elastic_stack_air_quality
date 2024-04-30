# Postup vytvoření vizualizací

> Pro správnou funkci vizualizací je nutné nastavit interval zobrazovaného data od roku 2000 do dneška.

## How to Import

Všechny vizualizace se importují najednou ze souboru `export_visualization.json` v adresáři `results`. Chcete-li to provést, přejděte v Kibana do Management > Saved Objects > Import a vyberte soubor `export_visualisation.json`. Poté se vizualizace rozdělí na 3 části, kde je třeba je správně přiřadit k jejich indexům.
Vytvořte Dashboard a přidejte do něj úspěšně přidané vizualizace.

## Vizualizace 1

![Link Name](./screenshots/india_1.png)

Pro vytvoření tohoto grafu typu `Area` byla použita pole `aqi` a `city.keyword` z indexu `india_ap`. Průměrná hodnota (Aggregation > `Average`) pole `aqi` je nastavena pro osu Y a `city.keyword` s Aggregation > `Terms` s počtem 15. 
Vizuálně Mode > `stacked` a také pro seřazení sestupně v Panel Settings bylo vybráno Order Buckets by Sum.

## Vizualizace 2

![Link Name](./screenshots/seoul_1.png)

Pro vytvoření tohoto grafu typu `Horizontal Bar` jsem vzal maximální hodnotu (Aggregation > `Max`) pro `pm10` na ose Y a Aggregation > `Terms` na ose X pro `address.keyword` (počet: 12) z indexu `seoul_ap`.

## Vizualizace 3

![Link Name](./screenshots/mexico_1.png)

Pro vytvoření tohoto grafu typu `Vertical bar` jsem vzal hodnotu Median pro `pm25` na ose Y a Aggregation > `Terms` pro `station_id` velikostí: 150, s rezervou, i když je jich méně. Pro seřazení v sestupném pořadí v Panel Settings bylo vybráno uspořádání Buckets by Sum. Pro zobrazení vodorovných sloupců bylo zapnuté v Panel Settings > Y-Axis Lines > `LeftAxis-1`.

## Vizualizace 4

![Link Name](./screenshots/india_2.png)

Pro vytvoření tohoto grafu typu `Pie` jsem vzal hodnotu pro Slice Size: `Count` (počet hodnot v Split Slices) a pro Split Slices > Aggregation > `Terms` s polem `aqi_bucket.keyword`. Protože jsem chtěl zahrnout i chybějící hodnoty, musíme povolit Data > Show missing values.

## Vizualizace 5

![Link Name](./screenshots/seoul_2.png)

Pro vytvoření tohoto grafu typu `Heat Map` byla jako Metrics zvolena `Count` a pro každou z os X a Y byly vytvořeny tři skupiny (Min, Average a High) obsahu `pm10` a `pm25`. Pro obě osy byla použita Sub Aggregation > Range. Pro `pm10` na ose X (min: 0-30, průměr: 30-50, vysoká: 50-100), pro `pm25` již na ose Y (min: 0-12, průměr: 12-35, vysoká: 35-80). 
Nastavení vizuální častí:
- Options > Color Schema > `Blues`
- Number of colors > 10. 

Výsledkem je matice `Heat Map` 3x3 s poměry 3 kategorií mezi oběma polí `pm10` a `pm25`.
 
## Vizualizace 6

![Link Name](./screenshots/mexico_2.png)

Pro vytvoření tohoto grafu typu `Line` byla vybrána pole `pb`, konkrétně Aggregation > `Percentiles` (pro percentily 1, 5, 25, 50, 75, 95 a 99) pro Y-Axis Aggregation > `Data Range` pole `timestamp_filter` se segmenty *0-2013*, *2013-2014*, *2014-2015*, *2015-2016* a *2016-2017* pro osu X. Z vizuální části byly pro přehlednost zařazeny následující údaje:
- Metics & Axes > Metrics > Mode > `stacked` 
- Metics & Axes > Metrics > Chart Type > `area`
- Metics & Axes > Metrics > Line Mode > `smoothed`

## Vizualizace 7, 8 a 9

Další tři grafy typu `Metric` (pro každý z indexů), v každém z nich bylo do Metrics přidáno 5 hodnot Metric s Aggregation > `Median` pro pole: `no2`, `o3`, `co`, `pm10` a `pm25`. 
Hlavním problémem bylo přeložit indexy `no2`, `o3` a `co`, které byly v indexu `mexico_ap` v PPM a v ostatních indexech v $\frac{μg}{m^3}$. V mexické pipeline logstash byl použit Ruby blok pro tento účel.


Příklad indického grafu:
![Link Name](./screenshots/india_3.png)
