---
title: Rejestruj zapytania w Azure Monitor
description: Informacje referencyjne dotyczące języka zapytań Kusto używanego przez Azure Monitor. Zawiera dodatkowe elementy charakterystyczne dla Azure Monitor i elementy nieobsługiwane w zapytaniach dziennika Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491396"
---
# <a name="log-queries-in-azure-monitor"></a>Rejestruj zapytania w Azure Monitor
Dzienniki Azure Monitor opierają się na usłudze Azure Eksplorator danych, a zapytania dzienników są zapisywane przy użyciu tego samego języka zapytań Kusto (KQL). Jest to bogaty język zaprojektowany tak, aby można było go łatwo odczytywać i tworzyć, aby można było zacząć pisać zapytania z niektórymi podstawowymi wskazówkami.

Obszary w Azure Monitor, w których będą używane zapytania:

- [Log Analytics](../log-query/log-analytics-overview.md). Podstawowe narzędzie w Azure Portal do edytowania zapytań dzienników i interaktywnego analizowania ich wyników. Nawet jeśli zamierzasz użyć zapytania dziennika w innym miejscu Azure Monitor, zazwyczaj zapiszesz go i testujesz go w Log Analytics przed skopiowaniem go do lokalizacji ostatecznej.
- [Reguły alertów dzienników](../platform/alerts-overview.md). Aktywne identyfikowanie problemów z danych w obszarze roboczym.  Każda reguła alertu jest oparta na zapytaniu dziennika, które jest automatycznie uruchamiane w regularnych odstępach czasu.  Wyniki są sprawdzane w celu określenia, czy ma zostać utworzony alert.
- [Skoroszyty](../platform/workbooks-overview.md). Uwzględnij wyniki zapytań dzienników przy użyciu różnych wizualizacji w interaktywnych raportach wizualnych w Azure Portal.
- [Pulpity nawigacyjne platformy Azure](../learn/tutorial-logs-dashboards.md). Przypnij wyniki dowolnego zapytania do pulpitu nawigacyjnego platformy Azure, który pozwala na wspólne wizualizację danych dzienników i metryk, a opcjonalnie udostępnianie innym użytkownikom platformy Azure.
- [Logic Apps](../platform/logicapp-flow-connector.md).  Użyj wyników zapytania dziennika w zautomatyzowanym przepływie pracy przy użyciu Logic Apps.
- [Program PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Użyj wyników zapytania dziennika w skrypcie programu PowerShell z wiersza polecenia lub Azure Automation elementu Runbook, który używa polecenia Get-AzOperationalInsightsSearchResults.
- [Interfejs API dzienników Azure monitor](https://dev.loganalytics.io). Pobierz dane dziennika z obszaru roboczego z dowolnego klienta interfejsu API REST.  Żądanie interfejsu API zawiera zapytanie, które jest uruchamiane względem Azure Monitor, aby określić dane do pobrania.

## <a name="getting-started"></a>Wprowadzenie
Najlepszym sposobem na rozpoczęcie uczenia się w celu pisania zapytań dzienników za pomocą KQL jest użycie dostępnych samouczków i przykładów.

- [Samouczek log Analytics](log-analytics-tutorial.md) — samouczek dotyczący korzystania z funkcji log Analytics, które są używane w Azure Portal do edytowania i uruchamiania zapytań. Pozwala również pisać proste zapytania bez bezpośredniej pracy z językiem zapytań. Jeśli wcześniej nie korzystasz z Log Analytics, Zacznij tutaj, aby poznać narzędzie, które będzie używane z innymi samouczkami i przykładami.
- [Samouczek KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) — Instruktaż przedstawiający podstawowe koncepcje KQL i typowe operatory. Jest to najlepsze miejsce do rozpoczęcia pracy, aby rozpocząć pracę z językiem i strukturą zapytań dzienników. 
- [Przykładowe zapytania](example-queries.md) — opis przykładowych zapytań dostępnych w log Analytics. Możesz użyć zapytań bez modyfikacji lub użyć ich jako przykładów, aby poznać KQL.
- [Przykłady zapytań](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) — przykładowe zapytania ilustrujące różne różne koncepcje.



## <a name="reference-documentation"></a>Dokumentacja referencyjna
[Dokumentacja dotycząca KQL](/azure/data-explorer/kusto/query/) , w tym informacje dotyczące wszystkich poleceń i operatorów, jest dostępna w dokumentacji usługi Azure Eksplorator danych. Nawet w przypadku korzystania z usługi KQL należy regularnie używać odwołania do badania nowych poleceń i scenariuszy, które nie były wcześniej używane.


## <a name="language-differences"></a>Różnice w językach
Chociaż Azure Monitor korzysta z tego samego KQL, co usługa Azure Eksplorator danych, istnieją pewne różnice. Dokumentacja KQL będzie określać te operatory, które nie są obsługiwane przez Azure Monitor lub które mają różne funkcje. Operatory specyficzne dla Azure Monitor są udokumentowane w Azure Monitor zawartości. W poniższych sekcjach przedstawiono listę różnic między wersjami języka na potrzeby szybkiego odwoływania się do programu.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrukcje nie są obsługiwane w Azure Monitor

* [Użyj](/azure/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkcje nie są obsługiwane w Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatory nie są obsługiwane w Azure Monitor

* [Sprzężenie między klastrami](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Wtyczki nie są obsługiwane w Azure Monitor

* [Wtyczka języka Python](/azure/kusto/query/pythonplugin)
* [sql_request, wtyczka](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Dodatkowe operatory w Azure Monitor
Następujące operatory obsługują określone funkcje Azure Monitor i nie są dostępne poza programem Azure Monitor.

* [Aplikacja ()](app-expression.md)
* [zasób ()](resource-expression.md)
* [obszar roboczy ()](workspace-expression.md)

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [samouczkiem dotyczącym pisania zapytań](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Uzyskaj dostęp do kompletnej [dokumentacji referencyjnej dotyczącej języka zapytań Kusto](/azure/kusto/query/).

