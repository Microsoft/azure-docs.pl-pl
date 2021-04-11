---
title: Tworzenie alertów za pomocą usługi SQL Insights (wersja zapoznawcza)
description: Tworzenie alertów za pomocą usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609904"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Tworzenie alertów za pomocą usługi SQL Insights (wersja zapoznawcza)
Usługa SQL Insights zawiera zestaw szablonów reguł alertów, których można użyć do tworzenia [reguł alertów w Azure monitor w](../alert/../alerts/alerts-overview.md) przypadku typowych problemów z programem SQL. Reguły alertów w usłudze SQL Insights są regułami alertów dziennika opartymi na danych wydajności przechowywanych w tabeli *InsightsMetrics* w dziennikach Azure monitor.  

> [!NOTE]
> Jeśli masz prośby o więcej szablonów reguł alertów usługi SQL Insights, Prześlij opinię przy użyciu linku w dolnej części tej strony lub korzystając z linku opinia usługi SQL Insights w Azure Portal.

## <a name="enable-alert-rules"></a>Włącz reguły alertów 
Wykonaj następujące kroki, aby włączyć alerty w Azure Monitor z Azure Portal.Utworzone reguły alertów zostaną ograniczone do wszystkich zasobów SQL monitorowanych w ramach wybranego profilu monitorowania.  Gdy wyzwalana jest reguła alertu, będzie ona wyzwalana dla określonego wystąpienia lub bazy danych SQL.

> [!NOTE]
> Możesz również utworzyć niestandardowe [reguły alertów dziennika](../alerts/alerts-log.md) , uruchamiając zapytania dotyczące zestawów danych w tabeli *InsightsMetrics* , a następnie zapisując te zapytania jako regułę alertu. 

Wybierz pozycję **SQL (wersja zapoznawcza)** z sekcji **Insights** w menu Azure monitor w Azure Portal. Kliknij pozycję **alerty**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Przycisk alerty":::

Po prawej stronie strony zostanie otwarte okienko **alerty** . Domyślnie zostanie wyświetlone wyzwolone alerty dla zasobów SQL w wybranym profilu monitorowania na podstawie reguł alertów, które zostały już utworzone. Wybierz pozycję **Szablony alertów**, co spowoduje wyświetlenie listy dostępnych szablonów, których można użyć do utworzenia reguły alertu.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Szablony alertów":::

Na stronie **Tworzenie reguły alertu** zapoznaj się z ustawieniami domyślnymi reguły i zmodyfikuj je w razie potrzeby. Możesz również wybrać [grupę akcji](../alerts/action-groups.md) , aby utworzyć powiadomienia i akcje w momencie wyzwolenia reguły alertu. Kliknij pozycję **Włącz regułę alertu** , aby utworzyć regułę alertu po zweryfikowaniu wszystkich jego właściwości.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Strona reguł alertów":::

Aby natychmiast wdrożyć regułę alertu, kliknij przycisk **Wdróż regułę alertu**. Kliknij przycisk **Wyświetl szablon** , jeśli chcesz wyświetlić szablon reguły przed jego faktycznym wdrożeniem.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Wdróż regułę alertów":::

W przypadku wybrania opcji wyświetlania szablonów wybierz pozycję **Wdróż** na stronie szablon, aby utworzyć regułę alertu.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Wdróż z szablonu widoku":::


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [alertach w Azure monitor](../alerts/alerts-overview.md).

