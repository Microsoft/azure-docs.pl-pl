---
title: Samouczek — używanie niestandardowego obrazu maszyny wirtualnej w zestawie skalowania z Azure PowerShell
description: Dowiedz się, jak za pomocą programu Azure PowerShell utworzyć niestandardowy obraz maszyny wirtualnej, którego można użyć do wdrożenia zestawu skalowania maszyny wirtualnej
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: daef03b411a451fc3e5b73e46091672810b0f9bd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76278293"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem programu Azure PowerShell

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej ze źródłowej maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Tworzenie i konfigurowanie źródłowej maszyny wirtualnej

>[!NOTE]
> Ten samouczek zawiera opis kroków procesu tworzenia i używania obrazu uogólnionej maszyny wirtualnej. Tworzenie zestawu skalowania na podstawie wyspecjalizowanego wirtualnego dysku twardego nie jest obsługiwane.

Najpierw utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), a następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Ta maszyna wirtualna będzie używana jako źródło dla niestandardowego obrazu maszyny wirtualnej. Poniższy przykład obejmuje tworzenie maszyny wirtualnej *myCustomVM* w grupie zasobów *myResourceGroup*. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które będą używane jako poświadczenia logowania dla maszyny wirtualnej:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Aby nawiązać połączenie z maszyną wirtualną, wyświetl publiczny adres IP za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) w następujący sposób:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Utwórz połączenie zdalne z maszyną wirtualną. Jeśli korzystasz z usługi Azure Cloud Shell, wykonaj ten krok przy użyciu wiersza polecenia lokalnego programu PowerShell lub klienta pulpitu zdalnego. Podaj własny adres IP z poprzedniego polecenia. Po wyświetleniu monitu wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej w pierwszym kroku:

```powershell
mstsc /v:<IpAddress>
```

Aby dostosować maszynę wirtualną, należy zainstalować podstawowy serwer internetowy. Dzięki temu wdrożona maszyna wirtualna będzie miała wszystkie pakiety wymagane do uruchamiania aplikacji internetowej. Otwórz wiersz polecenia lokalnego programu PowerShell na maszynie wirtualnej i zainstaluj serwer internetowy IIS za pomocą polecenia [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) w następujący sposób:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ostatnim krokiem jest uogólnienie maszyny wirtualnej w celu przygotowania jej do użycia jako obraz niestandardowy. Program Sysprep usuwa wszystkie informacje i konfiguracje osobiste oraz resetuje maszynę wirtualną do czystego stanu przeznaczonego do przyszłych wdrożeń. Aby uzyskać więcej informacji, zobacz [How to Use Sysprep: An Introduction (Używanie programu Sysprep: wprowadzenie)](https://technet.microsoft.com/library/bb457073.aspx).

Aby uogólnić maszynę wirtualną, uruchom program Sysprep i ustaw maszynę wirtualną jako gotową do użycia. Po zakończeniu zamknij maszynę wirtualną za pomocą programu Sysprep:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Gdy program Sysprep zakończy proces i zamknie maszynę wirtualną, połączenie zdalne z maszyną wirtualną zostanie zamknięte automatycznie.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej ze źródłowej maszyny wirtualnej
Źródłowa maszyna wirtualna została dostosowana przez zainstalowanie serwera internetowego IIS. Utworzymy niestandardowy obraz maszyny wirtualnej, który będzie używany z zestawem skalowania.

Aby można było utworzyć obraz, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej za pomocą polecenia [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Następnie ustaw dla maszyny wirtualnej stan uogólniony za pomocą polecenia [Set-AzVm](/powershell/module/az.compute/set-azvm), aby poinformować platformę Azure, że maszyna wirtualna jest gotowa do użycia jako obraz niestandardowy. Obraz można utworzyć tylko za pomocą uogólnionej maszyny wirtualnej:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Cofanie przydziału i uogólnianie maszyny wirtualnej może potrwać kilka minut.

Utwórz obraz maszyny wirtualnej za pomocą poleceń [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) i [New-AzImage](/powershell/module/az.compute/new-azimage). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```

## <a name="configure-the-network-security-group-rules"></a>Skonfiguruj reguły sieciowej grupy zabezpieczeń
Przed utworzeniem zestawu skalowania należy skonfigurować reguły kojarzenia sieciowych grup zabezpieczeń, aby zezwolić na dostęp do protokołu HTTP, RDP i komunikacji zdalnej. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$rule2 = New-AzNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389

$rule3 = New-AzNetworkSecurityRuleConfig -Name remoting-rule -Description "Allow PS Remoting" -Access Allow -Protocol Tcp -Direction Inbound -Priority 120 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 5985

New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName "myResourceGroup" -Location "EastUS" -SecurityRules $rule1,$rule2,$rule3
```

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Tworzenie zestawu skalowania z niestandardowego obrazu maszyny wirtualnej
Utwórz zestaw skalowania za pomocą polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss) z parametrem `-ImageName` umożliwiającym zdefiniowanie niestandardowego obrazu maszyny wirtualnej utworzonego w poprzednim kroku. Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNSG"
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa zestaw skalowania, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) w następujący sposób:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona domyślna strona serwera IIS, jak pokazano w poniższym przykładzie:

![Serwer IIS uruchomiony za pomocą niestandardowego obrazu maszyny wirtualnej](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem programu Azure PowerShell:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-powershell.md)
