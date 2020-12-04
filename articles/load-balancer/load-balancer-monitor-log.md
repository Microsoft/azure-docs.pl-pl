---
title: Monitoruj operacje, zdarzenia i liczniki dla Load Balancer publicznego w warstwie Podstawowa
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć rejestrowanie dla Azure Load Balancer
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572783"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Azure Monitor dzienników dla standardowego Azure Load Balancer

Możesz użyć różnych typów dzienników na platformie Azure do zarządzania standardowymi modułami równoważenia obciążenia i rozwiązywania problemów. Dzienniki mogą być przesyłane strumieniowo do centrum zdarzeń lub obszaru roboczego Log Analytics. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob Storage i przeglądać w różnych narzędziach, takich jak Excel i Power BI.  Więcej informacji o różnych typach dzienników można znaleźć na poniższej liście.

* **Dzienniki aktywności:** Za pomocą [widoku dzienniki aktywności można monitorować akcje dotyczące zasobów](../azure-resource-manager/management/view-activity-logs.md) , aby wyświetlić wszystkie działania przesyłane do subskrypcji platformy Azure i ich stan. Dzienniki aktywności są domyślnie włączone i można je wyświetlić w Azure Portal. Te dzienniki są dostępne zarówno dla usługi równoważenia obciążenia podstawowego, jak i standardowego.
* **Metryki usługa Load Balancer w warstwie Standardowa:** Ten dziennik służy do wykonywania zapytań o metryki wyeksportowane jako dzienniki dla standardowego Azure Load Balancer. Te dzienniki są dostępne tylko dla usług równoważenia obciążenia w warstwie Standardowa.

> [!IMPORTANT]
> **Sondy kondycji i dzienniki zdarzeń alertów Load Balancer nie są obecnie funkcjonalne i są wymienione w [znanych problemach dotyczących Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Menedżer zasobów. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [opis Menedżer zasobów wdrożenia i wdrożenia klasycznego](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Włącz rejestrowanie sondowania zdarzeń i kondycji, aby rozpocząć zbieranie danych dostępnych w tych dziennikach. Wykonaj następujące kroki, aby włączyć rejestrowanie.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze modułu równoważenia obciążenia, przed kontynuowaniem [Utwórz moduł równoważenia obciążenia](./quickstart-load-balancer-standard-public-portal.md) .

1. W portalu kliknij pozycję **grupy zasobów**.
2. Wybierz **\<resource-group-name>** lokalizację modułu równoważenia obciążenia.
3. Wybierz swój moduł równoważenia obciążenia.
4. Wybierz **Activity log** pozycję  >  **Ustawienia diagnostyczne** dziennika aktywności.
5. W okienku **Ustawienia diagnostyki** w obszarze **Ustawienia diagnostyki** wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
6. W okienku tworzenie **ustawień diagnostycznych** wprowadź **MyLBDiagnostics** w polu **Nazwa** .
7. Dla **ustawień diagnostycznych** dostępne są trzy opcje.  Można wybrać jeden, dwa lub wszystkie trzy i skonfigurować każdy z nich w celu spełnienia wymagań:
   * **Archiwizowanie na koncie magazynu**
   * **Przesyłanie strumieniowe do centrum zdarzeń**
   * **Wysyłanie do usługi Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Zarchiwizuj na koncie magazynu
    Konieczne jest już konto magazynu utworzone dla tego procesu.  Aby utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Zaznacz pole wyboru obok pozycji **Archiwizowanie na koncie magazynu**.
    2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz konto magazynu** .
    3. Wybierz **subskrypcję** , w której konto magazynu zostało utworzone w polu ściągania.
    4. Wybierz nazwę konta magazynu w obszarze **konto magazynu** w polu ściąganie.
    5. Wybierz przycisk OK.

    ### <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
    Musisz już utworzyć centrum zdarzeń dla tego procesu.  Aby utworzyć centrum zdarzeń, zobacz [Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md)

    1. Zaznacz pole wyboru obok pozycji **strumień do centrum zdarzeń**
    2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz centrum zdarzeń** .
    3. Wybierz **subskrypcję** , w której utworzono centrum zdarzeń w polu ściągania.
    4. **Wybierz pozycję przestrzeń nazw centrum zdarzeń** w polu ściągania.
    5. **Wybierz pozycję Nazwa zasad centrum zdarzeń** w polu ściągania.
    6. Wybierz przycisk OK.

    ### <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
    Musisz mieć już utworzony i skonfigurowany obszar roboczy usługi log Analytics dla tego procesu.  Aby utworzyć obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/learn/quick-create-workspace.md)

    1. Zaznacz pole wyboru obok pozycji **Wyślij do log Analytics**.
    2. W polu rozwijanym wybierz **subskrypcję** , w której znajduje się obszar roboczy log Analytics.
    3. Wybierz **obszar roboczy log Analytics** w polu ściągania.


8.  Poniżej sekcji **Metryka** w okienku **Ustawienia diagnostyki** zaznacz pole wyboru obok pozycji: **AllMetrics**

9. Sprawdź, czy wszystko wygląda poprawnie, a następnie kliknij pozycję **Zapisz** w górnej części okienka tworzenie **ustawień diagnostycznych** .

## <a name="activity-log"></a>Dziennik aktywności

Dziennik aktywności jest domyślnie generowany. Można ją skonfigurować do eksportowania na poziomie subskrypcji, postępując zgodnie [z instrukcjami w tym artykule](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Więcej informacji na temat tych dzienników można znaleźć w artykule [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można przeglądać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** Pobierz informacje z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub Azure Portal. Instrukcje krok po kroku dla każdej metody opisano szczegółowo w [operacjach inspekcji w Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md) artykule.
* **Power BI:** Jeśli nie masz jeszcze konta [Power BI](https://powerbi.microsoft.com/pricing) , możesz wypróbować go bezpłatnie. Przy użyciu [integracji dzienników inspekcji platformy Azure dla Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)można analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosować widoki zgodnie z wymaganiami.

## <a name="metrics-as-logs"></a>Metryki jako dzienniki
Za pomocą metryk do rejestrowania funkcji eksportu dostępnych przez Azure Monitor można eksportować metryki Load Balancer. Te metryki spowodują wygenerowanie wpisu dziennika dla każdego interwału próbkowania minuty.

Metryka eksportowania dziennika jest włączana dla poszczególnych poziomów zasobów. Te dzienniki można włączyć, przechodząc do bloku ustawień diagnostycznych, filtrując według grupy zasobów i wybierając Load Balancer, dla których chcesz włączyć eksport metryk. Gdy strona Ustawienia diagnostyczne Load Balancer jest ustawiona, wybierz pozycję AllMetrics, aby wyeksportować kwalifikujące się metryki jako dzienniki.

Zapoznaj się z sekcją [ograniczenia](#limitations) w tym artykule, aby uzyskać ograniczenia dotyczące eksportu metryk.

### <a name="view-and-analyze-metrics-as-logs"></a>Wyświetlanie i analizowanie metryk jako dzienników
Po włączeniu AllMetrics w ustawieniach diagnostycznych usługa Load Balancer w warstwie Standardowa, jeśli jest używany centrum zdarzeń lub Log Analytics obszaru roboczego, te dzienniki zostaną wypełnione w tabeli AzureMonitor. W przypadku eksportowania do magazynu należy nawiązać połączenie z kontem magazynu i pobrać wpisy dziennika JSON dla dzienników sondowania zdarzeń i kondycji. Po pobraniu plików JSON można je przekonwertować na woluminy CSV i wyświetlać w programie Excel, Power BI lub dowolnym innym narzędziu do wizualizacji danych. 

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
Gdy informacje diagnostyczne są przesyłane strumieniowo do centrum zdarzeń, mogą służyć do scentralizowanej analizy dzienników w narzędziu SIEM innej firmy z integracją Azure Monitor. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
Zasoby na platformie Azure mogą być wysyłane bezpośrednio do obszaru roboczego Log Analytics, w którym można uruchamiać złożone zapytania, aby uzyskać informacje na temat rozwiązywania problemów i analizy.  Aby uzyskać więcej informacji, zobacz [zbieranie dzienników zasobów platformy Azure w obszarze roboczym log Analytics w Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Ograniczenia
W przypadku korzystania z metryk do rejestrowania funkcji eksportowania dla modułów równoważenia obciążenia istnieją obecnie następujące ograniczenia:
* Metryki są obecnie wyświetlane przy użyciu nazw wewnętrznych podczas eksportowania jako dzienników można znaleźć mapowanie w poniższej tabeli.
* Wymiarowanie metryk nie jest zachowywane. Na przykład przy użyciu metryk takich jak DipAvailability (stan sondowania kondycji) nie będzie można podzielić ani wyświetlić według adresu IP zaplecza
* Metryki używanych portów i przyznanych portów przydziałów adresów sieciowych nie są obecnie dostępne do eksportu jako dzienniki

## <a name="next-steps"></a>Następne kroki
* [Przejrzyj dostępne metryki dla Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Utwórz i przetestuj zapytania następujące instrukcje Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Prześlij opinię na temat tego artykułu lub Load Balancer funkcji, korzystając z poniższych linków
