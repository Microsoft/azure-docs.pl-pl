---
title: 'Samouczek: Tworzenie & zarządzanie zestawem skalowania maszyn wirtualnych platformy Azure — Azure PowerShell'
description: Dowiedz się, jak za pomocą programu Azure PowerShell utworzyć zestaw skalowania maszyn wirtualnych oraz wykonywać niektóre typowe zadania zarządzania, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie pojemności zestawu skalowania.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 102ab619a3f341658383dc4997f9fbfc2cd12a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519184"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Samouczek: tworzenie zestawu skalowania maszyn wirtualnych i zarządzanie nim przy użyciu programu Azure PowerShell

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu skalowania maszyn wirtualnych i nawiązywanie połączenia z nim
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów wystąpień maszyn wirtualnych
> * Ręczne skalowanie zestawu skalowania
> * Wykonywanie typowych zadań zarządzania zestawem skalowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed utworzeniem zestawu skalowania maszyn wirtualnych. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). W tym przykładzie grupa zasobów o nazwie Moja *zasobów* jest tworzona w regionie *wschodnim* . 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Nazwa grupy zasobów jest podawana podczas tworzenia lub modyfikowania zestawu skalowania w różnych miejscach tego samouczka.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Najpierw ustaw nazwę użytkownika i hasło administratora wystąpień maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut.

> [!IMPORTANT]
> Jeśli nie można nawiązać połączenia z zestawem skalowania, może być konieczne utworzenie sieciowej grupy zabezpieczeń przez dodanie parametru *[-SecurityGroupName "WebSecurity"](/powershell/module/az.compute/new-azvmss)* .


## <a name="view-the-vm-instances-in-a-scale-set"></a>Wyświetlanie wystąpień maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenia [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) w następujący sposób:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Następujące przykładowe dane wyjściowe zawierają dwa wystąpienia maszyn wirtualnych w zestawie skalowania:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny wirtualnej, dodaj parametr `-InstanceId` do polecenia [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). W poniższym przykładzie są widoczne informacje o wystąpieniu maszyny wirtualnej *1*:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Wyświetlanie informacji o połączeniu
Do modułu równoważenia obciążenia, który kieruje ruch do poszczególnych wystąpień maszyn wirtualnych, jest przypisany publiczny adres IP. Domyślnie do modułu równoważenia obciążenia platformy Azure, który przesyła dalej ruch połączenia zdalnego na danym porcie do poszczególnych maszyn wirtualnych, są dodawane reguły translatora adresów sieciowych (NAT). Aby nawiązać połączenie z wystąpieniami maszyn wirtualnych w zestawie skalowania, musisz utworzyć połączenie zdalne z przypisanym publicznym adresem IP za pośrednictwem określonego numeru portu.

Aby wyświetlić listę portów NAT umożliwiających połączenie z wystąpieniami maszyn wirtualnych w zestawie skalowania, najpierw pobierz obiekt modułu równoważenia obciążenia za pomocą polecenia [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Następnie Wyświetl reguły NAT dla ruchu przychodzącego za pomocą [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Następujące przykładowe dane wyjściowe zawierają nazwę wystąpienia, publiczny adres IP modułu równoważenia obciążenia oraz numer portu, do którego reguły NAT kierują ruch:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Nazwa reguły w kolumnie *Name* (Nazwa) jest zgodna z nazwą wystąpienia maszyny wirtualnej wyświetloną w poprzednim poleceniu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Na przykład aby nawiązać połączenie z wystąpieniem maszyny wirtualnej *0*, użyj nazwy *myScaleSet3389.0* i portu *50001*. Aby nawiązać połączenie z wystąpieniem maszyny wirtualnej *1*, użyj wartości *myScaleSet3389.1* i portu *50002*. Aby korzystać z komunikacji zdalnej programu PowerShell, musisz połączyć się z odpowiednią regułą wystąpienia maszyny wirtualnej dotyczącą portu *TCP**5985*.

Publiczny adres IP modułu równoważenia obciążenia możesz wyświetlić za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Przykładowe dane wyjściowe:

```powershell
IpAddress
---------
52.168.121.216
```

Utwórz połączenie zdalne z pierwszym wystąpieniem maszyny wirtualnej. Podaj swój publiczny adres IP i numer portu wymaganego wystąpienia maszyny wirtualnej, jak pokazano w poprzednich poleceniach. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia zestawu skalowania (domyślnie w przykładowych poleceniach, *azureuser* i *P \@ ssw0rd!*). Jeśli korzystasz z usługi Azure Cloud Shell, wykonaj ten krok przy użyciu wiersza polecenia lokalnego programu PowerShell lub klienta pulpitu zdalnego. W poniższym przykładzie nawiązano połączenie z wystąpieniem maszyny wirtualnej *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Po zalogowaniu do wystąpienia maszyny wirtualnej możesz ręcznie wprowadzić zmiany w konfiguracji zgodnie z potrzebami. Na razie zamknij połączenie zdalne.


## <a name="understand-vm-instance-images"></a>Opis obrazów wystąpień maszyn wirtualnych
Witryna Azure Marketplace zawiera wiele obrazów, za pomocą których można tworzyć wystąpienia maszyn wirtualnych. Aby wyświetlić listę dostępnych wydawców, użyj polecenia [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Aby wyświetlić listę obrazów danego wydawcy, użyj polecenia [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). Listę obrazów można także filtrować za pomocą argumentów `-PublisherName` lub `-Offer`. Poniższa przykładowa lista została odfiltrowana w celu wyświetlenia wszystkich obrazów z nazwą wydawcy *MicrosoftWindowsServer* i ofertą *WindowsServer*:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

Następujące przykładowe dane wyjściowe zawierają wszystkie dostępne obrazy systemu Windows Server:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Podczas tworzenia zestawu skalowania na początku tego samouczka dla wystąpień maszyn wirtualnych został użyty domyślny obraz maszyny wirtualnej z systemem *Windows Server 2016 DataCenter*. Można wskazać inny obraz maszyny wirtualnej na podstawie danych wyjściowych polecenia [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). W poniższym przykładzie zostanie utworzony zestaw skalowania z parametrem `-ImageName` umożliwiającym wskazanie obrazu maszyny wirtualnej *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Nie trzeba wdrażać następującego zestawu skalowania, ponieważ utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> Zalecamy użycie *najnowszej* wersji obrazu. Określ wartość "Najnowsza", aby użyć najnowszej wersji obrazu dostępnej w czasie wdrażania. Uwaga nawet jeśli używasz "Najnowsza", obraz maszyny wirtualnej nie zostanie automatycznie zaktualizowany po wdrożeniu, nawet jeśli zostanie udostępniona nowa wersja.

## <a name="understand-vm-instance-sizes"></a>Opis rozmiarów wystąpień maszyn wirtualnych
Rozmiar wystąpienia maszyny wirtualnej, czyli *jednostka SKU*, określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla wystąpienia maszyny wirtualnej. Wystąpienia maszyn wirtualnych w zestawie skalowania muszą mieć rozmiary odpowiednie do oczekiwanych obciążeń.

### <a name="vm-instance-sizes"></a>Rozmiary wystąpień maszyn wirtualnych
W poniższej tabeli przedstawiono typowe kategorie rozmiarów maszyn wirtualnych podzielone według przypadków użycia.

| Typ                     | Typowe rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Ogólnego przeznaczenia](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0–7| Zrównoważona moc procesora CPU w stosunku do pamięci. Opcja idealna w przypadku tworzenia i testowania, małych i średnich aplikacji oraz rozwiązań dotyczących danych.  |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/sizes-compute.md)   | Fs, F             | Duża moc procesora CPU w stosunku do pamięci. Opcja dobra w przypadku aplikacji o średnim ruchu, urządzeń sieciowych i procesów wsadowych.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Duża ilość pamięci na rdzeń. Opcja bardzo dobra w przypadku relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../virtual-machines/sizes-storage.md)      | Ls                | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Maszyny wirtualne wyspecjalizowane pod kątem intensywnego renderowania grafiki i edytowania materiałów wideo.       |
| [Wysoka wydajność](../virtual-machines/sizes-hpc.md) | H, A8-11          | Maszyny wirtualne z najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Znajdowanie dostępnych rozmiarów wystąpień maszyn wirtualnych
Aby wyświetlić listę rozmiarów wystąpień maszyn wirtualnych dostępnych w danym regionie, użyj polecenia [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

Rezultat jest podobny do poniższego, skróconego przykładu, który pokazuje zasoby przypisane do każdego rozmiaru maszyny wirtualnej:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Podczas tworzenia zestawu skalowania na początku tego samouczka dla wystąpień maszyn wirtualnych została użyta domyślna jednostka SKU maszyny wirtualnej *Standard_DS1_v2*. Można wskazać inny rozmiar wystąpienia maszyny wirtualnej na podstawie danych wyjściowych polecenia [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). W poniższym przykładzie zostanie utworzony zestaw skalowania z parametrem `-VmSize` umożliwiającym wskazanie rozmiaru wystąpienia maszyny wirtualnej *Standard_F1*. Nie trzeba wdrażać następującego zestawu skalowania, ponieważ utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Utworzony zestaw skalowania obejmował dwa wystąpienia maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w zestawie skalowania, można ręcznie zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę wystąpień maszyn wirtualnych, a następnie konfiguruje moduł równoważenia obciążenia w celu dystrybucji ruchu.

Najpierw utwórz obiekt zestawu skalowania za pomocą polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss), a następnie podaj nową wartość parametru `sku.capacity`. Aby zastosować zmiany pojemności, użyj polecenia [Update-AzVmss](/powershell/module/az.compute/update-azvmss). W poniższym przykładzie liczba wystąpień maszyn wirtualnych w zestawie skalowania jest ustawiana na *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Aby wyświetlić aktualną liczbę wystąpień w zestawie skalowania, użyj polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

W następujących przykładowych danych wyjściowych widać, że pojemność zestawu skalowania wynosi *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Typowe zadania zarządzania
Potrafisz już utworzyć zestaw skalowania, wyświetlić informacje o połączeniu i połączyć się z wystąpieniami maszyn wirtualnych. W tym samouczku omówiliśmy używanie innego obrazu systemu operacyjnego dla wystąpień maszyn wirtualnych, wybieranie innego rozmiaru maszyny wirtualnej oraz ręczne skalowanie liczby wystąpień. Typowe operacje zarządzania obejmują uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Zatrzymywanie i cofanie przydziału wystąpień maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. W następującym przykładzie zostaje zatrzymane wystąpienie *1*:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Domyślnie następuje cofnięcie przydziału zatrzymanych maszyn wirtualnych, co sprawia, że opłaty za operacje obliczeniowe nie są naliczane. Jeśli zatrzymana maszyna wirtualna ma zachować stan Aprowizowano, dodaj parametr `-StayProvisioned` do poprzedniego polecenia. W przypadku zatrzymanych maszyn wirtualnych ze stanem Aprowizowano są naliczane opłaty za operacje obliczeniowe.

### <a name="start-vm-instances-in-a-scale-set"></a>Uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. W następującym przykładzie zostaje uruchomione wystąpienie *1*:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Ponowne uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Retart-AzVmss](/powershell/module/az.compute/restart-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. W następującym przykładzie zostaje uruchomione ponownie wystąpienie *1*:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Usunięcie grupy zasobów powoduje również usunięcie wszystkich znajdujących się w niej zasobów, takich jak wystąpienia maszyn wirtualnych, sieć wirtualna i dyski. Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono wykonywanie niektórych podstawowych zadań w zakresie tworzenia zestawu skalowania i zarządzania nim przy użyciu programu Azure PowerShell:

> [!div class="checklist"]
> * Tworzenie zestawu skalowania maszyn wirtualnych i nawiązywanie połączenia z nim
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Ręczne skalowanie zestawu skalowania
> * Wykonywanie typowych zadań zarządzania zestawem skalowania

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat dysków zestawów skalowania.

> [!div class="nextstepaction"]
> [Korzystanie z dysków danych z zestawami skalowania](tutorial-use-disks-powershell.md)
