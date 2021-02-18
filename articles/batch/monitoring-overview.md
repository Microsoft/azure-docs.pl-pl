---
title: Monitoruj Azure Batch
description: Dowiedz się więcej na temat usług monitorowania platformy Azure, metryk, dzienników diagnostycznych i innych funkcji monitorowania dla Azure Batch.
ms.topic: how-to
ms.date: 04/05/2018
ms.openlocfilehash: b926f9c6d173cd0b8d886047eae490e4a151988f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595438"
---
# <a name="monitor-batch-solutions"></a>Monitorowanie rozwiązań usługi Batch

Platforma Azure i usługa Batch oferują szereg usług, narzędzi i interfejsów API do monitorowania rozwiązań usługi Batch. Ten artykuł omówienia ułatwia wybór podejścia do monitorowania, które odpowiada Twoim potrzebom.

Aby zapoznać się z omówieniem składników i usług platformy Azure dostępnych do monitorowania zasobów platformy Azure, zobacz [monitorowanie aplikacji i zasobów platformy Azure](../azure-monitor/overview.md).

## <a name="subscription-level-monitoring"></a>Monitorowanie na poziomie subskrypcji

Na poziomie subskrypcji, który obejmuje konta usługi Batch, [Dziennik aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md) zbiera dane zdarzeń operacyjnych w [różnych kategoriach](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

W przypadku kont usługi Batch dziennik aktywności zbiera zdarzenia związane z tworzeniem i usuwaniem kont oraz zarządzaniem kluczami.

Jednym ze sposobów na pobranie zdarzeń z dziennika aktywności jest użycie Azure Portal. Kliknij kolejno pozycje **wszystkie usługi**  >  **Dziennik aktywności**. Można też wykonywać zapytania o zdarzenia za pomocą interfejsu wiersza polecenia platformy Azure, poleceń cmdlet programu PowerShell lub interfejsu API REST Azure Monitor. Możesz również wyeksportować dziennik aktywności lub skonfigurować [alerty dziennika aktywności](../azure-monitor/alerts/alerts-activity-log.md).

## <a name="batch-account-level-monitoring"></a>Monitorowanie na poziomie konta w usłudze Batch

Monitoruj każde konto usługi Batch przy użyciu funkcji [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [metryki](../azure-monitor/essentials/data-platform-metrics.md) i opcjonalne [dzienniki diagnostyczne](../azure-monitor/essentials/platform-logs-overview.md) dla zasobów objętych zakresem na poziomie konta usługi Batch, takich jak pule, zadania i zadania. Zbieraj te dane i korzystaj z nich ręcznie lub programowo, aby monitorować działania na koncie w usłudze Batch i diagnozować problemy. Aby uzyskać szczegółowe informacje, zobacz [metryki usługi Batch, alerty i dzienniki na potrzeby oceny i monitorowania diagnostyki](batch-diagnostics.md).
 
> [!NOTE]
> Metryki są domyślnie dostępne na koncie w usłudze Batch bez dodatkowej konfiguracji i zawierają 30-dniową historię zmian. Należy włączyć rejestrowanie diagnostyczne dla konta usługi Batch i nawiązać dodatkowe koszty przechowywania lub przetwarzania danych dzienników diagnostycznych. 

## <a name="batch-resource-monitoring"></a>Monitorowanie zasobów wsadowych

W aplikacjach wsadowych należy używać interfejsów API programu Batch do monitorowania stanu zasobów, takich jak zadania, zadania, węzły i pule. Na przykład:

* [Liczenie zadań i węzłów obliczeniowych według stanu](batch-get-resource-counts.md)
* [Twórz zapytania umożliwiające wydajne wyświetlanie zasobów wsadowych](batch-efficient-list-queries.md)
* [Tworzenie zależności zadań](batch-task-dependencies.md)
* Korzystanie z [zadania Menedżera zadań](/rest/api/batchservice/job/add#jobmanagertask)
* Monitoruj [stan zadania](/rest/api/batchservice/task/list#taskstate)
* Monitoruj [stan węzła](/rest/api/batchservice/computenode/list#computenodestate)
* Monitoruj [stan puli](/rest/api/batchservice/pool/get#poolstate)
* Monitoruj [użycie puli na koncie](/rest/api/batchservice/pool/listusagemetrics)
* [Liczba węzłów puli według stanu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Liczniki wydajności maszyn wirtualnych i monitorowanie aplikacji

* [Application Insights](../azure-monitor/app/app-insights-overview.md) to usługa platformy Azure, za pomocą której można programowo monitorować dostępność, wydajność i użycie zadań i zadań usługi Batch. Łatwe pobieranie liczników wydajności z węzłów obliczeniowych (maszyn wirtualnych) i informacji niestandardowych dotyczących zadań z maszyn wirtualnych. 

  Aby zapoznać się z przykładem, zobacz [monitorowanie i debugowanie aplikacji usługi Batch .NET przy użyciu Application Insights](monitor-application-insights.md) i towarzyszącego [przykładu kodu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Korzystanie z Application Insights może pociągnąć za sobą dodatkowe koszty. Zobacz [Opcje cenowe](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Opcjonalnie Skonfiguruj rozwiązanie do przetwarzania wsadowego, aby [wyświetlać Application Insights dane](https://github.com/Azure/batch-insights) , takie jak liczniki wydajności maszyn wirtualnych, w Batch Explorer.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej na temat [rejestrowania diagnostycznego](batch-diagnostics.md) w usłudze Batch.
