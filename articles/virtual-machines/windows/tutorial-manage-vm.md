---
title: Samouczek — Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą Azure PowerShell
description: Z tego samouczka dowiesz się, jak używać programu Azure PowerShell w celu tworzenia maszyn wirtualnych z systemem Windows na platformie Azure i zarządzania nimi.
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ae298562b5d3306cf6c20b4c2fb7b378ba99c986
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561623"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Samouczek: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą programu Azure PowerShell

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano podstawowe zadania wdrożenia maszyny wirtualnej platformy Azure, takie jak wybieranie rozmiaru i obrazu maszyny wirtualnej oraz wdrażanie maszyny wirtualnej. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. W poniższym przykładzie grupa zasobów o nazwie *myResourceGroupVM* zostanie utworzona w regionie *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Grupa zasobów jest określana podczas tworzenia lub modyfikowania maszyn wirtualnych, co zostało przedstawione w tym samouczku.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Podczas tworzenia maszyny wirtualnej masz dostęp do kilku opcji, takich jak obraz systemu operacyjnego, konfiguracja sieci i poświadczenia administracyjne. W tym przykładzie jest tworzona maszyna wirtualna o nazwie *myVM* z uruchomioną domyślną wersją systemu Windows Server 2016 Datacenter.

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Łączenie z maszyną wirtualną

Po zakończeniu wdrożenia utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

Uruchom następujące polecenia, aby zwrócić publiczny adres IP maszyny wirtualnej. Zapisz ten adres IP, aby połączyć się z nim w przeglądarce w celu przetestowania połączenia z siecią Web w przyszłym kroku.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Użyj następującego polecenia na maszynie lokalnej, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zastąp adres IP adresem *publicIPAddress* maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia maszyny wirtualnej.

```powershell
mstsc /v:<publicIpAddress>
```

W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika i hasło utworzone dla maszyny wirtualnej, a następnie kliknij przycisk **OK**.

## <a name="understand-marketplace-images"></a>Informacje o obrazach w portalu Marketplace

Witryna Azure Marketplace zawiera wiele obrazów, za pomocą których można utworzyć nową maszynę wirtualną. W poprzednich krokach utworzono maszynę wirtualną przy użyciu obrazu systemu Windows Server 2016 Datacenter. W tym kroku moduł PowerShell jest używany do wyszukiwania w witrynie Marketplace innych obrazów systemu Windows, które mogą być również używane jako podstawa nowych maszyn wirtualnych. Proces ten składa się z wyszukiwania wydawcy, oferty, jednostki SKU i opcjonalnie numeru wersji w celu [zidentyfikowania](cli-ps-findimage.md#terminology) obrazu.

Użyj polecenia [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), aby uzyskać listę wydawców obrazów:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Użyj polecenia [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer), aby uzyskać listę ofert obrazów. To polecenie umożliwia filtrowanie zwracanej listy według określonego wydawcy o nazwie `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Wyniki będą wyglądać podobnie do następującego przykładu: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

Polecenie [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) spowoduje następnie odfiltrowanie według nazwy wydawcy i oferty w celu uzyskania listy nazw obrazów.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Wyniki będą wyglądać podobnie do następującego przykładu: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Te informacje mogą być używane na potrzeby wdrażania maszyny wirtualnej za pomocą określonego obrazu. W tym przykładzie wdrażasz maszynę wirtualną przy użyciu obrazu systemu Windows Server 2016 z kontenerami.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Get-Job`.

## <a name="understand-vm-sizes"></a>Omówienie rozmiarów maszyn wirtualnych

Rozmiar maszyny wirtualnej określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla maszyny wirtualnej. Tworzone maszyny wirtualne powinny mieć rozmiar odpowiadający obciążeniu. Jeśli obciążenie zwiększy się, można również zmienić rozmiar istniejącej maszyny wirtualnej.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W poniższej tabeli przedstawiono kategorie rozmiarów podzielone według przypadków użycia.  

| Typ                     | Typowe rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Ogólnego przeznaczenia](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Zrównoważona moc procesora CPU w stosunku do pamięci. Opcja idealna w przypadku tworzenia i testowania, małych i średnich aplikacji oraz rozwiązań dotyczących danych.  |
| [Optymalizacja pod kątem obliczeń](../sizes-compute.md)   | Fsv2          | Duża moc procesora CPU w stosunku do pamięci. Opcja dobra w przypadku aplikacji o średnim ruchu, urządzeń sieciowych i procesów wsadowych.        |
| [Optymalizacja pod kątem pamięci](../sizes-memory.md)    | Esv3, EV3, M, DSv2, Dv2  | Duża ilość pamięci na rdzeń. Opcja bardzo dobra w przypadku relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../sizes-storage.md)      | Lsv2, LS              | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Maszyny wirtualne wyspecjalizowane pod kątem intensywnego renderowania grafiki i edytowania materiałów wideo.       |
| [Wysoka wydajność](../sizes-hpc.md) | H        | Maszyny wirtualne z najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). |

### <a name="find-available-vm-sizes"></a>Wyszukiwanie dostępnych rozmiarów maszyn wirtualnych

Aby wyświetlić listę dostępnych rozmiarów maszyn wirtualnych w danym regionie, użyj polecenia [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize).

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej

Po wdrożeniu maszyny wirtualnej można zmienić jej rozmiar w celu zwiększenia lub zmniejszenia alokacji zasobów.

Przed zmianą rozmiaru maszyny wirtualnej sprawdź, czy wybrany rozmiar jest dostępny w bieżącym klastrze maszyny wirtualnej. Polecenie [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) zwraca listę rozmiarów.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Jeśli rozmiar jest dostępny, można go zmienić dla włączonej maszyny wirtualnej, ale zostanie ona ponownie uruchomiona w trakcie tej operacji.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Jeśli wybrany rozmiar jest niedostępny w bieżącym klastrze, przed rozpoczęciem operacji zmiany rozmiaru należy cofnąć przydział maszyny wirtualnej. Cofnięcie przydziału maszyny wirtualnej spowoduje usunięcie wszystkich danych na dysku tymczasowym, a publiczny adres IP zmieni się, chyba że jest używany statyczny adres IP.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Stany zasilania maszyny wirtualnej

Maszyna wirtualna platformy Azure może znajdować się w jednym z wielu stanów zasilania. 


| Stan zasilania | Opis
|----|----|
| Uruchamianie | Maszyna wirtualna jest uruchamiana. |
| Uruchomienie | Maszyna wirtualna została uruchomiona. |
| Zatrzymywanie | Maszyna wirtualna jest zatrzymywana. |
| Zatrzymano | Maszyna wirtualna została zatrzymana. Opłaty za operacje obliczeniowe są także naliczane w przypadku maszyn wirtualnych w stanie Zatrzymano.  |
| Cofanie przydziału | Przydział maszyny wirtualnej jest cofany. |
| Cofnięto przydział | Wskazuje, że maszyna wirtualna została usunięta z funkcji hypervisor, ale jest nadal dostępna na płaszczyźnie kontroli. Opłaty za operacje obliczeniowe nie są naliczane w przypadku maszyn wirtualnych w stanie `Deallocated`. |
| - | Stan zasilania maszyny wirtualnej jest nieznany. |


Aby pobrać stan określonej maszyny wirtualnej, użyj polecenia [Get-AzVM](/powershell/module/az.compute/get-azvm). Pamiętaj, aby określić prawidłową nazwę maszyny wirtualnej i grupy zasobów.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Dane wyjściowe będą wyglądać podobnie do następującego przykładu:

```powershell
Status
------
PowerState/running
```

Aby pobrać stan mocy wszystkich maszyn wirtualnych w ramach subskrypcji, użyj opcji [Virtual Machines wszystkie interfejsy API](/rest/api/compute/virtualmachines/listall) z parametrem **statusOnly** na *wartość true*.

## <a name="management-tasks"></a>Zadania zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć skrypty służące do automatyzowania zadań powtarzających się lub złożonych. Dzięki użyciu programu Azure PowerShell wiele typowych zadań zarządzania można uruchamiać z poziomu wiersza polecenia lub w skryptach.

### <a name="stop-a-vm"></a>Zatrzymywanie maszyny wirtualnej

Do zatrzymywania maszyny wirtualnej i cofania jej przydziału służy polecenie [Stop-AzVM](/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Jeśli maszyna wirtualna ma zachować stan Aprowizowano, użyj parametru -StayProvisioned.

### <a name="start-a-vm"></a>Uruchamianie maszyny wirtualnej

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Wszystkie elementy znajdujące się wewnątrz grupy zasobów zostaną usunięte podczas usuwania tej grupy.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje dotyczące podstawowych zadań tworzenia maszyny wirtualnej i zarządzania nią, takie jak:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat dysków maszyn wirtualnych.  

> [!div class="nextstepaction"]
> [Tworzenie dysków maszyn wirtualnych i zarządzanie nimi](./tutorial-manage-data-disk.md)