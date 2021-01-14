---
title: Metryki i alerty w usłudze Azure Traffic Manager
description: W tym artykule przedstawiono metryki i alerty dostępne dla Traffic Manager na platformie Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: bd3588c207fe8828744a665539830d81805bd7b3
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184275"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager metryki i alerty

Traffic Manager zapewnia oparte na systemie DNS Równoważenie obciążenia, które obejmuje wiele metod routingu i opcji monitorowania punktów końcowych. W tym artykule opisano metryki i powiązane alerty, które są dostępne dla klientów. 

## <a name="metrics-available-in-traffic-manager"></a>Metryki dostępne w Traffic Manager 

Traffic Manager udostępnia następujące metryki dla poszczególnych profilów, których klienci mogą używać do zrozumienia użycia Menedżera ruchu i stanu punktów końcowych w ramach tego profilu.  

### <a name="queries-by-endpoint-returned"></a>Zwrócone zapytania według punktu końcowego
Użyj [tej metryki](../azure-monitor/platform/metrics-supported.md) , aby wyświetlić liczbę zapytań przetwarzanych przez profil Traffic Manager w określonym przedziale czasu. Możesz również wyświetlić te same informacje na poziomie szczegółowości poziomu punktu końcowego, który pomaga zrozumieć, ile razy punkt końcowy został zwrócony w odpowiedzi na zapytanie z Traffic Manager.

W poniższym przykładzie rysunek 1 wyświetla wszystkie odpowiedzi na zapytania, które zwraca profil Traffic Manager. 

  
![Zagregowany widok wszystkich zapytań](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Rysunek 1: widok zagregowany ze wszystkimi zapytaniami*
  
Rysunek 2 wyświetla te same informacje, jednak jest on dzielony przez punkty końcowe. W efekcie można zobaczyć ilość odpowiedzi na zapytania, w których został zwrócony określony punkt końcowy.

![Metryki Traffic Manager — podzielony widok woluminu zapytania na punkt końcowy](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Rysunek 2: widok podzielony z pokazanym woluminem zapytania na punkt końcowy*

## <a name="endpoint-status-by-endpoint"></a>Stan punktu końcowego według punktu końcowego
Użyj [tej metryki](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) , aby poznać stan kondycji punktów końcowych w profilu. Przyjmuje dwie wartości:
 - Użyj **1** , jeśli punkt końcowy działa.
 - Jeśli punkt końcowy nie działa, należy użyć wartości **0** .

Ta Metryka może być pokazywana jako wartość zagregowana reprezentująca stan wszystkich metryk (rysunek 3) lub można ją podzielić (zobacz rysunek 4), aby wyświetlić stan określonych punktów końcowych. Jeśli jest to pierwsze, jeśli poziom agregacji został wybrany jako **Średni**, wartość tej metryki jest średnią arytmetyczną stanu wszystkich punktów końcowych. Na przykład jeśli profil ma dwa punkty końcowe i tylko jeden z nich jest w dobrej kondycji, ta Metryka ma wartość **0,50** , jak pokazano na rysunku 3. 


![Metryki Traffic Manager — Widok złożony stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Rysunek 3: Widok złożony metryki stanu punktu końcowego — wybrano agregację "Średnia"*


![Metryki Traffic Manager — podzielony widok stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Ilustracja 4. widok podzielony metryk stanu punktu końcowego*

Te metryki można wykorzystać za pomocą portalu [usługi Azure monitor](../azure-monitor/platform/metrics-supported.md), [interfejsu API REST](/rest/api/monitor/), [wiersza polecenia platformy Azure](/cli/azure/monitor)i [Azure PowerShell](/powershell/module/az.applicationinsights), a także za pomocą sekcji metryki środowiska w portalu Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Alerty dotyczące metryk Traffic Manager
Oprócz przetwarzania i wyświetlania metryk z Traffic Manager, Azure Monitor umożliwia klientom Konfigurowanie i odbieranie alertów skojarzonych z tymi metrykami. Możesz wybrać warunki, które muszą zostać spełnione w tych metrykach, aby alert wystąpił, jak często te warunki muszą być monitorowane i jak powinny być wysyłane do Ciebie alerty. Aby uzyskać więcej informacji, zobacz [Azure monitor dokumentacja alertów](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [usługi Azure monitor](../azure-monitor/platform/metrics-supported.md)
- Dowiedz się, jak [utworzyć wykres przy użyciu Azure monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)