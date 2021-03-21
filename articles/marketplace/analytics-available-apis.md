---
title: Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace
description: Użyj tych interfejsów API do programistycznego uzyskiwania dostępu do danych analitycznych w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584153"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace

Poniżej znajduje się lista interfejsów API służących do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace i skojarzonych z nimi funkcji.

- [Interfejsy API ściągania zestawu danych](#dataset-pull-apis)
- [Interfejsy API zarządzania zapytaniami](#query-management-apis)
- [Interfejsy API zarządzania raportami](#report-management-apis)
- [Interfejsy API ściągania wykonywania raportów](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Interfejsy API ściągania zestawu danych

***Tabela 1: interfejsy API ściągania zestawu danych***

| **Interfejs API** | **Funkcjonalność** |
| --- | --- |
| [Pobierz wszystkie zestawy danych](analytics-api-get-all-datasets.md) | Pobiera wszystkie dostępne zestawy danych. Zestawy danych wyświetlają tabele, kolumny, metryki i zakresy czasu. |
|||

## <a name="query-management-apis"></a>Interfejsy API zarządzania zapytaniami

***Tabela 2: interfejsy API zarządzania zapytaniami***

| **Interfejs API** | **Funkcjonalność** |
| --- | --- |
| [Utwórz kwerendę raportu](analytics-programmatic-access.md#create-report-query-api) | Tworzy niestandardowe zapytania definiujące zestaw danych, z którego należy wyeksportować kolumny i metryki. |
| [Pobierz kwerendy raportu](analytics-api-get-report-queries.md) | Pobiera wszystkie zapytania dostępne do użycia w raportach. Pobiera domyślnie wszystkie zapytania zdefiniowane przez system i użytkownika. |
| [Usuń zapytania dotyczące raportu](analytics-api-delete-report-queries.md) | Usuwa zapytania zdefiniowane przez użytkownika. |
|||

## <a name="report-management-apis"></a>Interfejsy API zarządzania raportami

***Tabela 3: interfejsy API zarządzania raportami***

| **Interfejs API** | **Funkcjonalność** |
| --- | --- |
| [Utwórz raport](analytics-programmatic-access.md#create-report-api) | Planuje wykonywanie zapytania w regularnych odstępach czasu. |
| [Wypróbuj kwerendy raportu](analytics-api-try-report-queries.md) | Wykonuje instrukcję kwerendy raportu. Zwraca tylko 10 rekordów, których Partner może użyć do sprawdzenia, czy dane są zgodnie z oczekiwaniami. |
| [Pobierz raport](analytics-api-get-report.md) | Pobierz wszystkie raporty, które zostały zaplanowane. |
| [Zaktualizuj raport](analytics-api-update-report.md) | Modyfikowanie parametru raportu. |
| [Usuń raport](analytics-api-delete-report.md) | Usuwa wszystkie rekordy wykonywania raportu i raportów. |
| [Wstrzymaj wykonywanie raportów](analytics-api-pause-report-executions.md) | Wstrzymuje zaplanowane wykonywanie raportów. |
| [Wznów wykonywanie raportów](analytics-api-resume-report-executions.md) | Wznawia zaplanowane wykonanie wstrzymanego raportu. |
|||

## <a name="report-execution-pull-apis"></a>Interfejsy API ściągania wykonywania raportów

***Tabela 4: interfejsy API ściągania wykonywania raportów***

| **Interfejs API** | **Funkcjonalność** |
| --- | --- |
| [Pobierz wykonania raportu](analytics-programmatic-access.md#get-report-executions-api) | Pobierz wszystkie wykonania, które wystąpiły dla danego raportu. |
|||

## <a name="next-steps"></a>Następne kroki

- Interfejsy API można wypróbować za pomocą [adresu URL interfejsu API struktury Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
