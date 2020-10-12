---
title: Konfigurowanie wymuszonego tunelowania dla połączeń lokacja-lokacja
description: Jak przekierować lub "Wymuś" cały ruch związany z Internetem z powrotem do lokalizacji lokalnej.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e9444291c40ef504a674ee18351ba581695d1dd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394521"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu modelu wdrażania usługi Azure Resource Manager

Wymuszane tunelowanie umożliwia przekierowywanie lub „wymuszanie” przekazywania całego ruchu internetowego z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN między lokacjami do celów przeprowadzania inspekcji. Jest to krytyczne wymaganie dotyczące zabezpieczeń w przypadku większości zasad IT dla przedsiębiorstw. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure zawsze przechodzi bezpośrednio z infrastruktury sieci platformy Azure do Internetu bez opcji, która umożliwia inspekcję i inspekcję ruchu. Nieautoryzowany dostęp do Internetu może potencjalnie prowadzić do ujawnienia informacji lub innych rodzajów naruszeń zabezpieczeń.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

W tym artykule omówiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu modelu wdrażania Menedżer zasobów. Wymuszone tunelowanie można skonfigurować przy użyciu programu PowerShell, a nie za pośrednictwem portalu. Jeśli chcesz skonfigurować Wymuszone tunelowanie dla klasycznego modelu wdrażania, wybierz pozycję artykuł klasyczny z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Tunelowanie wymuszone — informacje

Na poniższym diagramie przedstawiono sposób działania wymuszonego tunelowania. 

![Wymuszone tunelowanie](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

W powyższym przykładzie podsieć frontonu nie jest wymuszana. Obciążenia w podsieci frontonu mogą nadal akceptować i odpowiadać na żądania klientów bezpośrednio z Internetu. Tunele warstwy środkowej i zaplecza są wymuszone. Wszystkie połączenia wychodzące z tych dwóch podsieci do Internetu zostaną wymuszone lub przekierowane z powrotem do lokacji lokalnej za pośrednictwem jednego z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i sprawdzić dostęp do Internetu z maszyn wirtualnych lub usług w chmurze na platformie Azure, pozostawiając jednocześnie wymaganą architekturę usługi wielowarstwowej. Jeśli w sieciach wirtualnych nie ma obciążeń związanych z Internetem, można również zastosować Wymuszone tunelowanie do całych sieci wirtualnych.

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia

Wymuszone tunelowanie na platformie Azure jest konfigurowane za pośrednictwem tras zdefiniowanych przez użytkownika sieci wirtualnej. Przekierowywanie ruchu do lokacji lokalnej jest wyrażone jako trasa domyślna do bramy sieci VPN platformy Azure. Aby uzyskać więcej informacji dotyczących routingu i sieci wirtualnych zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).

* Każda podsieć sieci wirtualnej ma wbudowaną, systemową tabelę routingu. Tabela routingu systemu zawiera trzy następujące grupy tras:
  
  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej.
  * **Trasy lokalne:** Z bramą sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio z Internetem. Pakiety przeznaczone dla prywatnych adresów IP, które nie są objęte poprzednimi dwiema trasami, są usuwane.
* Ta procedura powoduje użycie tras zdefiniowanych przez użytkownika (UDR) w celu utworzenia tabeli routingu w celu dodania trasy domyślnej, a następnie skojarzenie tabeli routingu z podsieciami sieci wirtualnej w celu włączenia wymuszonego tunelowania w tych podsieciach.
* Wymuszone tunelowanie musi być skojarzone z siecią wirtualną, która ma bramę sieci VPN opartą na trasach. Należy ustawić "domyślną lokację" między lokacjami lokalnymi między różnymi lokalizacjami połączonymi z siecią wirtualną. Ponadto lokalne urządzenie sieci VPN musi być skonfigurowane przy użyciu 0.0.0.0/0 jako selektorów ruchu. 
* Wymuszone tunelowanie ExpressRoute nie jest konfigurowane za pośrednictwem tego mechanizmu, ale zamiast tego jest włączane poprzez anonsowanie trasy domyślnej za pośrednictwem sesji komunikacji równorzędnej BGP ExpressRoute. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Przegląd konfiguracji

Poniższa procedura ułatwia utworzenie grupy zasobów i sieci wirtualnej. Następnie utworzysz bramę sieci VPN i skonfigurujesz Wymuszone tunelowanie. W tej procedurze Sieć wirtualna "wielowarstwowa" sieci wirtualnej ma trzy podsieci: "fronton", "MidTier" i "zaplecze" z czterema połączeniami między różnymi lokalizacjami: "DefaultSiteHQ" i trzema gałęziami.

Kroki procedury ustawiają "DefaultSiteHQ" jako domyślne połączenie z lokacją dla wymuszonego tunelowania i konfiguruje podsieci "MidTier" i "zaplecza", aby używać tunelowania wymuszonego.

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell).

> [!IMPORTANT]
> Wymagane jest zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell. W przeciwnym razie podczas uruchamiania niektórych poleceń cmdlet mogą pojawić się błędy walidacji.
>
>

### <a name="to-log-in"></a>Aby się zalogować

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania

> [!NOTE]
> W przyszłych wydaniach mogą pojawić się ostrzeżenia informujące o tym, że typ obiektu wyjściowego tego polecenia cmdlet zostanie zmodyfikowany. Jest to oczekiwane zachowanie i można je bezpiecznie zignorować.
>
>


1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Utwórz sieć wirtualną i określ podsieci.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Utwórz bramę sieci lokalnej.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Utwórz tabelę tras i regułę trasy.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Skojarz tabelę tras z Midtier i podsieciami zaplecza.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Utwórz bramę sieci wirtualnej. Wykonanie tego kroku trwa trochę czasu, Czasami nawet 45 minut, ponieważ tworzysz i konfigurujesz bramę. Jeśli widzisz błędy ValidateSet dotyczące wartości GatewaySKU, sprawdź, czy zainstalowano [najnowszą wersję poleceń cmdlet programu PowerShell](#before). Najnowsza wersja poleceń cmdlet programu PowerShell zawiera nowe zweryfikowane wartości dla najnowszych jednostek SKU bramy.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Przypisz domyślną lokację do bramy sieci wirtualnej. **-GatewayDefaultSite** to parametr polecenia cmdlet, który umożliwia działanie wymuszonej konfiguracji routingu, dlatego należy zadbać o poprawne skonfigurowanie tego ustawienia. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Ustanów połączenia sieci VPN typu lokacja-lokacja.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
