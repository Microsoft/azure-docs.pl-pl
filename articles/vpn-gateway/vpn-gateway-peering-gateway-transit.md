---
title: Konfigurowanie tranzytu bramy sieci VPN na potrzeby wirtualnych sieci równorzędnych
description: Skonfiguruj tranzyt bramy dla komunikacji równorzędnej sieci wirtualnej, aby bezproblemowo połączyć dwie sieci wirtualne platformy Azure w jeden z celów łączności.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872183"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurowanie tranzytu bramy sieci VPN na potrzeby wirtualnych sieci równorzędnych

Ten artykuł pomaga skonfigurować tranzyt bramy na potrzeby wirtualnych sieci równorzędnych. [Wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md) łączą bezproblemowo dwie sieci wirtualne platformy Azure, scalając je w jedną na potrzeby łączności. [Tranzyt bramy](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) to właściwość komunikacji równorzędnej, która umożliwia jednej sieci wirtualnej korzystanie z bramy sieci VPN w równorzędnej sieci wirtualnej w przypadku połączeń między różnymi lokalizacjami lub z siecią wirtualną. Na poniższym diagramie przedstawiono sposób działania tranzytu bramy w wirtualnych sieciach równorzędnych.

![Diagram przesyłania bramy](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Na diagramie tranzyt bramy umożliwia równorzędnym sieciom wirtualnym użycie bramy sieci VPN platformy Azure w centralnej sieci wirtualnej korzystającej z modelu usługi RM (Hub-RM). Połączenia dostępne dla bramy sieci VPN, w tym połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi, mają zastosowanie we wszystkich trzech sieciach wirtualnych. Opcja tranzyt jest dostępna w przypadku komunikacji równorzędnej między tymi samymi lub różnymi modelami wdrażania. W przypadku konfigurowania przesyłania między różnymi modelami wdrażania Sieć wirtualna centrum i Brama sieci wirtualnej muszą znajdować się w Menedżer zasobów modelu wdrażania, a nie w klasycznym modelu wdrażania.
>

W architekturze sieciowej typu gwiazdy tranzyt bramy umożliwia ułożonym promieniście sieciom wirtualnym współużytkowanie bramy sieci VPN znajdującej się w centrum, zamiast wdrażać bramy sieci VPN w każdej promienistej sieci wirtualnej. Trasy do połączonych za pośrednictwem bramy sieci wirtualnych lub sieci lokalnych będą propagowane do tabel routingu wirtualnych sieci równorzędnych za pomocą tranzytu bramy. Automatyczne propagowanie tras z bramy sieci VPN można wyłączyć. Utwórz tabelę routingu za pomocą opcji „**Wyłącz propagację tras BGP**” i skojarz tabelę routingu z podsieciami, aby zapobiec dystrybucji tras do tych podsieci. Aby uzyskać więcej informacji, zobacz [Virtual network routing table](../virtual-network/manage-route-table.md) (Tabela routingu sieci wirtualnej).

W tym artykule istnieją dwa scenariusze:

* **Ten sam model wdrażania**: obie sieci wirtualne są tworzone w modelu wdrażania Menedżer zasobów.
* **Różne modele wdrażania**: Sieć wirtualna szprych jest tworzona w klasycznym modelu wdrażania, a sieć wirtualna centrum i Brama znajdują się w Menedżer zasobów model wdrażania.

>[!NOTE]
> Jeśli wprowadzisz zmiany w topologii sieci i masz klientów sieci VPN z systemem Windows, pakiet klienta VPN dla klientów systemu Windows musi zostać pobrany i zainstalowany ponownie, aby zmiany zostały zastosowane do klienta programu.
>

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące sieci wirtualne i uprawnienia:

### <a name="virtual-networks"></a><a name="vnet"></a>Sieci wirtualne

|Sieć wirtualna|Model wdrażania| Brama sieci wirtualnej|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Tak](tutorial-create-gateway-portal.md)|
| Szprych-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| Nie |
| Spoke-Classic | [Motyw](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Nie |

### <a name="permissions"></a><a name="permissions"></a>Uprawnienia

Konta używane do tworzenia wirtualnych sieci równorzędnych muszą mieć niezbędne role lub uprawnienia. W poniższym przykładzie, jeśli Komunikacja równorzędna była dwie sieci wirtualne o nazwach **Hub-RM** i **szprych-klasyczny**, Twoje konto musi mieć następujące role lub uprawnienia dla każdej sieci wirtualnej:

|Sieć wirtualna|Model wdrażania|Rola|Uprawnienia|
|---|---|---|---|
|Hub-RM|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasyczny|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Nie dotyczy|
|Spoke-Classic|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasyczny|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) i przypisywania określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (tyko usługa Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Ten sam model wdrażania

W tym scenariuszu sieci wirtualne znajdują się w modelu wdrażania Menedżer zasobów. Wykonaj następujące kroki, aby utworzyć lub zaktualizować komunikację równorzędną sieci wirtualnych w celu włączenia przesyłania bramy.

### <a name="to-add-a-peering-and-enable-transit"></a>Aby dodać komunikację równorzędną i włączyć przesyłanie

1. W [Azure Portal](https://portal.azure.com)Utwórz lub zaktualizuj komunikację równorzędną sieci wirtualnej z centrum-RM. Przejdź do sieci wirtualnej **Hub-RM** . Wybierz pozycję **Komunikacja równorzędna**, a następnie pozycję **+ Dodaj** , aby otworzyć aplet **Dodaj komunikację równorzędną**.
1. Na stronie **Dodawanie komunikacji równorzędnej** Skonfiguruj wartości dla **tej sieci wirtualnej**.

   * Nazwa łącza komunikacji równorzędnej: nadaj nazwę linku. Przykład: **HubRMToSpokeRM**
   * Ruch do zdalnej sieci wirtualnej: **Zezwalaj**
   * Ruch przekierowany z zdalnej sieci wirtualnej: **Zezwalaj**
   * Brama sieci wirtualnej: **Użyj bramy tej sieci wirtualnej**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Zrzut ekranu przedstawia Dodawanie komunikacji równorzędnej.":::

1. Na tej samej stronie Kontynuuj, aby skonfigurować wartości dla **zdalnej sieci wirtualnej**.

   * Nazwa łącza komunikacji równorzędnej: nadaj nazwę linku. Przykład: **SpokeRMtoHubRM**
   * Model wdrażania: **Menedżer zasobów**
   * Virtual Network: **szprych-RM**
   * Ruch do zdalnej sieci wirtualnej: **Zezwalaj**
   * Ruch przekierowany z zdalnej sieci wirtualnej: **Zezwalaj**
   * Brama sieci wirtualnej: **Użyj bramy zdalnej sieci wirtualnej**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Zrzut ekranu przedstawia wartości dla zdalnej sieci wirtualnej.":::

1. Wybierz pozycję **Dodaj** , aby utworzyć komunikację równorzędną.
1. Sprawdź stan komunikacji równorzędnej jako **połączony** w obu sieciach wirtualnych.

### <a name="to-modify-an-existing-peering-for-transit"></a>Aby zmodyfikować istniejącą komunikację równorzędną do tranzytu

Jeśli Komunikacja równorzędna została już utworzona, można zmodyfikować komunikację równorzędną na potrzeby przesyłania.

1. Przejdź do sieci wirtualnej. Wybierz pozycję **Komunikacja równorzędna** i wybierz komunikację równorzędną, którą chcesz zmodyfikować.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Zrzut ekranu przedstawia Wybieranie komunikacji równorzędnej.":::

1. Aktualizowanie komunikacji równorzędnej sieci wirtualnej.

   * Ruch do zdalnej sieci wirtualnej: **Zezwalaj**
   * Ruch przesyłany dalej do sieci wirtualnej; **Zezwalaj**
   * Brama sieci wirtualnej: **Użyj bramy zdalnej sieci wirtualnej**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Zrzut ekranu przedstawia modyfikowanie bramy komunikacji równorzędnej.":::

1. **Zapisz** ustawienia komunikacji równorzędnej.

### <a name="powershell-sample"></a><a name="ps-same"></a>Przykładowy skrypt programu PowerShell

Do utworzenia lub zaktualizowania komunikacji równorzędnej przedstawionej na powyższym przykładzie można również użyć programu PowerShell. Zastąp zmienne nazwami swoich sieci wirtualnych i grup zasobów.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>Różne modele wdrażania

W tej konfiguracji Sieć wirtualna szprych **-klasyczna** jest w klasycznym modelu wdrażania, a koncentrator koncentratora sieci wirtualnej **— RM** jest w Menedżer zasobów model wdrażania. Podczas konfigurowania przesyłania między modelami wdrażania należy skonfigurować bramę sieci wirtualnej dla Menedżer zasobów sieci wirtualnej, a nie do klasycznego sieci wirtualnej.

W przypadku tej konfiguracji wystarczy skonfigurować sieć wirtualną **Hub-RM** . Nie trzeba konfigurować żadnych elementów w sieci wirtualnej **szprych-klasyczny** .

1. W Azure Portal przejdź do sieci wirtualnej **Hub-RM** , wybierz pozycję **Komunikacja równorzędna**, a następnie wybierz pozycję **+ Dodaj**.
1. Na stronie **Dodawanie komunikacji równorzędnej** skonfiguruj następujące wartości:

   * Nazwa łącza komunikacji równorzędnej: nadaj nazwę linku. Przykład: **HubRMToClassic**
   * Ruch do zdalnej sieci wirtualnej: **Zezwalaj**
   * Ruch przekierowany z zdalnej sieci wirtualnej: **Zezwalaj**
   * Brama sieci wirtualnej: **Użyj bramy tej sieci wirtualnej**
   * Zdalna Sieć wirtualna: **klasyczny**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Dodaj stronę komunikacji równorzędnej dla satelity — klasyczny":::

1. Sprawdź, czy subskrypcja jest poprawna, a następnie wybierz sieć wirtualną z listy rozwijanej.
1. Wybierz pozycję **Dodaj** , aby dodać komunikację równorzędną.
1. Sprawdź stan komunikacji równorzędnej jako **połączony** w sieci wirtualnej Hub-RM. 

W przypadku tej konfiguracji nie trzeba konfigurować żadnych elementów w sieci wirtualnej **szprych-klasyczny** . Po wyświetleniu **połączenia** stan sieci wirtualnej szprychy może korzystać z łączności za pośrednictwem bramy sieci VPN w sieci wirtualnej centrum.

### <a name="powershell-sample"></a><a name="ps-different"></a>Przykładowy skrypt programu PowerShell

Do utworzenia lub zaktualizowania komunikacji równorzędnej przedstawionej na powyższym przykładzie można również użyć programu PowerShell. Zastąp zmienne i identyfikator subskrypcji wartościami Twojej sieci wirtualnej, grup zasobów oraz subskrypcji. Komunikację równorzędną sieci wirtualnej należy utworzyć tylko w centralnej sieci wirtualnej.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ograniczeniach i zachowaniu komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) i [ustawieniach komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#create-a-peering) przed utworzeniem komunikacji równorzędnej sieci wirtualnej w środowisku produkcyjnym.
* Dowiedz się, jak [utworzyć topologię sieciową gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną sieci wirtualnej i tranzytem bramy.
* [Utwórz sieć wirtualną sieci równorzędnej przy użyciu tego samego modelu wdrażania](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Tworzenie komunikacji równorzędnej sieci wirtualnej z różnymi modelami wdrażania](../virtual-network/create-peering-different-deployment-models.md).