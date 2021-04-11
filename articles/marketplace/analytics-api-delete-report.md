---
title: Usuwanie interfejsu API raportów
description: Ten interfejs API służy do usuwania wszystkich rekordów raportów i wykonywania raportów dla komercyjnych raportów analizy portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584113"
---
# <a name="delete-report-api"></a>Usuwanie interfejsu API raportów

Po wykonaniu ten interfejs API usuwa wszystkie rekordy wykonywania raportu i raportów.

**Składnia żądania**

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Nagłówek żądania**

| Nagłówek | Typ | Opis |
| ------------ | ------------- | ------------- |
| Autoryzacja | ciąg | Wymagane. Token dostępu usługi Azure AD w formularzu `Bearer <token>` |
| Typ zawartości | ciąg | `Application/JSON` |
||||

**Parametr ścieżki**

Brak

**Parametr zapytania**

| Nazwa parametru | Wymagane | ciąg | Opis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Tak | ciąg | Identyfikator raportu, który jest modyfikowany |
|||||

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest następujący:

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
| `ReportId` | Unikatowy identyfikator UUID usuniętego raportu |
| `ReportName` | Nazwa nadana raportowi podczas tworzenia |
| `Description` | Opis udzielony podczas tworzenia raportu |
| `QueryId` | Identyfikator zapytania, który został przesłany w momencie utworzenia raportu |
| `Query` | Tekst zapytania, który zostanie wykonany dla tego raportu |
| `User` | Identyfikator użytkownika użyty do utworzenia raportu |
| `CreatedTime` | Godzina utworzenia raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ModifiedTime` | Godzina ostatniej modyfikacji raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `StartTime` | Godzina rozpoczęcia wykonywania raportu. Format godziny to RRRR-MM-DDTgg: mm: SSS |
| `ReportStatus` | Stan wykonywania raportu. Możliwe wartości są wstrzymane, aktywne i nieaktywne. |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu |
| `CallbackUrl` | Adres URL wywołania zwrotnego podany w żądaniu |
| `Format` | Format plików raportów |
|||
