---
title: Wypróbuj interfejs API zapytań raportów
description: Użyj tego interfejsu API, aby wykonać zapytanie o Raport dla komercyjnych raportów analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583933"
---
# <a name="try-report-queries-api"></a>Wypróbuj interfejs API zapytań raportów

Ten interfejs API wykonuje instrukcję kwerendy raportu. Interfejs API zwraca tylko 10 rekordów, z których może korzystać partner, aby sprawdzić, czy dane są zgodnie z oczekiwaniami.

> [!IMPORTANT]
> Ten interfejs API ma limit czasu wykonywania zapytania wynoszący 100 sekund. Jeśli zauważysz, że interfejs API trwa dłużej niż 100 sekund, bardzo często jest możliwe, że zapytanie jest syntaktycznie poprawne lub w innym przypadku otrzymano kod błędu inny niż 200. Rzeczywiste generowanie raportu zostanie przekazane, jeśli Składnia zapytania jest poprawna.

**Składnia żądania**

| **Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Nagłówek żądania**

| **Nagłówek** | **Typ** | **Opis** |
| --- | --- | --- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD) w formularzu `Bearer <token>` |
| Content-Type | ciąg | `Application/JSON` |
|||

**QueryParameter**

| **Nazwa parametru** | **Typ** | **Opis** |
| --- | --- | --- |
| `exportQuery` | ciąg | Zgłoś ciąg zapytania, który musi zostać wykonany |
| `queryId` | ciąg | Prawidłowy istniejący identyfikator zapytania |
|||

**Parametr** ścieżki  

Brak

**Ładunek żądania**

Brak

**Słownik**

Brak

**Odpowiedź**

Ładunek odpowiedzi jest następujący:

Kod odpowiedzi: 200, 400, 401, 403, 404, 500

Ładunek odpowiedzi: 10 najważniejszych wierszy wykonywania zapytania
