---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — łączenie chmury prywatnej z siecią platformy Azure przy użyciu usługi ExpressRoute
description: Opis sposobu łączenia środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895992"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Łączenie środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute

Chmurę prywatną CloudSimple można połączyć z siecią wirtualną platformy Azure za pomocą usługi Azure ExpressRoute.  Ta wysoka przepustowość, połączenie o małym opóźnieniu pozwala uzyskiwać dostęp do usług uruchomionych w ramach subskrypcji platformy Azure w środowisku chmury prywatnej.

Połączenie sieci wirtualnej umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych sieci vSAN w chmurze prywatnej.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Konfigurowanie połączenia sieci wirtualnej

Aby skonfigurować połączenie sieci wirtualnej z chmurą prywatną, potrzebujesz klucza autoryzacji, identyfikatora URI obwodu równorzędnego i dostępu do subskrypcji platformy Azure. Te informacje są dostępne na stronie połączenie Virtual Network w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Uzyskiwanie informacji o komunikacji równorzędnej dla usługi Azure Virtual Network do połączenia CloudSimple](virtual-network-connection.md). Jeśli masz problemy z uzyskaniem informacji, Prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">żądanie pomocy technicznej</a>.

> [!TIP]
> Jeśli masz już sieć wirtualną platformy Azure, podsieć bramy i bramę sieci wirtualnej, możesz przejść do kroku 4.

1. Utwórz sieć wirtualną w ramach subskrypcji platformy Azure i sprawdź, czy wybrana przestrzeń adresowa różni się od przestrzeni adresowej chmury prywatnej.  Jeśli masz już sieć wirtualną platformy Azure, możesz użyć istniejącej.  Aby uzyskać szczegółowe informacje, zobacz [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).
2. Utwórz podsieć bramy w sieci wirtualnej platformy Azure.  Jeśli masz już podsieć bramy w sieci wirtualnej platformy Azure, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie podsieci bramy](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Utwórz bramę sieci wirtualnej w sieci wirtualnej.  Jeśli masz istniejącą bramę sieci wirtualnej, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie bramy sieci wirtualnej](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Utwórz połączenie między siecią wirtualną i chmurą prywatną przez zrealizowanie klucza autoryzacji zgodnie z opisem w temacie [łączenie sieci wirtualnej z subskrypcją innego obwodu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Jeśli używasz istniejącej bramy sieci wirtualnej i ma połączenie ExpressRoute z tą samą lokalizacją co obwód ExpressRoute CloudSimple, połączenie nie zostanie nawiązane.  Utwórz nową sieć wirtualną i wykonaj kroki opisane w poprzednich krokach.

## <a name="test-the-virtual-network-connection"></a>Testowanie połączenia sieci wirtualnej

Po utworzeniu połączenia można sprawdzić stan połączenia, wybierając pozycję **Właściwości** w obszarze **Ustawienia**.  Stan stanu aprowizacji powinien wskazywać na **powodzenie**.

![Stan połączenia](media/azure-expressroute-connection.png)

Aby przetestować połączenie sieci wirtualnej:

1. Utwórz maszynę wirtualną w ramach subskrypcji platformy Azure.
2. Znajdź adres IP programu vCenter w chmurze prywatnej (Zobacz Twój powitalny adres e-mail).
3. Wyślij polecenie ping do usługi Cloud vCenter z maszyny wirtualnej utworzonej w sieci wirtualnej platformy Azure.
4. Wyślij polecenie ping do maszyny wirtualnej platformy Azure z maszyny wirtualnej działającej w programie vCenter w chmurze prywatnej.

Jeśli masz problemy z nawiązywaniem połączenia, Prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">żądanie pomocy technicznej</a>.
