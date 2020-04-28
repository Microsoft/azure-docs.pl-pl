---
title: Migrowanie do Menedżera zasobów za pomocą programu PowerShell
description: W tym artykule oprzeszona jest migracja zasobów usługi IaaS obsługiwana przez platformę, takich jak maszyny wirtualne(VM), sieci wirtualne i konta magazynu z klasycznego do usługi Azure Resource Manager przy użyciu poleceń programu Azure PowerShell
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 314d7a4725709f00ba5cdbf54595857502bc5805
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865944"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager przy użyciu programu PowerShell

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

W tych krokach pokazano, jak używać poleceń programu Azure PowerShell do migracji zasobów infrastruktury jako usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager.

Jeśli chcesz, możesz również migrować zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/migration-classic-resource-manager-cli.md).

* Aby uzyskać informacje na temat obsługiwanych scenariuszy migracji, zobacz [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Aby uzyskać szczegółowe wskazówki i przewodnik po migracji, zobacz [Głębokie głębokie zagłębienie techniczne w zakresie migracji obsługiwanej przez platformę z klasycznego do usługi Azure Resource Manager.](migration-classic-resource-manager-deep-dive.md)
* [Przejrzyj najczęstsze błędy migracji](migration-classic-resource-manager-errors.md).

<br>
Oto schemat blokowy, aby zidentyfikować kolejność, w jakiej kolejności należy wykonać kroki podczas procesu migracji.

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Krok 1: Planowanie migracji
Oto kilka sprawdzonych rozwiązań zalecanych podczas oceny migracji zasobów IaaS z klasycznego do Menedżera zasobów:

* Zapoznaj się z [obsługiwanymi i nieobsługiwanymi funkcjami i konfiguracjami](migration-classic-resource-manager-overview.md). Jeśli masz maszyny wirtualne, które używają nieobsługiwał konfiguracji lub funkcji, poczekaj na konfigurację lub obsługę funkcji, które mają być ogłoszone. Alternatywnie, jeśli odpowiada twoim potrzebom, usuń tę funkcję lub wyjmij z tej konfiguracji, aby włączyć migrację.
* Jeśli masz zautomatyzowane skrypty, które wdrażają infrastrukturę i aplikacje dzisiaj, spróbuj utworzyć podobną konfigurację testów przy użyciu tych skryptów do migracji. Alternatywnie można skonfigurować przykładowe środowiska przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane w przypadku migracji z klasycznego do Menedżera zasobów. Aby przeprowadzić migrację sieci wirtualnej z bramą aplikacji, usuń bramę przed uruchomieniem operacji Przygotowanie w celu przeniesienia sieci. Po zakończeniu migracji ponownie połącz bramę w usłudze Azure Resource Manager.
>
> Bramy usługi Azure ExpressRoute łączące się z obwodami usługi ExpressRoute w innej subskrypcji nie mogą być migrowane automatycznie. W takich przypadkach usuń bramę usługi ExpressRoute, zmigruj sieć wirtualną i ponownie utwórz bramę. Aby uzyskać więcej informacji, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego do modelu wdrażania Menedżera zasobów](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Krok 2: Instalowanie najnowszej wersji programu PowerShell
Istnieją dwie główne opcje instalowania programu Azure PowerShell: [Galeria programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [Instalator platformy sieci Web (WebPI).](https://aka.ms/webpi-azps) Interfejs WebPI otrzymuje comiesięczne aktualizacje. Galeria programu PowerShell otrzymuje aktualizacje w sposób ciągły. Ten artykuł jest oparty na platformie Azure PowerShell w wersji 2.1.0.

Aby uzyskać instrukcje instalacji, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Krok 3: Upewnij się, że jesteś administratorem subskrypcji
Aby przeprowadzić tę migrację, należy dodać jako współadministrator dla subskrypcji w [witrynie Azure portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu **Centrum** wybierz polecenie **Subskrypcja**. Jeśli go nie widzisz, wybierz pozycję **Wszystkie usługi**.
3. Znajdź odpowiedni wpis subskrypcji, a następnie przyjrzyj się pole **mojemu roli.** Dla coadministrator, wartość powinna być _konto admin_.

Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub administratorem usługi, aby uzyskać subskrypcję, aby uzyskać do niej dodam.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Ustaw subskrypcję i zarejestruj się w celu migracji
Najpierw uruchom monit programu PowerShell. W przypadku migracji skonfiguruj środowisko zarówno dla menedżera klasycznego, jak i Menedżera zasobów.

Zaloguj się na swoje konto dla modelu Menedżera zasobów.

```powershell
    Connect-AzAccount
```

Pobierz dostępne subskrypcje za pomocą następującego polecenia:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawia domyślną nazwę subskrypcji na **Moja subskrypcja platformy Azure**. Zastąp przykładowa nazwa subskrypcji własną.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Rejestracja jest krokiem jednorazowym, ale należy to zrobić raz przed podjęciem próby migracji. Bez rejestracji zostanie wyświetlony następujący komunikat o błędzie:
>
> *BadRequest: Subskrypcja nie jest zarejestrowana do migracji.*

Zarejestruj się u dostawcy zasobów migracji za pomocą następującego polecenia:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Poczekaj pięć minut na zakończenie rejestracji. Sprawdź stan zatwierdzenia za pomocą następującego polecenia:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Upewnij się, że `Registered` registrationState jest przed kontynuowaniem.

Przed przełączeniem do klasycznego modelu wdrażania upewnij się, że masz wystarczającą liczbę procesorów wirtualnych maszyny wirtualnej usługi Azure Resource Manager w regionie platformy Azure bieżącego wdrożenia lub sieci wirtualnej. Za pomocą następującego polecenia programu PowerShell można sprawdzić bieżącą liczbę procesorów wirtualnych w usłudze Azure Resource Manager. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [Limity i Usługa Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

W tym przykładzie sprawdza dostępność w regionie **Zachodnie stany USA.** Zastąp przykładowa nazwa regionu własną.

```powershell
    Get-AzVMUsage -Location "West US"
```

Teraz zaloguj się na swoje konto dla klasycznego modelu wdrażania.

```powershell
    Add-AzureAccount
```

Pobierz dostępne subskrypcje za pomocą następującego polecenia:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawia domyślną subskrypcję **na subskrypcję moja platforma Azure**. Zastąp przykładowa nazwa subskrypcji własną.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Krok 5: Uruchamianie poleceń w celu migracji zasobów IaaS
* [Migrowanie maszyn wirtualnych w usłudze w chmurze (nie w sieci wirtualnej)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrowanie maszyn wirtualnych w sieci wirtualnej](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrowanie konta magazynu](#step-52-migrate-a-storage-account)

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu są idempotentne. Jeśli masz problem inny niż nieobsługicona funkcja lub błąd konfiguracji, zaleca się ponowienie próby operacji przygotowania, przerwania lub zatwierdzenia. Platforma następnie próbuje akcji ponownie.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 5.1: Opcja 1 — migrowanie maszyn wirtualnych w usłudze w chmurze (nie w sieci wirtualnej)
Pobierz listę usług w chmurze za pomocą następującego polecenia. Następnie wybierz usługę w chmurze, którą chcesz przeprowadzić migrację. Jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub mają role sieci web lub procesu roboczego, polecenie zwraca komunikat o błędzie.

```powershell
    Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia usługi w chmurze. W tym przykładzie nazwa usługi to **Moja usługa**. Zastąp przykładową nazwę usługi własną nazwą usługi.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Przygotuj maszyny wirtualne w usłudze w chmurze do migracji. Masz dwie opcje do wyboru.

* **Opcja 1: Migrowanie maszyn wirtualnych do sieci wirtualnej utworzonej przez platformę.**

    Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze za pomocą następujących poleceń:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla wszelkie ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, można przejść do kroku Przygotowanie.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opcja 2: Migracja do istniejącej sieci wirtualnej w modelu wdrażania Menedżera zasobów.**

    W tym przykładzie ustawia nazwę grupy zasobów na **myResourceGroup**, nazwę sieci wirtualnej na **myVirtualNetwork**, a nazwę podsieci na **mySubNet**. Zastąp nazwy w przykładzie nazwami własnych zasobów.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla wszelkie ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, można przejść do następującego kroku Przygotowanie:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po zakończeniu operacji Przygotowanie z jedną z powyższych opcji kwerendy stanu migracji maszyn wirtualnych. Upewnij się, że `Prepared` są w stanie.

W tym przykładzie ustawia nazwę maszyny Wirtualnej na **myVM**. Zastąp przykładowa nazwa własną nazwą maszyny Wirtualnej.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Sprawdź konfigurację przygotowanych zasobów przy użyciu programu PowerShell lub witryny Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 5.1: Opcja 2 — migrowanie maszyn wirtualnych w sieci wirtualnej

Aby przeprowadzić migrację maszyn wirtualnych w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Maszyny wirtualne automatycznie migrują za pomocą sieci wirtualnej. Wybierz sieć wirtualną, którą chcesz przeprowadzić migrację.
> [!NOTE]
> [Migrowanie pojedynczej maszyny wirtualnej](migrate-single-classic-to-resource-manager.md) utworzonej przy użyciu klasycznego modelu wdrażania przez utworzenie nowej maszyny wirtualnej Menedżera zasobów z dyskami zarządzanymi przy użyciu plików VHD (OS i danych) maszyny wirtualnej.
<br>

> [!NOTE]
> Nazwa sieci wirtualnej może się różnić od tej pokazanej w nowym portalu. Nowy portal Platformy Azure `[vnet-name]`wyświetla nazwę jako , ale `Group [resource-group-name] [vnet-name]`rzeczywista nazwa sieci wirtualnej jest typu . Przed rozpoczęciem migracji wyszukuj rzeczywistą nazwę `Get-AzureVnetSite | Select -Property Name` sieci wirtualnej za pomocą polecenia lub wyświetl ją w starym portalu Azure. 

W tym przykładzie ustawia nazwę sieci wirtualnej na **myVnet**. Zastąp przykładową nazwę sieci wirtualnej własną.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Jeśli sieć wirtualna zawiera role sieci web lub procesu roboczego lub maszyny wirtualne z nieobsługiwionymi konfiguracjami, zostanie wyświetlony komunikat o błędzie sprawdzania poprawności.

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Następujące polecenie wyświetla wszelkie ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, można przejść do następującego kroku Przygotowanie:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację przygotowanych maszyn wirtualnych przy użyciu programu Azure PowerShell lub witryny Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Krok 5.2: Migrowanie konta magazynu
Po zakończeniu migracji maszyn wirtualnych wykonaj następujące testy wstępne przed migracją kont magazynu.

> [!NOTE]
> Jeśli na koncie magazynu nie ma skojarzonych dysków ani danych maszyny Wirtualnej, można przejść bezpośrednio do sekcji "Sprawdzanie poprawności kont magazynu i rozpoczynanie migracji".

* Wstępnie sprawdza, czy zmigrowano wszystkie maszyny wirtualne lub konto magazynu ma zasoby dysku:
    * Migrowanie maszyn wirtualnych, których dyski są przechowywane na koncie magazynu.

        Następujące polecenie zwraca właściwości RoleName i DiskName wszystkich dysków maszyn wirtualnych na koncie magazynu. RoleName to nazwa maszyny wirtualnej, do której jest dołączony dysk. Jeśli to polecenie zwraca dyski, upewnij się, że maszyny wirtualne, do których są dołączone te dyski, są migrowane przed migracją konta magazynu.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Usuń nieprzyłączone dyski maszyn wirtualnych przechowywane na koncie magazynu.

        Znajdź niezałączone dyski maszyn wirtualnych na koncie magazynu za pomocą następującego polecenia:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Jeśli poprzednie polecenie zwraca dyski, usuń te dyski za pomocą następującego polecenia:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Usuwanie obrazów maszyn wirtualnych przechowywanych na koncie magazynu.

        Następujące polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskami systemu operacyjnego przechowywanymi na koncie magazynu.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Następujące polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskami danych przechowywanymi na koncie magazynu.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Usuń wszystkie obrazy maszyn wirtualnych zwrócone przez poprzednie polecenia za pomocą tego polecenia:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Sprawdzanie poprawności kont magazynu i rozpoczynanie migracji.

    Sprawdź poprawność każdego konta magazynu do migracji za pomocą następującego polecenia. W tym przykładzie nazwa konta magazynu to **myStorageAccount**. Zastąp przykładową nazwę nazwą własnego konta magazynu.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Następnym krokiem jest przygotowanie konta magazynu do migracji.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Sprawdź konfigurację dla przygotowanego konta magazynu przy użyciu programu Azure PowerShell lub witryny Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Następne kroki
* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
