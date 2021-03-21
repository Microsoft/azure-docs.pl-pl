---
title: Model szeregów czasowych — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o modelu szeregów czasowych w Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698097"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Model szeregów czasowych w Azure Time Series Insights Gen2

W tym artykule opisano model szeregów czasowych, możliwości i sposób rozpoczynania kompilowania i aktualizowania własnych modeli w środowisku Azure Time Series Insights Gen2.

> [!TIP]
>
> * Zapoznaj się z przykładem środowiska [demonstracyjnego farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) w przypadku modelu na żywo.
> * Dowiedz się, [jak korzystać z modelu szeregów czasowych](./how-to-edit-your-model.md) przy użyciu Eksploratora Azure Time Series Insights.

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

W celu uproszczenia i łatwego zarządzania contextualization szeregów czasowych model szeregów czasowych umożliwia korzystanie z następujących funkcji w Azure Time Series Insights Gen2. Ułatwia:

* Tworzenie i zarządzanie obliczeniami lub formułami wykorzystującymi funkcje skalarne, operacje agregujące i tak dalej.
* Zdefiniuj relacje nadrzędny-podrzędny, aby włączyć nawigację, wyszukiwanie i odwołanie.
* Zdefiniuj właściwości, które są skojarzone z wystąpieniami, zdefiniowanymi jako *pola wystąpień* i używaj ich do tworzenia hierarchii.

### <a name="components"></a>Składniki

Model szeregów czasowych ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](#time-series-model-instances)
* [Hierarchie modelu szeregów czasowych](#time-series-model-hierarchies)
* [Typy modeli szeregów czasowych](#time-series-model-types)

Te składniki są łączone w celu określenia modelu szeregów czasowych i organizowania danych.

[![Wykres przeglądów modelu szeregów czasowych](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model szeregów czasowych można utworzyć i zarządzać nim za pomocą [eksploratora Azure Time Series Insights](./concepts-ux-panels.md). Ustawienia modelu szeregów czasowych mogą być zarządzane za pomocą [interfejsu API ustawień modelu](/rest/api/time-series-insights/reference-model-apis).

## <a name="time-series-model-instances"></a>Wystąpienia modelu szeregów czasowych

*Wystąpienia* modelu szeregów czasowych są wirtualnymi reprezentacjami szeregów czasowych.

W większości przypadków wystąpienia są jednoznacznie identyfikowane przez **deviceId** lub **AssetID**, które są zapisywane jako identyfikatory szeregów czasowych.

Wystąpienia mają opisowe informacje skojarzone z tymi nazwami *Właściwości wystąpienia*, takie jak identyfikator szeregów czasowych, typ, nazwa, opis, hierarchie i pola wystąpień. Co najmniej właściwości wystąpienia zawiera informacje o hierarchii.

*Pola wystąpienia* to zbiór informacji opisowych, które mogą zawierać wartości poziomów hierarchii, a także producenta, operatora i tak dalej.

Po skonfigurowaniu źródła zdarzeń dla środowiska Azure Time Series Insights Gen2 wystąpienia są automatycznie odnajdywane i tworzone w modelu szeregów czasowych. Wystąpienia można utworzyć lub zaktualizować za pomocą Eksploratora Azure Time Series Insights przy użyciu zapytań modelu szeregów czasowych.

[Demonstracja farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) zawiera kilka przykładów wystąpienia na żywo.

[![Przykład wystąpienia modelu szeregów czasowych](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Właściwości wystąpienia

Wystąpienia są definiowane przez **timeSeriesId**, **typeId**, **name**, **Description**, **hierarchyIds** i **instanceFields**. Każde wystąpienie jest mapowane na tylko jeden *Typ* i co najmniej jedną *hierarchię*.

| Właściwość | Opis |
| --- | ---|
| timeSeriesId | Unikatowy identyfikator szeregów czasowych, z którym jest skojarzone wystąpienie. W większości przypadków wystąpienia są jednoznacznie identyfikowane przez właściwość, taką jak deviceId lub assetId. W niektórych przypadkach można użyć bardziej szczegółowego identyfikatora złożonego łączącego do 3 właściwości. |
| Parametru | Unikatowy identyfikator ciągu z rozróżnianą wielkością liter w modelu szeregów czasowych, z którym jest skojarzone wystąpienie. Domyślnie wszystkie wykryte nowe wystąpienia są skojarzone z domyślnym typem.
| name | Właściwość **name** jest opcjonalna i uwzględnia wielkość liter. Jeśli **Nazwa** nie jest dostępna, wartość domyślna to **timeSeriesId**. W przypadku podanej nazwy **timeSeriesId** jest nadal [dostępny.](./concepts-ux-panels.md#4-time-series-well) |
| description (opis) | Opis tekstowy wystąpienia. |
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
> W przypadku usługi instance API Create, Read, Update i Delete (CRUD) zapoznaj się z artykułem [wykonywanie zapytań dotyczących danych](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) i [dokumentacją interfejsu API usługi instance](/rest/api/time-series-insights/reference-model-apis#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelu szeregów czasowych

*Hierarchie* modelu szeregów czasowych organizują wystąpienia, określając nazwy właściwości i ich relacje.

Można skonfigurować wiele hierarchii w danym Azure Time Series Insights środowisku Gen2. Wystąpienie modelu szeregów czasowych może być mapowane na pojedynczą hierarchię lub wiele hierarchii (relacja wiele-do-wielu).

[Demonstracja farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) wyświetla standardowe wystąpienie i hierarchię typów.

[![Przykład hierarchii modelu szeregów czasowych](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definicja hierarchii

Hierarchie są definiowane przez **Identyfikator** hierarchii, **nazwę** i **Źródło**.

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

* `Location` definiuje hierarchię z elementem nadrzędnym `states` i podrzędnym `cities` . Każdy `location` z nich może mieć wielokrotność `states` , która z kolei może mieć wiele `cities` .
* `ManufactureDate` definiuje hierarchię z elementem nadrzędnym `year` i podrzędnym `month` . Każdy `ManufactureDate` z nich może mieć wielokrotność `years` , która z kolei może mieć wiele `months` .

> [!TIP]
> Aby uzyskać pomoc dotyczącą tworzenia, odczytywania, aktualizowania i usuwania interfejsu API hierarchii, przeczytaj artykuł dotyczący [zapytań dotyczących danych](concepts-query-overview.md#time-series-model-query-tsm-q-apis) oraz [dokumentację interfejsu API REST usługi Hierarchy](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

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

Szeregi czasowe **ID1** i **ID4** są wyświetlane jako część hierarchii **H1** w [Eksploratorze Azure Time Series Insights](./concepts-ux-panels.md) , ponieważ mają w pełni zdefiniowane i prawidłowo uporządkowane parametry *kompilacji*, *podłogi* i *pokoju* .

Inne są klasyfikowane jako *nienadrzędne wystąpienia* , ponieważ nie są zgodne z określoną hierarchią danych.

## <a name="time-series-model-types"></a>Typy modeli szeregów czasowych

*Typy* modeli szeregów czasowych pomagają definiować zmienne lub formuły do wykonywania obliczeń. Typy są skojarzone z określonym wystąpieniem.

Typ może mieć co najmniej jedną zmienną. Na przykład wystąpienie modelu szeregów czasowych może być typu *czujnika temperatury*, który składa się z *średniej temperatury*, *temperatury minimalnej* i *maksymalnej temperatury*.

[Demonstracja farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) umożliwia wizualizację kilku typów modelu szeregów czasowych skojarzonych z odpowiednimi wystąpieniami.

[![Przykładowy Typ modelu szeregów czasowych](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Aby uzyskać informacje o typach interfejsów API Create, Read, Update i Delete (CRUD), zapoznaj się z artykułem [wykonywanie zapytań dotyczących danych](concepts-query-overview.md#time-series-model-query-tsm-q-apis) i [dokumentacją interfejsu API REST](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Właściwości typu

Typy modeli szeregów czasowych są zdefiniowane przez **Identyfikator**, **nazwę**, **Opis** i **zmienne**.

| Właściwość | Opis |
| ---| ---|
| identyfikator | Unikatowy identyfikator ciągu z rozróżnianą wielkością liter dla typu. |
| name | Ciąg używany do podania nazwy dla typu. |
| description (opis) | Opis ciągu dla typu. |
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
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Typy modeli szeregów czasowych mogą mieć wiele zmiennych, które określają reguły formuł i obliczeń dla zdarzeń. Przeczytaj więcej [na temat sposobu definiowania zmiennych modelu szeregów czasowych](./concepts-variables.md)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z dokumentacją dotyczącą [modelu szeregów czasowych](/rest/api/time-series-insights/reference-model-apis) , aby uzyskać więcej informacji na temat edytowania modelu za pośrednictwem interfejsów API.

* Eksplorowanie formuł i obliczeń, które można tworzyć za pomocą [zmiennych modelu szeregów czasowych](./concepts-variables.md)

* Dowiedz się więcej o wysyłaniu [zapytań do danych](concepts-query-overview.md) w Azure Time Series Insights Gen2