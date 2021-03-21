---
title: Monitorowanie operacji, zdarzeń i liczników dla publicznego modułu równoważenia obciążenia
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć rejestrowanie dla Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709822"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Dzienniki Azure Monitor dla usługi Azure usługa Load Balancer w warstwie Standardowa

Możesz użyć różnych typów dzienników Azure Monitor, aby zarządzać usługą Azure usługa Load Balancer w warstwie Standardowa i rozwiązywać problemy. Dzienniki mogą być przesyłane strumieniowo do centrum zdarzeń lub obszaru roboczego Log Analytics. Można wyodrębnić wszystkie dzienniki z usługi Azure Blob Storage i wyświetlić je w narzędziach takich jak Excel i Power BI. 

Typy dzienników są następujące:

* **Dzienniki aktywności:** Możesz wyświetlić wszystkie działania przesyłane do subskrypcji platformy Azure wraz z ich stanami. Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników aktywności w celu monitorowania akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md). Dzienniki aktywności są domyślnie włączone i można je wyświetlić w Azure Portal. Te dzienniki są dostępne zarówno dla usługi Azure Basic Load Balancer, jak i usługa Load Balancer w warstwie Standardowa.
* **Metryki usługa Load Balancer w warstwie Standardowa:** Ten dziennik służy do wykonywania zapytań o metryki wyeksportowane jako dzienniki dla usługa Load Balancer w warstwie Standardowa. Te dzienniki są dostępne tylko dla usługa Load Balancer w warstwie Standardowa.

> [!IMPORTANT]
> Sondy kondycji i dzienniki zdarzeń alertów Load Balancer nie są obecnie funkcjonalne i są wymienione w [znanych problemach dotyczących Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Azure Resource Manager. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [opis Menedżer zasobów wdrożenia i wdrożenia klasycznego](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Włącz rejestrowanie sondowania zdarzeń i kondycji, aby rozpocząć zbieranie danych dostępnych w tych dziennikach. Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze modułu równoważenia obciążenia, przed kontynuowaniem [Utwórz moduł równoważenia obciążenia](./quickstart-load-balancer-standard-public-portal.md) .
1. W portalu wybierz pozycję **grupy zasobów**.
2. Wybierz **\<resource-group-name>** lokalizację modułu równoważenia obciążenia.
3. Wybierz swój moduł równoważenia obciążenia.
4. Wybierz pozycję  >  **Ustawienia diagnostyczne** dziennika aktywności.
5. W okienku **Ustawienia diagnostyki** w obszarze **Ustawienia diagnostyki** wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
6. W okienku tworzenie **ustawień diagnostycznych** wpisz **MyLBDiagnostics** w polu **Nazwa** .
7. Dla **ustawień diagnostycznych** dostępne są trzy opcje. Można wybrać jeden, dwa lub wszystkie trzy i skonfigurować każdy z nich w celu spełnienia wymagań:

   * **Archiwizuj na koncie magazynu**. Konieczne jest już konto magazynu utworzone dla tego procesu. Aby utworzyć konto magazynu, zobacz temat [Tworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Zaznacz pole wyboru **Archiwizuj do konta magazynu** .
     2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz konto magazynu** .
     3. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której utworzono konto magazynu.
     4. Z listy rozwijanej **konto magazynu** wybierz nazwę konta magazynu.
     5. Wybierz przycisk **OK**.

   * **Przesyłaj strumieniowo do centrum zdarzeń**. Musisz już utworzyć centrum zdarzeń dla tego procesu. Aby utworzyć centrum zdarzeń, zobacz [Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md).
     1. Zaznacz pole wyboru **Przesyłaj strumieniowo do centrum zdarzeń**.
     2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz centrum zdarzeń** .
     3. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której utworzono centrum zdarzeń.
     4. Z listy rozwijanej **Wybierz przestrzeń nazw centrum zdarzeń** wybierz przestrzeń nazw.
     5. Z listy rozwijanej **Wybierz nazwę zasad centrum zdarzeń** wybierz nazwę.
     6. Wybierz przycisk **OK**.

   * **Wyślij do log Analytics**. Musisz mieć już utworzony i skonfigurowany obszar roboczy usługi log Analytics dla tego procesu. Aby utworzyć obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
     1. Zaznacz pole wyboru **Wyślij do usługi Log Analytics**.
     2. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której znajduje się obszar roboczy log Analytics.
     3. Z listy rozwijanej **obszar roboczy log Analytics** wybierz obszar roboczy.

8. W sekcji **Metryka** w okienku **Ustawienia diagnostyki** zaznacz pole wyboru **AllMetrics** .

9. Sprawdź, czy wszystko wygląda poprawnie, a następnie wybierz pozycję **Zapisz** w górnej części okienka tworzenia **ustawień diagnostycznych** .

## <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dziennik aktywności jest domyślnie generowany. Możesz skonfigurować go do eksportowania na poziomie subskrypcji, wykonując [instrukcje przedstawione w tym artykule](../azure-monitor/essentials/activity-log.md). Więcej informacji na temat tych dzienników można znaleźć w artykule [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](../azure-resource-manager/management/view-activity-logs.md) .

Dane dziennika aktywności można wyświetlać i analizować za pomocą jednej z następujących metod:

* **Narzędzia platformy Azure:** Pobierz informacje z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, usługi Azure REST API lub Azure Portal. [Operacje inspekcji z Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md) artykułu zawierają instrukcje krok po kroku dla każdej z tych metod.
* **Power BI:** Jeśli nie masz jeszcze konta [Power BI](https://powerbi.microsoft.com/pricing) , możesz wypróbować go bezpłatnie. Za pomocą [integracji dzienników inspekcji platformy Azure dla Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)można analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych. Można też dostosować widoki zgodnie z wymaganiami.

## <a name="view-and-analyze-metrics-as-logs"></a>Wyświetlanie i analizowanie metryk jako dzienników
Korzystając z funkcji eksportowania w Azure Monitor, można eksportować metryki Load Balancer. Te metryki spowodują wygenerowanie wpisu dziennika dla każdego interwału próbkowania w ciągu minuty.

Eksportowanie metryk do dzienników jest włączone na poziomie poszczególnych zasobów. Aby włączyć te dzienniki:

1. Przejdź do okienka **ustawień diagnostycznych** .
1. Filtruj według grupy zasobów, a następnie wybierz wystąpienie Load Balancer, dla którego chcesz włączyć eksport metryk. 
1. Gdy strona Ustawienia diagnostyczne dla Load Balancer jest ustawiona, wybierz pozycję **AllMetrics** , aby wyeksportować kwalifikujące się metryki jako dzienniki.

Aby uzyskać ograniczenia dotyczące eksportu metryk, zobacz sekcję [ograniczenia](#limitations) w tym artykule.

Po włączeniu **AllMetrics** w ustawieniach diagnostycznych usługa Load Balancer w warstwie Standardowa, jeśli korzystasz z centrum zdarzeń lub log Analytics obszaru roboczego, te dzienniki zostaną wypełnione w tabeli **AzureMonitor** . 

Jeśli eksportujesz do magazynu, Połącz się z kontem magazynu i Pobierz wpisy dziennika JSON dla dzienników sondowania zdarzeń i kondycji. Po pobraniu plików JSON można przekonwertować je na woluminy CSV i wyświetlać je w programie Excel, Power BI lub dowolnym innym narzędziu do wizualizacji danych. 

> [!TIP]
> Jeśli znasz program Visual Studio i podstawowe pojęcia dotyczące zmieniania wartości dla stałych i zmiennych w języku C#, możesz użyć [narzędzi konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
Gdy informacje diagnostyczne są przesyłane strumieniowo do centrum zdarzeń, można użyć go do scentralizowanej analizy dzienników w narzędziu partner SIEM z integracją Azure Monitor. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
Informacje diagnostyczne dotyczące zasobów na platformie Azure można wysyłać bezpośrednio do obszaru roboczego Log Analytics. W tym obszarze roboczym można uruchamiać złożone zapytania względem informacji dotyczących rozwiązywania problemów i analizy. Aby uzyskać więcej informacji, zobacz [zbieranie dzienników zasobów platformy Azure w obszarze roboczym log Analytics w Azure monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Ograniczenia
Funkcja eksportu metryk do dzienników dla Azure Load Balancer ma następujące ograniczenia:
* Metryki są obecnie wyświetlane za poorednictwem nazw wewnętrznych podczas eksportowania ich jako dzienników. Mapowanie można znaleźć w poniższej tabeli.
* Wymiarowanie metryk nie jest zachowywane. Na przykład przy użyciu metryk, takich jak **DipAvailability** (stan sondy kondycji) nie będzie można podzielić ani przeglądać według adresu IP zaplecza.
* Metryki dla używanych portów i przyznanych portów przydziałów adresów sieciowych nie są obecnie dostępne do eksportu jako dzienniki.

## <a name="next-steps"></a>Następne kroki
* [Przeglądanie dostępnych metryk dla modułu równoważenia obciążenia](./load-balancer-standard-diagnostics.md)
* [Twórz i Testuj zapytania według następujących instrukcji Azure Monitor](../azure-monitor/logs/log-query-overview.md)