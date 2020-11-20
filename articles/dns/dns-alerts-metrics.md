---
title: Metryki i alerty — Azure DNS
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z Azure DNS metrykami i alertami.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 85b681cbeb1b06784529e321c9e907ab2bdecb73
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954651"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS metryki i alerty
Azure DNS to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. W tym artykule opisano metryki i alerty dla usługi Azure DNS.

## <a name="azure-dns-metrics"></a>Metryki Azure DNS

Azure DNS udostępnia metryki dla klientów umożliwiające monitorowanie określonych aspektów stref DNS hostowanych w usłudze. Ponadto za pomocą metryk Azure DNS można konfigurować i odbierać alerty na podstawie warunków zainteresowania. Metryki są udostępniane za pośrednictwem [usługi Azure monitor](../azure-monitor/index.yml). Azure DNS udostępnia następujące metryki za pośrednictwem Azure Monitor dla stref DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

[Definicję tych metryk](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) można również znaleźć na stronie dokumentacji Azure monitor.
>[!NOTE]
> W tej chwili te metryki są dostępne tylko dla publicznych stref DNS hostowanych w Azure DNS. Jeśli masz strefy prywatne hostowane w Azure DNS, te metryki nie będą dostarczać danych dla tych stref. Ponadto funkcja metryk i alertów jest obsługiwana tylko w chmurze publicznej platformy Azure. Obsługa dla suwerennych chmur będzie występować w późniejszym czasie. 

Najbardziej szczegółowym elementem, dla którego można zobaczyć metryki, jest strefa DNS. Obecnie nie są wyświetlane metryki dla poszczególnych rekordów zasobów w obrębie strefy.

### <a name="query-volume"></a>Wolumin zapytania

Metryka *woluminu zapytania* w Azure DNS pokazuje ilość zapytań DNS (ruch zapytań) odbieranych przez Azure DNS dla strefy DNS. Jednostka miary jest liczbą, a agregacja jest sumą wszystkich zapytań odebranych w danym okresie czasu. 

Aby wyświetlić tę metrykę, wybierz pozycję metryki (wersja zapoznawcza) środowisko Eksploratora na karcie monitorowanie w Azure Portal. Wybierz strefę DNS z listy rozwijanej zasób, wybierz metrykę woluminu zapytania, a następnie wybierz pozycję suma jako agregacja. Poniższy zrzut ekranu przedstawia przykład.  Aby uzyskać więcej informacji na temat środowiska Eksplorator metryk i wykresów, zobacz [Azure Monitor Eksplorator metryk](../azure-monitor/platform/metrics-charts.md).

![Wolumin zapytania](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Ilustracja: metryki woluminów zapytań Azure DNS*

### <a name="record-set-count"></a>Liczba zestawów rekordów
Metryka *liczby zestawów rekordów* przedstawia liczbę zestawów rekordów w Azure DNS dla strefy DNS. Zliczane są wszystkie zestawy rekordów zdefiniowane w strefie. Jednostka miary jest liczbą i agregacją jest maksimum wszystkich zestawów rekordów. Aby wyświetlić tę metrykę, wybierz pozycję **metryki (wersja zapoznawcza)** środowisko Eksploratora na karcie **monitorowanie** w Azure Portal. Wybierz strefę DNS z listy rozwijanej **zasób** , wybierz metrykę **zliczania zestawu rekordów** , a następnie wybierz **wartość Max** jako **agregację**. Aby uzyskać więcej informacji na temat środowiska Eksplorator metryk i wykresów, zobacz [Azure Monitor Eksplorator metryk](../azure-monitor/platform/metrics-charts.md). 

![Liczba zestawów rekordów](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Ilustracja: metryki liczby zestawów rekordów Azure DNS*


### <a name="record-set-capacity-utilization"></a>Wykorzystanie pojemności zestawu rekordów
Metryka *wykorzystania pojemności zestawu rekordów* w Azure DNS pokazuje procent wykorzystania pojemności zestawu rekordów dla strefy DNS. Każda strefa DNS w Azure DNS podlega limitowi rekordów, który definiuje maksymalną liczbę zestawów rekordów, które są dozwolone dla strefy (zobacz [limity DNS](dns-zones-records.md#limits)). W związku z tym ta Metryka pokazuje, jak zamykasz limit zestawu rekordów. Na przykład jeśli masz zestawy rekordów 500 skonfigurowane dla strefy DNS, a strefa ma domyślny limit rekordów równy 5000, Metryka RecordSetCapacityUtilization będzie zawierać wartość 10% (uzyskaną przez podzielenie 500 przez 5000). Jednostka miary jest **wartością procentową** , a typ **agregacji** to **maksimum**. Aby wyświetlić tę metrykę, wybierz pozycję metryki (wersja zapoznawcza) środowisko Eksploratora na karcie monitorowanie w Azure Portal. Wybierz strefę DNS z listy rozwijanej zasób, wybierz pozycję zestaw rekordów Metryka wykorzystania pojemności i wybierz wartość maksymalna jako agregację. Poniższy zrzut ekranu przedstawia przykład. Aby uzyskać więcej informacji na temat środowiska Eksplorator metryk i wykresów, zobacz [Azure Monitor Eksplorator metryk](../azure-monitor/platform/metrics-charts.md). 

![Zrzut ekranu, który whows przykład sposobu wyświetlania metryk.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Ilustracja: Ustawianie rekordu Azure DNS metryk wykorzystania pojemności*

## <a name="alerts-in-azure-dns"></a>Alerty w Azure DNS
Azure Monitor oferuje możliwość zgłaszania alertów dotyczących dostępnych wartości metryk. Metryki DNS są dostępne w nowym środowisku konfiguracji alertów. Jak opisano szczegółowo w [dokumentacji Azure monitor alertów](../azure-monitor/platform/alerts-metric.md), można wybrać strefy DNS jako zasób, wybrać typ sygnału metryki i skonfigurować logikę alertów oraz inne parametry, takie jak **okres** i **częstotliwość**. Można dodatkowo zdefiniować [grupę akcji](../azure-monitor/platform/action-groups.md) dla momentu spełnienia warunku alertu, zgodnie z którym alert zostanie dostarczony za pośrednictwem wybranych akcji. Aby uzyskać więcej informacji na temat konfigurowania alertów dotyczących metryk Azure Monitor, zobacz [Tworzenie i wyświetlanie alertów oraz zarządzanie nimi przy użyciu Azure monitor](../azure-monitor/platform/alerts-metric.md). 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Azure DNS](dns-overview.md).