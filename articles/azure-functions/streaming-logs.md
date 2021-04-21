---
title: Przesyłanie strumieniowe dzienników wykonywania w Azure Functions
description: 115–145 znaków, w tym spacje. To streszczenie jest wyświetlane w wynikach wyszukiwania.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830902"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Włączanie dzienników wykonywania przesyłania strumieniowego w Azure Functions

Podczas tworzenia aplikacji często chcesz zobaczyć, co jest zapisywane w dziennikach niemal w czasie rzeczywistym podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia plików dziennika generowanych przez wykonania funkcji.

* **Wbudowane przesyłanie strumieniowe dzienników:** platforma App Service umożliwia wyświetlanie strumienia plików dziennika aplikacji. Jest to równoważne danych wyjściowych widocznych [](functions-develop-local.md) podczas debugowania funkcji podczas lokalnego testowania i korzystania z **karty Test** w portalu. Zostaną wyświetlone wszystkie informacje oparte na dzienniku. Aby uzyskać więcej informacji, zobacz [Stream logs (Przesyłanie strumieniowe dzienników).](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie może być używana z aplikacją uruchamianą w systemie Linux w ramach planu Zużycie.

* **Live Metrics Stream:** gdy aplikacja funkcji jest połączona z usługą [Application Insights](configure-monitoring.md#enable-application-insights-integration), możesz wyświetlać dane dziennika i inne metryki niemal w czasie rzeczywistym w Azure Portal przy użyciu [Live Metrics Stream](../azure-monitor/app/live-stream.md). Użyj tej metody podczas monitorowania funkcji uruchomionych w wielu wystąpieniach lub w systemie Linux w planie zużycie. Ta metoda używa [przykładowych danych](configure-monitoring.md#configure-sampling).

Strumienie dzienników można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistycznych. 

## <a name="portal"></a>Portal

Oba typy strumieni dzienników można wyświetlić w portalu.

### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz **kartę Funkcje platformy** w aplikacji funkcji. Następnie w obszarze **Monitorowanie wybierz pozycję** Przesyłanie strumieniowe **dzienników.**

![Włączanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Umożliwia to połączenie aplikacji z usługą przesyłania strumieniowego dzienników, a dzienniki aplikacji są wyświetlane w oknie. Możesz przełączać się między **dziennikami aplikacji i** **dziennikami serwera internetowego.**  

![Wyświetlanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Aby wyświetlić Live Metrics Stream aplikacji, wybierz **kartę Przegląd** aplikacji funkcji. Po skonfigurowaniu Application Insights zostanie wyświetlony link Application Insights **w** obszarze **Skonfigurowane funkcje.** Ten link umożliwia Application Insights aplikacji.

W Application Insights wybierz pozycję **Live Metrics Stream**. [Przykładowe wpisy dziennika są](configure-monitoring.md#configure-sampling) wyświetlane w obszarze **Przykładowa telemetria**.

![Wyświetlanie Live Metrics Stream w portalu](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Narzędzia podstawowe

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć przy użyciu interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) Użyj następujących poleceń, aby się zalogować, wybrać subskrypcję i przesyłać strumieniowo pliki dziennika:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć przy [użyciu Azure PowerShell](/powershell/azure/). W przypadku programu PowerShell użyj polecenia [Set-AzWebApp,](/powershell/module/az.websites/set-azwebapp) aby włączyć rejestrowanie w aplikacji funkcji, jak pokazano w poniższym fragmencie kodu: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Aby uzyskać więcej informacji, zobacz [kompletny przykładowy kod](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Następne kroki

+ [Monitorowanie usługi Azure Functions](functions-monitoring.md)
+ [Analizowanie Azure Functions danych telemetrycznych w Application Insights](analyze-telemetry-data.md)
