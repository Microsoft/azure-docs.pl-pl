---
title: Zaktualizuj interfejs API raportów
description: Ten interfejs API służy do zgłaszania parametrów dla komercyjnych raportów analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583926"
---
# <a name="update-report-api"></a>Zaktualizuj interfejs API raportów

Ten interfejs API ułatwia modyfikowanie parametru raportu.

**Składnia żądania**

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Ładunek żądania**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Słownik**

W tej tabeli przedstawiono definicje kluczy elementów w ładunku żądania.

| Parametr | Wymagane | Opis | Dozwolone wartości |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Tak | Nazwa, która ma zostać przypisana do raportu | ciąg |
| `Description` | Nie | Opis utworzonego raportu | ciąg |
| `StartTime` | Tak | Znacznik czasu, po którym rozpocznie się generowanie raportu | ciąg |
| `RecurrenceInterval` | Nie | Częstotliwość, z jaką raport powinien zostać wygenerowany w godzinach. Wartość minimalna to 4 | liczba całkowita |
| `RecurrenceCount` | Nie | Liczba raportów do wygenerowania. Wartość domyślna jest nieokreślona | liczba całkowita |
| `Format` | Tak | Format pliku wyeksportowanego pliku. Wartość domyślna to CSV. | WOLUMINY CSV/TSV |
| `CallbackUrl` | Tak | adres URL wywołania zwrotnego HTTPS do wywołania przy generowaniu raportu | ciąg |
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
| `ReportId` | Unikatowy identyfikator uniwersalny (UUID) aktualizowanego raportu |
| `ReportName` | Nazwa nadana raportowi w ładunku żądania |
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
