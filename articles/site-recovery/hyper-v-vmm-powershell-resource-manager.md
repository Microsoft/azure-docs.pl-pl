---
title: Konfigurowanie funkcji Hyper-V (z programem VMM) odzyskiwanie awaryjne w lokacji dodatkowej przy użyciu programu Azure Site Recovery/PowerShell
description: Opisuje sposób konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do pomocniczej lokacji programu VMM przy użyciu Azure Site Recovery i programu PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086221"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w lokacji dodatkowej przy użyciu programu PowerShell (Menedżer zasobów)

W tym artykule przedstawiono sposób automatyzacji czynności związanych z replikacją maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager do Virtual Machine Managerj chmury w dodatkowej lokacji lokalnej przy użyciu [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się ze [składnikami i architekturą scenariusza](hyper-v-vmm-architecture.md).
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](./vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że serwery Virtual Machine Manager i hosty funkcji Hyper-V są zgodne z wymaganiami dotyczącymi [obsługi](./vmware-physical-secondary-support-matrix.md).
- Sprawdź, czy maszyny wirtualne, które chcesz replikować, są zgodne z [obsługą zreplikowanej maszyny](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapuje między lokalnymi Virtual Machine Manager sieciami maszyn wirtualnych w chmurze źródłowej i docelowej. Mapowanie wykonuje następujące czynności:

- Łączy maszyny wirtualne z odpowiednimi docelowymi sieciami maszyn wirtualnych po przejściu w tryb failover.
- Optymalnie umieszcza repliki maszyn wirtualnych na docelowych serwerach hostów funkcji Hyper-V.
- Jeśli nie skonfigurujesz mapowania sieci, maszyny wirtualne repliki nie będą połączone z siecią maszyny wirtualnej po przejściu w tryb failover.

Przygotuj Virtual Machine Manager w następujący sposób:

- Upewnij się, że w źródłowym i docelowym serwerze Virtual Machine Manager znajdują się [Virtual Machine Manager sieci logicznych](/system-center/vmm/network-logical) :
  - Sieć logiczna na serwerze źródłowym powinna być skojarzona z chmurą źródłową, w której znajdują się hosty funkcji Hyper-V.
  - Sieć logiczna na serwerze docelowym powinna być skojarzona z chmurą docelową.
- Upewnij się, że masz [sieci VMNetwork](/system-center/vmm/network-virtual) na źródłowym i docelowym serwerze Virtual Machine Manager. Sieci maszyn wirtualnych powinny być połączone z siecią logiczną w każdej lokalizacji.
- Połącz maszyny wirtualne na źródłowych hostach funkcji Hyper-V ze źródłową siecią maszyny wirtualnej.

## <a name="prepare-for-powershell"></a>Przygotowanie do programu PowerShell

Upewnij się, że masz Azure PowerShell gotowy do użycia:

- Jeśli używasz już programu PowerShell, Uaktualnij go do wersji 0.8.10 lub nowszej. [Dowiedz się więcej](/powershell/azure/) na temat sposobu konfigurowania programu PowerShell.
- Po skonfigurowaniu i skonfigurowaniu programu PowerShell Przejrzyj [polecenia cmdlet usługi](/powershell/azure/).
- Aby dowiedzieć się więcej na temat używania wartości parametrów, danych wejściowych i wyjść w programie PowerShell, Przeczytaj przewodnik [wprowadzenie](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Konfigurowanie subskrypcji

1. W programie PowerShell Zaloguj się do konta platformy Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Pobierz listę subskrypcji z identyfikatorami subskrypcji. Zanotuj identyfikator subskrypcji, w której chcesz utworzyć magazyn Recovery Services.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Ustaw subskrypcję magazynu.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Utwórz grupę zasobów Azure Resource Manager, jeśli jej nie masz.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Utwórz nowy magazyn Recovery Services. Zapisz obiekt magazynu w zmiennej, która ma zostać użyta później.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Obiekt magazynu można pobrać po utworzeniu go przy użyciu `Get-AzRecoveryServicesVault` polecenia cmdlet.

## <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

1. Pobierz istniejący magazyn.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Ustaw kontekst magazynu.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Zainstaluj dostawcę Site Recovery

1. Na maszynie Virtual Machine Manager Utwórz katalog, uruchamiając następujące polecenie:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Wyodrębnij pliki przy użyciu pobranego pliku instalacyjnego dostawcy.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Zainstaluj dostawcę i poczekaj na zakończenie instalacji.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Zarejestruj serwer w magazynie.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Tworzenie i kojarzenie zasad replikacji

1. Utwórz zasady replikacji w tym przypadku dla funkcji Hyper-V 2012 R2 w następujący sposób:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Chmura Virtual Machine Manager może zawierać hosty funkcji Hyper-V z różnymi wersjami systemu Windows Server, ale zasady replikacji dotyczą określonej wersji systemu operacyjnego. Jeśli masz różne hosty działające w różnych systemach operacyjnych, Utwórz oddzielne zasady replikacji dla każdego systemu. Jeśli na przykład masz pięć hostów uruchomionych w systemie Windows Server 2012 i trzech hostach z systemem Windows Server 2012 R2, Utwórz dwie zasady replikacji. Tworzysz jeden dla każdego typu systemu operacyjnego.

1. Pobierz kontener ochrony podstawowej (podstawowa Virtual Machine Manager w chmurze) i kontener ochrony odzyskiwania (Odzyskiwanie Virtual Machine Manager w chmurze).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Pobierz utworzone zasady replikacji przy użyciu przyjaznej nazwy.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Uruchom skojarzenie kontenera ochrony (Virtual Machine Manager Cloud) z zasadami replikacji.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Poczekaj na zakończenie zadania skojarzenia zasad. Aby sprawdzić, czy zadanie zostało zakończone, użyj następującego fragmentu kodu programu PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Po zakończeniu przetwarzania zadania uruchom następujące polecenie:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w sekcji [monitorowanie aktywności](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. Użyj tego polecenia, aby pobrać serwery dla bieżącego magazynu. Polecenie przechowuje Site Recovery serwerów w `$Servers` zmiennej tablicowej.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Uruchom to polecenie, aby pobrać sieci dla źródłowego serwera Virtual Machine Manager i docelowego serwera Virtual Machine Manager.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Źródłowy serwer Virtual Machine Manager może być pierwszy lub drugi z nich w macierzy serwerowej. Sprawdź nazwy serwerów Virtual Machine Manager i odpowiednio Pobierz sieci.

1. To polecenie cmdlet tworzy mapowanie między siecią podstawową i siecią odzyskiwania. Określa sieć podstawową jako pierwszy element `$PrimaryNetworks` . Określa sieć odzyskiwania jako pierwszy element `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Włącz ochronę maszyn wirtualnych

Po poprawnym skonfigurowaniu serwerów, chmur i sieci należy włączyć ochronę maszyn wirtualnych w chmurze.

1. Aby włączyć ochronę, uruchom następujące polecenie w celu pobrania kontenera ochrony:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Pobierz jednostkę ochrony (VM) w następujący sposób:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Włącz replikację dla maszyny wirtualnej.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Jeśli chcesz przeprowadzić replikację do CMK dysków zarządzanych na platformie Azure, wykonaj następujące czynności za pomocą polecenia AZ PowerShell 3.3.0 i nowszego:
>
> 1. Włącz tryb failover na dyskach zarządzanych przez zaktualizowanie właściwości maszyny wirtualnej
> 1. Użyj `Get-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, aby pobrać identyfikator dysku dla każdego dysku chronionego elementu
> 1. Utwórz obiekt słownika przy użyciu `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` polecenia cmdlet, aby zawierać mapowanie identyfikatora dysku na zestaw szyfrowania dysków. Te zestawy szyfrowania dysków są wstępnie utworzone przez użytkownika w regionie docelowym.
> 1. Zaktualizuj właściwości maszyny wirtualnej za pomocą `Set-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, przechodząc do obiektu dictionary w parametrze **DiskIdToDiskEncryptionSetMap** .

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Aby przetestować wdrożenie, Uruchom test pracy w trybie failover dla pojedynczej maszyny wirtualnej. Można również utworzyć plan odzyskiwania, który zawiera wiele maszyn wirtualnych, i uruchomić test pracy w trybie failover dla planu. Próba przejścia w tryb failover symuluje mechanizm pracy awaryjnej i odzyskiwania w sieci izolowanej.

1. Pobierz maszynę wirtualną, do której maszyny wirtualne będą działać w trybie failover.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Wykonaj test pracy w trybie failover.

   Dla jednej maszyny wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   W przypadku planu odzyskiwania:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w sekcji [monitorowanie aktywności](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Uruchamianie planowanych i nieplanowanych przełączeń w tryb failover

1. Wykonaj planowaną pracę w trybie failover.

   Dla jednej maszyny wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   W przypadku planu odzyskiwania:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Wykonaj niezaplanowaną pracę w trybie failover.

   Dla jednej maszyny wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   W przypadku planu odzyskiwania:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Monitorowanie aktywności

Użyj następujących poleceń, aby monitorować aktywność trybu failover. Poczekaj na zakończenie przetwarzania między zadaniami.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) na temat Site Recovery za pomocą Menedżer zasobów poleceń cmdlet programu PowerShell.
