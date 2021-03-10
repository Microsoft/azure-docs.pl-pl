---
title: Dostęp programistyczny typowych pytań dotyczących danych analizy
description: Często zadawane pytania dotyczące programistycznego uzyskiwania dostępu do danych analitycznych w centrum partnerskim dla ofert komercyjnych w portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583973"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Dostęp programistyczny typowych pytań dotyczących danych analizy

W tym artykule opisano często zadawane pytania dotyczące sposobu programistycznego dostępu do danych analitycznych w centrum partnerskim dla ofert komercyjnych w portalu Marketplace.

## <a name="api-responses"></a>Odpowiedzi interfejsu API

Jakie są różne scenariusze, w których mogę odebrać odpowiedź interfejsu API inną niż 200 (sukces)?

W tej tabeli opisano odpowiedzi interfejsu API i co należy zrobić, jeśli je otrzymasz.

| Opis błędu | Kod błędu | Rozwiązywanie problemów |
| ------------ | ------------- | ------------- |
| Brak autoryzacji | 401 | Jest to wyjątek uwierzytelniania. Sprawdź poprawność tokenu Azure Active Directory (Azure AD). Token usługi Azure AD jest ważny przez 60 minut, po którym należy ponownie wygenerować token usługi Azure AD. |
| Nieprawidłowa nazwa tabeli | 400 | Nazwa zestawu danych jest nieprawidłowa. Sprawdź ponownie nazwę zestawu danych, wywołując interfejs API "Pobierz wszystkie zbiory DataSets". |
| Niepoprawna nazwa kolumny | 400| Nazwa kolumny w zapytaniu jest niepoprawna. Sprawdź ponownie nazwę kolumny, wywołując interfejs API "Pobierz wszystkie zestawy danych" lub zapoznaj się z nazwami kolumn w następujących tabelach:<br><ul><li>[Tabela szczegółów zamówień](orders-dashboard.md#orders-details-table)</li><li>[Tabela szczegółów użycia](usage-dashboard.md#usage-details-table)</li><li>[Tabela szczegółów klienta](customer-dashboard.md#customer-details-table)</li><li>[Tabela szczegółów szczegółowych informacji o portalu Marketplace](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Wartość zerowa lub brak wartości | 400 | Brak obowiązkowych parametrów w ładunku żądania interfejsu API. |
| Nieprawidłowe parametry raportu | 400 | Upewnij się, że parametry raportu są poprawne. Na przykład możesz uzyskać wartość mniejszą niż 4 dla `RecurrenceInterval` parametru. |
| Interwał cyklu musi zawierać się w przedziale od 4 do 90 | 400 | Upewnij się, że wartość `RecurrenceInterval` parametru żądania należy do zakresu od 4 do 90. |
| Nieprawidłowy QueryId | 400 | Sprawdź ponownie wygenerowany `QueryId` . |
| Nieprawidłowe parametry raportu dotyczące czasu utworzenia raportu powinny wynosić co najmniej 4 godziny od bieżącego czasu UTC — | 400 | Parametr czasu rozpoczęcia w ramach ładunku żądania nie powinien znajdować się w przeszłości. Godzina rozpoczęcia raportu powinna wynosić co najmniej 4 godziny od bieżącego czasu UTC. |
| Nie znaleziono wymaganej wartości "String" | 400 | Sprawdź, czy zostały zaktualizowane parametry żądania `callbackurl` lub `format` . |
| Nie znaleziono żadnego elementu z podanym filtrem. | 404 | Sprawdź `reportID` parametr używany w interfejsie API pobierania raportów. |
| Brak wykonań, które wystąpiły dla danego warunku filtru. Sprawdź ponownie identyfikatorem ReportID lub executionId i ponów próbę uruchomienia interfejsu API po zaplanowanym czasie wykonywania raportu | 404 | Upewnij się, że `reportId` jest poprawny. Ponów próbę wykonania operacji API po zaplanowanym czasie wykonywania raportu określonym w ładunku żądania. |
| Wystąpił błąd wewnętrzny podczas tworzenia raportu. Identyfikator korelacji <> | 500 | Upewnij się, że format daty "StartTime", "QueryStartTime" i "QueryEndTime" są poprawne. |
| Usługa niedostępna | 500 | Jeśli ciągle otrzymujesz usługę niedostępną (błąd 5xx), Utwórz [bilet pomocy technicznej](support.md). |
||||

## <a name="no-records"></a>Brak rekordów

Otrzymuję odpowiedź interfejsu API 200, gdy pobieram Raport z bezpiecznej lokalizacji. Dlaczego nie otrzymuję żadnych rekordów?

Sprawdź, czy ciąg w zapytaniu ma jedną z wartości dopuszczalnych dla nagłówka kolumny. Na przykład, zapytanie zwróci zero wyników:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

W tym przykładzie wartości dozwolone dla SKUBillingType są płatne lub bezpłatne. Zapoznaj się z następującymi tabelami, aby uzyskać wszystkie możliwe wartości dla różnych kolumn:

- [Tabela szczegółów zamówień](orders-dashboard.md#orders-details-table)
- [Tabela szczegółów użycia](usage-dashboard.md#usage-details-table)
- [Tabela szczegółów klienta](customer-dashboard.md#customer-details-table)
- [Tabela szczegółów szczegółowych informacji o portalu Marketplace](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Następne kroki

- [Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace](analytics-available-apis.md)
