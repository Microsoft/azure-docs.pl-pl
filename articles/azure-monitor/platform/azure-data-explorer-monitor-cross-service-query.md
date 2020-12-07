---
title: Kwerenda między usługami Azure Monitor i Azure Eksplorator danych (wersja zapoznawcza)
description: Wykonaj zapytania dotyczące danych Eksplorator danych platformy Azure za pomocą narzędzi Azure Log Analytics Tools na odwrót, aby dołączyć i analizować wszystkie dane w jednym miejscu.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 0af9111223bb16f1c5843223e7fe1661ce99c623
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749250"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Zapytanie dotyczące wielu usług — Azure Monitor i Azure Eksplorator danych (wersja zapoznawcza)
Twórz zapytania krzyżowe między usługą [Azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/), [Application Insights](/azure/azure-monitor/app/app-insights-overview)i [log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor i obsługa zapytań międzyusługowych na platformie Azure Eksplorator danych
Dzięki temu można [tworzyć zapytania krzyżowe między usługą azure Eksplorator danych i Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) i [tworzyć zapytania obejmujące wiele usług między Azure monitor i Eksplorator danych platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::

Na przykład (wykonywanie zapytania dotyczącego usługi Azure Eksplorator danych from Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Gdzie zapytanie zewnętrzne bada tabelę w obszarze roboczym, a następnie łączy się z inną tabelą w klastrze usługi Azure Eksplorator danych (w tym przypadku ClusterName = help, DatabaseName = Samples) za pomocą nowej funkcji "ADX ()", na przykład jak można wykonać zapytanie do innego obszaru roboczego z wnętrza tekstu zapytania.

> [!NOTE]
> * Możliwość wykonywania zapytań dotyczących danych Azure Monitor z platformy Azure Eksplorator danych, bezpośrednio z poziomu narzędzi klienckich Eksplorator danych platformy Azure lub pośrednio przez uruchomienie zapytania w klastrze Eksplorator danych platformy Azure, jest w trybie podglądu.
> * Skontaktuj się z zespołem [zapytań międzyusługowych](mailto:adxproxy@microsoft.com) , korzystając z jakichkolwiek pytań.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Zapytanie eksportowane Log Analytics danych z konta usługi Azure Blob Storage

Eksportowanie danych z Azure Monitor na konto usługi Azure Storage umożliwia przechowywanie w niskich kosztach i możliwość ponownego przydzielenia dzienników do różnych regionów.

Użyj usługi Azure Eksplorator danych, aby wykonać zapytanie dotyczące danych, które zostały wyeksportowane z Log Analytics obszarów roboczych. Po skonfigurowaniu obsługiwane tabele, które są wysyłane z obszarów roboczych do konta usługi Azure Storage, będą dostępne jako źródło danych dla Eksplorator danych platformy Azure. [Wyeksportuj dane z Azure monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Usługa Azure Eksplorator danych Query z przepływu magazynu.":::

>[!tip] 
> * Aby wyeksportować wszystkie dane z obszaru roboczego Log Analytics do konta usługi Azure Storage lub centrum zdarzeń, użyj funkcji eksportu danych obszaru roboczego Log Analytics dzienników Azure Monitor. [Zobacz log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:
* [Twórz zapytania krzyżowe między usługą Azure Eksplorator danych i Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Zapytanie Azure Monitor danych z platformy Azure Eksplorator danych
* [Twórz zapytania obejmujące wiele usług między Azure monitor i Eksplorator danych platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Wykonywanie zapytań dotyczących danych Eksplorator danych platformy Azure z Azure Monitor
* [Log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Połącz i zbadaj konto usługi Azure Blob Storage za pomocą Log Analytics wyeksportowanych danych.