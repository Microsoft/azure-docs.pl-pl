---
title: 'Azure ExpressRoute: Konfigurowanie sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft'
description: Skonfiguruj połączenia IPsec/IKE na platformie Azure za pośrednictwem obwodu komunikacji równorzędnej firmy Microsoft ExpressRoute przy użyciu bramy sieci VPN typu lokacja-lokacja.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393143"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurowanie sieci VPN typu lokacja-lokacja za pośrednictwem komunikacji równorzędnej firmy Microsoft ExpressRoute

Ten artykuł pomaga skonfigurować bezpieczne szyfrowanie szyfrowane między siecią lokalną i sieciami wirtualnymi platformy Azure (sieci wirtualnych) za pośrednictwem połączenia prywatnego ExpressRoute. Komunikacji równorzędnej firmy Microsoft można użyć do ustanowienia tunelu sieci VPN IPsec/IKE typu lokacja-lokacja między wybranymi sieciami lokalnymi i usługą Azure sieci wirtualnych. Skonfigurowanie bezpiecznego tunelu za pośrednictwem usługi ExpressRoute umożliwia wymianę danych z poufnością, ochrona przed powtarzaniem, autentyczność i integralność.

>[!NOTE]
>Podczas konfigurowania sieci VPN typu lokacja-lokacja za pośrednictwem komunikacji równorzędnej firmy Microsoft jest naliczana opłata za ruch wychodzący bramy sieci VPN i sieci VPN. Aby uzyskać więcej informacji, zobacz [Cennik usługi VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektura


  ![Przegląd łączności](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Aby zapewnić wysoką dostępność i nadmiarowość, można skonfigurować wiele tuneli przez dwie pary MSEE-PE obwodu usługi ExpressRoute i włączyć równoważenie obciążenia między tunelami.

  ![Opcje wysokiej dostępności](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunele sieci VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft można kończyć przy użyciu bramy sieci VPN lub przy użyciu odpowiedniego wirtualnego urządzenia sieciowego (urządzenie WUS) dostępnego za pośrednictwem portalu Azure Marketplace. Trasy można wymieniać statycznie lub dynamicznie za pośrednictwem szyfrowanych tuneli bez udostępniania wymiany tras do podstawowej komunikacji równorzędnej firmy Microsoft. W przykładach w tym artykule protokół BGP (inny niż sesja BGP użyta do utworzenia komunikacji równorzędnej firmy Microsoft) służy do dynamicznego wymieniania prefiksów przez zaszyfrowane tunele.

>[!IMPORTANT]
>W przypadku sieci lokalnej zwykle jest przerywana komunikacja równorzędna firmy Microsoft w strefie DMZ, a prywatna Komunikacja równorzędna jest przerywana w ramach podstawowej strefy sieciowej. Dwie strefy byłyby segregowane przy użyciu zapór. W przypadku konfigurowania komunikacji równorzędnej firmy Microsoft przeznaczonej wyłącznie do włączania tunelowania zabezpieczonego za pośrednictwem ExpressRoute należy odfiltrować tylko publiczne adresy IP, które są anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft.
>
>

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

1. Skonfiguruj komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute.
2. Anonsowanie wybranych lokalnych prefiksów publicznych platformy Azure do sieci lokalnej za pośrednictwem komunikacji równorzędnej firmy Microsoft.
3. Konfigurowanie bramy sieci VPN i ustanawianie tuneli IPsec
4. Skonfiguruj lokalne urządzenie sieci VPN.
5. Utwórz połączenie między lokacjami IPsec/IKE.
6. Obowiązkowe Skonfiguruj zapory/filtrowanie na lokalnym urządzeniu sieci VPN.
7. Testowanie i weryfikowanie komunikacji IPsec za pośrednictwem obwodu usługi ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Skonfiguruj komunikację równorzędną firmy Microsoft

Aby skonfigurować połączenie sieci VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute, należy użyć komunikacji równorzędnej usługi ExpressRoute firmy Microsoft.

* Aby skonfigurować nowy obwód ExpressRoute, należy rozpocząć od artykułu [wymagania wstępne ExpressRoute](expressroute-prerequisites.md) , a następnie [utworzyć i zmodyfikować obwód ExpressRoute](expressroute-howto-circuit-arm.md).

* Jeśli masz już obwód usługi ExpressRoute, ale nie skonfigurowano komunikacji równorzędnej firmy Microsoft, skonfiguruj komunikację równorzędną firmy Microsoft przy użyciu funkcji [tworzenia i modyfikowania komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md#msft) .

Po skonfigurowaniu obwodu i komunikacji równorzędnej firmy Microsoft można łatwo wyświetlić go przy użyciu strony **Przegląd** w Azure Portal.

![zwarci](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Konfigurowanie filtrów tras

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Zasadniczo jest to lista dozwolonych wszystkich wartości społeczności protokołu BGP. 

![filtr tras](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

W tym przykładzie wdrożenie jest tylko w regionie " *zachodnie stany USA 2* ". Reguła filtru trasy jest dodawana, aby zezwalać tylko na anonse prefiksów regionalnych w regionie zachodnie stany USA 2, które mają wartość wspólnotową BGP *12076:51026*. Określ prefiksy regionalne, których chcesz zezwolić, wybierając pozycję **Zarządzaj regułą**.

W filtrze trasy należy również wybrać obwody usługi ExpressRoute, których dotyczy filtr trasy. Możesz wybrać obwody usługi ExpressRoute, wybierając pozycję **Dodaj obwód**. Na powyższym rysunku filtr trasy jest skojarzony z przykładowym obwodem ExpressRoute.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 Konfigurowanie filtru tras

Skonfiguruj filtr tras. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2. Sprawdź trasy BGP

Po pomyślnym utworzeniu komunikacji równorzędnej firmy Microsoft za pośrednictwem obwodu usługi ExpressRoute i przypisaniu filtru tras do obwodu można sprawdzić trasy protokołu BGP otrzymane z usługi MSEE na urządzeniach PE, które są połączone z MSEE. Polecenie weryfikacji różni się w zależności od systemu operacyjnego urządzeń PE.

#### <a name="cisco-examples"></a>Przykłady Cisco

Ten przykład używa polecenia Cisco IOS-XE. W tym przykładzie wystąpienie routingu wirtualnego i przesyłania dalej (VRF) służy do izolowania ruchu komunikacji równorzędnej.

```
show ip bgp vpnv4 vrf 10 summary
```

Poniższe częściowe dane wyjściowe pokazują, że 68 prefiksy zostały odebrane od sąsiada \* . 243.229.34 z numerem ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Aby wyświetlić listę prefiksów odebranych od sąsiada, Skorzystaj z następującego przykładu:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Aby potwierdzić, że otrzymujesz poprawny zestaw prefiksów, możesz zweryfikować krzyżowo. Następujące Azure PowerShell dane wyjściowe polecenia zawierają listę prefiksów anonsowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft dla każdej usługi i każdego regionu platformy Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Skonfiguruj bramę sieci VPN i tunele IPsec

W tej sekcji tunele sieci VPN IPsec są tworzone między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN. Przykłady używają urządzeń sieci VPN Cisco Cloud Service router (CSR1000).

Na poniższym diagramie przedstawiono tunele IPsec sieci VPN ustanowione między lokalnym urządzeniem sieci VPN 1 i pary wystąpień bramy sieci VPN platformy Azure. Dwa tunele IPsec sieci VPN ustanowione między lokalnym urządzeniem sieci VPN 2 i parą wystąpień bramy sieci VPN platformy Azure nie są zilustrowane na diagramie, a szczegóły konfiguracji nie są wymienione na liście. Jednak dodatkowe tunele sieci VPN zwiększają wysoką dostępność.

  ![Tunele sieci VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Za pośrednictwem pary tunelu IPsec zostaje ustanowiona sesja eBGP do wymiany tras sieci prywatnych. Na poniższym diagramie przedstawiono sesję eBGP ustanowioną za pośrednictwem pary tunelu IPsec:

  ![sesje eBGP przez parę tunelu](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Na poniższym diagramie przedstawiono streszczenie omówienia przykładowej sieci:

  ![Przykładowa sieć](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Przykłady szablonów Azure Resource Manager

W tych przykładach Brama sieci VPN i zakończenia tunelu IPsec są konfigurowane przy użyciu szablonu Azure Resource Manager. Jeśli dopiero zaczynasz korzystać z szablonów Menedżer zasobów lub poznać podstawy szablonu Menedżer zasobów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Szablon w tej sekcji tworzy środowisko platformy Azure Greenfield (VNet). Jeśli jednak masz istniejącą sieć wirtualną, możesz odwoływać się do niej w szablonie. Jeśli nie masz doświadczenia w konfiguracjach IPsec/IKE bramy sieci VPN, zobacz [Tworzenie połączenia typu lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Aby utworzyć tę konfigurację, nie trzeba używać szablonów Azure Resource Manager. Tę konfigurację można utworzyć przy użyciu Azure Portal lub programu PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1. Zadeklaruj zmienne

W tym przykładzie deklaracje zmiennych odpowiadają przykładowej sieci. Podczas deklarowania zmiennych należy zmodyfikować tę sekcję, aby odzwierciedlała Twoje środowisko.

* Zmienna **localAddressPrefix** jest tablicą lokalnych adresów IP, aby zakończyć tunele IPSec.
* **GatewaySku** określa PRZEPŁYWNOŚĆ sieci VPN. Aby uzyskać więcej informacji na temat gatewaySku i vpnType, zobacz [VPN Gateway ustawień konfiguracji](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby uzyskać cennik, zobacz [Cennik usługi VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ustaw wartość **vpnType** na **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 Utwórz sieć wirtualną (VNet)

Jeśli kojarzy istniejącą sieć wirtualną z tunelami sieci VPN, możesz pominąć ten krok.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 przypisanie publicznych adresów IP do wystąpień bramy sieci VPN
 
Przypisz publiczny adres IP dla każdego wystąpienia bramy sieci VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 określić zakończenie lokalnego tunelu VPN (Brama sieci lokalnej)

Lokalne urządzenia sieci VPN są określane jako **Brama sieci lokalnej**. Poniższy fragment kodu JSON określa również szczegóły zdalnego elementu równorzędnego BGP:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 Tworzenie bramy sieci VPN

Ta sekcja szablonu służy do konfigurowania bramy sieci VPN z wymaganymi ustawieniami konfiguracji Active-Active. Należy pamiętać o następujących wymaganiach:

* Utwórz bramę sieci VPN za pomocą VpnType **"RouteBased"** . To ustawienie jest obowiązkowe, jeśli chcesz włączyć routing BGP między bramą sieci VPN i lokalną siecią VPN.
* Aby ustanowić tunele sieci VPN między dwoma wystąpieniami bramy sieci VPN i danym urządzeniem lokalnym w trybie aktywny-aktywny, parametr **"activeActive"** ma **wartość "true** " w szablonie Menedżer zasobów. Aby dowiedzieć się więcej o bramach sieci VPN o wysokiej dostępności, zobacz [łączność z bramą VPN o wysokiej](../vpn-gateway/vpn-gateway-highlyavailable.md)dostępności.
* Aby skonfigurować sesje eBGP między tunelami sieci VPN, należy określić dwa różne WPW po obu stronach. Preferowane jest określenie prywatnych numerów ASN. Aby uzyskać więcej informacji, zobacz [Omówienie usług BGP i bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 Ustanów tunele IPsec

Ostatnia akcja skryptu tworzy tunele IPsec między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Skonfiguruj lokalne urządzenie sieci VPN

Brama sieci VPN platformy Azure jest zgodna z wieloma urządzeniami sieci VPN od różnych dostawców. Informacje o konfiguracji i urządzeniach, które zostały sprawdzone pod kątem współpracy z bramą sieci VPN, znajdują się w temacie [Informacje o urządzeniach sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Podczas konfigurowania urządzenia sieci VPN potrzebne są następujące elementy:

* Klucz współużytkowany. Jest to ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W przykładach użyto podstawowego klucza wspólnego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
* Publiczny adres IP bramy sieci VPN. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN za pomocą witryny Azure Portal, przejdź do pozycji Bramy sieci wirtualnej, a następnie kliknij nazwę bramy.

Zazwyczaj eBGP elementy równorzędne są połączone bezpośrednio (często za pośrednictwem połączenia sieci WAN). Jednak podczas konfigurowania eBGP przez tunele IPsec sieci VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft w usłudze ExpressRoute istnieje wiele domen routingu między elementami równorzędnymi eBGP. Użyj polecenia **eBGP-wieloskokowych** , aby określić relację sąsiada eBGP między dwoma niebezpośrednio połączonymi elementami równorzędnymi. Liczba całkowita, która następuje po eBGP-wieloskokowych polecenie określa wartość czasu wygaśnięcia w pakietach BGP. Polecenie **Maximum-Paths eibgp 2** umożliwia Równoważenie obciążenia ruchu między dwoma ścieżkami protokołu BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Przykład Cisco CSR1000

W poniższym przykładzie przedstawiono konfigurację dla programu Cisco CSR1000 na maszynie wirtualnej funkcji Hyper-V jako lokalne urządzenie sieci VPN:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Konfigurowanie filtrowania i zapory urządzeń sieci VPN (opcjonalnie)

Skonfiguruj zaporę i filtrowanie zgodnie z wymaganiami.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. testowanie i weryfikowanie tunelu IPsec

Stan tuneli protokołu IPsec można zweryfikować w usłudze Azure VPN Gateway za pomocą poleceń programu PowerShell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Przykładowe dane wyjściowe:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Aby sprawdzić stan tuneli w wystąpieniach usługi Azure VPN Gateway niezależnie, użyj następującego przykładu:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Przykładowe dane wyjściowe:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Możesz również sprawdzić stan tunelu na lokalnym urządzeniu sieci VPN.

Przykład Cisco CSR1000:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Przykładowe dane wyjściowe:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Protokół liniowy w interfejsie tunelu wirtualnego (VTI) nie zmienia się na "do" do momentu zakończenia operacji IKE fazy 2. Następujące polecenie weryfikuje skojarzenie zabezpieczeń:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Weryfikowanie kompleksowej łączności między firmowymi sieciami lokalnymi i siecią wirtualną platformy Azure

Jeśli tunele IPsec są skonfigurowane, a trasy statyczne są poprawnie ustawione, powinno być możliwe wysłanie polecenia ping do adresu IP zdalnego elementu równorzędnego BGP:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Weryfikowanie sesji BGP za pośrednictwem protokołu IPsec

Sprawdź stan elementu równorzędnego protokołu BGP w usłudze Azure VPN Gateway:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Przykładowe dane wyjściowe:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Aby sprawdzić listę prefiksów sieci odebranych za pośrednictwem eBGP z koncentratora sieci VPN w środowisku lokalnym, można filtrować według atrybutu "Origin":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

W przykładowym wyjściu numer ASN 65010 jest numerem systemu autonomicznego protokołu BGP w środowisku sieci VPN lokalnym.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Aby wyświetlić listę anonsowanych tras:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Przykładowe dane wyjściowe:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Przykład lokalnej usługi Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Listę sieci anonsowanych z lokalnego programu Cisco CSR1000 do bramy sieci VPN platformy Azure można wyświetlić przy użyciu następującego polecenia:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](how-to-npm.md)

* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
