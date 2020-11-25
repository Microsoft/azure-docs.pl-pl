---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010866"
---
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostkę SKU spełniającą Twoje wymagania na podstawie typów obciążeń, przepustowości, funkcji i umów SLA. W przypadku jednostek SKU bramy sieci wirtualnej w Strefy dostępności platformy Azure zobacz [jednostki SKU bramy strefy dostępności platformy Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Jednostki SKU bramy według tunelowania, połączenia i przepływności

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> Jednostki SKU VpnGw (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 i VpnGw5AZ) są obsługiwane tylko w przypadku modelu wdrażania Menedżer zasobów. Klasyczne sieci wirtualne powinny nadal używać starych (starszych) jednostek SKU.
>  * Aby uzyskać informacje na temat pracy ze starszymi jednostkami SKU bramy (Basic, standard i HighPerformance), zobacz [Praca z jednostkami SKU bramy sieci VPN (starsze jednostki SKU)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * W przypadku jednostek SKU bramy usługi ExpressRoute należy zapoznać się z tematem [Virtual Network Gateways for ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Jednostki SKU bramy według zestawu funkcji

Nowe jednostki SKU bramy sieci VPN usprawniają zestawy funkcji oferowane przez bramy:

| **SKU**| **Funkcje**|
| ---    | ---         |
|**Podstawowa** (* *)   | **Sieć VPN oparta na trasach**: 10 tuneli dla połączeń S2S/Connections; Brak uwierzytelniania usługi RADIUS dla P2S; Brak protokołu IKEv2 dla P2S<br>**Sieć VPN oparta na zasadach**: (IKEv1): 1 tunel S2S/połączenie; Brak P2S|
| **Wszystkie jednostki SKU Generation1 i Generation2 z wyjątkiem warstwy Podstawowa** | **Sieć VPN oparta na trasach**: do 30 tuneli (*), P2S, BGP, aktywne-aktywne, niestandardowe zasady protokołu IPSec/IKE, współistnienie EXPRESSROUTE/VPN |
|        |             |

(*) Można skonfigurować "PolicyBasedTrafficSelectors" do łączenia bramy sieci VPN opartej na trasach z wieloma lokalnymi urządzeniami zapory opartymi na zasadach. Aby zapoznać się ze szczegółami, zobacz artykuł [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Połączenie bram sieci VPN z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell).

( \* \* ) Podstawowa jednostka SKU jest uznawana za starszą wersję SKU. Podstawowa jednostka SKU ma pewne ograniczenia w zakresie funkcji. Rozmiaru bramy, która korzysta z podstawowej jednostki SKU, nie można zmienić na jedną z nowych jednostek SKU bramy. Zamiast tego należy przejść na nową jednostkę SKU, co wiąże się z koniecznością usunięcia i ponownego utworzenia bramy sieci VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Jednostki SKU bramy — obciążenia produkcyjne a Dev-Test

Z powodu różnic w umowach SLA i zestawach funkcji zaleca się następujące jednostki SKU dla trybu produkcyjnego i w środowisku tworzenia i testowania:

| **Obciążenie**                       | **Jednostki SKU**               |
| ---                                | ---                    |
| **Tryb produkcyjny, obciążenia krytyczne** | Wszystkie jednostki SKU Generation1 i Generation2 z wyjątkiem warstwy Podstawowa |
| **Środowisko tworzenia i testowania lub weryfikacja koncepcji**   | Podstawowa (* *)                 |
|                                    |                        |

( \* \* ) Podstawowa jednostka SKU jest uznawana za starszą wersję SKU i ma ograniczenia dotyczące funkcji. Zanim użyjesz podstawowej jednostki SKU sprawdź, czy potrzebna Ci funkcja jest obsługiwana.

W przypadku używania starych jednostek SKU (starsza wersja) zalecenia dotyczące produkcyjnej jednostki SKU są standardowe i HighPerformance. Aby uzyskać informacje i instrukcje dotyczące starych jednostek SKU, zobacz [jednostki SKU bramy (starsza wersja)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
