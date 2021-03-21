---
title: Migrowanie do Menedżer zasobów przy użyciu programu PowerShell
description: W tym artykule przedstawiono migrację zasobów IaaS obsługiwanych przez platformę, takich jak maszyny wirtualne, sieci wirtualne i konta magazynu z klasycznej do Azure Resource Manager przy użyciu poleceń Azure PowerShell
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c750e34e8081cf5a8b3d41cc8c52584a4353a336
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695162"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrowanie zasobów IaaS z klasycznej do Azure Resource Manager przy użyciu programu PowerShell

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](classic-vm-deprecation.md#how-does-this-affect-me).

W tych krokach pokazano, jak za pomocą poleceń Azure PowerShell przeprowadzić migrację zasobów infrastruktury jako usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania Azure Resource Manager.

Jeśli chcesz, możesz także przeprowadzić migrację zasobów przy użyciu [interfejsu wiersza polecenia platformy Azure](migration-classic-resource-manager-cli.md).

* Aby zapoznać się z obsługiwanymi scenariuszami migracji, zobacz [migracja zasobów IaaS z wersji klasycznej do Azure Resource Manager przy użyciu platformy obsługiwanej przez platformę](migration-classic-resource-manager-overview.md).
* Aby zapoznać się ze szczegółowymi wskazówkami i przewodnikiem dotyczącym migracji, zobacz [technicznie szczegółowee na platformie obsługiwana migracja z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Przejrzyj najczęstsze błędy migracji](migration-classic-resource-manager-errors.md).

<br>
Oto schemat blokowy służący do identyfikowania kolejności, w której należy wykonać kroki w procesie migracji.

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Krok 1. Planowanie migracji
Poniżej przedstawiono kilka najlepszych rozwiązań, które zalecamy podczas szacowania, czy migracji zasobów IaaS z klasycznego do Menedżer zasobów:

* Przeczytaj [obsługiwane i nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md). Jeśli masz maszyny wirtualne korzystające z nieobsługiwanych konfiguracji lub funkcji, poczekaj na anonsowanie obsługi konfiguracji lub funkcji. Alternatywnie, jeśli odpowiada Twoim potrzebom, Usuń tę funkcję lub wykorzystaj ją z tej konfiguracji w celu włączenia migracji.
* W przypadku zautomatyzowanych skryptów, które obecnie wdrażają infrastrukturę i aplikacje, spróbuj utworzyć podobną konfigurację testu przy użyciu tych skryptów do migracji. Alternatywnie można skonfigurować przykładowe środowiska przy użyciu Azure Portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane w przypadku migracji z wersji klasycznej do Menedżer zasobów. Aby przeprowadzić migrację sieci wirtualnej z bramą aplikacji, Usuń bramę przed uruchomieniem operacji przygotowywania w celu przeniesienia sieci. Po zakończeniu migracji Połącz się ponownie z bramą w Azure Resource Manager.
>
> Nie można automatycznie migrować bram usługi Azure ExpressRoute, które łączą się ze obwodami ExpressRoute w innej subskrypcji. W takich przypadkach Usuń bramę ExpressRoute, Przeprowadź migrację sieci wirtualnej i Utwórz ponownie bramę. Aby uzyskać więcej informacji, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania Menedżer zasobów](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Krok 2. Instalacja najnowszej wersji programu PowerShell
Dostępne są dwie główne opcje instalacji Azure PowerShell: [Galeria programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [Instalatora platformy sieci Web (Instalatora WebPI)](https://aka.ms/webpi-azps). Instalatora WebPI otrzymuje comiesięczne aktualizacje. Galeria programu PowerShell otrzymuje aktualizacje w sposób ciągły. Ten artykuł jest oparty na Azure PowerShell wersji 2.1.0.

Instrukcje instalacji znajdują się w temacie [How to Install and configure Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Krok 3. upewnienie się, że jesteś administratorem subskrypcji
Aby przeprowadzić migrację, należy dodać jako administratora dla subskrypcji w [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu **centrum** wybierz pozycję **subskrypcja**. Jeśli go nie widzisz, wybierz pozycję **wszystkie usługi**.
3. Znajdź odpowiedni wpis subskrypcji, a następnie poszukaj pola **My role** . W przypadku współadministratora wartość powinna być _administratorem konta_.

Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub współadministratorem, aby uzyskać subskrypcję, która ma zostać dodana.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4. Ustawianie subskrypcji i rejestrowanie się w celu migracji
Najpierw Uruchom wiersz polecenia programu PowerShell. W przypadku migracji Skonfiguruj środowisko dla środowiska klasycznego i Menedżer zasobów.

Zaloguj się do swojego konta, aby uzyskać Menedżer zasobów model.

```powershell
    Connect-AzAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. Ten przykład ustawia domyślną nazwę subskrypcji na **moją subskrypcję platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Rejestracja to jednorazowy krok, ale należy wykonać go raz przed podjęciem próby migracji. Bez rejestrowania zostanie wyświetlony następujący komunikat o błędzie:
>
> *Nieprawidłowego żądania: subskrypcja nie jest zarejestrowana na potrzeby migracji.*

Zarejestruj się w dostawcy zasobów migracji przy użyciu następującego polecenia:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Zaczekaj pięć minut na zakończenie rejestracji. Sprawdź stan zatwierdzenia przy użyciu następującego polecenia:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Przed kontynuowaniem upewnij się, że RegistrationState `Registered` .

Przed przełączeniem do klasycznego modelu wdrażania upewnij się, że masz wystarczającą Azure Resource Manager procesorów wirtualnych vCPU maszyny wirtualnej w regionie świadczenia usługi Azure bieżącego wdrożenia lub sieci wirtualnej. Aby sprawdzić bieżącą liczbę procesorów wirtualnych vCPU w Azure Resource Manager, można użyć następującego polecenia programu PowerShell. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [limity i Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Ten przykład sprawdza dostępność w regionie **zachodnie stany USA** . Zamień nazwę przykładowego regionu na własny.

```powershell
    Get-AzVMUsage -Location "West US"
```

Teraz Zaloguj się do swojego konta przy użyciu klasycznego modelu wdrażania.

```powershell
    Add-AzureAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawiono domyślną subskrypcję dla **mojej subskrypcji platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Krok 5. Uruchamianie poleceń w celu migrowania zasobów IaaS
* [Migrowanie maszyn wirtualnych w usłudze w chmurze (nie w sieci wirtualnej)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrowanie maszyn wirtualnych w sieci wirtualnej](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrowanie konta magazynu](#step-52-migrate-a-storage-account)

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu to idempotentne. Jeśli wystąpi problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, zalecamy ponowną próbę wykonania operacji przygotowywania, przerwania lub zatwierdzania. Następnie platforma próbuje wykonać akcję ponownie.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 5,1: Opcja 1 — Migrowanie maszyn wirtualnych w usłudze w chmurze (poza siecią wirtualną)
Pobierz listę usług w chmurze za pomocą następującego polecenia. Następnie wybierz usługę w chmurze, która ma zostać poddana migracji. Jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub mają role sieci Web lub proces roboczy, polecenie zwróci komunikat o błędzie.

```powershell
    Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia dla usługi w chmurze. W tym przykładzie nazwa usługi to **Moja usługa**. Zastąp przykładową nazwę usługi własną nazwą usługi.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Przygotuj maszyny wirtualne w usłudze w chmurze na potrzeby migracji. Dostępne są dwie opcje do wyboru.

* **Opcja 1: Migrowanie maszyn wirtualnych do sieci wirtualnej utworzonej na platformie.**

    Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze, używając następujących poleceń:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli komunikaty weryfikacyjne nie zawierają komunikatu o błędzie typu, można przejść do kroku Prepare.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opcja 2: migracja do istniejącej sieci wirtualnej w modelu wdrażania Menedżer zasobów.**

    W tym przykładzie jest ustawiana nazwa grupy zasobów dla **zasobu, nazwa** sieci wirtualnej do **myVirtualNetwork** oraz nazwa podsieci do **odpodsieci**. Zastąp nazwy w przykładzie nazwami własnych zasobów.

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

    Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli komunikaty weryfikacyjne nie zawierają błędów, można przejść do następującego kroku przygotowania:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po pomyślnym zakończeniu operacji przygotowania z jedną z powyższych opcji wykonaj zapytanie o stan migracji maszyn wirtualnych. Upewnij się, że są one w `Prepared` stanie.

W tym przykładzie nazwa maszyny wirtualnej jest ustawiana na **myVM**. Zastąp przykładową nazwę własną nazwą maszyny wirtualnej.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Sprawdź konfigurację przygotowanych zasobów przy użyciu programu PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 5,1: Opcja 2 — Migrowanie maszyn wirtualnych w sieci wirtualnej

Aby przeprowadzić migrację maszyn wirtualnych w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Maszyny wirtualne są automatycznie migrowane z siecią wirtualną. Wybierz sieć wirtualną, która ma zostać poddana migracji.
> [!NOTE]
> [Przeprowadź migrację pojedynczej maszyny wirtualnej](./windows/create-vm-specialized-portal.md) utworzonej przy użyciu klasycznego modelu wdrażania, tworząc nową Menedżer zasobów maszynę wirtualną z Managed disks przy użyciu plików VHD (systemu operacyjnego i danych) maszyny wirtualnej.
<br>

> [!NOTE]
> Nazwa sieci wirtualnej może się różnić od tego, co jest widoczne w nowym portalu. Nowy Azure Portal wyświetla nazwę jako `[vnet-name]` , ale rzeczywista nazwa sieci wirtualnej jest typu `Group [resource-group-name] [vnet-name]` . Przed rozpoczęciem migracji Wyszukaj rzeczywistą nazwę sieci wirtualnej przy użyciu polecenia `Get-AzureVnetSite | Select -Property Name` lub Wyświetl je w starym Azure Portal. 

Ten przykład ustawia nazwę sieci wirtualnej na **myVnet**. Zamień nazwę przykładowej sieci wirtualnej na własną.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Jeśli sieć wirtualna zawiera role sieci Web lub procesu roboczego lub maszyny wirtualne z nieobsługiwanymi konfiguracjami, zostanie wyświetlony komunikat o błędzie walidacji.

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, możesz przejść do następujących kroków:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację przygotowanych maszyn wirtualnych przy użyciu Azure PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Krok 5,2: Migrowanie konta magazynu
Po zakończeniu migrowania maszyn wirtualnych wykonaj następujące sprawdzenia wymagań wstępnych przed przeprowadzeniem migracji kont magazynu.

> [!NOTE]
> Jeśli konto magazynu nie ma skojarzonych dysków lub danych maszyny wirtualnej, możesz przejść bezpośrednio do sekcji "Weryfikowanie kont magazynu i rozpoczynanie migracji". Należy również pamiętać, że usunięcie dysków klasycznych, obrazów maszyn wirtualnych lub obrazów systemu operacyjnego nie powoduje usunięcia źródłowych plików VHD na koncie magazynu. Jednak spowoduje to przerwanie dzierżawy dla tych plików VHD, dzięki czemu można je ponownie wykorzystać do tworzenia dysków lub obrazów ARM po migracji.

* Sprawdzanie wymagań wstępnych w przypadku migrowania dowolnych maszyn wirtualnych lub konta magazynu ma zasoby dyskowe:
    * Migruj maszyny wirtualne, których dyski są przechowywane na koncie magazynu.

        Następujące polecenie zwraca właściwości rolename i diskname wszystkich dysków maszyny wirtualnej na koncie magazynu. Rolename to nazwa maszyny wirtualnej, do której jest dołączony dysk. Jeśli to polecenie zwraca dyski, upewnij się, że maszyny wirtualne, do których te dyski są dołączone, są migrowane przed przeprowadzeniem migracji konta magazynu.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Usuń niedołączone dyski maszyny wirtualnej przechowywane na koncie magazynu.

        Znajdź niedołączone dyski maszyny wirtualnej na koncie magazynu za pomocą następującego polecenia:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Jeśli poprzednie polecenie zwraca dyski, usuń te dyski przy użyciu następującego polecenia:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Usuń obrazy maszyn wirtualnych przechowywane na koncie magazynu.

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
* Sprawdź poprawność kont magazynu i Rozpocznij migrację.

    Sprawdź poprawność każdego konta magazynu do migracji przy użyciu następującego polecenia. W tym przykładzie nazwa konta magazynu to **mojekontomagazynu**. Zastąp przykładową nazwę nazwą własnego konta magazynu.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Następnym krokiem jest przygotowanie konta magazynu do migracji.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Sprawdź konfigurację przygotowanego konta magazynu przy użyciu Azure PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Następne kroki
* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md)
