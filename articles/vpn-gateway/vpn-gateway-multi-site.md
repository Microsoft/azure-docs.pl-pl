---
title: 'Łączenie sieci wirtualnej z wieloma lokacjami przy użyciu VPN Gateway: klasyczny'
description: Połącz wiele lokalnych lokacjami lokalnymi z klasyczną siecią wirtualną przy użyciu VPN Gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660699"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN (klasyczne)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
>

W tym artykule opisano sposób dodawania połączeń typu lokacja-lokacja (S2S) do bramy sieci VPN z istniejącym połączeniem przy użyciu programu PowerShell. Ten typ połączenia jest często określany mianem konfiguracji "wiele lokacji". Kroki opisane w tym artykule mają zastosowanie do sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania (nazywanego również zarządzaniem usługami). Te kroki nie dotyczą współistniejących konfiguracji połączeń ExpressRoute/lokacja-lokacja.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ta tabela jest aktualizowana w miarę jak nowe artykuły i dodatkowe narzędzia stają się dostępne dla tej konfiguracji. Gdy artykuł jest dostępny, prosimy o bezpośredni link do niego z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Informacje o łączeniu

Można połączyć wiele lokacji lokalnych z pojedynczą siecią wirtualną. Jest to szczególnie atrakcyjne w przypadku tworzenia rozwiązań w chmurze hybrydowej. Tworzenie połączenia między lokacjami z bramą sieci wirtualnej platformy Azure jest podobne do tworzenia innych połączeń typu lokacja-lokacja. W rzeczywistości można użyć istniejącej bramy sieci VPN platformy Azure, o ile Brama jest dynamiczna (oparta na trasach).

Jeśli masz już bramę statyczną połączoną z siecią wirtualną, możesz zmienić typ bramy na dynamiczną bez konieczności odbudowywania sieci wirtualnej w celu uwzględnienia wiele lokacji. Przed zmianą typu routingu upewnij się, że lokalna Brama sieci VPN obsługuje konfiguracje sieci VPN oparte na trasach.

![Diagram z obsługą wiele lokacji](./media/vpn-gateway-multi-site/multisite.png "wiele lokacji")

## <a name="points-to-consider"></a>Kwestie do rozważenia

**Nie będzie można używać portalu do wprowadzania zmian w tej sieci wirtualnej.** Należy wprowadzić zmiany w pliku konfiguracji sieci, zamiast korzystać z portalu. Jeśli wprowadzisz zmiany w portalu, zastąpią one ustawienia odwołań do kilku witryn dla tej sieci wirtualnej.

Należy wygodnie korzystać z pliku konfiguracji sieci przez czas ukończenia procedury wielolokacjowej. Jeśli jednak w konfiguracji sieci istnieje wiele osób, należy się upewnić, że każdy wie o tym ograniczeniu. Nie oznacza to, że w ogóle nie można używać portalu. Można jej używać dla wszystkiego innego, z wyjątkiem wprowadzania zmian w konfiguracji tej konkretnej sieci wirtualnej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji Sprawdź, czy masz następujące elementy:

* Zgodny sprzęt sieci VPN dla każdej lokalizacji lokalnej. Sprawdź [Informacje o urządzeniach sieci VPN pod kątem łączności Virtual Network](vpn-gateway-about-vpn-devices.md) , aby sprawdzić, czy urządzenie, którego chcesz użyć, jest znane jako zgodne.
* Zewnętrzny adres IP protokołu IPv4 dla każdego urządzenia sieci VPN. Adres IP nie może znajdować się za translatorem adresów sieciowych. Jest to wymagane.
* Osoba, która jest w sposób nieużywany do konfigurowania sprzętu sieci VPN. Musisz mieć silny opis sposobu konfigurowania urządzenia sieci VPN lub pracy z osobą, która robi.
* Zakresy adresów IP, które mają być używane dla sieci wirtualnej (jeśli jeszcze nie zostały utworzone).
* Zakresy adresów IP dla każdej z lokacji sieci lokalnej, z którymi będziesz się łączyć. Należy upewnić się, że zakresy adresów IP dla każdej z lokacji sieci lokalnej, z którymi chcesz się połączyć, nie nakładają się na siebie. W przeciwnym razie Portal lub interfejs API REST odrzuci zakazaną konfigurację.<br>Na przykład jeśli masz dwie lokalne lokacje sieciowe, które zawierają zakres adresów IP 10.2.3.0/24 i masz pakiet z adresem docelowym 10.2.3.3, platforma Azure nie będzie mogła wiedzieć, do której lokacji chcesz wysłać pakiet, ponieważ zakresy adresów nakładają się na siebie. Aby zapobiec problemom z routingiem, platforma Azure nie zezwala na przekazywanie pliku konfiguracji, który ma nakładające się zakresy.

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Tworzenie sieci VPN typu lokacja-lokacja
Jeśli masz już sieć VPN typu lokacja-lokacja z bramą dynamicznego routingu, świetnie! Możesz przystępować [eksport ustawień konfiguracji sieci wirtualnej](#export). Jeśli nie, wykonaj następujące czynności:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Jeśli masz już sieć wirtualną typu lokacja-lokacja, ale ma ona statyczną (opartą na zasadach) bramę routingu:
1. Zmień typ bramy na Routing dynamiczny. Sieć VPN z obsługą wiele lokacji wymaga dynamicznej (nazywanego również bramą routingu opartego na trasach). Aby zmienić typ bramy, należy najpierw usunąć istniejącą bramę, a następnie utworzyć nową.
2. Skonfiguruj nową bramę i Utwórz tunel VPN. Aby uzyskać instrukcje, zobacz [Określanie jednostki SKU i typu sieci VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Upewnij się, że typ routingu jest określony jako "dynamiczny".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Jeśli nie masz sieci wirtualnej typu lokacja-lokacja:
1. Utwórz sieć wirtualną lokacja-lokacja, wykonując następujące instrukcje: [utwórz Virtual Network z połączeniem sieci VPN typu lokacja-lokacja](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Konfigurowanie bramy routingu dynamicznego przy użyciu następujących instrukcji: [skonfiguruj VPN Gateway](./vpn-gateway-howto-site-to-site-classic-portal.md). Upewnij się, że wybrano opcję **Routing dynamiczny** dla danego typu bramy.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Wyeksportuj plik konfiguracji sieci

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

```powershell
azure config mode asm
```

Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Add-AzureAccount
```

Wyeksportuj plik konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. W razie potrzeby można zmienić lokalizację pliku do wyeksportowania do innej lokalizacji.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otwórz plik konfiguracji sieciowej
Otwórz plik konfiguracji sieciowej pobrany w ostatnim kroku. Użyj dowolnego edytora XML. Plik powinien wyglądać podobnie do poniższego:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Dodaj odwołania do wielu lokacji
Po dodaniu lub usunięciu informacji o odwołaniu do lokacji wprowadzisz zmiany w konfiguracji ConnectionsToLocalNetwork/LocalNetworkSiteRef. Dodanie nowego tunelu do lokalnego odwołania do lokacji spowoduje, że platforma Azure zostanie utworzona. W poniższym przykładzie konfiguracja sieci dotyczy połączenia z jedną lokacją. Zapisz plik po zakończeniu wprowadzania zmian.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Aby dodać dodatkowe odwołania do lokacji (utworzyć konfigurację z konfiguracją wiele lokacji), po prostu Dodaj dodatkowe linie "LocalNetworkSiteRef", jak pokazano w poniższym przykładzie:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Zaimportuj plik konfiguracji sieci
Zaimportuj plik konfiguracji sieci. Po zaimportowaniu tego pliku ze zmianami zostaną dodane nowe tunele. Tunele będą używały utworzonej wcześniej bramy dynamicznej. Do zaimportowania pliku można użyć programu PowerShell.

## <a name="6-download-keys"></a>6. Pobierz klucze
Po dodaniu nowych tuneli należy użyć polecenia cmdlet "Get-AzureVNetGatewayKey" programu PowerShell, aby pobrać klucze wstępne protokołu IPsec/IKE dla każdego tunelu.

Na przykład:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Jeśli wolisz, możesz również użyć interfejsu API REST usługi *Get Virtual Network Gateway* w celu uzyskania kluczy wstępnych.

## <a name="7-verify-your-connections"></a>7. Sprawdź połączenia
Sprawdź stan tunelu z obsługą wiele lokacji. Po pobraniu kluczy dla każdego tunelu należy zweryfikować połączenia. Użyj polecenie "Get-AzureVnetConnection", aby uzyskać listę tuneli sieci wirtualnej, jak pokazano w poniższym przykładzie. VNet1 jest nazwą sieci wirtualnej.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Przykład powrotu:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bramach sieci VPN, zobacz [Informacje o bramach sieci VPN](vpn-gateway-about-vpngateways.md).