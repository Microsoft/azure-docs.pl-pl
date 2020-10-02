---
title: 'Konfigurowanie współistniejących połączeń ExpressRoute i VPN S2S: klasyczny'
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania połączeń usługi ExpressRoute oraz sieci VPN typu lokacja-lokacja, które mogą współistnieć, w klasycznym modelu wdrożenia.
documentationcenter: na
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: 4cc96a8183e07a17ae880549cdc2b8bdca516322
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651500"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Konfigurowanie współistniejących połączeń usługi ExpressRoute i połączeń typu lokacja-lokacja (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-howto-coexist-classic.md)
> 
> 

Ten artykuł pomaga skonfigurować współistniejące połączenia usługi ExpressRoute i połączenia sieci VPN typu lokacja-lokacja. Możliwość skonfigurowania sieci VPN typu lokacja-lokacja i usługi ExpressRoute niesie ze sobą pewne korzyści. Sieć VPN typu lokacja-lokacja można skonfigurować jako bezpieczną ścieżkę trybu failover dla usługi ExpressRoute lub użyć tej sieci do połączenia z lokacjami, które nie zostały połączone za pośrednictwem usługi ExpressRoute. Ten artykuł zawiera instrukcje konfiguracji obu scenariuszy. Dotyczy on klasycznego modelu wdrożenia. Ta konfiguracja nie jest dostępna w portalu.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Przed wykonaniem poniższych instrukcji należy wstępnie skonfigurować obwody usługi ExpressRoute. Przed wykonaniem poniższych kroków należy koniecznie wykonać instrukcje [tworzenia obwodu usługi ExpressRoute](expressroute-howto-circuit-classic.md) i [konfigurowania routingu](expressroute-howto-routing-classic.md).
> 
> 

## <a name="limits-and-limitations"></a>Limity i ograniczenia
* **Routing tranzytowy nie jest obsługiwany.** Nie można skierować połączenia (przez platformę Azure) między lokalną siecią połączoną za pośrednictwem sieci VPN typu lokacja-lokacja i lokalną siecią połączoną za pośrednictwem usługi ExpressRoute.
* **Połączenia typu punkt-lokacja nie są obsługiwane.** Nie można włączyć połączeń VPN typu punkt-lokacja do tej samej sieci wirtualnej, która jest połączona z usługą ExpressRoute. Sieć VPN typu punkt-lokacja i usługa ExpressRoute nie mogą współistnieć dla tej samej sieci wirtualnej.
* **Nie można włączyć tunelowania wymuszonego dla bramy sieci VPN typu lokacja-lokacja.** Można tylko „wymusić” przesyłanie całego ruchu skierowanego do Internetu z powrotem do sieci lokalnej za pośrednictwem usługi ExpressRoute.
* **Podstawowa brama jednostki SKU nie jest obsługiwana.** Należy użyć innej niż podstawowa bramy jednostki SKU zarówno dla [bramy usługi ExpressRoute](expressroute-about-virtual-network-gateways.md), jak i [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Obsługiwana jest tylko brama sieci VPN oparta na trasach.** Należy użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) opartej na trasach.
* **Dla bramy sieci VPN należy skonfigurować trasę statyczną.** Jeśli sieć lokalna jest połączona z usługą ExpressRoute oraz siecią VPN typu lokacja-lokacja, aby skierować połączenie sieci VPN typu lokacja-lokacja do publicznego Internetu, trzeba mieć skonfigurowaną trasę statyczną w sieci lokalnej.
* **Najpierw należy skonfigurować bramę usługi ExpressRoute.** Przed dodaniem bramy sieci VPN typu lokacja-lokacja należy utworzyć bramę usługi ExpressRoute.

## <a name="configuration-designs"></a>Projekty konfiguracji
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurowanie sieci VPN typu lokacja-lokacja jako ścieżki pracy awaryjnej dla usługi ExpressRoute
Połączenie sieci VPN typu lokacja-lokacja można skonfigurować do przechowywania kopii zapasowych dla usługi ExpressRoute. Dotyczy to tylko sieci wirtualnych połączonych ze ścieżką prywatnej sieci równorzędnej Azure. Nie ma rozwiązania pracy awaryjnej opartego na sieci VPN dla usług dostępnych przez publiczne sesje komunikacji równorzędnej platformy Azure ani komunikacji równorzędnej firmy Microsoft. Obwód usługi ExpressRoute jest zawsze połączeniem podstawowym. Dane będą przepływać przez ścieżkę sieci VPN typu lokacja-lokacja tylko w wypadku awarii obwodu usługi ExpressRoute. 

> [!NOTE]
> Obwód usługi ExpressRoute jest preferowany w porównaniu z siecią VPN typu lokacja-lokacja, jeśli obydwie trasy są takie same, a na platformie Azure dopasowanie najdłuższego prefiksu będzie używane do wybierania trasy do miejsca docelowego pakietu.
> 
> 

![Diagram przedstawiający połączenie sieci VPN typu lokacja-lokacja jako kopii zapasowej dla ExpressRoute.](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurowanie sieci VPN typu lokacja-lokacja do łączenia z witrynami niepołączonymi przez usługę ExpressRoute
Można skonfigurować sieć w taki sposób, by niektóre witryny łączyły się bezpośrednio z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja, a niektóre przez usługę ExpressRoute. 

![Współistnienie](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Nie można skonfigurować sieci wirtualnej jako routera tranzytowego.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Wybieranie czynności do wykonania
Istnieją dwa różne zestawy procedur do wyboru służące do konfigurowania połączeń, które mogą współistnieć. Wybór procedury konfiguracji będzie zależeć od tego, czy masz istniejącą sieć wirtualną, z którą chcesz się połączyć, czy chcesz utworzyć nową.

* Nie mam sieci wirtualnej i muszę ją utworzyć.
  
    Jeśli nie masz jeszcze sieci wirtualnej, ta procedura zawiera instrukcje tworzenia nowej sieci wirtualnej za pomocą klasycznego modelu wdrożenia i tworzenia nowych połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja. Aby przeprowadzić konfigurację, wykonaj kroki opisane w sekcji artykułu [Aby utworzyć nową sieć wirtualną i współistniejące połączenia](#new).
* Mam już sieć wirtualną wdrożoną w ramach modelu klasycznego.
  
    Być może masz już gotową sieć wirtualną z istniejącym połączeniem sieci VPN typu lokacja-lokacja lub połączeniem usługi ExpressRoute. Sekcja artykułu [Aby skonfigurować współistniejące połączenia dla już istniejących sieci wirtualnych](#add) zawiera instrukcje usuwania bramy, a następnie tworzenia nowych połączeń usługi ExpressRoute i połączeń VPN typu lokacja-lokacja. Należy pamiętać, że podczas tworzenia nowych połączeń kroki muszą być wykonywane po kolei. Do tworzenia bram i połączeń nie używaj instrukcji z innych artykułów.
  
    W tej procedurze tworzenie połączeń, które mogą współistnieć, wymaga usunięcia bramy, a następnie skonfigurowania nowych bram. Oznacza to, że podczas usuwania i odtwarzania bramy oraz połączeń wystąpi przestój względem połączeń obejmujących wiele lokalizacji, ale nie trzeba będzie migrować żadnych maszyn wirtualnych ani usług do nowej sieci wirtualnej. Podczas konfigurowania bramy maszyny wirtualne i usługi będą mogły nadal komunikować się za pośrednictwem modułu równoważenia obciążenia, jeżeli zostały w taki sposób skonfigurowane.

## <a name="install-powershell-cmdlets"></a>Instalowanie poleceń cmdlet programu PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Aby utworzyć nową sieć wirtualną i współistniejące połączenia
Ta procedura zawiera instrukcje tworzenia sieci wirtualnej i połączeń typu lokacja-lokacja oraz usługi ExpressRoute, które będą współistnieć.

1. Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu Azure PowerShell.  Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell). Pamiętaj, że polecenia cmdlet, które zostaną użyte do tej konfiguracji, mogą trochę różnić się od tych, które znasz. Koniecznie użyj poleceń cmdlet podanych w tych instrukcjach. 
2. Utwórz schemat dla sieci wirtualnej. Więcej informacji na temat schematu konfiguracji znajduje się w artykule [Azure Virtual Network configuration schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schemat konfiguracji sieci wirtualnej Azure).
   
    Podczas tworzenia schematu pamiętaj, aby użyć następujących wartości:
   
   * Wartość podsieci bramy dla sieci wirtualnej musi wynosić /27; prefiks może też być krótszy (np. /26 lub /25).
   * Typ połączenia bramy ma wartość „Dedykowane”.
     
    ```xml
    <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
      <AddressSpace>
        <AddressPrefix>10.17.159.192/26</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="Subnet-1">
          <AddressPrefix>10.17.159.192/27</AddressPrefix>
        </Subnet>
        <Subnet name="GatewaySubnet">
          <AddressPrefix>10.17.159.224/27</AddressPrefix>
          /Subnet>
      </Subnets>
      <Gateway>
        <ConnectionsToLocalNetwork>
          <LocalNetworkSiteRef name="MyLocalNetwork">
            <Connection type="Dedicated" />
          </LocalNetworkSiteRef>
        </ConnectionsToLocalNetwork>
      </Gateway>
    </VirtualNetworkSite>
    ```
3. Po utworzeniu i skonfigurowaniu pliku schematu xml przekaż plik. Spowoduje to utworzenie sieci wirtualnej.
   
    Użyj poniższego polecenia cmdlet do przekazania pliku, zastępując wartość swoją własną.
   
    ```azurepowershell
    Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
    ```
4. <a name="gw"></a>Utwórz bramę usługi ExpressRoute. Koniecznie określ wartość *Standard*, *HighPerformance* lub *UltraPerformance* dla parametru GatewaySKU oraz wartość *DynamicRouting* dla parametru GatewayType.
   
    Użyj poniższego przykładu, podstawiając wartości zamiast swoich własnych.

    ```azurepowershell
    New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
    ```
5. Połącz bramę usługi ExpressRoute z obwodem usługi ExpressRoute. Po ukończeniu tego kroku zostanie nawiązane połączenie między siecią lokalną i platformą Azure za pośrednictwem usługi ExpressRoute.
   
    ```azurepowershell
    New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
    ```
6. <a name="vpngw"></a>Następnie utwórz bramę sieci VPN typu lokacja-lokacja. Parametr GatewaySKU musi mieć wartość *Standard*, *HighPerformance* lub *UltraPerformance*, a parametr GatewayType — *DynamicRouting*.
   
    ```azurepowershell
    New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
    ```
   
    Aby pobrać ustawienia bramy sieci wirtualnej, w tym identyfikator bramy i publiczny adres IP, użyj polecenia `Get-AzureVirtualNetworkGateway`.
   
    ```azurepowershell
    Get-AzureVirtualNetworkGateway
   
    GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
    GatewayName          : S2SVPN
    LastEventData        :
    GatewayType          : DynamicRouting
    LastEventTimeStamp   : 5/29/2015 4:41:41 PM
    LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
    LastEventID          : 23002
    State                : Provisioned
    VIPAddress           : 104.43.x.y
    DefaultSite          :
    GatewaySKU           : HighPerformance
    Location             :
    VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
    SubnetId             :
    EnableBgp            : False
    OperationDescription : Get-AzureVirtualNetworkGateway
    OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
    OperationStatus      : Succeeded
    ```
7. Utwórz obiekt bramy sieci VPN witryny lokalnej. To polecenie nie powoduje skonfigurowania bramy lokalnej sieci VPN. Umożliwia raczej zapewnienie ustawień bramy lokalnej, np. publicznego adresu IP i lokalnej przestrzeni adresowej, aby brama sieci VPN Azure mogła się z nimi połączyć.
   
   > [!IMPORTANT]
   > Lokalna witryna dla sieci VPN typu lokacja-lokacja nie jest definiowana w pliku netcfg. Zamiast tego musisz użyć tego polecenia cmdlet do określania lokalnych parametrów witryny. Nie możesz jej definiować przy użyciu portalu ani pliku netcfg.
   > 
   > 
   
    Użyj poniższego przykładu, zastępując wartości swoimi własnymi.
   
    ```azurepowershell
    New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
    ```
   
   > [!NOTE]
   > Jeżeli sieć lokalna ma wiele tras, możesz je wszystkie przekazać w postaci tablicy.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Aby pobrać ustawienia bramy sieci wirtualnej, w tym identyfikator bramy i publiczny adres IP, użyj polecenia `Get-AzureVirtualNetworkGateway`. Zobacz poniższy przykład.

    ```azurepowershell
    Get-AzureLocalNetworkGateway

    GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
    GatewayName          : MyLocalNetwork
    IpAddress            : 23.39.x.y
    AddressSpace         : {10.1.2.0/24}
    OperationDescription : Get-AzureLocalNetworkGateway
    OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
    OperationStatus      : Succeeded
    ```


1. Skonfiguruj lokalne urządzenie sieci VPN do połączenia z nową bramą. Podczas konfigurowania urządzenia VPN użyj informacji pobranych w kroku 6. Więcej informacji na temat konfigurowania urządzenia VPN znajduje się w artykule [VPN Device Configuration](../vpn-gateway/vpn-gateway-about-vpn-devices.md) (Konfigurowanie urządzenia VPN).
2. Połącz bramę sieci VPN typu lokacja-lokacja na platformie Azure z bramą lokalną.
   
    W tym przykładzie connectedEntityId jest identyfikatorem bramy lokalnej, który można znaleźć, uruchamiając polecenie `Get-AzureLocalNetworkGateway`. Identyfikator VirtualNetworkGatewayId można znaleźć przy użyciu polecenia cmdlet `Get-AzureVirtualNetworkGateway`. Po wykonaniu tego kroku zostanie nawiązane połączenie między siecią lokalną i platformą Azure za pośrednictwem połączenia VPN typu lokacja-lokacja.

    ```azurepowershell
    New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Aby skonfigurować współistniejące połączenia dla istniejącej sieci wirtualnej
Jeśli masz istniejącą sieć wirtualną, sprawdź rozmiar podsieci bramy. Jeśli podsieć bramy ma wartość /28 lub /29, musisz najpierw usunąć bramę sieci wirtualnej i zwiększyć rozmiar podsieci bramy. W krokach w tej sekcji przedstawiono, jak to zrobić.

Jeśli podsieć bramy ma wartość /27 lub większą, a sieć wirtualna jest połączona za pośrednictwem usługi ExpressRoute, możesz pominąć poniższe kroki i przejść do tematu [„Krok 6 — tworzenie bramy sieci VPN typu lokacja-lokacja”](#vpngw) w poprzedniej sekcji.

> [!NOTE]
> Po usunięciu istniejącej bramy podczas pracy nad tą konfiguracją lokalizacja miejscowa straci połączenie z siecią wirtualną.
> 
> 

1. Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell). Pamiętaj, że polecenia cmdlet, które zostaną użyte do tej konfiguracji, mogą trochę różnić się od tych, które znasz. Koniecznie użyj poleceń cmdlet podanych w tych instrukcjach. 
2. Usuń istniejącą bramę usługi ExpressRoute lub sieci VPN typu lokacja-lokacja. Użyj poniższego polecenia cmdlet, zastępując wartości swoimi własnymi.
   
    ```azurepowershell
    Remove-AzureVNetGateway –VnetName MyAzureVNET
    ```
3. Wyeksportuj schemat sieci wirtualnej. Użyj poniższego polecenia cmdlet programu PowerShell, zastępując wartości swoimi własnymi.
   
    ```azurepowershell
    Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
    ```
4. Edytuj schemat pliku konfiguracji sieci w taki sposób, aby wartość podsieci bramy wynosiła /27 lub miała krótszy prefiks (np. /26 lub /25). Zobacz poniższy przykład. 
   
   > [!NOTE]
   > Jeśli nie masz wystarczającej liczby adresów IP w sieci wirtualnej, aby zwiększyć rozmiar podsieci bramy, dodaj więcej przestrzeni adresowej IP. Więcej informacji na temat schematu konfiguracji znajduje się w artykule [Azure Virtual Network configuration schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schemat konfiguracji sieci wirtualnej Azure).
   > 
   > 
   
    ```xml
    <Subnet name="GatewaySubnet">
      <AddressPrefix>10.17.159.224/27</AddressPrefix>
    </Subnet>
    ```
5. Jeśli poprzednia brama pochodziła z sieci VPN typu lokacja-lokacja, musisz również zmienić typ połączenia na **Dedykowane**.
   
    ```xml
    <Gateway>
      <ConnectionsToLocalNetwork>
        <LocalNetworkSiteRef name="MyLocalNetwork">
          <Connection type="Dedicated" />
        </LocalNetworkSiteRef>
      </ConnectionsToLocalNetwork>
    </Gateway>
    ```
6. Na tym etapie będziesz mieć sieć wirtualną bez bram. W celu utworzenia nowych bram i wykonania połączeń wykonaj instrukcje z części [Krok 4 — tworzenie bramy usługi ExpressRoute](#gw) znajdujące się w poprzednim zestawie kroków.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

