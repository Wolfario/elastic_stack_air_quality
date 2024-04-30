# Návod ke zprovoznění semestrální práce

## Spuštění nástroje docker-compose

> Před spuštěním se ujistěte, že v logstash > data nejsou žádné soubory nebo adresáře: `dead_letter_queue`, `.lock`, `queue`, `uuid`, `plugins`.

docker-compose se spouští jediným příkazem `docker-compose up -d`.

## Kibana a vytvoření indexů

Po spuštění nástroje docker-compose v libovolném prohlížeči přejděte do rozhraní Kibana na adrese: http://127.0.0.1:5601.

1) Pro vytvoření indexů, přejděte na Management > Index Patterns
2) Zadejte všechny 3 dostupné indexy (mexico_ap, seoul_ap a india_ap). Každý index má stejný Time Filter: `timestamp_filter`.

## Spouštění otázek

Spouštění otázek se provádí v Dev Tools > Console po vytvoření indexů. Otázky jsou ve souboru `QUERIES.md`.

## Vizualizace

Podrobnosti o vizualizaci najdete v adresáři `results` v souborech `HOWTO.md` a `README.md`.

## Vypnutí projektu

Chcete-li projekt vypnout, vypněte docker-compose příkazem: `docker-compose down` a také je nutné smazat soubory (resp. adresáře): `dead_letter_queue`, `.lock`, `queue`, `uuid`, `plugins` v adresáři logstash > data.