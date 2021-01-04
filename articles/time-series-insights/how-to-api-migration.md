---
title: Migrowanie do nowych wersji interfejsu API Azure Time Series Insights Gen2 | Microsoft Docs
description: Jak zaktualizować środowiska Azure Time Series Insights Gen2, aby korzystać z nowych, ogólnie dostępnych wersji.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740559"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrowanie do nowych wersji interfejsu API Azure Time Series Insights Gen2

## <a name="overview"></a>Omówienie

Jeśli utworzono środowisko Azure Time Series Insights Gen2, gdy było ono w publicznej wersji zapoznawczej (przed 16 lipca 2020), zaktualizuj środowisko TSI, aby użyć nowych ogólnie dostępnych wersji interfejsów API, wykonując kroki opisane w tym artykule. Ta zmiana nie ma wpływu na użytkowników, którzy korzystają z wersji Gen1 Azure Time Series Insights.

> [!IMPORTANT]
> Aktualizacje opisane w tym artykule spowodują uaktualnienie wersji interfejsu API używanego w środowisku TSI. Ta zmiana nie odnosi się do nowych [reguł spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md) wprowadzonych dla środowisk Gen2.

Nowa wersja interfejsu API to `2020-07-31` i używa zaktualizowanej [składni wyrażenia szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Użytkownicy muszą migrować [zmienne modelu szeregów czasowych](./concepts-variables.md)środowiska, zapisane zapytania, Power BI zapytania i wszelkie niestandardowe narzędzia wywołujące wywołania do punktów końcowych interfejsu API. Jeśli masz jakieś pytania lub wątpliwości dotyczące procesu migracji, Prześlij bilet pomocy technicznej za pomocą Azure Portal i zapoznaj się z tym dokumentem.

> [!IMPORTANT]
> Wersja interfejsu API wersji zapoznawczej `2018-11-01-preview` będzie nadal obsługiwana do 31 października 2020. Przed upływem tego celu należy wykonać wszystkie odpowiednie kroki tej migracji, aby uniknąć przerw w działaniu usługi.

## <a name="migrate-time-series-model-and-saved-queries"></a>Migruj model szeregów czasowych i zapisane zapytania

Aby ułatwić użytkownikom Migrowanie [zmiennych modelu szeregów czasowych](./concepts-variables.md) i zapisanych zapytań, istnieje wbudowane narzędzie dostępne za pomocą [Eksploratora Azure Time Series Insights](https://insights.timeseries.azure.com). Przejdź do środowiska, które chcesz zmigrować, i postępuj zgodnie z poniższymi instrukcjami. **Możesz ukończyć migrację częściowo i wrócić do jej wykonania w późniejszym czasie, jednak żadna z tych aktualizacji nie może zostać cofnięta.**

> [!NOTE]
> Aby zaktualizować model szeregów czasowych i zapisane zapytania, musisz być współautorem środowiska. Jeśli nie jesteś współautorem, będziesz mieć możliwość migrowania osobistych zapisanych zapytań. Sprawdź [zasady dostępu do środowiska](./concepts-access-policies.md) i poziom dostępu przed kontynuowaniem.

1. W Eksploratorze zostanie wyświetlony monit o zaktualizowanie składni używanej przez zmienne modelu szeregów czasowych i zapisanych zapytań.

    [![Monit](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Jeśli powiadomienie zostanie przypadkowo zamknięte, można je znaleźć w panelu powiadomień.

1. Kliknij przycisk **Pokaż aktualizacje** , aby otworzyć narzędzie migracji.

1. Kliknij pozycję **typy plików do pobrania**. Ponieważ migracja zastąpi bieżące typy, aby można było zmienić składnię zmiennej, konieczne będzie zapisanie kopii bieżących typów. Narzędzie wyświetli powiadomienie, gdy typy zostały pobrane.

    [![Typy plików do pobrania](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kliknij pozycję **Aktualizuj zmienne**. Narzędzie wyświetli powiadomienie, gdy zmienne zostały zaktualizowane.

    > [!IMPORTANT]
    > W przypadku aktualizowania typów aplikacje niestandardowe korzystające ze starszej wersji interfejsu API ( `2018-11-01-preview` ) będą musiały kontynuować pracę przy użyciu nowej wersji interfejsu API ( `2020-07-31` ). Jeśli nie masz pewności, której wersji interfejsu API używasz, skontaktuj się z administratorem przed aktualizacją. Możesz zamknąć narzędzie migracji i wrócić do tych kroków później. Dowiedz [się więcej na temat migrowania aplikacji niestandardowych](#migrate-custom-applications).

    [![Aktualizuj zmienne](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kliknij przycisk **Aktualizuj zapisane zapytania**. Narzędzie wyświetli powiadomienie, gdy zapisane zapytania zostaną zaktualizowane.

    [![Aktualizuj zapisane zapytania](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kliknij przycisk **Gotowe**.

    [![Zakończono migrację](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Zapoznaj się z zaktualizowanym środowiskiem, tworząc wykresy niektórych nowo utworzonych zmiennych i zapisanych zapytań. Jeśli podczas wykonywania wykresów zobaczysz nieoczekiwane zachowanie, prześlij nam swoją opinię za pomocą narzędzia do oceny w Eksploratorze.

## <a name="migrate-power-bi-queries"></a>Migrowanie zapytań Power BI

Jeśli wygenerowałeś zapytania przy użyciu łącznika Power BI, nastąpi wywołanie do Azure Time Series Insights przy użyciu wersji interfejsu API podglądu i starej składni wyrażenia szeregów czasowych. Te zapytania będą nadal pomyślnie pobierać dane, dopóki interfejs API wersji zapoznawczej nie zostanie uznany za przestarzały.

Aby zaktualizować zapytania do korzystania z nowej wersji interfejsu API i nowej składni wyrażeń szeregów czasowych, należy ponownie wygenerować zapytania z Eksploratora. Przeczytaj więcej na temat [tworzenia zapytań przy użyciu łącznika Power BI](./how-to-connect-power-bi.md).

> [!NOTE]
> Musisz mieć co najmniej 2020 lipca Power BI Desktop. Jeśli tego nie zrobisz, może zostać wyświetlona błąd "Nieprawidłowa wersja ładunku zapytania".

## <a name="migrate-custom-applications"></a>Migrowanie aplikacji niestandardowych

Jeśli aplikacja niestandardowa przeprowadza wywołania do następujących punktów końcowych REST, wystarczy zaktualizować wersję interfejsu API do poziomu `2020-07-31` identyfikatora URI:

- Interfejsy API modelu szeregów czasowych
  - Interfejsy API ustawień modelu
    - [Pobierz](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Aktualizowanie](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Interfejsy API wystąpienia
    - [Wszystkie operacje wsadowe](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Wyszukiwanie](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Sugerowanie](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - Interfejsy API hierarchii
    - [Wszystkie operacje wsadowe](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Interfejsy API
    - [Usuń, Pobierz operacje](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

W przypadku następujących punktów końcowych REST należy zaktualizować wersję interfejsu API do `2020-07-31` wartości w identyfikatorze URI i upewnić się, że wszystkie wystąpienia `tsx` Właściwości używają zaktualizowanej [składni wyrażenia szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax).

- Interfejsy API
  - [Operacja Put](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- Interfejsy API zapytań
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [Getseries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Przykłady

#### <a name="typesbatchput"></a>TypesBatchPut

Stara treść żądania (używana przez program `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Zaktualizowana treść żądania (używana przez program `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Alternatywnie `filter` można również `$event.Mode.String = 'outdoor'` . `value`Musi używać nawiasów do ucieczki znaku specjalnego ( `_` ).

#### <a name="getevents"></a>GetEvents

Stara treść żądania (używana przez program `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Zaktualizowana treść żądania (używana przez program `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Alternatywnie `filter` można również `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>Getseries

Stara treść żądania (używana przez program `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Zaktualizowana treść żądania (używana przez program `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Alternatywnie `value` można również `$event['Bar-Pressure-Offset'].Double` . Jeśli nie określono żadnego typu danych, przyjmuje się, że typ danych jest zawsze podwójny. Notacja nawiasu klamrowego musi być używana do ucieczki znaków specjalnych ( `-` ).

#### <a name="aggregateseries"></a>AggregateSeries

Stara treść żądania (używana przez program `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Zaktualizowana treść żądania (używana przez program `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Alternatywnie `value` można również `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Potencjalne błędy

#### <a name="invalidinput"></a>InvalidInput

Jeśli zobaczysz następujący błąd, korzystasz z nowej wersji interfejsu API ( `2020-07-31` ), ale składnia TSX nie została zaktualizowana. Zapoznaj się z [składnią wyrażenia szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax) i powyższymi przykładami migracji. `tsx`Przed ponownym przesłaniem żądania interfejsu API upewnij się, że wszystkie właściwości zostały prawidłowo zaktualizowane.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Przetestuj środowisko za pośrednictwem [eksploratora Azure Time Series Insights](./concepts-ux-panels.md) lub za pośrednictwem niestandardowej aplikacji.