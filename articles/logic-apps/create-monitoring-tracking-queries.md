---
title: Wyświetlanie i tworzenie zapytań dotyczących usługi Logic Apps w dziennikach Azure Monitor
description: Wyświetlaj i twórz zapytania w Azure Monitor dziennikach dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574555"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Wyświetlaj i twórz zapytania dotyczące monitorowania i śledzenia w Azure Monitor dziennikach dla Azure Logic Apps

Można wyświetlić podstawowe zapytania, które tworzą wyniki z [dzienników Azure monitor](../azure-monitor/logs/log-query-overview.md) i tworzyć zapytania filtrujące wyniki na podstawie określonych kryteriów. Na przykład można znaleźć komunikaty na podstawie określonego numeru kontrolnego wymiany. Zapytania używają [języka zapytań Kusto](/azure/data-explorer/kusto/query/), który można edytować, jeśli chcesz wyświetlić różne wyniki. Aby uzyskać więcej informacji, zobacz [Azure monitor kwerendy dzienników](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, zapoznaj się [z tematem tworzenie log Analytics obszaru roboczego](../azure-monitor/logs/quick-create-workspace.md).

* Aplikacja logiki, która została skonfigurowana przy użyciu Azure Monitor rejestrowania i wysyłania tych informacji do Log Analytics obszaru roboczego. Dowiedz się [, jak skonfigurować dzienniki Azure monitor dla aplikacji logiki](../logic-apps/monitor-logic-apps.md).

* Jeśli używasz konta integracji, upewnij się, że zostało skonfigurowane konto z rejestrowaniem Azure Monitor, aby wysłać te informacje do Log Analytics obszaru roboczego. Dowiedz się, jak [skonfigurować rejestrowanie Azure monitor dla konta integracji](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Wyświetl zapytania w tle

Aby wyświetlić lub edytować zapytanie, które generuje wyniki w podsumowaniu obszaru roboczego, wykonaj następujące kroki:

1. Na dowolnej stronie wyników w dolnej części wybierz pozycję **Zobacz wszystko**.

   ![Zobacz wszystkie wyniki](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Zostanie otwarta strona dzienniki i zostanie wyświetlona kwerenda za poprzednią stroną wyników.

   ![Strona dzienników — widok zapytania](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na stronie **dzienniki** można wybrać następujące opcje:

   * Aby wyświetlić wyniki zapytania jako tabelę, w edytorze zapytań wybierz pozycję **tabela**.

   * Aby zmienić zapytanie, zaktualizuj ciąg zapytania i wybierz polecenie **Uruchom** , aby wyświetlić wyniki w tabeli.

## <a name="create-your-own-query"></a>Utwórz własne zapytanie

Aby znaleźć lub filtrować wyniki na podstawie określonych właściwości lub wartości, możesz utworzyć własne zapytanie, rozpoczynając od pustego zapytania lub korzystając z istniejącego zapytania. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z zapytaniami dzienników w Azure monitor](../azure-monitor/logs/get-started-queries.md).

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz obszar roboczy log Analytics.

1. W menu obszaru roboczego w obszarze **Ogólne** wybierz pozycję **dzienniki**.

1. Zacznij od pustego zapytania lub wszelkich dostępnych istniejących zapytań.

   * Aby sprawdzić, czy istniejące zapytania są dostępne, na pasku narzędzi zapytania wybierz pozycję **przykłady**  >  **historia** zapytań, która pokazuje zapytania z poprzedniego przebiegu zapytania lub wybierz **Eksploratora zapytań**, w którym są wyświetlane wstępnie skompilowane zapytania.

     Na przykład rozwiązanie Logic Apps B2B zapewnia te prekompilowane zapytania:

     ![Zacznij od "Logic Apps B2B" wstępnie skompilowanych zapytań](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Aby rozpocząć od pustego zapytania, w edytorze zapytań Rozpocznij pisanie [języka zapytań Kusto](/azure/data-explorer/kusto/query/) dla zapytania.

     ![Rozpocznij od pustego zapytania](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Następne kroki

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)