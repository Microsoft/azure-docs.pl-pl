---
title: Samouczek — Równoważenie obciążenia maszyn wirtualnych z systemem Windows na platformie Azure
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell utworzyć moduł równoważenia obciążenia dla bezpiecznej aplikacji o wysokiej dostępności na 3 maszynach wirtualnych z systemem Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9d33944e046e1e5e2324f73ae26c78cf29d8f97d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734717"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Samouczek: równoważenie obciążenia maszyn wirtualnych z systemem Windows na platformie Azure w celu utworzenia aplikacji o wysokiej dostępności za pomocą programu Azure PowerShell
Równoważenie obciążenia zwiększa dostępność dzięki rozdzieleniu żądań przychodzących pomiędzy wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki modułu równoważenia obciążenia platformy Azure, które dystrybuują ruch i zapewniają wysoką dostępność. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie modułu równoważenia obciążenia na platformie Azure
> * Tworzenie sondy kondycji modułu równoważenia obciążenia
> * Tworzenie reguł ruchu modułu równoważenia obciążenia
> * Tworzenie prostej witryny internetowej usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyn wirtualnych i dołączanie ich do modułu równoważenia obciążenia
> * Wyświetlanie działającego modułu równoważenia obciążenia
> * Dodawanie i usuwanie maszyn wirtualnych w module równoważenia obciążenia

## <a name="azure-load-balancer-overview"></a>Omówienie modułu równoważenia obciążenia platformy Azure
Moduł równoważenia obciążenia platformy Azure jest modułem w warstwie 4 (TCP, UDP), który zapewnia wysoką dostępność, rozkładając ruch przychodzący na maszyny wirtualne w dobrej kondycji. Sonda kondycji modułu równoważenia obciążenia monitoruje określony port na każdej maszynie wirtualnej i dystrybuuje ruch tylko do działającej maszyny wirtualnej.

Zdefiniuj konfigurację IP frontonu z co najmniej jednym publicznym adresem IP. Ta konfiguracja frontonu zapewnia dostęp do aplikacji i modułu równoważenia obciążenia za pośrednictwem Internetu. 

Maszyny wirtualne łączą się z modułem równoważenia obciążenia za pośrednictwem wirtualnej karty sieciowej. Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia.

Aby sterować przepływem ruchu, zdefiniuj reguły równoważenia obciążenia dla poszczególnych portów i protokołów mapowanych na Twoje maszyny wirtualne.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania poszczególnych składników modułu równoważenia obciążenia. Zanim będzie można utworzyć moduł równoważenia obciążenia, należy utworzyć grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupLoadBalancer* w lokalizacji *Wschodnie* :

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Utwórz publiczny adres IP przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). W poniższym przykładzie zostanie utworzony publiczny adres IP o nazwie *myPublicIP* w grupie zasobów *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Utwórz pulę adresów IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład obejmuje tworzenie puli adresów IP frontonu o nazwie *myFrontEndPool* i dołączanie do niej adresu *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Utwórz pulę adresów zaplecza przy użyciu polecenia [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. Poniższy przykład tworzy pulę adresów zaplecza o nazwie *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Następnie utwórz moduł równoważenia obciążenia przy użyciu polecenia [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). W poniższym przykładzie zostanie utworzony moduł równoważenia obciążenia o nazwie *myLoadBalancer*, korzystający z pul adresów IP frontonu i zaplecza utworzonych w poprzednich krokach:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Domyślnie maszyna wirtualna jest wykluczana z dystrybucji ruchu przez moduł równoważenia obciążenia, jeśli dwie kolejne próby podejmowane w 15-sekundowych odstępach zakończą się niepowodzeniem. Sonda kondycji jest tworzona z użyciem protokołu lub konkretnej strony kontroli kondycji aplikacji. 

W poniższym przykładzie zostanie utworzona sonda TCP. Możesz także tworzyć niestandardowe sondy HTTP na potrzeby bardziej szczegółowych kontroli kondycji. W przypadku użycia niestandardowej sondy HTTP należy utworzyć stronę kontroli kondycji, na przykład *healthcheck.aspx*. Aby dany host pozostał w rotacji, sonda musi zwrócić do modułu równoważenia obciążenia kod odpowiedzi **HTTP 200 OK**.

Aby utworzyć sondę kondycji TCP, użyj polecenia [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe* monitorująca poszczególne maszyny wirtualne przy użyciu portu *TCP**80*:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Aby zastosować sondę kondycji, zaktualizuj moduł równoważenia obciążenia przy użyciu polecenia [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby mieć pewność, że ruch będzie kierowany tylko do maszyn wirtualnych w dobrej kondycji, zdefiniuj również sondę kondycji do użycia.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). W poniższym przykładzie zostanie utworzona reguła modułu równoważenia obciążenia o nazwie *myLoadBalancerRule* w celu równoważenia obciążenia na porcie *TCP**80*:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Zaktualizuj moduł równoważenia obciążenia przy użyciu polecenia [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej
Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej. Więcej informacji na temat sieci wirtualnych zawiera samouczek [Manage Azure Virtual Networks (Zarządzanie sieciami wirtualnymi platformy Azure)](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Wirtualne karty sieciowe można utworzyć przy użyciu polecenia [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). W poniższym przykładzie zostaną utworzone trzy wirtualne karty sieciowe. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych
Aby poprawić wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie trzech maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Job`. Utworzenie i skonfigurowanie wszystkich trzech maszyn wirtualnych może potrwać kilka minut.


### <a name="install-iis-with-custom-script-extension"></a>Instalowanie usług IIS za pomocą rozszerzenia niestandardowego skryptu
W poprzednim samouczku dotyczącym [dostosowywania maszyny wirtualnej z systemem Windows](tutorial-automate-vm-deployment.md) przedstawiono proces automatyzowania dostosowywania maszyny wirtualnej przy użyciu rozszerzenia niestandardowego skryptu dla systemu Windows. Tej samej metody można użyć do zainstalowania i skonfigurowania usług IIS na maszynach wirtualnych.

Zainstaluj rozszerzenie niestandardowego skryptu przy użyciu polecenia [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). To rozszerzenie uruchamia polecenie `powershell Add-WindowsFeature Web-Server`, aby zainstalować serwer internetowy usług IIS, a następnie aktualizuje stronę *Default.htm* w celu wyświetlenia nazwy hosta maszyny wirtualnej:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testowanie modułu równoważenia obciążenia
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona witryna internetowa z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Działająca witryna internetowa usług IIS](./media/tutorial-load-balancer/running-iis-website.png)

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między trzy maszyny wirtualne używane przez aplikację, możesz wymusić odświeżenie w przeglądarce internetowej.


## <a name="add-and-remove-vms"></a>Dodawanie i usuwanie maszyn wirtualnych
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. W tej sekcji pokazano, jak usunąć lub dodać maszyny wirtualne w module równoważenia obciążenia.

### <a name="remove-a-vm-from-the-load-balancer"></a>Usuwanie maszyny wirtualnej z modułu równoważenia obciążenia
Przejdź do karty sieciowej przy użyciu polecenia [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface), a następnie ustaw właściwość *LoadBalancerBackendAddressPools* wirtualnej karty sieciowej na *$null*. Na koniec zaktualizuj wirtualną kartę sieciową:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między pozostałe dwie maszyny wirtualne używane przez aplikację, możesz wymusić odświeżenie w przeglądarce internetowej. Możesz teraz wykonać czynności konserwacyjne na maszynie wirtualnej, na przykład zainstalować aktualizacje systemu operacyjnego lub ponownie uruchomić maszynę wirtualną.

### <a name="add-a-vm-to-the-load-balancer"></a>Dodawanie maszyny wirtualnej do modułu równoważenia obciążenia
Gdy przeprowadzisz już konserwację maszyny wirtualnej lub jeśli wymagane jest zwiększenie pojemności, ustaw właściwość *LoadBalancerBackendAddressPools* wirtualnej karty sieciowej na *BackendAddressPool* z poziomu polecenia [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

Przejdź do modułu równoważenia obciążenia:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem utworzono moduł równoważenia obciążenia i dołączono do niego maszyny wirtualne. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie modułu równoważenia obciążenia na platformie Azure
> * Tworzenie sondy kondycji modułu równoważenia obciążenia
> * Tworzenie reguł ruchu modułu równoważenia obciążenia
> * Tworzenie prostej witryny internetowej usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyn wirtualnych i dołączanie ich do modułu równoważenia obciążenia
> * Wyświetlanie działającego modułu równoważenia obciążenia
> * Dodawanie i usuwanie maszyn wirtualnych w module równoważenia obciążenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać siecią maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi i sieciami wirtualnymi](./tutorial-virtual-network.md)
