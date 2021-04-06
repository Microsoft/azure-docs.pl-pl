---
title: Specyfikacja zapytania niestandardowego
description: Dowiedz się, jak używać niestandardowych zapytań do programistycznego wyodrębniania danych z tabel analiz dla ofert w portalu komercyjnym firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584023"
---
# <a name="custom-query-specification"></a>Specyfikacja zapytania niestandardowego

Partnerzy mogą używać tej specyfikacji zapytania, aby łatwo formułować niestandardowe zapytania do wyodrębniania danych z tabel analizy. Zapytania mogą służyć do wybierania tylko żądanych kolumn i metryk, które pasują do określonego kryterium. W obszarze Specyfikacja języka jest definicją zestawu danych, w którym można napisać zapytanie niestandardowe.

## <a name="datasets"></a>Zestawy danych

W taki sam sposób, w jaki niektóre zapytania są uruchamiane dla bazy danych, która zawiera tabele i kolumny, zapytanie niestandardowe działa na zestawach, które mają kolumny i metryki. Pełną listę dostępnych zestawów danych do tworzenia zapytania można uzyskać za pomocą interfejsu API zestawów danych.

Oto przykład zestawu danych wyświetlanego jako kod JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Części zestawu danych

- Nazwa zestawu danych jest taka sama jak nazwa tabeli bazy danych. Na przykład ISVUsage. Zestaw danych zawiera listę kolumn, które można wybrać, takich jak MarketplaceSubscriptionId.
- Zestaw danych ma także metryki, takie jak funkcje agregacji w bazie danych. Na przykład NormalizedUsage.
- Istnieją stałe przedziały czasu, w których można wyeksportować dane.

### <a name="formulating-a-query-on-a-dataset"></a>Formułowanie zapytania dla zestawu danych

Oto przykładowe zapytania, które pokazują, jak wyodrębnić różne typy danych.

| Zapytanie | Opis |
| ------------ | ------------- |
| **Wybierz pozycję** MarketplaceSubscriptionId, IDKlienta **z** ISVUsage **TIMESPAN LAST_MONTH** | To zapytanie będzie miało każdy unikatowy `MarketplaceSubscriptionId` i odpowiadający mu `CustomerId` w ciągu ostatniego 1 miesiąca. |
| **Wybierz pozycję** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **z** **zamówienia ISVUsage według** **limitu** EstimatedExtendedChargeCC 10 | To zapytanie spowoduje uzyskanie 10 pierwszych subskrypcji w kolejności malejącej liczby licencji sprzedawanych w ramach każdej subskrypcji. |
| **Wybierz pozycję** CustomerId, NormalizedUsage, RawUsage **z** **porządku ISVUsage za pomocą** NormalizedUsage, **gdzie** NormalizedUsage > 100000 **order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | To zapytanie spowoduje uzyskanie NormalizedUsage i RawUsage wszystkich klientów, którzy mają NormalizedUsage większy niż 100 000. |
| **Wybierz pozycję** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **z** ISVUsage, **gdzie** CustomerID w ("2a31c234-1f4e-4c60-909E-76d234f93161", "80780748-3f9a-11EB-b378-0242ac130002") | To zapytanie spowoduje uzyskanie `MarketplaceSubscriptionId` przychodu i wygenerowanego przez dwie wartości w każdym miesiącu `CustomerId` `2a31c234-1f4e-4c60-909e-76d234f93161` `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Specyfikacja zapytania

W tej sekcji opisano definicję i strukturę zapytania.

### <a name="grammar-reference"></a>Odwołanie do gramatyki

W tej tabeli opisano symbole używane w zapytaniach.

| Symbol | Znaczenie |
| ------------ | ------------- |
| ? | Opcjonalne |
| * | Zero lub więcej |
| + | Co najmniej jeden |
| &#124; | Lub jedna z listy |
| | |

### <a name="query-definition"></a>Definicja zapytania

Instrukcja zapytania zawiera następujące klauzule: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? i TimeSpan?.

- **SelectClause**: **SELECT** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: kolumny i metryki zdefiniowane w zestawie danych
- **FromClause**: **z** DataSetName
    - **DataSetName**: Nazwa zestawu danych zdefiniowana w zestawie danych
- **WhereClause**: **gdzie** warunek filtru (**i** warunek filtru) *
    - **Warunek filtru**: ColumOrMetricName — wartość operatora
        - **Operator**: = | > | < | >= | <= |! = | LUBIĘ | NIE PODOBA CI SIĘ | W | NIE W
        - **Wartość**: liczba | StringLiteral | MultiNumberList | MultiStringList 
            - **Numer**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: "[a-za-Z0-9_] *"
            - **MultiNumberList**: (Number (, Number) *)
            - **MultiStringList**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **order by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - **LimitClause**: **Limit** [0-9] +
    - **TimeSpan**: **TimeSpan** (dzisiaj | WCZORAJ | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | OKRES istnienia

### <a name="query-structure"></a>Struktura zapytania

Zapytanie raportu składa się z wielu części:

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- CZASU

Każda część została opisana poniżej.

#### <a name="select"></a>SELECT

Ta część zapytania określa kolumny, które zostaną wyeksportowane. Kolumny, które można wybrać, to pola wymienione w `selectableColumns` i `availableMetrics` części zestawu danych. Końcowe eksportowane wiersze zawsze będą zawierać odrębne wartości w zaznaczonych kolumnach. Na przykład nie będzie żadnych zduplikowanych wierszy w wyeksportowanym pliku. Metryki będą obliczane dla każdej odrębnej kombinacji wybranych kolumn.

**Przykład:**
- **Wybierz** `OfferName` pozycję, `NormalizedUsage`

#### <a name="from"></a>FROM

Ta część zapytania wskazuje zestaw danych, z którego dane mają zostać wyeksportowane. Nazwa zestawu danych podaną w tym miejscu musi być prawidłową nazwą zestawu danych zwracaną przez interfejs API zestawów danych.

**Przykład:**
- WNIOSEK `ISVUsage`
- WNIOSEK `ISVOrder`

#### <a name="where"></a>WHERE

Ta część zapytania służy do określania warunków filtru dla zestawu danych. W ostatnim wyeksportowanym pliku będą obecne tylko wiersze zgodne ze wszystkimi warunkami wymienionymi w tej klauzuli. Warunek filtru może znajdować się w dowolnej kolumnie wymienionej w `selectableColumns` i `availableMetrics` . Wartości określone w warunku filtru mogą być listami liczb lub listami ciągów tylko wtedy, gdy operatorem jest `IN` lub `NOT IN` . Wartości mogą zawsze być podawane jako ciąg literału i zostaną one przekonwertowane na typy natywne kolumn. Należy oddzielić wiele warunków filtru z `AND` operacją.

**Przykład:**

- MarketplaceSubscriptionId = "868368da-957d-4959-8992-3c12dc7e6260"
- Klient **like** "% Contosso%"
- **Nie ma** elementu CustomerId (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - MarketplaceSubscriptionId = "7b487ac0-ce12-b732-dcd6-91a1e4e74a50" i CustomerId = "0f8b7fa0-eb83-a183-1225-ca153ef807aa"

#### <a name="order-by"></a>ORDER BY

Ta część zapytania określa kryteria sortowania dla eksportowanych wierszy. Kolumny, w których można zdefiniować porządkowanie, muszą pochodzić z `selectableColumns` i `availableMetrics` zestawu danych. Jeśli nie określono kierunku porządkowania, będzie on domyślnie `DESC` w kolumnie. Porządkowanie można zdefiniować dla wielu kolumn, oddzielając je przecinkami.

**Przykład:**

- **Porządkuj według** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (DW) **DESC**
- **Porządkuj według** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Ta część zapytania określa liczbę wierszy, które zostaną wyeksportowane. Określona liczba musi być dodatnią liczbą całkowitą różną od zera.

#### <a name="timespan"></a>CZASU

Ta część zapytania określa czas, przez jaki dane muszą zostać wyeksportowane. Możliwe wartości powinny pochodzić z `availableDateRanges` pola w definicji zestawu danych.

### <a name="case-sensitivity-in-query-specification"></a>Uwzględnianie wielkości liter w specyfikacji zapytania

Specyfikacja jest całkowicie bez uwzględniania wielkości liter. Wstępnie zdefiniowane słowa kluczowe, nazwy kolumn i wartości można określić przy użyciu wielkich lub małych liter.

## <a name="see-also"></a>Zobacz też

- [Lista zapytań systemowych](analytics-system-queries.md)
