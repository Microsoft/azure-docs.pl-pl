---
title: Pobierz wszystkie interfejsy API zestawów danych
description: Użyj tego interfejsu API, aby uzyskać wszystkie dostępne zestawy danych dla komercyjnej analizy portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583944"
---
# <a name="get-all-datasets-api"></a>Pobierz wszystkie interfejsy API zestawów danych

Interfejs API pobierania wszystkich zestawów danych pobiera wszystkie dostępne zestawy danych. Zestawy danych wyświetlają tabele, kolumny, metryki i zakresy czasu.

**Składnia żądania**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Nagłówek żądania**

| **Nagłówek** | **Typ** | **Opis** |
| --- | --- | --- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |

**Parametr ścieżki**

Brak

**Parametr zapytania**

| **Nazwa parametru** | **Typ** | **Wymagane** | **Opis** |
| --- | --- | --- | --- |
| `datasetName` | ciąg | Nie | Filtruj, aby uzyskać szczegółowe informacje o jednym zestawie danych |

**Ładunek żądania**

Brak

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest następujący:

Kod odpowiedzi: 200, 400, 401, 403, 404, 500

Przykład ładunku odpowiedzi:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Słownik**

Ta tabela definiuje kluczowe elementy w odpowiedzi:

| **Parametr** | **Opis** |
| --- | --- |
| `DatasetName` | Nazwa zestawu danych, który definiuje ten obiekt Array |
| `SelectableColumns` | Kolumny pierwotne, które można określić w wybranych kolumnach |
| `AvailableMetrics` | Agregacja/nazwy kolumn metryk, które można określić w wybranych kolumnach |
| `AvailableDateRanges` | Zakres dat, który może być używany w zapytaniach raportu dla zestawu danych |
| `NextLink` | Łącze do następnej strony, jeśli dane są podzielone na strony |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku. Możliwe wartości to 200, 400, 401, 403, 500 |
