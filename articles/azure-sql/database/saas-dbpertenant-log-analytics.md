---
title: Azure Monitor dzienników przy użyciu aplikacji wielodostępnej
description: Konfigurowanie i używanie dzienników Azure Monitor z wielodostępnym Azure SQL Database aplikacji SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588788"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Konfigurowanie i używanie dzienników Azure Monitor z wielodostępnym Azure SQL Database aplikacji SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono Konfigurowanie [dzienników Azure monitor](../../azure-monitor/logs/log-query-overview.md) i używanie ich do monitorowania pul elastycznych i baz danych. Ten samouczek jest oparty na [samouczku dotyczącego monitorowania i zarządzania wydajnością](saas-dbpertenant-performance-monitoring.md). Pokazano, jak używać dzienników Azure Monitor, aby rozszerzyć monitorowanie i alerty podane w Azure Portal. Dzienniki Azure Monitor obsługują monitorowanie tysięcy pul elastycznych i setek tysięcy baz danych. Dzienniki Azure Monitor to pojedyncze rozwiązanie monitorujące, które może zintegrować monitorowanie różnych aplikacji i usług platformy Azure w ramach wielu subskrypcji platformy Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i Konfigurowanie dzienników Azure Monitor.
> * Użyj dzienników Azure Monitor do monitorowania pul i baz danych.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Wingtip bilety SaaS bazy danych dla dzierżawców. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip bilety SaaS Database-per-dzierżawców](./saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).

Zapoznaj się z [samouczkiem dotyczącym monitorowania i zarządzania wydajnością](saas-dbpertenant-performance-monitoring.md) w omówieniu scenariuszy i wzorców SaaS oraz ich wpływu na wymagania dotyczące monitorowania.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorowanie wydajności bazy danych i elastycznej puli przy użyciu dzienników Azure Monitor i zarządzanie nimi

W przypadku Azure SQL Database, monitorowanie i alerty są dostępne w bazach danych i pulach w Azure Portal. To wbudowane monitorowanie i alerty jest wygodne, ale jest również specyficzne dla zasobów. Oznacza to, że jest mniej dobrze dostosowany do monitorowania dużych instalacji lub zapewnienia ujednoliconego widoku w ramach zasobów i subskrypcji.

W przypadku scenariuszy o dużej ilości można użyć dzienników Azure Monitor do monitorowania i generowania alertów. Azure Monitor to oddzielna usługa platformy Azure, która umożliwia analizowanie za pośrednictwem dzienników zebranych w obszarze roboczym z potencjalnie wielu usług. Dzienniki Azure Monitor udostępniają wbudowane narzędzia do wizualizacji danych, które umożliwiają analizę danych operacyjnych. Rozwiązanie SQL Analytics zawiera kilka wstępnie zdefiniowanych pul elastycznych i monitorowania baz danych oraz wyświetlenie alertów i zapytań. Dzienniki Azure Monitor udostępniają również projektanta widoków niestandardowych.

Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics. Log Analytics obszary robocze i rozwiązania do analiz otwierają się w Azure Portal. Azure Portal to nowszy punkt dostępu, ale może znajdować się za portalem pakietu Operations Management Suite w niektórych obszarach.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Tworzenie danych diagnostycznych wydajności przez symulowanie obciążenia dla dzierżawców 

1. W programie PowerShell ISE Otwórz *.. \\ WingtipTicketsSaaS-MultiTenantDb — główne \\ moduły uczenia \\ wydajności i zarządzania \\Demo-PerformanceMonitoringAndManagement.ps1*. Pozostaw ten skrypt otwarty, ponieważ w tym samouczku możesz chcieć uruchomić kilka scenariuszy generowania obciążenia.
1. Jeśli jeszcze tego nie zrobiono, Zainicjuj obsługę partii dzierżawców, aby zapewnić bardziej interesujący kontekst monitorowania. Ten proces trwa kilka minut.

   a. Ustaw **$DemoScenario = 1**, _Zainicjuj obsługę partii dzierżawców_.

   b. Aby uruchomić skrypt i wdrożyć dodatkowe 17 dzierżawców, naciśnij klawisz F5.

1. Teraz uruchom generator obciążenia, aby uruchomić symulowane obciążenie wszystkich dzierżawców.

    a. Ustaw **$DemoScenario = 2**, _Generuj normalne obciążenie o natężeniu (około 30 jednostek DTU)_.

    b. Aby uruchomić skrypt, naciśnij klawisz F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz bilety Wingtip SaaS dla skryptów aplikacji dla dzierżawców

Wingtip bilety SaaS wielodostępne skrypty bazy danych i kod źródłowy aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Aby uzyskać instrukcje dotyczące pobierania i odblokowywania skryptów programu PowerShell w programie Wingtip, zobacz [Ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Zainstaluj i skonfiguruj obszar roboczy Log Analytics i rozwiązanie Azure SQL Analytics

Azure Monitor jest oddzielną usługą, która musi być skonfigurowana. Azure Monitor dzienników zbiera dane dziennika, telemetrię i metryki w obszarze roboczym Log Analytics. Podobnie jak w przypadku innych zasobów na platformie Azure, należy utworzyć obszar roboczy Log Analytics. Nie trzeba tworzyć obszaru roboczego w tej samej grupie zasobów co monitorowane aplikacje. Zdarza się to często najczęściej. W przypadku aplikacji biletów Wingtip Użyj pojedynczej grupy zasobów, aby upewnić się, że obszar roboczy został usunięty z aplikacją.

1. W programie PowerShell ISE Otwórz *.. \\ WingtipTicketsSaaS-MultiTenantDb — główne \\ moduły uczenia dotyczące \\ monitorowania wydajności i zarządzania \\ \\Demo-LogAnalytics.ps1log Analytics*.
1. Aby uruchomić skrypt, naciśnij klawisz F5.

Teraz możesz otworzyć dzienniki Azure Monitor w Azure Portal. Zebranie danych telemetrycznych w obszarze roboczym Log Analytics i udostępnienie jej jako widocznej może potrwać kilka minut. Im dłużej opuścisz system zbierania danych diagnostycznych, tym bardziej interesujące jest środowisko. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorowanie pul i baz danych przy użyciu obszaru roboczego Log Analytics i rozwiązania SQL Analytics


W tym ćwiczeniu Otwórz Log Analytics obszar roboczy w Azure Portal, aby wyszukać dane telemetryczne zebrane dla baz danych i pul.

1. Przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie usługi** , aby otworzyć obszar roboczy log Analytics. Następnie wyszukaj Log Analytics.

   ![Otwórz obszar roboczy Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wybierz obszar roboczy o nazwie _wtploganalytics &lt; - &gt; User_.

1. Wybierz pozycję **Przegląd** , aby otworzyć rozwiązanie log analytics w Azure Portal.

   ![Omówienie](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Wdrożenie rozwiązania może potrwać kilka minut. 

1. Wybierz kafelek **Azure SQL Analytics** , aby go otworzyć.

    ![Kafelek przegląd](./media/saas-dbpertenant-log-analytics/overview.png)

1. Widoki w rozwiązaniu przesuwają się w bok z własnym wewnętrznym paskiem przewijania u dołu. W razie potrzeby Odśwież stronę.

1. Aby poznać stronę podsumowania, wybierz kafelki lub pojedyncze bazy danych, aby otworzyć Eksploratora przechodzenia do szczegółów.

    ![Pulpit nawigacyjny usługi log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zmień ustawienie filtru, aby zmodyfikować zakres czasu. Na potrzeby tego samouczka wybierz pozycję **Ostatnia 1 godzina**.

    ![Filtr czasu](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Wybierz pojedynczą bazę danych, aby poznać użycie zapytań i metryki dla tej bazy danych.

    ![Analiza bazy danych](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Aby wyświetlić metryki użycia, przewiń stronę analiza w prawo.
 
     ![Metryki bazy danych](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Przewiń stronę analiza w lewo i wybierz kafelek serwer na liście **Informacje o zasobach** .  

    ![Lista informacji o zasobach](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Zostanie otwarta strona z pulami i bazami danych na serwerze.

    ![Serwer z pulami i bazami danych](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Wybierz pulę. Na stronie puli, która zostanie otwarta, przewiń w prawo, aby wyświetlić metryki puli. 

    ![Metryki puli](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. W obszarze roboczym Log Analytics wybierz pozycję **Portal pakietu OMS** , aby otworzyć obszar roboczy.

    ![Obszar roboczy usługi Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

W obszarze roboczym Log Analytics można bardziej szczegółowo eksplorować dane dziennika i metryki. 

Monitorowanie i alerty w dziennikach Azure Monitor są oparte na zapytaniach dotyczących danych w obszarze roboczym, w przeciwieństwie do alertów zdefiniowanych dla każdego zasobu w Azure Portal. Korzystając z alertów dotyczących zapytań, można zdefiniować pojedynczy alert, który będzie wyglądał na wszystkie bazy danych, zamiast definiować go dla każdej bazy danych. Zapytania są ograniczone tylko przez dane dostępne w obszarze roboczym.

Aby uzyskać więcej informacji na temat używania dzienników Azure Monitor do wykonywania zapytań i ustawiania alertów, zobacz temat [Work with alert rules in Azure monitor Logs](../../azure-monitor/alerts/alerts-metric.md).

Azure Monitor dzienników dla SQL Database opłat na podstawie ilości danych w obszarze roboczym. W tym samouczku utworzono bezpłatny obszar roboczy, który jest ograniczony do 500 MB dziennie. Po osiągnięciu tego limitu dane nie są już dodawane do obszaru roboczego.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i Konfigurowanie dzienników Azure Monitor.
> * Użyj dzienników Azure Monitor do monitorowania pul i baz danych.

Wypróbuj [Samouczek dotyczący analizy dzierżawy](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które kompilują na wstępną Wingtip bilety SaaS wdrożenie aplikacji na dzierżawcę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Dzienniki usługi Azure Monitor](../../azure-monitor/insights/azure-sql.md)