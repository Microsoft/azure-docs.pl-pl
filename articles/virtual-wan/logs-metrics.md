---
title: Dzienniki i metryki
titleSuffix: Azure Virtual WAN
description: Informacje o dziennikach i metrykach wirtualnych sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 78165e9c14d4a83dbc20cbccd2f31dc8ac4c79ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440864"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Dzienniki i metryki wirtualnych sieci WAN platformy Azure

Wirtualne sieci WAN platformy Azure można monitorować przy użyciu Azure Monitor. Wirtualna sieć WAN jest usługą sieciową, która oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. Aby uzyskać informacje o zaporze platformy Azure, zobacz [dzienniki i metryki zapory platformy Azure](../firewall/logs-and-metrics.md).

W tym artykule omówiono metryki i diagnostykę, które są dostępne w portalu. Metryki są lekkie i obsługują scenariusze niemal w czasie rzeczywistym, dzięki czemu mogą być używane do tworzenia alertów i szybkiego wykrywania problemów.

## <a name="metrics"></a>Metryki

Metryki w Azure Monitor to wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Metryki są zbierane co minutę i są przydatne do zgłaszania alertów, ponieważ mogą być często próbkowane. Alert może być uruchamiany szybko z stosunkowo prostą logiką.

### <a name="site-to-site-vpn-gateways"></a>Bramy sieci VPN typu lokacja-lokacja

Następujące metryki są dostępne dla bram sieci VPN typu lokacja-lokacja usługi Azure:

* **Przepustowość bramy** — średnia przepustowość bramy między lokacjami w bajtach na sekundę.
* **Przepustowość tunelu** — średnia przepustowość tunelu w bajtach na sekundę.
* **Bajty wyjściowe tunelu** — wychodzące bajty tunelu. 
* Pakiety wychodzące **tunelu** — liczba pakietów wychodzących tunelu. 
* **Porzucanie pakietów** wychodzących przez tunelowanie — liczba odrzuceń pakietów wychodzących z niezgodności selektora ruchu tunelu. 
* **Bajty transferu danych wejściowych tunelu** — przychodzące bajty tunelu. 
* **Pakiet tunelowania** ruchu przychodzącego — liczba pakietów przychodzących tunelu. 
* **Porzucanie niezgodnych pakietów przez tunelowanie** w protokole TS — liczba odrzuceń pakietów przychodzących z niezgodności selektora ruchu tunelu. 

### <a name="point-to-site-vpn-gateways"></a>Bramy sieci VPN typu punkt-lokacja

Następujące metryki są dostępne dla bram sieci VPN typu punkt-lokacja usługi Azure:

* **Przepustowość P2S bramy** — średnia przepustowość pasma punkt-lokacja bramy w bajtach na sekundę.
* **Liczba połączeń P2S** — liczba połączeń punkt-lokacja bramy.

### <a name="azure-expressroute-gateways"></a>Bramy usługi Azure ExpressRoute

Następujące metryki są dostępne dla bram usługi Azure ExpressRoute:

* **BitsInPerSecond** — usługa BITS na sekundę na platformie Azure.
* **BitsOutPerSecond** — ruch przychodzący z platformy Azure w bitach na sekundę.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Wyświetl metryki bramy

Poniższe kroki ułatwiają lokalizowanie i wyświetlanie metryk:

1. W portalu przejdź do koncentratora wirtualnego, który ma bramę.

2. Wybierz pozycję **VPN (lokacja** -lokacja), aby zlokalizować bramę lokacja-lokacja, **ExpressRoute** lokalizację bramy ExpressRoute lub **Sieć VPN użytkownika (wskaż lokację)** , aby zlokalizować bramę typu punkt-lokacja. Na stronie można zobaczyć informacje o bramie. Skopiuj te informacje. Będzie on używany później do wyświetlania diagnostyki przy użyciu Azure Monitor.

3. Wybierz pozycję **Metryki**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Zrzut ekranu przedstawia okienko P N lokacji z zaznaczoną opcją widok w Azure Monitor.":::

4. Na stronie **metryki** możesz wyświetlić interesujące Cię metryki.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Zrzut ekranu przedstawia okienko P N lokacji z zaznaczoną opcją widok w Azure Monitor.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Dzienniki diagnostyczne

### <a name="site-to-site-vpn-gateways"></a>Bramy sieci VPN typu lokacja-lokacja

Następujące diagnostyki są dostępne dla bram sieci VPN typu lokacja-lokacja usługi Azure:

* **Dzienniki diagnostyczne bramy** — Diagnostyka specyficzna dla bramy, taka jak kondycja, konfiguracja, aktualizacje usługi, a także dodatkowa Diagnostyka.
* **Dzienniki diagnostyczne tunelu** — są to dzienniki dotyczące tunelu IPSec, takie jak zdarzenia łączenia i rozłączania dla tunelu IPsec lokacja-lokacja, negocjowane sygnatury dostępu współdzielonego, przyczyny rozłączenia, a także dodatkowe funkcje diagnostyczne.
* **Kierowanie dzienników diagnostycznych** — są to dzienniki związane ze zdarzeniami dla tras statycznych, protokołu BGP, aktualizacji trasy, a także dodatkowej diagnostyki.
* **Dzienniki diagnostyczne IKE** — Diagnostyka specyficzna dla usługi IKE dla połączeń IPsec.

### <a name="point-to-site-vpn-gateways"></a>Bramy sieci VPN typu punkt-lokacja

Następujące diagnostyki są dostępne dla bram sieci VPN typu punkt-lokacja usługi Azure:

* **Dzienniki diagnostyczne bramy** — Diagnostyka specyficzna dla bramy, taka jak kondycja, konfiguracja, aktualizacje usługi, a także inne funkcje diagnostyczne.
* **Dzienniki diagnostyczne IKE** — Diagnostyka specyficzna dla usługi IKE dla połączeń IPsec.
* **Dzienniki diagnostyczne P2S** — są to sieci VPN użytkownika (punkt-lokacja) P2S konfiguracji i zdarzeń klienta. Obejmują one łączenie/rozłączanie klientów, przydzielanie adresów klienta sieci VPN, a także inne funkcje diagnostyczne.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Wyświetlanie dzienników diagnostycznych

Poniższe kroki ułatwiają znalezienie i wyświetlenie diagnostyki:

1. W portalu przejdź do wirtualnego zasobu sieci WAN. W sekcji **Omówienie** wirtualnej sieci WAN w portalu wybierz pozycję **Essentials** , aby rozwinąć widok i uzyskać informacje o grupie zasobów. Skopiuj informacje o grupie zasobów.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Zrzut ekranu przedstawia okienko P N lokacji z zaznaczoną opcją widok w Azure Monitor.":::

2. W sekcji monitorowanie przejdź do grupy zasobów. Wybierz pozycję **Ustawienia diagnostyczne**, a następnie wprowadź informacje o zasobach. To są informacje o zasobie skopiowane w kroku 2 z sekcji [Wyświetl metryki bramy](#metrics-steps) wcześniej w tym artykule.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Zrzut ekranu przedstawia okienko P N lokacji z zaznaczoną opcją widok w Azure Monitor.":::

3. Na stronie Wyniki wybierz pozycję **+ Dodaj ustawienie diagnostyczne**, a następnie wybierz opcję. Możesz zdecydować się na wysyłanie do Log Analytics, przesyłanie strumieniowe do centrum zdarzeń lub po prostu archiwizowanie na koncie magazynu.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="Zrzut ekranu przedstawia okienko P N lokacji z zaznaczoną opcją widok w Azure Monitor.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Przykładowe zapytanie Log Analytics

Dzienniki znajdują się w **obszarze roboczym usługi Azure log Analytics**. Zapytanie można skonfigurować w Log Analytics. Poniższy przykład zawiera zapytanie w celu uzyskania diagnostyki trasy lokacja-lokacja.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

**W razie**konieczności Zastąp wartości poniżej wartościami poniżej.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Dzienniki aktywności

Wpisy **dziennika aktywności** są zbierane domyślnie i mogą być wyświetlane w Azure Portal. Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć dzienników aktywności platformy Azure (znanych wcześniej jako *dzienników operacyjnych* i *dzienników inspekcji*).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak monitorować dzienniki i metryki zapory platformy Azure, zobacz [Samouczek: monitorowanie dzienników zapory platformy Azure](../firewall/tutorial-diagnostics.md).
* Aby dowiedzieć się więcej o metrykach w Azure Monitor, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
