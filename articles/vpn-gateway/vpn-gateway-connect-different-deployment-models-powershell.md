---
title: 'Łączenie klasycznych sieci wirtualnych do Azure Resource Manager sieci wirtualnych: PowerShell'
description: Utwórz połączenie sieci VPN między klasycznymi sieci wirtualnych i Menedżer zasobów sieci wirtualnych przy użyciu VPN Gateway i programu PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 2c9b8a769dec1a2aa461a34203c98a228cf71d16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082056"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą programu PowerShell

Ten artykuł pomaga połączyć klasyczne sieci wirtualnych z Menedżer zasobów sieci wirtualnych, aby umożliwić zasobom znajdującym się w osobnych modelach wdrażania komunikację ze sobą. Kroki opisane w tym artykule korzystają z programu PowerShell, ale można również utworzyć tę konfigurację przy użyciu Azure Portal, wybierając artykuł z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Połączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżer zasobów jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenie między sieci wirtualnychami, które znajdują się w różnych subskrypcjach i w różnych regionach. Można także połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi, tak długo, jak Brama, z której zostały skonfigurowane, jest dynamiczna lub oparta na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej i nie chcesz jej utworzyć, możesz zamiast tego rozważyć łączenie się z usługą sieci wirtualnych przy użyciu wirtualnych sieci równorzędnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Poniższe kroki przeprowadzą Cię przez ustawienia niezbędne do skonfigurowania bramy dynamicznej lub opartej na trasach dla każdej sieci wirtualnej i utworzenia połączenia sieci VPN między bramami. Ta konfiguracja nie obsługuje bram statycznych lub opartych na zasadach.

### <a name="prerequisites"></a><a name="pre"></a>Wymagania wstępne

* Oba sieci wirtualnych zostały już utworzone. Jeśli musisz utworzyć sieć wirtualną Menedżera zasobów, zobacz [Tworzenie grupy zasobów i sieci wirtualnej](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Aby utworzyć klasyczną sieć wirtualną, zobacz [Tworzenie klasycznej](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)sieci wirtualnej.
* Zakresy adresów dla sieci wirtualnych nie nakładają się na siebie ani nie nakładają się na żadne z zakresów dla innych połączeń, z którymi bramy mogą być połączone.
* Zainstalowano najnowsze polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji [, zobacz Jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/) . Upewnij się, że zainstalowano zarówno polecenie cmdlet programu Service Management (SM), jak i Menedżer zasobów (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Ustawienia klasycznej sieci wirtualnej**

Nazwa sieci wirtualnej = ClassicVNet <br>
Lokalizacja = zachodnie stany USA <br>
Virtual Network przestrzenie adresowe = 10.0.0.0/24 <br>
Podsieć-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nazwa sieci lokalnej = RMVNetLocal <br>
Gatewaytype = DynamicRouting

**Menedżer zasobów ustawienia sieci wirtualnej**

Nazwa sieci wirtualnej = RMVNet <br>
Grupa zasobów = RG1 <br>
Virtual Network przestrzeni adresów IP = 192.168.0.0/16 <br>
Podsieć-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Lokalizacja = Wschodnie stany USA <br>
Publiczna nazwa IP bramy = gwpip <br>
Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa bramy Virtual Network = RMGateway <br>
Konfiguracja adresowania IP bramy = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Sekcja 1 — Konfigurowanie klasycznej sieci wirtualnej
### <a name="1-download-your-network-configuration-file"></a>1. Pobierz plik konfiguracji sieci
1. Zaloguj się do swojego konta platformy Azure w konsoli programu PowerShell z podwyższonym poziomem uprawnień. Poniższe polecenie cmdlet poprosi o poświadczenia logowania do konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell. Polecenia cmdlet programu klasycznej usługi Service Management (SM) Azure PowerShell są używane w tej sekcji.

   ```azurepowershell
   Add-AzureAccount
   ```

   Uzyskaj swoją subskrypcję platformy Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Wyeksportuj plik konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. W razie potrzeby można zmienić lokalizację pliku do wyeksportowania do innej lokalizacji.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Otwórz pobrany plik XML, aby go edytować. Przykład pliku konfiguracji sieci można znaleźć w [schemacie konfiguracji sieci](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Sprawdź podsieć bramy
W elemencie **VirtualNetworkSites** Dodaj podsieć bramy do sieci wirtualnej, jeśli nie została jeszcze utworzona. Podczas pracy z plikiem konfiguracji sieci podsieć bramy musi mieć nazwę "GatewaySubnet" lub platforma Azure nie może rozpoznać jej jako podsieci bramy ani jej używać.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Przykład:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. Dodaj lokację sieci lokalnej
Dodana lokacja sieci lokalnej reprezentuje sieć wirtualną RM, z którą chcesz nawiązać połączenie. Dodaj element **LocalNetworkSites** do pliku, jeśli taki jeszcze nie istnieje. Na tym etapie konfiguracji VPNGatewayAddress może być dowolnym prawidłowym publicznym adresem IP, ponieważ Brama nie została jeszcze utworzona dla Menedżer zasobów sieci wirtualnej. Po utworzeniu bramy zastępujemy ten symbol zastępczy adres IP poprawnym publicznym adresem IP przypisanym do bramy RM.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Skojarz sieć wirtualną z lokacją sieci lokalnej
W tej sekcji określimy lokację sieci lokalnej, z którą chcesz połączyć sieć wirtualną. W tym przypadku jest to Menedżer zasobów sieci wirtualnej, do której odwołuje się wcześniej. Upewnij się, że nazwy są zgodne. Ten krok nie powoduje utworzenia bramy. Określa sieć lokalną, z którą zostanie nawiązane połączenie z bramą.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Zapisz plik i przekaż go
Zapisz plik, a następnie zaimportuj go do platformy Azure, uruchamiając następujące polecenie. Upewnij się, że ścieżka pliku jest zmieniana w zależności od potrzeb środowiska.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zobaczysz podobny wynik wskazujący, że importowanie zakończyło się pomyślnie.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. Tworzenie bramy

Przed uruchomieniem tego przykładu zapoznaj się z plikiem konfiguracji sieci pobranym dla dokładnych nazw oczekiwanych przez platformę Azure. Plik konfiguracji sieci zawiera wartości dla klasycznych sieci wirtualnych. Czasami nazwy klasycznych sieci wirtualnych są zmieniane w pliku konfiguracji sieciowej podczas tworzenia ustawień klasycznej sieci wirtualnej w Azure Portal z powodu różnic między modelami wdrażania. Na przykład, jeśli użyto Azure Portal do utworzenia klasycznej sieci wirtualnej o nazwie "Klasyczna Sieć wirtualna" i utworzono ją w grupie zasobów o nazwie "ClassicRG", nazwa, która jest zawarta w pliku konfiguracji sieci, jest konwertowana na "Grupa ClassicRG klasycznej sieci wirtualnej". Podczas określania nazwy sieci wirtualnej zawierającej spacje, użyj znaków cudzysłowu wokół wartości.


Użyj poniższego przykładu, aby utworzyć bramę dynamicznego routingu:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stan bramy można sprawdzić za pomocą polecenia cmdlet **Get-azurevnetgateway nastąpi** .

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Sekcja 2 — Konfigurowanie bramy sieci wirtualnej RM



W wymaganiach wstępnych przyjęto, że utworzono już sieć wirtualną RM. W tym kroku utworzysz bramę VPN Gateway dla sieci wirtualnej RM. Nie uruchamiaj tych kroków do momentu pobrania publicznego adresu IP dla bramy klasycznej sieci wirtualnej. 

1. Zaloguj się do konta platformy Azure w konsoli programu PowerShell. Poniższe polecenie cmdlet poprosi o poświadczenia logowania do konta platformy Azure. Po zalogowaniu się zostaną pobrane ustawienia konta, aby były dostępne do Azure PowerShell. Opcjonalnie możesz użyć funkcji "Wypróbuj ją", aby uruchomić Azure Cloud Shell w przeglądarce.

   Jeśli używasz Azure Cloud Shell, Pomiń następujące polecenie cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Aby sprawdzić, czy używasz odpowiedniej subskrypcji, uruchom następujące polecenie cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Utwórz bramę sieci lokalnej. W sieci wirtualnej brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W takim przypadku Brama sieci lokalnej odwołuje się do Twojej klasycznej lokalizacji wirtualnej. Nadaj mu nazwę, za pomocą której platforma Azure może odwoływać się do niej, a także określ prefiks przestrzeni adresowej. Platforma Azure używa prefiksu adresu IP w celu określenia, który ruch danych należy skierować do lokalizacji lokalnej. Jeśli chcesz dostosować tutaj informacje później, przed utworzeniem bramy możesz zmodyfikować wartości i ponownie uruchomić przykład.
   
   **-Name** to nazwa, którą chcesz przypisać, aby odwołać się do bramy sieci lokalnej.<br>
   **-AddressPrefix** jest przestrzenią adresową dla klasycznej sieci wirtualnej.<br>
   **-GatewayIpAddress** to publiczny adres IP bramy klasycznej sieci wirtualnej. Pamiętaj, aby zmienić następujący przykładowy tekst "n. n. n. n" w celu odzwierciedlenia poprawnego adresu IP.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Zażądaj przydzielenia publicznego adresu IP do bramy sieci wirtualnej dla Menedżer zasobów wirtualnej. Nie można określić adresu IP, którego chcesz użyć. Adres IP jest przydzielany dynamicznie do bramy sieci wirtualnej. Nie oznacza to jednak, że adres IP się zmienia. Jedyna zmiana adresu IP bramy sieci wirtualnej polega na tym, że brama została usunięta i ponownie utworzona. Nie zmienia się w przypadku zmiany rozmiarów, resetowania ani innych wewnętrznych czynności konserwacyjnych/uaktualnień bramy.

   W tym kroku ustawimy również zmienną używaną w późniejszym kroku.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Sprawdź, czy sieć wirtualna ma podsieć bramy. Jeśli nie istnieje podsieć bramy, Dodaj ją. Upewnij się, że podsieć bramy ma nazwę *GatewaySubnet*.
5. Pobierz podsieć używaną przez bramę, uruchamiając następujące polecenie. W tym kroku ustawimy również zmienną, która będzie używana w następnym kroku.
   
   **-Name** to nazwa sieci wirtualnej Menedżer zasobów.<br>
   **-ResourceGroupName** to grupa zasobów, z którą skojarzona jest sieć wirtualna. Podsieć bramy musi już istnieć dla tej sieci wirtualnej i musi mieć nazwę *GatewaySubnet* , aby działać prawidłowo.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Utwórz konfigurację adresowania IP bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   W tym kroku parametry **-SubnetId** i **-PublicIpAddressId** muszą zostać przesłane odpowiednio do właściwości ID z podsieci i obiektów adresów IP. Nie można użyć prostego ciągu. Te zmienne są ustawiane w kroku, aby zażądać publicznego adresu IP i kroku, aby pobrać podsieć.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Utwórz bramę sieci wirtualnej Menedżer zasobów, uruchamiając następujące polecenie. `-VpnType`Musi być *RouteBased*. Utworzenie bramy może potrwać 45 minut lub dłużej.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Skopiuj publiczny adres IP po utworzeniu bramy sieci VPN. Jest ona używana podczas konfigurowania ustawień sieci lokalnej dla klasycznej platformy wirtualnej. Aby pobrać publiczny adres IP, można użyć następującego polecenia cmdlet. Publiczny adres IP jest wymieniony w polu zwraca jako *adres*IP.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Sekcja 3 — Modyfikowanie ustawień klasycznej lokacji lokalnej sieci wirtualnej

W tej sekcji można korzystać z klasycznej sieci wirtualnej. Zastępczy adres IP, który został użyty podczas określania ustawień lokacji lokalnej, który zostanie użyty do nawiązania połączenia z bramą sieci wirtualnej Menedżer zasobów. Ze względu na to, że pracujesz z klasyczną siecią wirtualną, użyj programu PowerShell lokalnie zainstalowanej na komputerze, a nie Azure Cloud Shell TryIt.

1. Wyeksportuj plik konfiguracji sieci.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Za pomocą edytora tekstów zmodyfikuj wartość dla VPNGatewayAddress. Zamień zastępczy adres IP na publiczny adres IP bramy Menedżer zasobów, a następnie Zapisz zmiany.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Zaimportuj zmodyfikowany plik konfiguracji sieci na platformę Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Sekcja 4 — Tworzenie połączenia między bramami
Utworzenie połączenia między bramami wymaga programu PowerShell. Może być konieczne dodanie konta platformy Azure w celu użycia klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, użyj polecenie **Add-AzureAccount**.

1. W konsoli programu PowerShell Ustaw klucz współużytkowany. Przed uruchomieniem poleceń cmdlet programu zapoznaj się z pobranym plikiem konfiguracji sieci dla dokładnych nazw oczekiwanych przez platformę Azure. Gdy określasz nazwę sieci wirtualnej zawierającej spacje, użyj znaków pojedynczego cudzysłowu wokół wartości.<br><br>W poniższym przykładzie **-VNetName** jest nazwą klasycznej sieci wirtualnej i **-LocalNetworkSiteName** jest nazwą określoną dla lokalnej lokacji sieciowej. **-SharedKey** jest wartością wygenerowaną i określoną przez użytkownika. W przykładzie użyto elementu "abc123", ale można wygenerować i użyć bardziej złożonej. Ważną kwestią jest to, że wartość określona w tym miejscu musi być taka sama jak wartość określona w następnym kroku podczas tworzenia połączenia. Wartość zwracana powinna wskazywać **stan: powodzenie**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Utwórz połączenie sieci VPN, uruchamiając następujące polecenia:
   
   Ustaw zmienne.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Utwórz połączenie. Należy zauważyć, że **-ConnectionType** jest IPSec, nie Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Sekcja 5 — Weryfikowanie połączeń

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej z siecią wirtualną Menedżer zasobów

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z sieci wirtualnej Menedżer zasobów do klasycznej sieci wirtualnej

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
