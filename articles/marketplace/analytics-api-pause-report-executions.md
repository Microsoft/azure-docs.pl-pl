---
title: Wstrzymywanie interfejsu API wykonywania raportów
description: Użyj tego interfejsu API, aby wstrzymać zaplanowane wykonywanie komercyjnego raportu analizy portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584033"
---
# <a name="pause-report-executions-api"></a>Wstrzymywanie interfejsu API wykonywania raportów

Ten interfejs API podczas wykonywania wstrzymuje zaplanowane wykonywanie raportów.

**Składnia żądania**

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Nagłówek żądania**

| Nagłówek | Typ | Opis |
| ------------ | ------------- | ------------- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |
||||

**Parametr ścieżki**

Brak

**Parametr zapytania**

| Nazwa parametru | Wymagany | Typ | Opis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Tak | ciąg | Identyfikator modyfikowanego raportu |
|||||

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest strukturalny w formacie JSON:

Kod odpowiedzi: 200, 400, 401, 403, 404, 500 — ładunek odpowiedzi:

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
      "RecurrenceCount": 0,
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

| Parametr | Opis |
| ------------ | ------------- |
| `ReportId` | Unikatowy identyfikator uniwersalny (UUID) usuniętego raportu |
| `ReportName` | Nazwa nadana raportowi podczas tworzenia |
| `Description` | Opis udzielony podczas tworzenia raportu |
| `QueryId` | Identyfikator zapytania, który został przesłany w momencie utworzenia raportu |
| `Query` | Tekst zapytania, który zostanie wykonany dla tego raportu |
| `User` | Identyfikator użytkownika używany do tworzenia raportu |
| `CreatedTime` | Godzina utworzenia raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ModifiedTime` | Godzina ostatniej modyfikacji raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `StartTime` | Godzina rozpoczęcia wykonywania raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ReportStatus` | Stan wykonywania raportu. Możliwe wartości są wstrzymane, aktywne i nieaktywne. |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu |
| `CallbackUrl` | Adres URL wywołania zwrotnego podany w żądaniu |
| `Format` | Format plików raportów |
|||
