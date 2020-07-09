---
title: Model szeregów czasowych — Azure Time Series Insights | Microsoft Docs
description: Informacje o modelu szeregów czasowych w wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: c5a22987b1d67f9e9f8384e5376343af2f91b5e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049976"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Model szeregów czasowych w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano model szeregów czasowych, możliwości i sposób rozpoczynania kompilowania i aktualizowania własnych modeli w środowisku Azure Time Series Insights w wersji zapoznawczej.

> [!TIP]
>
> * Zapoznaj się z przykładem środowiska [demonstracyjnego farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) w przypadku modelu na żywo.
> * Przeczytaj informacje o [Azure Time Series Insights Eksploratorze wersji zapoznawczej](time-series-insights-update-explorer.md) , aby dowiedzieć się, jak NAWIGOWAĆ po interfejsie użytkownika modelu szeregów czasowych.
> * Dowiedz się, [jak korzystać z modelu szeregów czasowych](time-series-insights-update-how-to-tsm.md) przy użyciu programu Time Series Insights Web Explorer.

## <a name="summary"></a>Podsumowanie

Tradycyjnie dane zbierane z urządzeń IoT nie zawierają informacji kontekstowych, co utrudnia szybkie znajdowanie i analizowanie czujników. Główną motywacją dla modelu szeregów czasowych jest uproszczenie wyszukiwania i analizowania danych dotyczących IoT lub szeregów czasowych. Realizuje ten cel, umożliwiając nadzorowanie, konserwację i wzbogacanie danych szeregów czasowych, aby pomóc w przygotowaniu gotowych do użycia przez klienta zestawach elementów do analizy.

## <a name="scenario-contosos-new-smart-oven"></a>Scenariusz: Nowa inteligentna pieca firmy Contoso

**Rozważmy fikcyjny scenariusz Smart pieca firmy Contoso.** W tym scenariuszu Załóżmy, że każda z inteligentnych pieców firmy Contoso ma pięć czujników temperatury, jeden dla każdego z czterech największych nagrywarek i jeden dla samego pieca. Przede wszystkim każdy czujnik temperatury firmy Contoso wysyła, przechowuje i wizualizował dane indywidualnie. W przypadku urządzeń kuchennych firma Contoso opiera się na wykresach podstawowych, po jednym dla każdego czujnika.

Podczas gdy firma Contoso była zadowolony z początkowego rozwiązania danych i wizualizacji, widoczne są pewne ograniczenia:

* Klienci chcieli wiedzieć, jak gorąca ogólna piekarnik będzie miała miejsce, gdy większość największych nagrywarek była włączona. Firma Contoso miała więcej trudności z analizą i przedstawienie ujednoliconej odpowiedzi na temat warunków ogólnego pieca.
* Inżynierowie firmy Contoso chciały sprawdzić, czy najczęściej używane palniki nie spowodują niewydajnego narysowania. Wystąpił problem z odłożeniami, które są ze sobą powiązane z czujnikami temperatury i napięcia oraz jak je zlokalizować w sklepie.
* Zespół ds. jakości firmy Contoso chciał przeprowadzić inspekcję i porównanie historii między dwiema wersjami czujników. Wystąpiły trudności z ustaleniem, jakie dane należały do których wersji czujnika.

Bez możliwości struktury, organizowania i definiowania rozłożonego modelu szeregów czasowych pieców inteligentnych, każdy czujnik temperatury utrzymuje odizolowane, oddzielone i mniej informacyjne punkty danych. Przekształcenie tych punktów danych w szczegółowe informacje umożliwiające podejmowanie działań było trudniejsze, ponieważ każdy zestaw danych jest niezależny od innych.

Te ograniczenia ujawniły znaczenie narzędzi do agregowania i wizualizacji danych inteligentnych, które mogą towarzyszyć nowemu piecowi firmy Contoso:

* Wizualizacja danych okazuje się przydatna, gdy możliwe jest kojarzenie i łączenie danych w wygodnym widoku. Przykład pokazuje czujniki napięcia wraz z czujnikami temperatury.
* Zarządzanie danymi wielowymiarowymi dla kilku jednostek wraz z porównaniem, powiększaniem i zakresem czasu może być trudne do osiągnięcia.

**Model szeregów czasowych zapewnia wygodne rozwiązanie** dla wielu scenariuszy napotkanych w tym fikcyjnym przykładzie:

[![Przykład grafu inteligentnego dla modelu szeregów czasowych](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Model szeregów czasowych odgrywa istotną rolę w zapytaniach i nawigacji, ponieważ contextualizes dane przez umożliwienie porównań między zakresami czasu i między rodzajami czujników i urządzeń. (**A**)
* Dane są dodatkowo zaewidencjonowania, ponieważ dane utrwalane w modelu szeregów czasowych są zachowywane jako zmienne i są ponownie stosowane w czasie zapytania.
* Model szeregów czasowych organizuje i agreguje dane w celu zwiększenia możliwości wizualizacji i zarządzania. (**B**)

### <a name="key-capabilities"></a>Najważniejsze możliwości

W celu uproszczenia i łatwego zarządzania contextualization szeregów czasowych model szeregów czasowych umożliwia korzystanie z następujących funkcji w wersji zapoznawczej programu Time Series Insights. Ułatwia:

* Tworzenie i zarządzanie obliczeniami lub formułami wykorzystującymi funkcje skalarne, operacje agregujące i tak dalej.
* Zdefiniuj relacje nadrzędny-podrzędny, aby włączyć nawigację, wyszukiwanie i odwołanie.
* Zdefiniuj właściwości, które są skojarzone z wystąpieniami, zdefiniowanymi jako *pola wystąpień*i używaj ich do tworzenia hierarchii.

### <a name="components"></a>Składniki

Model szeregów czasowych ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](#time-series-model-instances)
* [Hierarchie modelu szeregów czasowych](#time-series-model-hierarchies)
* [Typy modeli szeregów czasowych](#time-series-model-types)

Te składniki są łączone w celu określenia modelu szeregów czasowych i organizowania danych Azure Time Series Insights.

[![Wykres przeglądów modelu szeregów czasowych](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model szeregów czasowych można utworzyć i zarządzać nim za pomocą interfejsu [Time Series Insights w wersji zapoznawczej](time-series-insights-update-how-to-tsm.md) . Ustawienia modelu szeregów czasowych mogą być zarządzane za pomocą [interfejsu API ustawień modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Wystąpienia modelu szeregów czasowych

*Wystąpienia* modelu szeregów czasowych są wirtualnymi reprezentacjami szeregów czasowych.

W większości przypadków wystąpienia są jednoznacznie identyfikowane przez **deviceId** lub **AssetID**, które są zapisywane jako identyfikatory szeregów czasowych.

Wystąpienia mają opisowe informacje skojarzone z tymi nazwami *Właściwości wystąpienia*, takie jak identyfikator szeregów czasowych, typ, nazwa, opis, hierarchie i pola wystąpień. Co najmniej właściwości wystąpienia zawiera informacje o hierarchii.

*Pola wystąpienia* to zbiór informacji opisowych, które mogą zawierać wartości poziomów hierarchii, a także producenta, operatora i tak dalej.

Po skonfigurowaniu źródła zdarzeń dla środowiska Time Series Insights wystąpienia są automatycznie odnajdywane i tworzone w modelu szeregów czasowych. Wystąpienia można utworzyć lub zaktualizować za pomocą Eksploratora Time Series Insights przy użyciu zapytań modelu szeregów czasowych.

[Demonstracja farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) zawiera kilka przykładów wystąpienia na żywo.

[![Przykład wystąpienia modelu szeregów czasowych](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Właściwości wystąpienia

Wystąpienia są definiowane przez **timeSeriesId**, **typeId**, **name**, **Description**, **hierarchyIds**i **instanceFields**. Każde wystąpienie jest mapowane na tylko jeden *Typ*i co najmniej jedną *hierarchię*.

| Właściwość | Opis |
| --- | ---|
| timeSeriesId | Unikatowy identyfikator szeregów czasowych, z którym jest skojarzone wystąpienie. W większości przypadków wystąpienia są jednoznacznie identyfikowane przez właściwość, taką jak deviceId lub assetId. W niektórych przypadkach można użyć bardziej szczegółowego identyfikatora złożonego łączącego do 3 właściwości. |
| Parametru | Unikatowy identyfikator ciągu z rozróżnianą wielkością liter w modelu szeregów czasowych, z którym jest skojarzone wystąpienie. Domyślnie wszystkie wykryte nowe wystąpienia są skojarzone z domyślnym typem.
| name | Właściwość **name** jest opcjonalna i uwzględnia wielkość liter. Jeśli **Nazwa** nie jest dostępna, wartość domyślna to **timeSeriesId**. W przypadku podanej nazwy **timeSeriesId** jest nadal [dostępny.](time-series-insights-update-explorer.md#4-time-series-well) |
| description | Opis tekstowy wystąpienia. |
| hierarchyIds | Definiuje hierarchie, do których należy wystąpienie. |
| instanceFields | Właściwości wystąpienia i wszystkie dane statyczne, które definiują wystąpienie. Definiują wartości hierarchii lub właściwości nie będących hierarchią, a także obsługują indeksowanie w celu wykonywania operacji wyszukiwania. |

> [!NOTE]
> Hierarchie są kompilowane przy użyciu pól wystąpień. Dodatkowe **instanceFields** można dodać do dalszych definicji właściwości wystąpienia.

Wystąpienia mają następującą reprezentację JSON:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Aby uzyskać informacje na temat obsługi interfejsu API Time Series Insights i tworzenia, odczytu, aktualizacji i usuwania (CRUD), zapoznaj się z artykułem [wykonywanie zapytań dotyczących danych](concepts-query-overview.md#time-series-model-query-tsm-q-apis) i [dokumentacją interfejsu API usługi instance](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelu szeregów czasowych

*Hierarchie* modelu szeregów czasowych organizują wystąpienia, określając nazwy właściwości i ich relacje.

W danym środowisku Time Series Insights można skonfigurować wiele hierarchii. Wystąpienie modelu szeregów czasowych może być mapowane na pojedynczą hierarchię lub wiele hierarchii (relacja wiele-do-wielu).

Interfejs klienta [demonstracji programu contoso wiatr farmy](https://insights.timeseries.azure.com/preview/samples) wyświetla standardowe wystąpienie i hierarchię typów.

[![Przykład hierarchii modelu szeregów czasowych](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definicja hierarchii

Hierarchie są definiowane przez **Identyfikator**hierarchii, **nazwę**i **Źródło**.

| Właściwość | Opis |
| ---| ---|
| identyfikator | Unikatowy identyfikator dla hierarchii, który jest używany na przykład podczas definiowania wystąpienia. |
| name | Ciąg używany do podania nazwy hierarchii. |
| source | Określa hierarchię lub ścieżkę organizacyjną, która jest kolejnością nadrzędny-podrzędny hierarchii, która ma zostać utworzona przez użytkowników. Właściwości nadrzędny-podrzędny mapowania pól wystąpień. |

Hierarchie są reprezentowane w formacie JSON jako:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

W poprzednim przykładzie JSON:

* `Location`definiuje hierarchię z elementem nadrzędnym `states` i podrzędnym `cities` . Każdy `location` z nich może mieć wielokrotność `states` , która z kolei może mieć wiele `cities` .
* `ManufactureDate`definiuje hierarchię z elementem nadrzędnym `year` i podrzędnym `month` . Każdy `ManufactureDate` z nich może mieć wielokrotność `years` , która z kolei może mieć wiele `months` .

> [!TIP]
> Aby uzyskać pomoc dotyczącą Time Series Insights wystąpienia interfejsu API i CRUD, przeczytaj artykuł dotyczący [zapytań dotyczących danych](concepts-query-overview.md#time-series-model-query-tsm-q-apis) oraz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Przykład hierarchii

Rozważmy przykład, w którym hierarchia **H1** ma `building` , `floor` i `room` w ramach swojej definicji **instanceFieldNames** :

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

W przypadku pól wystąpienia używanych w poprzedniej definicji i kilku szeregów czasowych atrybuty i wartości hierarchii są wyświetlane jak pokazano w poniższej tabeli:

| Identyfikator szeregów czasowych | Pola wystąpienia |
| --- | --- |
| ID1 | "Building" = "1000", "piętro" = "10", "Pokój" = "55"  |
| ID2 | "Building" = "1000", "Pokój" = "55" |
| ID3 | "piętro" = "10" |
| ID4 | "Building" = "1000", "piętro" = "10"  |
| ID5 | Nie ustawiono żadnego z "kompilowania", "podłogi" lub "pokoju". |

Szeregi czasowe **ID1** i **ID4** są wyświetlane jako część hierarchii **H1** w [Eksploratorze Azure Time Series Insights](time-series-insights-update-explorer.md) , ponieważ mają w pełni zdefiniowane i prawidłowo uporządkowane parametry *kompilacji*, *podłogi*i *pokoju* .

Inne są klasyfikowane jako *nienadrzędne wystąpienia* , ponieważ nie są zgodne z określoną hierarchią danych.

## <a name="time-series-model-types"></a>Typy modeli szeregów czasowych

*Typy* modeli szeregów czasowych pomagają definiować zmienne lub formuły do wykonywania obliczeń. Typy są skojarzone z określonym wystąpieniem Time Series Insights.

Typ może mieć co najmniej jedną zmienną. Na przykład wystąpienie modelu szeregów czasowych może być typu *czujnika temperatury*, który składa się z *średniej temperatury*, *temperatury minimalnej*i *maksymalnej temperatury*.

[Demonstracja farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) umożliwia wizualizację kilku typów modelu szeregów czasowych skojarzonych z odpowiednimi wystąpieniami.

[![Przykładowy Typ modelu szeregów czasowych](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Aby uzyskać pomoc techniczną dla Time Series Insights wystąpienia interfejsu API i CRUD, przeczytaj artykuł dotyczący [zapytań dotyczących danych](concepts-query-overview.md#time-series-model-query-tsm-q-apis) i [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Właściwości typu

Typy modeli szeregów czasowych są zdefiniowane przez **Identyfikator**, **nazwę**, **Opis**i **zmienne**.

| Właściwość | Opis |
| ---| ---|
| identyfikator | Unikatowy identyfikator ciągu z rozróżnianą wielkością liter dla typu. |
| name | Ciąg używany do podania nazwy dla typu. |
| description | Opis ciągu dla typu. |
| modyfikacj | Określ zmienne skojarzone z typem. |

Typy są zgodne z następującym przykładem JSON:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Zmienne

Typy Time Series Insights mogą mieć wiele zmiennych, które określają reguły formuł i obliczeń dla zdarzeń.

Każda zmienna może być jednym z trzech *rodzajów*: *liczbowej*, *kategorii*i *agregacji*.

* Rodzaje **liczbowe** pracują z ciągłymi wartościami.
* Rodzaje **kategorii** działają ze zdefiniowanym zestawem wartości dyskretnych.
* **Zagregowane** wartości łączą wiele zmiennych pojedynczego rodzaju (wszystkie liczbowe lub wszystkie kategorii).

W poniższej tabeli przedstawiono właściwości, które są istotne dla poszczególnych rodzajów zmiennych.

[![Tabela zmiennych modelu szeregów czasowych](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Zmienne liczbowe

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników lub przekształcone przy użyciu wyrażeń szeregów czasowych. Zmienne rodzaju liczbowego muszą być typu *Double*.|
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. Opcje interpolacji *krokowej* i *liniowej* są dostępne dla zmiennych liczbowych. |
| Agregacja zmiennych | Obsługa obliczeń za pomocą operatorów *AVG*, *min*, *Max*, *sum*, *Count*, *First*, *Last* i Time (*AVG*, *min*, *Max*, *sum*, *Left*). |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Zmienne kategorii

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników. Zmienne typu kategorii muszą mieć wartość *Long* lub *String*. |
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. Opcja interpolacji *kroku* jest dostępna dla zmiennych kategorii. |
| Kategorie zmiennych | Kategorie tworzą mapowanie między wartościami pochodzącymi z urządzenia lub czujników do etykiety. |
| Kategoria domyślna zmiennej | Kategoria domyślna dotyczy wszystkich wartości, które nie są mapowane we właściwości "Kategorie". |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Zmienne agregujące

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Agregacja zmiennych | Obsługa obliczeń za pomocą wartości *AVG*, *min*, *Max*, *sum*, *Count*, *First*, *Last*. |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Zmienne są przechowywane w definicji typu w modelu szeregów czasowych i mogą być dostarczane wewnętrznie za pośrednictwem [interfejsów API zapytań](concepts-query-overview.md) w celu zastąpienia przechowywanej definicji.

## <a name="next-steps"></a>Następne kroki

* Informacje o wysyłaniu [zapytań dotyczących danych](concepts-query-overview.md)

* Zapoznaj się z dokumentacją dotyczącą [modelu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
