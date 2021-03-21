---
title: Wznów interfejs API wykonywania raportów
description: Ten interfejs API umożliwia wznowienie zaplanowanego wykonania wstrzymanego komercyjnego raportu analitycznego w portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583937"
---
# <a name="resume-report-executions-api"></a>Wznów interfejs API wykonywania raportów

Ten interfejs API podczas wykonywania wznawia zaplanowane wykonanie wstrzymanego komercyjnego raportu analitycznego w portalu Marketplace.

**Składnia żądania**

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
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
| `ReportId` | Unikatowy identyfikator uniwersalny (UUID) wznowionego raportu |
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
