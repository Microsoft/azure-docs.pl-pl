---
title: Pobierz interfejs API kwerend raportów
description: Użyj tego interfejsu API, aby uzyskać wszystkie zapytania, które są dostępne do użycia w komercyjnych raportach analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584093"
---
# <a name="get-report-queries-api"></a>Pobierz interfejs API kwerend raportów

Interfejs API zapytania Get Report pobiera wszystkie zapytania, które są dostępne do użycia w raportach. Domyślnie pobiera wszystkie zapytania zdefiniowane przez system i użytkownika.

**Składnia żądania**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Nagłówek żądania**

| **Nagłówek** | **Typ** | **Opis** |
| --- | --- | --- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |
||||

**Parametr ścieżki**

Brak

**Parametr zapytania**

| **Nazwa parametru** | **Typ** | **Wymagane** | **Opis** |
| --- | --- | --- | --- |
| `queryId` | ciąg | Nie | Filtruj, aby uzyskać szczegółowe informacje tylko o zapytaniach o IDENTYFIKATORze podanym w argumencie |
| `queryName` | ciąg | Nie | Filtruj, aby uzyskać szczegółowe informacje tylko o zapytaniach o nazwie podanej w argumencie |
| `IncludeSystemQueries` | boolean | Nie | Dołącz wstępnie zdefiniowane zapytania systemowe do odpowiedzi |
| `IncludeOnlySystemQueries` | boolean | Nie | Uwzględnij tylko kwerendy systemowe w odpowiedzi |
|||||

**Ładunek żądania**

Brak

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

W tej tabeli opisano kluczowe definicje elementów w odpowiedzi.

| **Parametr** | **Opis** |
| --- | --- |
| `QueryId` | Unikatowy identyfikator UUID zapytania |
| `Name` | Nazwa nadana kwerendzie w momencie tworzenia zapytania |
| `Description` | Opis podanym podczas tworzenia zapytania |
| `Query` | Ciąg zapytania raportu |
| `Type` | Ustaw na `userDefined` dla kwerend utworzonych przez użytkownika i `system` dla wstępnie zdefiniowanych zapytań systemowych |
| `User` | Identyfikator użytkownika, który utworzył kwerendę |
| `CreatedTime` | Godzina utworzenia zapytania |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku. Możliwe wartości to 200, 400, 401, 403, 500 |
|||
