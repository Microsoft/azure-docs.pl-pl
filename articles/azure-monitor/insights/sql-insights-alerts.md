---
title: Tworzenie alertów za pomocą usługi SQL Insights (wersja zapoznawcza)
description: Tworzenie alertów za pomocą usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726866"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Tworzenie alertów za pomocą usługi SQL Insights (wersja zapoznawcza)
Usługa SQL Insights zawiera zestaw szablonów reguł alertów, których można użyć do tworzenia reguł [alertów](../alert/../alerts/alerts-overview.md) w Azure Monitor w przypadku typowych problemów z bazą danych SQL. Reguły alertów usługi SQL Insights to reguły alertów dzienników oparte na danych wydajności przechowywanych w tabeli *InsightsMetrics* w Azure Monitor dzienników.  

> [!NOTE]
> Aby utworzyć alert dla usługi SQL Insights przy użyciu szablonu usługi Resource Manager, zobacz Resource Manager przykłady szablonów [dla usługi SQL Insights.](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)


> [!NOTE]
> Jeśli masz żądania dotyczące większej liczby szablonów reguł alertów usługi SQL Insights, wyślij opinię za pomocą linku w dolnej części tej strony lub za pomocą linku opinii usługi SQL Insights w Azure Portal.

## <a name="enable-alert-rules"></a>Włączanie reguł alertów 
Aby włączyć alerty w programie Azure Monitor z Azure Portal, należy wykonać następujące Azure Portal.Tworzone reguły alertów będą mieć zakres ograniczony do wszystkich zasobów SQL monitorowanych w ramach wybranego profilu monitorowania.  Po wyzwoleniu reguły alertu zostanie ona wyzwolona dla określonego wystąpienia SQL lub bazy danych.

> [!NOTE]
> Możesz również utworzyć niestandardowe reguły [alertów dzienników,](../alerts/alerts-log.md) uruchamiając zapytania dotyczące zestawów danych w tabeli *InsightsMetrics,* a następnie zapisując te zapytania jako regułę alertu. 

Wybierz **pozycję SQL (wersja zapoznawcza)** w sekcji **Szczegółowe** informacje w menu Azure Monitor w Azure Portal. Kliknij pozycję **Alerty**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Przycisk Alerty":::

W **prawej** części strony zostanie otwarte okienko Alerty. Domyślnie będą wyświetlane wyzgnione alerty dla zasobów SQL w wybranym profilu monitorowania na podstawie już utworzonych reguł alertów. Wybierz **pozycję Szablony** alertów, aby wyświetlić listę dostępnych szablonów, których można użyć do utworzenia reguły alertu.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Szablony alertów":::

Na stronie **Tworzenie reguły alertu** przejrzyj ustawienia domyślne reguły i edytuj je zgodnie z potrzebami. Możesz również wybrać grupę [akcji, aby](../alerts/action-groups.md) utworzyć powiadomienia i akcje po wyzwoleniu reguły alertu. Kliknij **pozycję Włącz regułę** alertu, aby utworzyć regułę alertu po zweryfikowaniu wszystkich jej właściwości.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Strona reguły alertów":::

Aby natychmiast wdrożyć regułę alertu, kliknij pozycję **Wd wdrażaj regułę alertu.** Kliknij **pozycję Wyświetl szablon,** jeśli chcesz wyświetlić szablon reguły przed jego wdrożeniem.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Wdrażanie reguły alertu":::

Jeśli zdecydujesz się wyświetlić szablony, wybierz **pozycję** Wd wdrażaj na stronie szablonu, aby utworzyć regułę alertu.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Wdrażanie z szablonu widoku":::


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [alertach w Azure Monitor](../alerts/alerts-overview.md).

