---
title: Szybki Start — tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell
description: W tym przewodniku szybki start dowiesz się, jak utworzyć kopię zapasową maszyn wirtualnych platformy Azure za pomocą modułu Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 81005a3032355f566aef2a6794cf2ec5038b0dd8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737120"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Tworzenie kopii zapasowej maszyny wirtualnej za pomocą programu PowerShell

[Azure POWERSHELL AZ](/powershell/azure/new-azureps-module-az) module służy do tworzenia zasobów platformy Azure i zarządzania nimi z poziomu wiersza polecenia lub skryptów.

[Azure Backup](backup-overview.md) tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule pokazano, jak utworzyć kopię zapasową maszyny wirtualnej platformy Azure za pomocą AZ module. Alternatywnie można utworzyć kopię zapasową maszyny wirtualnej przy użyciu [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md)lub w [Azure Portal](quick-backup-vm-portal.md).

W tym przewodniku Szybki start opisano wykonywanie kopii zapasowej istniejącej maszyny wirtualnej platformy Azure. Jeśli musisz utworzyć maszynę wirtualną, możesz [utworzyć maszynę wirtualną za pomocą programu Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json).

Ten przewodnik Szybki Start wymaga Azure PowerShell AZ module w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Logowanie i rejestrowanie

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```

2. Przy pierwszym użyciu Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji za pomocą [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)w następujący sposób:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[Magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, który przechowuje dane kopii zapasowej chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Podczas tworzenia magazynu:

- W polu Grupa zasobów i lokalizacja określ grupę zasobów i lokalizację maszyny wirtualnej, dla której chcesz utworzyć kopię zapasową.
- Jeśli użyto tego [przykładowego skryptu](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json) do utworzenia maszyny wirtualnej, Grupa **zasobów jest grupą zasobów,** maszyna wirtualna to **_myVM_*, a zasoby znajdują się w regionie **WestEurope** .
- Azure Backup automatycznie obsługuje magazyn do przechowywania kopii zapasowych danych. Domyślnie magazyn używa [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). Nadmiarowość geograficzna gwarantuje, że kopia zapasowa danych jest replikowana do pomocniczego regionu platformy Azure, a setki kilometrów od regionu podstawowego.

Teraz Utwórz magazyn:

1. Użyj  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) , aby utworzyć magazyn:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Ustaw kontekst magazynu za pomocą [opcji Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)w następujący sposób:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Zmień konfigurację nadmiarowości magazynu (LRS/GRS) magazynu za pomocą [opcji Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)w następujący sposób:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Nadmiarowość magazynu można modyfikować tylko wtedy, gdy nie ma żadnych elementów kopii zapasowych chronionych do magazynu.

## <a name="enable-backup-for-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

Możesz włączyć tworzenie kopii zapasowej dla maszyny wirtualnej platformy Azure i określić zasady tworzenia kopii zapasowych.

- Zasady definiują, kiedy działają kopie zapasowe oraz jak długo mają być przechowywane punkty odzyskiwania utworzone w kopiach zapasowych.
- Domyślne zasady ochrony uruchamiają kopię zapasową raz dziennie dla maszyny wirtualnej i zachowują utworzone punkty odzyskiwania przez 30 dni. Za pomocą tych zasad domyślnych można szybko chronić maszynę wirtualną.

Włącz kopię zapasową w następujący sposób:

1. Najpierw ustaw zasady domyślne za pomocą [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Włącz tworzenie kopii zapasowej maszyny wirtualnej za pomocą [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Określ zasady, grupę zasobów i nazwę maszyny wirtualnej.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Kopie zapasowe są uruchamiane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych. Możesz również uruchomić kopię zapasową na żądanie:

- Pierwsze zadanie początkowej kopii zapasowej tworzy punkt pełnego odzyskiwania.
- Po początkowej kopii zapasowej każde zadanie tworzenia kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
- Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Aby uruchomić kopię zapasową na żądanie, należy użyć [kopii zapasowej-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Należy określić kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Każda maszyna wirtualna, której kopia zapasowa ma być tworzona, jest traktowana jako element. Aby rozpocząć zadanie tworzenia kopii zapasowej, należy uzyskać informacje o maszynie wirtualnej za pomocą [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Uruchom zadanie tworzenia kopii zapasowej na żądanie w następujący sposób:

1. Określ kontener, Uzyskaj informacje o maszynie wirtualnej i uruchom kopię zapasową.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Może być konieczne odczekanie do 20 minut, ponieważ pierwsze zadanie tworzenia kopii zapasowej tworzy pełny punkt odzyskiwania. Monitoruj zadanie zgodnie z opisem w następnej procedurze.

## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

1. Uruchom [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) , aby monitorować stan zadania.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Dane wyjściowe są podobne do poniższego przykładu, który pokazuje zadanie jako **niepostępu**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Gdy zadanie ma stan **ukończono**, maszyna wirtualna jest chroniona i ma przechowywany pełny punkt odzyskiwania.

## <a name="clean-up-the-deployment"></a>Wyczyść wdrożenie

Jeśli kopia zapasowa maszyny wirtualnej nie jest już potrzebna, można ją wyczyścić.

- Jeśli chcesz wypróbować przywracanie maszyny wirtualnej, Pomiń oczyszczanie.
- Jeśli użyto istniejącej maszyny wirtualnej, możesz pominąć końcowe polecenie cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , aby pozostawić grupę zasobów i maszynę wirtualną.

Wyłącz ochronę, Usuń punkty przywracania i magazyn. Następnie Usuń grupę zasobów i skojarzone z nią zasoby maszyn wirtualnych w następujący sposób:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania.

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) utworzyć kopię zapasową maszyn wirtualnych w Azure Portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) szybko przywrócić maszynę wirtualną