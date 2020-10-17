---
title: 'Azure VPN Gateway: Konfigurowanie wymuszonego tunelowania — połączenia między lokacjami: klasyczny'
description: Dowiedz się, jak skonfigurować Wymuszone tunelowanie dla sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: af4359efb48898c12bb8ee7ffb882448b5012d19
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151348"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu klasycznego modelu wdrażania

Wymuszane tunelowanie umożliwia przekierowywanie lub „wymuszanie” przekazywania całego ruchu internetowego z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN między lokacjami do celów przeprowadzania inspekcji. Jest to krytyczne wymaganie dotyczące zabezpieczeń w przypadku większości zasad IT dla przedsiębiorstw. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure będzie zawsze przepływał od infrastruktury sieci platformy Azure bezpośrednio z Internetu bez opcji, aby umożliwić inspekcję lub inspekcję ruchu. Nieautoryzowany dostęp do Internetu może potencjalnie prowadzić do ujawnienia informacji lub innych rodzajów naruszeń zabezpieczeń.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

W tym artykule omówiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania. Wymuszone tunelowanie można skonfigurować przy użyciu programu PowerShell, a nie za pośrednictwem portalu. Jeśli chcesz skonfigurować Wymuszone tunelowanie dla Menedżer zasobówgo modelu wdrażania, wybierz pozycję Menedżer zasobów artykuł z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [Klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia

Wymuszone tunelowanie na platformie Azure jest konfigurowane za pośrednictwem tras zdefiniowanych przez użytkownika sieci wirtualnej (UDR). Przekierowywanie ruchu do lokacji lokalnej jest wyrażone jako trasa domyślna do bramy sieci VPN platformy Azure. W poniższej sekcji wymieniono bieżące ograniczenie tabeli routingu i trasy dla Virtual Network platformy Azure:

* Każda podsieć sieci wirtualnej ma wbudowaną, systemową tabelę routingu. Tabela routingu systemu zawiera trzy następujące grupy tras:

  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej.
  * **Trasy lokalne:** Z bramą sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio z Internetem. Pakiety przeznaczone dla prywatnych adresów IP, które nie są objęte poprzednimi dwiema trasami, zostaną usunięte.
* Dzięki wykorzystaniu tras zdefiniowanych przez użytkownika można utworzyć tabelę routingu w celu dodania trasy domyślnej, a następnie skojarzyć tabelę routingu z podsieciami sieci wirtualnej w celu włączenia wymuszonego tunelowania w tych podsieciach.
* Należy ustawić "domyślną lokację" między lokacjami lokalnymi między różnymi lokalizacjami połączonymi z siecią wirtualną.
* Wymuszone tunelowanie musi być skojarzone z siecią wirtualną, która ma bramę sieci VPN z routingiem dynamicznym (nie bramą statyczną).
* Wymuszone tunelowanie ExpressRoute nie jest konfigurowane za pośrednictwem tego mechanizmu, ale zamiast tego jest włączane poprzez anonsowanie trasy domyślnej za pośrednictwem sesji komunikacji równorzędnej BGP ExpressRoute. Aby uzyskać więcej informacji, zobacz [co to jest ExpressRoute?](../expressroute/expressroute-introduction.md).

## <a name="configuration-overview"></a>Przegląd konfiguracji

W poniższym przykładzie nie jest wymuszane tunelowanie podsieci frontonu. Obciążenia w podsieci frontonu mogą nadal akceptować i odpowiadać na żądania klientów bezpośrednio z Internetu. Tunele warstwy środkowej i zaplecza są wymuszone. Wszystkie połączenia wychodzące z tych dwóch podsieci do Internetu zostaną wymuszone lub przekierowane z powrotem do lokacji lokalnej za pośrednictwem jednego z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i sprawdzić dostęp do Internetu z maszyn wirtualnych lub usług w chmurze na platformie Azure, pozostawiając jednocześnie wymaganą architekturę usługi wielowarstwowej. Można również zastosować Wymuszone tunelowanie do całych sieci wirtualnych, jeśli w sieciach wirtualnych nie ma obciążeń mających dostęp do Internetu.

![Wymuszone tunelowanie](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Skonfigurowana Sieć wirtualna. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania

Poniższa procedura pomoże Ci określić Wymuszone tunelowanie dla sieci wirtualnej. Kroki konfiguracji odpowiadają plikowi konfiguracji sieci wirtualnej.  W tym przykładzie Sieć wirtualna "wielowarstwowa" sieci wirtualnej ma trzy podsieci: "fronton", "MidTier" i "zaplecze" z czterema połączeniami między różnymi lokalizacjami: "DefaultSiteHQ" i trzema gałęziami.


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Poniższe kroki spowodują ustawienie "DefaultSiteHQ" jako domyślnego połączenia z lokacją dla wymuszonego tunelowania oraz skonfigurowanie MidTier i podsieci zaplecza do korzystania z wymuszonego tunelowania.

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień. Połącz się z kontem przy użyciu poniższego przykładu:

   ```powershell
   Add-AzureAccount
   ```

1. Utwórz tabelę routingu. Użyj następującego polecenia cmdlet, aby utworzyć tabelę tras.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. Dodaj domyślną trasę do tabeli routingu. 

   Poniższy przykład dodaje trasę domyślną do tabeli routingu utworzonej w kroku 1. Należy zauważyć, że jedyną obsługiwaną trasą jest prefiks docelowy "0.0.0.0/0" do skoku "bramy vpngateway".

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. Skojarz tabelę routingu z podsieciami. 

   Po utworzeniu tabeli routingu i dodaniu trasy Użyj poniższego przykładu, aby dodać lub skojarzyć tabelę tras z podsiecią wirtualną. Przykład dodaje tabelę tras "MidTier" do podsieci zaplecza i sieci wirtualnej z obsługą wielowarstwowej warstwy wirtualnej.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. Przypisz domyślną lokację do wymuszonego tunelowania. 

   W poprzednim kroku skrypty przykładowego polecenia cmdlet utworzyły tabelę routingu i skojarzono tabelę tras do dwóch podsieci sieci wirtualnej. Drugi krok polega na wybraniu lokacji lokalnej między połączeniami z wieloma lokacjami sieci wirtualnej jako domyślną lokacją lub tunelem.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Dodatkowe polecenia cmdlet programu PowerShell

### <a name="to-delete-a-route-table"></a>Aby usunąć tabelę tras

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Aby wyświetlić tabelę tras

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Aby usunąć trasę z tabeli tras

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Aby usunąć trasę z podsieci

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Aby wyświetlić tabelę tras skojarzoną z podsiecią

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Aby usunąć domyślną lokację z wirtualnej bramy sieci VPN

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
