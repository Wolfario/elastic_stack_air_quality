# 3 Otázky pro ElastiSearch:

1. V indexu `india_ap` najděte a vypište město (`city`), datum (`timestamp_filter`) a hodnotu `AQI`, pro 3 záznamy pro které byl tento index `AQI` nejvyšší (resp. nejnižší) za celé období měření.

```json
// pro nejvyšší (velmi špatná kvalita vzduchu)
GET /india_ap/_search
{
  "size": 3,
  "sort": [
    {
      "aqi": {
        "order": "asc"
      }
    }
  ],
  "query": {
    "match_all": {}
  },
  "_source": ["city", "timestamp_filter", "aqi"]
}

// pro nejnižší (nejlepší kvalita vzduchu)
GET /india_ap/_search
{
  "size": 3,
  "sort": [
    {
      "aqi": {
        "order": "desc"
      }
    }
  ],
  "query": {
    "match_all": {}
  },
  "_source": ["city", "timestamp_filter", "aqi"]
}
```

2. V indexu `seoul_ap` použijte wildcard hledání k nalezení takových zaznamů, jejichž adresy obsahují „Gangdong“, a najděte mezi nimi nejvyšší ukazatel sloupce `so2` a nejnižší a vypište je.

```json
GET /seoul_ap/_search
{
  "query": {
    "wildcard": {
      // obsahuje substring "Gangdong"
      "address.keyword": {
        "value": "*Gangdong*"
      }
    }
  },
  "size": 0,
  "aggs": {
    "max_so2": {
      "max": {
        "field": "so2"
      }
    },
    "min_so2": {
      "min": {
        "field": "so2"
      }
    }
  }
}
```
Spuštěním tohoto dotazu si můžeme všimnout, že **Soulský index obsahuje neplatná naměřená data**, protože hodnota *-1* nemůže být platná (dataset obsahuje také měření s hodnotou *0*, což také naznačuje jeho neplatnost). Protože chceme platné výsledky pro naši otázku, přidáme filtr pro hodnoty větší než 0.

```json
// opravený dotaz s filtrem gt 0
GET /seoul_ap/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "wildcard": {
            "address.keyword": {
              // obsahuje substring "Gangdong"
              "value": "*Gangdong*"
            }
          }
        }
      ],
      "filter": [
        {
          "range": {
            "so2": {
              // validní záznamy mají hodnotu větší než 0
              "gt": 0
            }
          }
        }
      ]
    }
  },
  "size": 0,
  "aggs": {
    "max_so2": {
      "max": {
        "field": "so2"
      }
    },
    "min_so2": {
      "min": {
        "field": "so2"
      }
    }
  }
}
```

3. Najděte v indexu `mexico_ap` čísla stanic (`station_id`) v rozsahu od 50 do 120 včetně, které obsahují největší hodnoty polí: `pm25`, `pm10`, `nox`, `o3`, `co`, `no`, `tmp`.

```json
GET /mexico_ap/_search
{
  "size": 0,
  "query": {
    "range": {
      // hledáme ID stanic v daném intervalu
      "station_id": {
        "gte": 50,
        "lte": 120
      }
    }
  },
  "aggs": {
    "max_pm25": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          // size 1 a descending pro max_value je největší hodnota
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            // pro pole pm25 a obdobně pro ostatní níž
            "field": "pm25"
          }
        }
      }
    },
    "max_pm10": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "pm10"
          }
        }
      }
    },
    "max_nox": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "nox"
          }
        }
      }
    },
    "max_o3": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "o3"
          }
        }
      }
    },
    "max_co": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "co"
          }
        }
      }
    },
    "max_no": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "no"
          }
        }
      }
    },
    "max_tmp": {
      "terms": {
        "field": "station_id",
        "size": 1,
        "order": {
          "max_value": "desc"
        }
      },
      "aggs": {
        "max_value": {
          "max": {
            "field": "tmp"
          }
        }
      }
    }
  }
}
```

V `key` je vysledný `station_id` a `"max_value": {"value"}` je ta největší hodnota měření.