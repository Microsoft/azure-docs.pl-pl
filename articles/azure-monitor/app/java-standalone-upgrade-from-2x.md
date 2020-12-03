---
title: Uaktualnianie z wersji 2. x Azure Monitor Application Insights Java
description: Uaktualnianie z Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 9a0e8237d81428b1ecab95627fe106a563d2090c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532453"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Uaktualnianie z programu Application Insights Java 2. x SDK

Jeśli używasz już Application Insights zestawu SDK języka Java 2. x w aplikacji, nie musisz go usunąć.
Agent Java 3,0 wykryje go, a następnie przechwytuje i skorelowanie wszelkich niestandardowych danych telemetrycznych wysyłanych za pośrednictwem zestawu 2. x, przy jednoczesnym pominięciu autokolekcji wykonywanej przez zestaw SDK 2. x, aby zapobiec zduplikowanym telemetrii.

Jeśli używasz agenta Application Insights 2. x, musisz usunąć `-javaagent:` argument JVM, który wskazuje na agenta 2. x.

W pozostałej części niniejszego dokumentu opisano ograniczenia i zmiany, które mogą wystąpić podczas uaktualniania z wersji 2. x do 3,0, a także kilka obejść, które mogą być przydatne.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers i TelemetryProcessors

Zestaw SDK 2. x TelemetryInitializers i TelemetryProcessors nie zostanie uruchomiony w przypadku korzystania z agenta 3,0.
Wiele przypadków użycia, które wcześniej wymagały, można rozwiązać w 3,0 przez skonfigurowanie [niestandardowych wymiarów](./java-standalone-config.md#custom-dimensions) lub skonfigurowanie [procesorów telemetrii](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Wiele aplikacji w jednym JVM

Obecnie 3,0 obsługuje tylko pojedyncze [Parametry połączenia i nazwę roli](./java-standalone-config.md#connection-string-and-role-name) na uruchomiony proces. W szczególności nie jest jeszcze dostępna wiele Tomcat Web Apps w tym samym wdrożeniu Tomcat przy użyciu różnych parametrów połączenia lub różnych nazw ról.

## <a name="operation-names"></a>Nazwy operacji

Nazwy operacji w 3,0 zostały zmienione w celu ogólnego udostępnienia lepszego widoku zagregowanego w portalu Application Insights U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Nazwy operacji w 3,0":::

Jednak w przypadku niektórych aplikacji można nadal preferować Zagregowany widok w U/X, który został dostarczony przez poprzednie nazwy operacji, w takim przypadku można użyć funkcji [procesorów telemetrii](./java-standalone-telemetry-processors.md) (wersja zapoznawcza) w 3,0, aby replikować poprzednie zachowanie.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Dodaj prefiks nazwy operacji przy użyciu metody http ( `GET` , `POST` itp.).

W zestawie SDK 2. x nazwy operacji zostały poprzedzone przez metodę http ( `GET` `POST` itp.), np.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Nazwy operacji poprzedzone przez metodę http":::

Poniższy fragment kodu umożliwia skonfigurowanie 3 procesorów telemetrycznych, które łączą się, aby replikować poprzednie zachowanie.
Procesory telemetrii wykonują następujące akcje (w podanej kolejności):

1. Pierwszy procesor telemetrii jest procesorem zakresu (ma typ `span` ), co oznacza, że ma zastosowanie do `requests` i `dependencies` .

   Będzie pasować do dowolnego zakresu, który ma atrybut o nazwie `http.method` i ma nazwę zakresu rozpoczynającą się od `/` .

   Następnie wyodrębni tę nazwę zakresu do atrybutu o nazwie `tempName` .

2. Drugi procesor telemetrii jest również procesorem.

   Będzie pasować do dowolnego zakresu, który ma atrybut o nazwie `tempName` .

   Następnie zaktualizuje nazwę zakresu przez połączenie dwóch atrybutów `http.method` i `tempName` , rozdzielone spacją.

3. Ostatni procesor telemetrii jest procesorem atrybutów (ma typ `attribute` ), co oznacza, że ma zastosowanie do całej telemetrii, która ma atrybuty (obecnie `requests` `dependencies` i `traces` ).

   Będzie ona pasować do wszystkich danych telemetrycznych, które mają atrybut o nazwie `tempName` .

   Następnie usunie atrybut o nazwie `tempName` , tak aby nie był raportowany jako wymiar niestandardowy.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Ustaw nazwę operacji na pełną ścieżkę

Ponadto w przypadku zestawu SDK 2. x, w niektórych przypadkach, nazwy operacji zawierają pełną ścieżkę, np.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Nazwy operacji z pełną ścieżką":::

Poniższy fragment kodu konfiguruje 4 procesory telemetrii, które łączą się, aby replikować poprzednie zachowanie.
Procesory telemetrii wykonują następujące akcje (w podanej kolejności):

1. Pierwszy procesor telemetrii jest procesorem zakresu (ma typ `span` ), co oznacza, że ma zastosowanie do `requests` i `dependencies` .

   Będzie pasować do dowolnego zakresu, który ma atrybut o nazwie `http.url` .

   Następnie zostanie zaktualizowana nazwa zakresu przy użyciu `http.url` wartości atrybutu.

   Będzie to miało koniec, z tą różnicą, że wygląda na to `http.url` `http://host:port/path` , że chcesz tylko uzyskać tę `/path` część.

2. Drugi procesor telemetrii jest również procesorem.

   Będzie on pasować do dowolnego zakresu, który ma atrybut o nazwie `http.url` (innymi słowy, każdy z nich jest w każdym przypadku, gdy jest zgodny z pierwszym procesorem).

   Następnie wyodrębni część nazwy zakresu do atrybutu o nazwie `tempName` .

3. Trzeci procesor telemetrii jest również procesorem.

   Będzie pasować do dowolnego zakresu, który ma atrybut o nazwie `tempPath` .

   Następnie zaktualizuje nazwę zakresu z atrybutu `tempPath` .

4. Ostatni procesor telemetrii jest procesorem atrybutów (ma typ `attribute` ), co oznacza, że ma zastosowanie do całej telemetrii, która ma atrybuty (obecnie `requests` `dependencies` i `traces` ).

   Będzie ona pasować do wszystkich danych telemetrycznych, które mają atrybut o nazwie `tempPath` .

   Następnie usunie atrybut o nazwie `tempPath` , tak aby nie był raportowany jako wymiar niestandardowy.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Nazwy zależności

Nazwy zależności w 3,0 również zostały zmienione, a następnie ponownie, aby zapewnić lepszy widok zagregowany w portalu Application Insights U/X.

W przypadku niektórych aplikacji można nadal preferować widok zagregowany w U/X, który został dostarczony przez poprzednie nazwy zależności. w takim przypadku można użyć podobnych technik jak powyżej, aby replikować poprzednie zachowanie.

## <a name="operation-name-on-dependencies"></a>Nazwa operacji dla zależności

Wcześniej w zestawie SDK 2. x nazwa operacji w telemetrii żądania została również ustawiona dla telemetrii zależności.
Application Insights Java 3,0 nie wypełnia już nazwy operacji na telemetrii zależności.
Jeśli chcesz zobaczyć nazwę operacji dla żądania, które jest elementem nadrzędnym telemetrii zależności, możesz napisać zapytanie dzienników (Kusto), aby dołączyć z tabeli zależności do tabeli żądania.
