---
title: Zapytania dziennika Azure Monitor | Microsoft Docs
description: Odwołania do zasobów na potrzeby uczenia się, jak pisać zapytania dzienników w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 080af271870362e6e0633aaf590820f2fadb6144
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607381"
---
# <a name="azure-monitor-log-queries"></a>Zapytania dzienników usługi Azure Monitor

Dzienniki Azure Monitor są oparte na usłudze Azure Eksplorator danych, a Azure Monitor zapytania dzienników używają wersji tego samego języka zapytań Kusto. [Dokumentacja języka zapytań Kusto](/azure/kusto/query) zawiera wszystkie szczegóły dotyczące języka i powinna być podstawowym zasobem do pisania zapytań dziennika Azure monitor. Ta strona zawiera linki do innych zasobów na potrzeby uczenia się, jak pisać zapytania i różnice w Azure Monitor implementacji języka.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Wprowadzenie

- [Rozpocznij pracę z Azure Monitor Log Analytics](get-started-portal.md) to lekcja do pisania zapytań i pracy z wynikami w Azure Portal.
- [Wprowadzenie do Azure monitor zapytań dzienników](get-started-queries.md) jest lekcja do pisania zapytań przy użyciu danych dziennika Azure monitor.

## <a name="concepts"></a>Pojęcia

- [Analizowanie danych dziennika w Azure monitor](./log-query-overview.md) zawiera krótkie omówienie zapytań dzienników i opisuje sposób, w jaki dane dzienników Azure monitor są strukturalne.
- [Wyświetlanie i analizowanie danych dziennika w Azure monitor](./log-query-overview.md) wyjaśnia portale, w których tworzysz i uruchamiasz zapytania dzienników.

## <a name="reference"></a>Tematy pomocy

- [Dokumentacja języka zapytań](/azure/kusto/query)  to kompletna dokumentacja języka dla języka zapytań Kusto.
- [Różnice w języku zapytań dziennika Azure monitor](data-explorer-difference.md) opisują różnice między wersjami języka zapytań Kusto.
- [Standardowe właściwości w Azure monitor rekordy dziennika](../platform/log-standard-properties.md) opisują właściwości, które są standardowe dla wszystkich Azure monitor danych dziennika.
- [Wykonaj zapytania dotyczące dzienników wielu zasobów w Azure monitor](./cross-workspace-query.md) opisuje sposób pisania zapytań dzienników, które używają danych z wielu obszarów roboczych log Analytics i aplikacji Application Insights.

## <a name="examples"></a>Przykłady

- [Przykłady zapytań dziennika Azure monitor](examples.md) zawierają przykładowe zapytania korzystające z Azure monitor danych dziennika.

## <a name="lessons"></a>Lekcje

- [Praca z ciągami w zapytaniach dziennika Azure monitor](string-operations.md) opisuje sposób pracy z danymi ciągu.
- [Praca z wartościami daty i godziny w zapytaniach dziennika Azure monitor](datetime-operations.md) opisuje sposób pracy z danymi daty i godziny.
- [Agregacje w zapytaniach dziennika Azure monitor](aggregations.md) i [agregacje zaawansowane w zapytaniach dziennika Azure monitor](advanced-aggregations.md) opisują sposób agregowania i podsumowywania danych.
- [Sprzężenia w zapytaniach dziennika Azure monitor](joins.md) opisują sposób sprzęgania danych z wielu tabel.
- [Praca z danymi JSON i strukturami danych w zapytaniach dziennika Azure monitor](json-data-structures.md) opisuje sposób analizowania danych JSON.
- [Pisanie zaawansowanych zapytań dzienników w Azure monitor](advanced-query-writing.md) opisuje strategie tworzenia złożonych zapytań i ponownego użycia kodu.
- [Tworzenie wykresów i diagramów z zapytań dziennika Azure monitor](charts.md) opisuje sposób wizualizacji danych z zapytania dziennika.

## <a name="cheatsheets"></a>Ściągawki

- [Zapytanie SQL do Azure monitor dziennika](sql-cheatsheet.md) pomaga użytkownikom, którzy już znają program SQL.
- [Splunk do zapytania dziennika Azure monitor](splunk-cheatsheet.md) pomaga użytkownikom, którzy już znają Splunk.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj dostęp do kompletnej [dokumentacji referencyjnej dotyczącej języka zapytań Kusto](/azure/kusto/query/).

