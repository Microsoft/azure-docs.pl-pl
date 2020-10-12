---
title: Odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V przy użyciu Azure Site Recovery i programu PowerShell
description: Automatyzuj odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery przy użyciu programu PowerShell i Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132505"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Skonfiguruj odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR) przez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych platformy Azure oraz lokalnych maszyn wirtualnych i serwerów fizycznych.

W tym artykule opisano sposób korzystania z programu Windows PowerShell wraz z Azure Resource Manager, aby replikować maszyny wirtualne funkcji Hyper-V do platformy Azure. W przykładzie użytym w tym artykule przedstawiono sposób replikowania pojedynczej maszyny wirtualnej uruchomionej na hoście funkcji Hyper-V do platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell udostępnia polecenia cmdlet do zarządzania platformą Azure przy użyciu programu Windows PowerShell. Site Recovery polecenia cmdlet programu PowerShell, dostępne w Azure PowerShell dla Azure Resource Manager, pomagają chronić i odzyskiwać serwery na platformie Azure.

Nie musisz być ekspertem programu PowerShell, aby korzystać z tego artykułu, ale musisz zrozumieć podstawowe pojęcia, takie jak moduły, polecenia cmdlet i sesje. Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](/powershell) i [Korzystanie z Azure PowerShell z Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

> [!NOTE]
> Partnerzy firmy Microsoft w programie w programie Cloud Solution Provider (CSP) mogą konfigurować i zarządzać ochroną serwerów klientów do odpowiednich subskrypcji programu CSP (subskrypcje dzierżawców).

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że są spełnione następujące wymagania wstępne:

- Konto [Microsoft Azure](https://azure.microsoft.com/) . Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Ponadto możesz przeczytać o [cenniku programu Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Aby uzyskać informacje o tej wersji i sposobie jej instalowania, zobacz [install Azure PowerShell](/powershell/azure/install-az-ps).

Ponadto w konkretnym przykładzie opisanym w tym artykule są spełnione następujące wymagania wstępne:

- Host funkcji Hyper-V z systemem Windows Server 2012 R2 lub Microsoft Hyper-V Server 2012 R2 zawierający co najmniej jedną maszynę wirtualną. Serwery funkcji Hyper-V powinny być połączone z Internetem bezpośrednio lub za pomocą serwera proxy.
- Maszyny wirtualne, które mają być replikowane, powinny być zgodne z [wymaganiami wstępnymi](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1. Logowanie do konta platformy Azure

1. Otwórz konsolę programu PowerShell i Uruchom to polecenie, aby zalogować się do konta platformy Azure. Polecenie cmdlet wyświetla stronę internetową z prośbą o poświadczenia konta: `Connect-AzAccount` .
   - Alternatywnie możesz uwzględnić poświadczenia konta jako parametr w `Connect-AzAccount` poleceniu cmdlet, używając parametru **Credential** .
   - Jeśli jesteś partnerem programu CSP działającym w imieniu dzierżawy, określ klienta jako dzierżawcę przy użyciu nazwy domeny głównej tenantID lub dzierżawcy. Na przykład: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Skojarz subskrypcję, której chcesz używać z kontem, ponieważ konto może mieć kilka subskrypcji:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Sprawdź, czy subskrypcja jest zarejestrowana, aby korzystać z dostawców platformy Azure dla Recovery Services i Site Recovery przy użyciu następujących poleceń:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Sprawdź, czy w danych wyjściowych polecenia **RegistrationState** jest ustawiona na **zarejestrowane**, możesz przejść do kroku 2. Jeśli nie, należy zarejestrować brakującego dostawcę w subskrypcji, uruchamiając następujące polecenia:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Sprawdź, czy dostawcy zarejestrowali się pomyślnie, używając następujących poleceń:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Krok 2. Konfigurowanie magazynu

1. Utwórz Azure Resource Manager grupę zasobów, w której chcesz utworzyć magazyn, lub Użyj istniejącej grupy zasobów. Utwórz nową grupę zasobów w następujący sposób. `$ResourceGroupName`Zmienna zawiera nazwę grupy zasobów, którą chcesz utworzyć, a zmienna $Geo zawiera region świadczenia usługi Azure, w którym ma zostać utworzona grupa zasobów (na przykład "Brazylia Południowa").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Aby uzyskać listę grup zasobów w ramach subskrypcji, uruchom `Get-AzResourceGroup` polecenie cmdlet.
1. Utwórz nowy magazyn Recovery Services platformy Azure w następujący sposób:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Listę istniejących magazynów można pobrać przy użyciu `Get-AzRecoveryServicesVault` polecenia cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3. Ustawianie kontekstu magazynu Recovery Services

Ustaw kontekst magazynu w następujący sposób:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4. Tworzenie lokacji funkcji Hyper-V

1. Utwórz nową lokację funkcji Hyper-V w następujący sposób:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. To polecenie cmdlet uruchamia zadanie Site Recovery, aby utworzyć lokację, i zwraca obiekt zadania Site Recovery. Poczekaj na zakończenie zadania i sprawdź, czy zadanie zostało ukończone pomyślnie.
1. Użyj `Get-AzRecoveryServicesAsrJob` polecenia cmdlet, aby pobrać obiekt zadania i sprawdzić bieżący stan zadania.
1. Wygeneruj i Pobierz klucz rejestracji dla lokacji w następujący sposób:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Skopiuj pobrany klucz do hosta funkcji Hyper-V. Klucz jest potrzebny do zarejestrowania hosta funkcji Hyper-V w lokacji programu.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5. Instalowanie dostawcy i agenta

1. Pobierz Instalatora dla najnowszej wersji dostawcy [firmy Microsoft](https://aka.ms/downloaddra).
1. Uruchom Instalatora na hoście funkcji Hyper-V.
1. Po zakończeniu instalacji przejdź do kroku rejestracji.
1. Po wyświetleniu monitu podaj pobrany klucz i wykonaj rejestrację hosta funkcji Hyper-V.
1. Sprawdź, czy host funkcji Hyper-V jest zarejestrowany w lokacji w następujący sposób:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Jeśli używasz serwera podstawowego funkcji Hyper-V, Pobierz plik Instalatora i wykonaj następujące kroki:

1. Wyodrębnij pliki z _AzureSiteRecoveryProvider.exe_ do katalogu lokalnego, uruchamiając następujące polecenie:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Uruchom następujące polecenie:

   ```console
   .\setupdr.exe /i
   ```

   Wyniki są rejestrowane w _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Zarejestruj serwer, uruchamiając następujące polecenie:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Krok 6. Tworzenie zasad replikacji

Przed rozpoczęciem należy określić konto magazynu w tym samym regionie świadczenia usługi Azure co magazyn i mieć włączoną replikację geograficzną.

1. Utwórz zasady replikacji w następujący sposób:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Sprawdź zwrócone zadanie, aby upewnić się, że tworzenie zasad replikacji powiodło się.

1. Pobierz kontener ochrony odnoszący się do lokacji w następujący sposób:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Skojarz kontener ochrony z zasadami replikacji w następujący sposób:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Poczekaj na pomyślne ukończenie zadania skojarzenia.

1. Pobierz Mapowanie kontenera ochrony.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Krok 7. Włączanie ochrony maszyny wirtualnej

1. Pobierz element objęty ochroną, który odnosi się do maszyny wirtualnej, która ma być chroniona, w następujący sposób:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Ochrona maszyny wirtualnej. Jeśli do chronionej maszyny wirtualnej jest dołączony więcej niż jeden dysk, określ dysk systemu operacyjnego za pomocą parametru **OSDiskName** .

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Poczekaj, aż maszyny wirtualne osiągną stan chroniony po replikacji początkowej. Może to chwilę potrwać, w zależności od takich czynników, jak ilość danych, które mają być replikowane, oraz dostępną przepustowość w strumieniu do platformy Azure. W przypadku stanu chronionego stan zadania i StateDescription są aktualizowane w następujący sposób:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Zaktualizuj właściwości odzyskiwania (takie jak rozmiar roli maszyny wirtualnej) i sieć platformy Azure, do której ma zostać dołączona karta sieciowa maszyny wirtualnej po przejściu w tryb failover.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Jeśli chcesz przeprowadzić replikację do CMK dysków zarządzanych na platformie Azure, wykonaj następujące czynności za pomocą polecenia AZ PowerShell 3.3.0 i nowszego:
>
> 1. Włącz tryb failover na dyskach zarządzanych przez zaktualizowanie właściwości maszyny wirtualnej
> 1. Użyj `Get-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, aby pobrać identyfikator dysku dla każdego dysku chronionego elementu
> 1. Utwórz obiekt słownika przy użyciu `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` polecenia cmdlet, aby zawierać mapowanie identyfikatora dysku na zestaw szyfrowania dysków. Te zestawy szyfrowania dysków są wstępnie utworzone przez użytkownika w regionie docelowym.
> 1. Zaktualizuj właściwości maszyny wirtualnej za pomocą `Set-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, przechodząc do obiektu dictionary w parametrze **DiskIdToDiskEncryptionSetMap** .

## <a name="step-8-run-a-test-failover"></a>Krok 8. Uruchamianie testu pracy w trybie failover

1. Uruchom test pracy w trybie failover w następujący sposób:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Sprawdź, czy testowa maszyna wirtualna została utworzona na platformie Azure. Zadanie testowej pracy w trybie failover jest zawieszone po utworzeniu testowej maszyny wirtualnej na platformie Azure.
1. Aby wyczyścić i ukończyć test pracy w trybie failover, uruchom polecenie:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) na temat Azure Site Recovery za pomocą Azure Resource Manager poleceń cmdlet programu PowerShell.
