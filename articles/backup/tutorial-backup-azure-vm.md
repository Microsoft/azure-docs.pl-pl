---
title: 'Samouczek: wiele kopii zapasowych maszyny wirtualnej platformy Azure za pomocą programu PowerShell'
description: Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowych wielu maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980961"
---
# <a name="back-up-azure-vms-with-powershell"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym samouczku opisano sposób wdrażania magazynu [Azure Backup](backup-overview.md) Recovery Services w celu utworzenia kopii zapasowej wielu maszyn wirtualnych platformy Azure przy użyciu programu PowerShell.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz magazyn Recovery Services i ustaw kontekst magazynu.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwól zadanie tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych zanim będzie można utworzyć kopię zapasową (lub chronić) maszyny wirtualnej, musisz spełnić [wymagania wstępne](backup-azure-arm-vms-prepare.md) , aby przygotować środowisko do ochrony maszyn wirtualnych.

> [!IMPORTANT]
> W tym samouczku przyjęto założenie, że utworzono już grupę zasobów i maszynę wirtualną platformy Azure.

## <a name="sign-in-and-register"></a>Logowanie i rejestrowanie

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

    ```powershell
    Connect-AzAccount
    ```

2. Przy pierwszym użyciu Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji za pomocą [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Jeśli jest już zarejestrowany, Pomiń ten krok.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[Magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, który przechowuje dane kopii zapasowej chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

* W tym samouczku utworzysz magazyn w tej samej grupie zasobów i lokalizacji co maszyna wirtualna, dla której chcesz utworzyć kopię zapasową.
* Azure Backup automatycznie obsługuje magazyn do przechowywania kopii zapasowych danych. Domyślnie magazyn używa [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). Nadmiarowość geograficzna gwarantuje, że kopia zapasowa danych jest replikowana do pomocniczego regionu platformy Azure, a setki kilometrów od regionu podstawowego.

Utwórz magazyn w następujący sposób:

1. Użyj  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault), aby utworzyć magazyn. Określ nazwę i lokalizację grupy zasobów maszyny wirtualnej, dla której chcesz utworzyć kopię zapasową.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodnie jest przechowywać obiekt magazynu Recovery Services kopii zapasowej w zmiennej.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Ustaw kontekst magazynu za pomocą [opcji Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Kontekst magazynu to typ danych chronionych w magazynie.
   * Po ustawieniu kontekstu ma zastosowanie do wszystkich kolejnych poleceń cmdlet

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Kopie zapasowe są uruchamiane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych. Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania.

* Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej raz dziennie o określonej godzinie.
* Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni.

Aby włączyć i utworzyć kopię zapasową maszyny wirtualnej platformy Azure w tym samouczku, wykonaj następujące czynności:

1. Określ kontener w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Każda maszyna wirtualna do tworzenia kopii zapasowych jest elementem. Aby rozpocząć zadanie tworzenia kopii zapasowej, należy uzyskać informacje o maszynie wirtualnej za pomocą [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom kopię zapasową na żądanie przy użyciu[kopii zapasowej AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Pierwsze zadanie początkowej kopii zapasowej tworzy punkt pełnego odzyskiwania.
    * Po początkowej kopii zapasowej każde zadanie tworzenia kopii zapasowej tworzy przyrostowe punkty odzyskiwania.
    * Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Włącz i uruchom kopię zapasową w następujący sposób:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli podczas tworzenia kopii zapasowej maszyny wirtualnej występują problemy, zapoznaj się [z artykułem dotyczącym rozwiązywania problemów](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Usuwanie magazynu usług Recovery Services

Jeśli musisz usunąć magazyn, najpierw usuń punkty odzyskiwania z magazynu, a następnie Wyrejestruj magazyn w następujący sposób:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj](backup-azure-vms-automation.md) się z bardziej szczegółowym przewodnikiem tworzenia kopii zapasowych i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell.
* [Zarządzanie maszynami wirtualnymi platformy Azure i monitorowanie ich](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md)
