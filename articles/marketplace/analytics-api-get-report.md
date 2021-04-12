---
title: Pobierz interfejs API raportów
description: Ten interfejs API umożliwia pobieranie raportów analizy, które zostały zaplanowane w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584048"
---
# <a name="get-report-api"></a>Pobierz interfejs API raportów

Ten interfejs API pobiera wszystkie raporty, które zostały zaplanowane.

**Składnia żądania**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Nagłówek żądania**

| **Nagłówek** | **Typ** | **Opis** |
| --- | --- | --- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |

**Parametr ścieżki**

Brak

**Parametr zapytania**

| **Nazwa parametru** | **Wymagane** | **Typ** | **Opis** |
| --- | --- | --- | --- |
| `reportId` | Nie | ciąg | Filtruj, aby uzyskać szczegółowe informacje o raportach z `reportId` podanym w tym argumencie |
| `reportName` | Nie | ciąg | Filtruj, aby uzyskać szczegółowe informacje o raportach z `reportName` podanym w tym argumencie |
| `queryId` | Nie | boolean | Dołącz wstępnie zdefiniowane zapytania systemowe do odpowiedzi |

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest strukturalny w formacie JSON w następujący sposób:

Kod odpowiedzi: 200, 400, 401, 403, 404, 500

Ładunek odpowiedzi:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
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
| `ReportId` | Unikatowy identyfikator UUID utworzonego raportu |
| `ReportName` | Nazwa nadana raportowi w ładunku żądania |
| `Description` | Opis uzyskany podczas tworzenia raportu |
| `QueryId` | Identyfikator zapytania, który został przesłany w momencie utworzenia raportu |
| `Query` | Tekst zapytania, który zostanie wykonany dla tego raportu |
| `User` | Identyfikator użytkownika użyty do utworzenia raportu |
| `CreatedTime` | Godzina utworzenia raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ModifiedTime` | Godzina ostatniej modyfikacji raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `StartTime` | Rozpocznie się wykonywanie czasu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ReportStatus` | Stan wykonywania raportu. Możliwe wartości są wstrzymane, aktywne i nieaktywne. |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu |
| `CallbackUrl` | Adres URL wywołania zwrotnego podany w żądaniu |
| `Format` | Format plików raportów |
