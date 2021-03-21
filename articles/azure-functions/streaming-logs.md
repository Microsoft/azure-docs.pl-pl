---
title: Dzienniki wykonywania przesyłania strumieniowego w Azure Functions
description: 115-145 znaków, w tym spacje. To streszczenie jest wyświetlane w wynikach wyszukiwania.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 085849386fce929ceaec4536844cf31b94fe7539
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033055"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Włączanie dzienników wykonywania przesyłania strumieniowego w Azure Functions

Podczas tworzenia aplikacji często warto zobaczyć, co jest zapisywane w dziennikach niemal w czasie rzeczywistym podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia plików dziennika generowanych przez wykonania funkcji.

* **Wbudowane przesyłanie strumieniowe dzienników**: platforma App Service umożliwia wyświetlenie strumienia plików dziennika aplikacji. Jest to równoznaczne z danymi wyjściowymi wyświetlanymi podczas debugowania funkcji podczas [lokalnego tworzenia](functions-develop-local.md) i korzystania z karty **test** w portalu. Wyświetlane są wszystkie informacje oparte na dzienniku. Aby uzyskać więcej informacji, zobacz [dzienniki przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie może być używana z aplikacją działającą w systemie Linux w planie zużycia.

* **Live Metrics Stream**: gdy aplikacja funkcji jest [połączona z Application Insights](configure-monitoring.md#enable-application-insights-integration), można wyświetlać dane dziennika i inne metryki niemal w czasie rzeczywistym w Azure Portal przy użyciu [Live Metrics Stream](../azure-monitor/app/live-stream.md). Użyj tej metody, gdy funkcje monitorowania działają w wielu wystąpieniach lub w systemie Linux w planie zużycia. Ta metoda używa [danych próbkowanych](configure-monitoring.md#configure-sampling).

Strumienie dzienników można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistycznych. 

## <a name="portal"></a>Portal

Oba typy strumieni dzienników można wyświetlić w portalu.

### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz kartę **funkcje platformy** w aplikacji funkcji. Następnie w obszarze **monitorowanie** wybierz pozycję **Rejestruj strumieniowo**.

![Włączanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Spowoduje to połączenie aplikacji z usługą przesyłania strumieniowego dzienników, a Dzienniki aplikacji są wyświetlane w oknie. Można przełączać się między **dziennikami aplikacji** a **dziennikami serwera sieci Web**.  

![Wyświetlanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Aby wyświetlić Live Metrics Stream aplikacji, wybierz kartę **Przegląd** aplikacji funkcji. Gdy Application Insights włączasz, zobaczysz link **Application Insights** w obszarze **skonfigurowane funkcje**. Ten link prowadzi do strony Application Insights Twojej aplikacji.

W Application Insights wybierz pozycję **Live Metrics Stream**. [Przykładowe wpisy dziennika](configure-monitoring.md#configure-sampling) są wyświetlane w obszarze **przykładowe dane telemetryczne**.

![Wyświetlanie Live Metrics Stream w portalu](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Narzędzia podstawowe

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Użyj następujących poleceń, aby się zalogować, wybierz swoją subskrypcję i pliki dziennika przesyłania strumieniowego:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć za pomocą [Azure PowerShell](/powershell/azure/). W przypadku programu PowerShell Użyj polecenia [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) , aby włączyć rejestrowanie w aplikacji funkcji, jak pokazano w poniższym fragmencie kodu: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Aby uzyskać więcej informacji, zobacz [kompletny przykład kodu](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Następne kroki

+ [Monitorowanie usługi Azure Functions](functions-monitoring.md)
+ [Analizuj dane telemetryczne Azure Functions w Application Insights](analyze-telemetry-data.md)
