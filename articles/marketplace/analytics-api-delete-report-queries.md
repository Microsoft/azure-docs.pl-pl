---
title: Usuwanie interfejsu API kwerend raportów
description: Ten interfejs API służy do usuwania zapytań zdefiniowanych przez użytkownika dla komercyjnych analiz portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584163"
---
# <a name="delete-report-queries-api"></a>Usuwanie interfejsu API kwerend raportów

Ten interfejs API usuwa zapytania zdefiniowane przez użytkownika.

**Składnia żądania**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Nagłówek żądania**

| **Nagłówek** | **Typ** | **Opis** |
| --- | --- | --- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |

**Parametr ścieżki**

| **Nazwa parametru** | **Typ** | **Opis** |
| --- | --- | --- |
| `queryId` | ciąg | Filtruj, aby uzyskać szczegółowe informacje tylko o zapytaniach o IDENTYFIKATORze podanym w tym argumencie |

**Parametr zapytania**

Brak

**Ładunek żądania**

Brak

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest uporządkowany w następujący sposób w formacie JSON.

Kod odpowiedzi: 200, 400, 401, 403, 404, 500

Ładunek odpowiedzi:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Słownik**

W tej tabeli przedstawiono definicje kluczy elementów w odpowiedzi.

| **Parametr** | **Opis** |
| --- | --- |
| `QueryId` | Unikatowy identyfikator UUID zapytania, które zostało usunięte. |
| `Name` | Nazwa zapytania, które zostało usunięte |
| `Description` | Opis usuniętego zapytania |
| `Query` | Ciąg zapytania raportu dla usuniętego zapytania |
| `Type` | Zdefiniowane przez użytkownika |
| `User` | Identyfikator użytkownika, który utworzył kwerendę |
| `CreatedTime` | Godzina utworzenia zapytania |
| `ModifiedTime` | Zero |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku. Możliwe wartości to 200, 400, 401, 403, 500 |
