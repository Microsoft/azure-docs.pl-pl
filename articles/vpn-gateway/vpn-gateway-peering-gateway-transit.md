---
title: Konfigurowanie tranzytu bramy sieci VPN na potrzeby wirtualnych sieci równorzędnych
description: Skonfiguruj tranzyt bramy dla komunikacji równorzędnej sieci wirtualnej, aby bezproblemowo połączyć dwie sieci wirtualne platformy Azure w jeden z celów łączności.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 3f90ccbc98dac957fcb9fb81cd24c7ae6c9ac664
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657554"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurowanie tranzytu bramy sieci VPN na potrzeby wirtualnych sieci równorzędnych

Ten artykuł pomaga skonfigurować tranzyt bramy na potrzeby wirtualnych sieci równorzędnych. [Wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md) łączą bezproblemowo dwie sieci wirtualne platformy Azure, scalając je w jedną na potrzeby łączności. [Tranzyt bramy](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) jest właściwością komunikacji równorzędnej, która umożliwia wykorzystanie bramy sieci VPN w równorzędnej sieci wirtualnej na potrzeby połączeń obejmujących wiele lokalizacji lub połączeń między sieciami wirtualnymi. Na poniższym diagramie przedstawiono sposób działania tranzytu bramy w wirtualnych sieciach równorzędnych.

![gateway-transit](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Na diagramie tranzyt bramy umożliwia równorzędnym sieciom wirtualnym użycie bramy sieci VPN platformy Azure w centralnej sieci wirtualnej korzystającej z modelu usługi RM (Hub-RM). Połączenia dostępne dla bramy sieci VPN, w tym połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi, mają zastosowanie we wszystkich trzech sieciach wirtualnych. Opcja tranzytu jest dostępna dla komunikacji równorzędnej między tymi samymi lub różnymi modelami wdrażania. Istnieje jedno ograniczenie: brama sieci VPN może być tylko w sieci wirtualnej korzystającej z modelu wdrażania usługi Resource Manager, jak pokazano na diagramie.

W architekturze sieciowej typu gwiazdy tranzyt bramy umożliwia ułożonym promieniście sieciom wirtualnym współużytkowanie bramy sieci VPN znajdującej się w centrum, zamiast wdrażać bramy sieci VPN w każdej promienistej sieci wirtualnej. Trasy do połączonych za pośrednictwem bramy sieci wirtualnych lub sieci lokalnych będą propagowane do tabel routingu wirtualnych sieci równorzędnych za pomocą tranzytu bramy. Automatyczne propagowanie tras z bramy sieci VPN można wyłączyć. Utwórz tabelę routingu za pomocą opcji „**Wyłącz propagację tras BGP**” i skojarz tabelę routingu z podsieciami, aby zapobiec dystrybucji tras do tych podsieci. Aby uzyskać więcej informacji, zobacz [Virtual network routing table](../virtual-network/manage-route-table.md) (Tabela routingu sieci wirtualnej).

W tym dokumencie opisano dwa scenariusze:

1. Obie sieci wirtualne korzystają z modelu wdrażania usługi Resource Manager
2. Promieniście ułożone sieci wirtualne korzystają z modelu klasycznego, a centralna sieć wirtualna z bramą używa modelu usługi Resource Manager


>[!NOTE]
> Jeśli wprowadzisz zmiany w topologii sieci i masz klientów sieci VPN z systemem Windows, pakiet klienta VPN dla klientów systemu Windows musi zostać pobrany i zainstalowany ponownie, aby zmiany zostały zastosowane do klienta programu.
>

## <a name="requirements"></a>Wymagania



Przykład przedstawiony w tym dokumencie wymaga utworzenia następujących zasobów:

1. Centralna sieć wirtualna korzystająca z modelu usługi RM (Hub-RM) z bramą sieci VPN
2. Promienista sieć wirtualna korzystająca z modelu usługi RM (Spoke-RM)
3. Promienista sieć wirtualna korzystająca z klasycznego modelu wdrażania (Spoke-Classic)
4. Konto, którego używasz, wymaga koniecznych ról i uprawnień. Szczegółowe informacje znajdują się w sekcji [Uprawnienia](#permissions) w tym artykule.

Instrukcje można znaleźć w następujących dokumentach:

1. [Create a VPN gateway in a virtual network](vpn-gateway-howto-site-to-site-resource-manager-portal.md) (Tworzenie bramy sieci VPN w sieci wirtualnej)
2. [Create virtual network peering with the same deployment model](../virtual-network/tutorial-connect-virtual-networks-portal.md) (Tworzenie wirtualnych sieci równorzędnych w tych samych modelu wdrażania)
3. [Create virtual network peering with different deployment models](../virtual-network/create-peering-different-deployment-models.md) (Tworzenie wirtualnych sieci równorzędnych w różnych modelach wdrażania)

## <a name="permissions"></a><a name="permissions"></a>Uprawnienia

Konta używane do tworzenia wirtualnych sieci równorzędnych muszą mieć niezbędne role lub uprawnienia. W poniższym przykładzie w celu nawiązania połączenia równorzędnego między dwiema sieciami wirtualnymi o nazwie Hub-RM i Spoke-Classic konto musi mieć następujące role lub uprawnienia dla każdej sieci wirtualnej:
    
|Sieć wirtualna|Model wdrażania|Rola|Uprawnienia|
|---|---|---|---|
|Hub-RM|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasyczny|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Nie dotyczy|
|Spoke-Classic|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasyczny|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) i przypisywania określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%252fazure%252fvirtual-network%252ftoc.json) (tyko usługa Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Komunikacja w sieciach równorzędnych w modelach usługi Resource Manager z tranzytem bramy

Postępuj zgodnie z instrukcjami, aby utworzyć lub zaktualizować wirtualne sieci równorzędne korzystające z tranzytu bramy.

1. Utwórz lub zaktualizuj komunikację równorzędną z sieci Spoke-RM do sieci Hub-RM przy użyciu witryny Azure Portal. Przejdź do zasobu sieci wirtualnej Spoke-RM i kliknij kolejno pozycje „Komunikacje równorzędne”, „Dodaj”:
    - Ustaw opcję „Resource Manager”.
    - Wybierz sieć wirtualną Hub-RM w odpowiedniej subskrypcji.
    - Upewnij się, że dla opcji „Zezwalaj na dostęp sieci wirtualnej” ustawiono wartość „Włączone”.
    - Ustaw opcję „**Użyj bram zdalnych**”.
    - Kliknij przycisk „OK”.

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Jeśli komunikacja równorzędna jest już utworzona, przejdź do tego zasobu komunikacji równorzędnej, a następnie włącz opcję „**Użyj bram zdalnych**” podobnie, jak pokazano na zrzucie ekranu w kroku (1).

3. W witrynie Azure Portal utwórz lub zaktualizuj wirtualną komunikację równorzędną z sieci Hub-RM do sieci Spoke-RM. Przejdź do zasobu sieci wirtualnej Hub-RM i kliknij kolejno pozycje „Komunikacje równorzędne”, „Dodaj”:
    - Ustaw opcję „Resource Manager”.
    - Upewnij się, że dla opcji „Zezwalaj na dostęp sieci wirtualnej” ustawiono wartość „Włączone”.
    - Wybierz sieć wirtualną „Spoke-RM” w odpowiedniej subskrypcji.
    - Ustaw opcję „**Zezwalaj na tranzyt bramy**”.
    - Kliknij przycisk „OK”.

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Jeśli komunikacja równorzędna jest już utworzona, przejdź do tego zasobu komunikacji równorzędnej, a następnie włącz opcję „**Zezwalaj na tranzyt bramy**” podobnie, jak pokazano na zrzucie ekranu w kroku (3).

5. Sprawdź, czy stan komunikacji równorzędnej w obu sieciach wirtualnych ma wartość „**Połączono**”.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

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

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Komunikacja w sieciach równorzędnych w modelu klasycznym i modelu usługi Resource Manager z tranzytem bramy

Te kroki są podobne do kroków w przykładzie z modelami usługi Resource Manager, z tym wyjątkiem, że operacje są wykonywane tylko na sieci wirtualnej o nazwie Hub-RM.

1. W witrynie Azure Portal utwórz lub zaktualizuj wirtualną komunikację równorzędną z sieci Hub-RM do sieci Spoke-RM. Przejdź do zasobu sieci wirtualnej Hub-RM i kliknij kolejno pozycje „Komunikacje równorzędne”, „Dodaj”:
   - Ustaw opcję „Wdrożenie klasyczne” dla modelu wdrażania sieci wirtualnej.
   - Wybierz sieć wirtualną „Spoke-Classic” w odpowiedniej subskrypcji.
   - Upewnij się, że dla opcji „Zezwalaj na dostęp sieci wirtualnej” ustawiono wartość „Włączone”.
   - Ustaw opcję „**Zezwalaj na tranzyt bramy**”.
   - Kliknij przycisk „OK”.

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Jeśli komunikacja równorzędna jest już utworzona, przejdź do tego zasobu komunikacji równorzędnej, a następnie włącz opcję „**Zezwalaj na tranzyt bramy**” podobnie, jak pokazano na zrzucie ekranu w kroku (1).

3. Nie trzeba wykonywać żadnych działań na sieci wirtualnej Spoke-Classic.

4. Sprawdź, czy stan komunikacji równorzędnej w sieci wirtualnej Hub-RM ma wartość „**Połączono**”.

Jeśli stan połączenia będzie miał wartość „Połączono”, promieniste sieci wirtualne mogą rozpocząć korzystanie z połączenia między sieciami wirtualnymi lub połączenia obejmującego wiele lokalizacji za pośrednictwem bramy sieci VPN umiejscowionej w centralnej sieci wirtualnej.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

Do utworzenia lub zaktualizowania komunikacji równorzędnej przedstawionej na powyższym przykładzie można również użyć programu PowerShell. Zastąp zmienne i identyfikator subskrypcji wartościami Twojej sieci wirtualnej, grup zasobów oraz subskrypcji. Komunikację równorzędną sieci wirtualnej należy utworzyć tylko w centralnej sieci wirtualnej.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ograniczeniach i zachowaniu komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) i [ustawieniach komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#create-a-peering) przed utworzeniem komunikacji równorzędnej sieci wirtualnej w środowisku produkcyjnym.
* Dowiedz się, jak [utworzyć topologię sieciową gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną sieci wirtualnej i tranzytem bramy.