---
title: Konfigurowanie metryk i dzienników w chmurze dla usługi Azure API Management Brama samoobsługowa | Microsoft Docs
description: Dowiedz się, jak skonfigurować metryki i dzienniki w chmurze dla bramy samohostowanej platformy Azure API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574051"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurowanie metryk i dzienników w chmurze dla usługi Azure API Management Brama samoobsługowa

Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania metryk i dzienników w chmurze dla [bramy samoobsługowej](./self-hosted-gateway-overview.md).

Brama samoobsługowa musi być skojarzona z usługą API Management i wymaga połączenia wychodzącego TCP/IP z platformą Azure na porcie 443. Brama korzysta z połączenia wychodzącego w celu wysyłania danych telemetrycznych do platformy Azure, jeśli została skonfigurowana. 

## <a name="metrics"></a>Metryki
Domyślnie Brama samoobsługowa emituje wiele metryk za pomocą [Azure monitor](https://azure.microsoft.com/services/monitor/), takich jak zarządzana Brama [w chmurze](api-management-howto-use-azure-monitor.md). 

Funkcję można włączać lub wyłączać przy użyciu `telemetry.metrics.cloud` klucza w ConfigMap wdrożenia bramy. Poniżej znajduje się podział dostępnych konfiguracji:

| Pole  | Domyślne | Opis |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Włącza rejestrowanie za Azure Monitor. Wartość może być `true` , `false` . |


Oto Przykładowa konfiguracja:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Brama samoobsługowa obecnie emituje następujące metryki za pomocą Azure Monitor:

| Metric  | Opis |
| ------------- | ------------- |
| Żądania  | Liczba żądań interfejsu API w danym okresie |
| Czas trwania żądań bramy | Liczba milisekund od momentu odebrania żądania w bramie do momentu pełnego wysłania odpowiedzi |
| Czas trwania żądań wewnętrznej bazy danych | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy zaplecza (łączenie, wysyłanie i odbieranie bajtów)  |

## <a name="logs"></a>Dzienniki

Brama samoobsługowa obecnie nie wysyła [dzienników diagnostycznych](./api-management-howto-use-azure-monitor.md#activity-logs) do chmury. Istnieje jednak możliwość [skonfigurowania i utrwalenia dzienników lokalnie](how-to-configure-local-metrics-logs.md) , w których wdrożono bramę samohostowaną. 

Jeśli Brama jest wdrożona w [usłudze Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/), można włączyć funkcję [Azure monitor dla kontenerów](../azure-monitor/containers/container-insights-overview.md) w celu zbierania dzienników z kontenerów i wyświetlania ich w log Analytics. 


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Informacje na temat [konfigurowania i utrwalania dzienników lokalnie](how-to-configure-local-metrics-logs.md)
