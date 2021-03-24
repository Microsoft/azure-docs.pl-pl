---
title: Przesłaniania próbek (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java
description: Informacje na temat konfigurowania zastąpień próbkowania w Application Insights Azure Monitor dla języka Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 17979bd548ca0d7b704ebdeb4d060bf35973b319
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024151"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Przesłaniania próbek (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java

> [!NOTE]
> Funkcja zastąpień próbkowania jest dostępna w wersji zapoznawczej, rozpoczynając od 3.0.3-BETA. 2.

Przesłaniania próbek umożliwiają przesłonięcie [domyślnego procentu próbkowania](./java-standalone-config.md#sampling), na przykład:
 * Ustaw wartość procent próbkowania na 0 (lub niewielką wartość), aby sprawdzić kondycję.
 * Ustaw wartość procent próbkowania na 0 (lub niewielką wartość) dla wywołań zależności.
 * Ustaw wartość procentową próbkowania na 100 dla ważnego typu żądania (np.), mimo że `/login` domyślne próbkowanie jest skonfigurowane jako mniejsze.

## <a name="terminology"></a>Terminologia

Przed poznaniem zastąpień próbkowania należy zrozumieć okres *.* Zakres jest ogólnym terminem dla:

* Żądanie przychodzące.
* Zależność wychodząca (na przykład połączenie zdalne z inną usługą).
* Zależność w procesie (na przykład pracy wykonywanej przez podskładniki usługi).

W przypadku zastąpień próbkowania te składniki zakresu są ważne:

* Atrybuty

Atrybuty span reprezentują standardowe i niestandardowe właściwości danego żądania lub zależności.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, Utwórz plik konfiguracji o nazwie *applicationinsights.json*. Zapisz ją w tym samym katalogu, co *ApplicationInsights-Agent- \* . jar*. Użyj następującego szablonu.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Jak to działa

Po rozpoczęciu zakresu atrybuty obecne w zakresie w tym czasie są używane do sprawdzenia, czy którykolwiek z zastąpień próbkowania jest zgodny.

Jeśli jeden z próbek zastąpi dopasowanie, jego procent próbkowania służy do decydowania, czy próbkować zakres, czy nie.

Używane jest tylko pierwsze przesłonięcie próbkowania, które jest zgodne.

Jeśli próbkowanie nie zastępuje żadnego dopasowania:

* Jeśli jest to pierwszy zakres śledzenia, zostanie użyta [Domyślna wartość procentowa próbkowania](./java-standalone-config.md#sampling) .
* Jeśli nie jest to pierwszy zakres śledzenia, zostanie użyta decyzja dotycząca próbkowania nadrzędnego.

> [!IMPORTANT]
> Gdy podjęto decyzję, aby nie zbierać zakresu, wszystkie przedziały podrzędne również nie będą zbierane, nawet jeśli są zastępujące próbki, które pasują do zakresu podrzędnego.
> To zachowanie jest konieczne, ponieważ w przeciwnym razie ślady przerwania byłyby wynikiem zebrania, ale są zbierane w celu przedziału, które nie zostały zebrane.

> [!NOTE]
> Decyzja o próbkowaniu jest oparta na wykorzystaniu skrótu traceId (nazywanego również operationId) na liczbę z zakresu od 0 do 100 i ten skrót jest porównywany z procentem próbkowania.
> Ponieważ wszystkie zakresy w danym śledzeniu będą miały ten sam traceId, będą mieć ten sam skrót i dlatego decyzja o próbkowaniu będzie spójna dla całego śladu.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Przykład: pomijanie zbierania danych telemetrycznych na potrzeby kontroli kondycji

Spowoduje to pominięcie zbierania danych telemetrycznych dla wszystkich żądań do `/health-checks` .

Spowoduje to również pominięcie zbierania wszelkich zakresów podrzędnych (zależności), które zwykle będą zbierane `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Przykład: pomijanie zbierania danych telemetrycznych dla wywołania zależności z zakłóceniami

Spowoduje to pominięcie zbierania danych telemetrycznych dla wszystkich `GET my-noisy-key` wywołań Redis.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Przykład: Zbieraj 100% danych telemetrycznych dla ważnego typu żądania

Spowoduje to zebranie 100% danych telemetrycznych `/login` .

Ponieważ zakresy podrzędne (zależności) respektują decyzję o próbkowaniu elementu nadrzędnego (nieobecne przesłonięcie próbkowania dla tego przedziału), zostaną one również zebrane dla wszystkich żądań "/login".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Wspólne atrybuty zakresu

W tej sekcji wymieniono niektóre typowe atrybuty span, które mogą być używane przez zastępujące próbki.

### <a name="http-spans"></a>Zakresy HTTP

| Atrybut  | Typ | Opis | 
|---|---|---|
| `http.method` | ciąg | Metoda żądania HTTP.|
| `http.url` | ciąg | Pełny adres URL żądania HTTP w formularzu `scheme://host[:port]/path?query[#fragment]` . Fragment nie jest zazwyczaj przesyłany za pośrednictwem protokołu HTTP. Ale jeśli fragment jest znany, powinien być dołączony.|
| `http.status_code` | liczba | [Kod stanu odpowiedzi HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | ciąg | Typ protokołu HTTP. |
| `http.user_agent` | ciąg | Wartość nagłówka [użytkownika http-agenta](https://tools.ietf.org/html/rfc7231#section-5.5.3) wysyłanego przez klienta. |

### <a name="jdbc-spans"></a>Zakresy JDBC

| Atrybut  | Typ | Opis  |
|---|---|---|
| `db.system` | ciąg | Identyfikator używanego produktu zarządzania bazami danych (DBMS). |
| `db.connection_string` | ciąg | Parametry połączenia używane do nawiązania połączenia z bazą danych. Zaleca się usunięcie osadzonych poświadczeń.|
| `db.user` | ciąg | Nazwa użytkownika służąca do uzyskiwania dostępu do bazy danych. |
| `db.name` | ciąg | Ciąg służący do raportowania nazwy bazy danych, do której uzyskuje się dostęp. W przypadku poleceń, które przełączają bazę danych, ten ciąg powinien być ustawiony na docelową bazę danych, nawet jeśli polecenie nie powiedzie się.|
| `db.statement` | ciąg | Instrukcja bazy danych, która jest uruchamiana.|
