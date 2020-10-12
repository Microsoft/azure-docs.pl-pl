---
title: Tworzenie kopii zapasowej udziału plików platformy Azure przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową udziału plików Azure Files przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076996"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Tworzenie kopii zapasowej udziału plików platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób użycia Azure PowerShell do tworzenia kopii zapasowej Azure Files udziału plików za pomocą magazynu [Azure Backup](backup-overview.md) Recovery Services.

W tym artykule wyjaśniono, jak:

> [!div class="checklist"]
>
> * Skonfiguruj program PowerShell i zarejestruj dostawcę Recovery Services.
> * Utwórz magazyn usługi Recovery Services.
> * Skonfiguruj kopię zapasową udziału plików platformy Azure.
> * Uruchom zadanie tworzenia kopii zapasowej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach Recovery Services.
* Zapoznaj się z dokumentacją [polecenia cmdlet](/powershell/module/az.recoveryservices) AZ. RecoveryServices w bibliotece platformy Azure.
* Zapoznaj się z następującą hierarchią obiektów programu PowerShell dla Recovery Services:

  ![Hierarchia obiektów Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skonfiguruj program PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > Minimalna wersja programu PowerShell wymagana do tworzenia kopii zapasowych udziałów plików platformy Azure to AZ. RecoveryServices 2.6.0. Użycie najnowszej wersji lub co najmniej minimalnej wersji pomaga uniknąć problemów z istniejącymi skryptami. Zainstaluj minimalną wersję przy użyciu następującego polecenia programu PowerShell:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Znajdź polecenia cmdlet programu PowerShell dla Azure Backup za pomocą tego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zapoznaj się z aliasami i poleceniami cmdlet dla Azure Backup, Azure Site Recovery i magazynu Recovery Services. Oto przykład tego, co może być widoczne. Nie jest to kompletna lista poleceń cmdlet.

    ![Lista poleceń cmdlet Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure przy użyciu polecenia **Connect-AzAccount**.
5. Na wyświetlonej stronie sieci Web zostanie wyświetlony monit o wprowadzenie poświadczeń konta.

    Alternatywnie możesz uwzględnić poświadczenia konta jako parametr w poleceniu cmdlet **Connect-AzAccount** za pomocą polecenia **-Credential**.

    Jeśli jesteś partnerem programu CSP działającym w imieniu dzierżawy, określ klienta jako dzierżawę. Użyj identyfikatora dzierżawy lub głównej nazwy domeny dzierżawy. Przykładem jest **Connect-AzAccount-dzierżawca "fabrikam.com"**.

6. Skojarz subskrypcję, która ma być używana z kontem, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz Azure Backup po raz pierwszy, użyj polecenia cmdlet **register-AzResourceProvider** , aby zarejestrować dostawcę Recovery Services platformy Azure w ramach subskrypcji:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź, czy dostawcy zarejestrowali się pomyślnie:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia Sprawdź, czy **RegistrationState** zmiany zostały **zarejestrowane**. Jeśli nie, uruchom ponownie polecenie cmdlet **register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest zasobem Menedżer zasobów, dlatego należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub można utworzyć grupę zasobów za pomocą polecenia cmdlet **New-AzResourceGroup** . Podczas tworzenia grupy zasobów należy określić jej nazwę i lokalizację.

Wykonaj następujące kroki, aby utworzyć magazyn Recovery Services:

1. Jeśli nie masz istniejącej grupy zasobów, Utwórz nową przy użyciu polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . W tym przykładzie utworzysz grupę zasobów w regionie zachodnie stany USA:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Użyj polecenia cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) , aby utworzyć magazyn. Określ tę samą lokalizację dla magazynu, który został użyty dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w subskrypcji, użyj polecenie [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do następujących. Należy zauważyć, że dane wyjściowe zapewniają skojarzoną grupę zasobów i lokalizację.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Zapisz obiekt magazynu w zmiennej i ustaw kontekst magazynu.

Wiele Azure Backup poleceń cmdlet wymaga obiektu magazynu Recovery Services jako danych wejściowych, dlatego warto przechowywać obiekt magazynu w zmiennej.

Kontekst magazynu to typ danych chronionych w magazynie. Ustaw go za pomocą polecenia [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Po ustawieniu kontekstu ma zastosowanie do wszystkich kolejnych poleceń cmdlet.

Poniższy przykład ustawia kontekst magazynu dla **testvault**:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobierz identyfikator magazynu

Planujemy zaniechania ustawienia kontekstu magazynu zgodnie z zaleceniami Azure PowerShell. Zamiast tego można zapisać lub pobrać identyfikator magazynu i przekazać go do odpowiednich poleceń. Jeśli nie ustawiono kontekstu magazynu lub chcesz określić polecenie do uruchomienia dla pewnego magazynu, Przekaż identyfikator magazynu jako `-vaultID` wszystkie odpowiednie polecenia w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają harmonogram tworzenia kopii zapasowych i czas przechowywania punktów odzyskiwania kopii zapasowych.

Zasady tworzenia kopii zapasowej są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określają czas przechowywania punktu odzyskiwania przed jego usunięciem. Kopie zapasowe można konfigurować codziennie, co tydzień, co miesiąc lub co rok.

Oto kilka poleceń cmdlet dla zasad tworzenia kopii zapasowych:

* Wyświetl domyślne przechowywanie zasad kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Wyświetlanie domyślnego harmonogramu zasad tworzenia kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Utwórz nowe zasady kopii zapasowej za pomocą polecenia [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Należy wprowadzić obiekty zasad harmonogramu i przechowywania jako dane wejściowe.

Domyślnie w obiekcie zasad harmonogramu jest zdefiniowany czas rozpoczęcia. Użyj poniższego przykładu, aby zmienić godzinę rozpoczęcia na żądaną godzinę rozpoczęcia. Wymagana godzina rozpoczęcia powinna być w formacie UTC (Universal Coordinated Time). W przykładzie przyjęto założenie, że żądany czas rozpoczęcia to 01:00 czasu UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać godzinę rozpoczęcia tylko w przypadku wielokrotności 30-minutowych. W poprzednim przykładzie może to być tylko "01:00:00" lub "02:30:00". Godzina rozpoczęcia nie może być "01:15:00".

W poniższym przykładzie są przechowywane zasady harmonogramu i zasady przechowywania w zmiennych. Następnie używa tych zmiennych jako parametrów dla nowych zasad (**NewAFSPolicy**). **NewAFSPolicy** pobiera codzienne kopie zapasowe i zachowuje ją przez 30 dni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe będą podobne do następujących:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Włącz tworzenie kopii zapasowej

Po zdefiniowaniu zasad tworzenia kopii zapasowych można włączyć ochronę udziału plików platformy Azure przy użyciu zasad.

### <a name="retrieve-a-backup-policy"></a>Pobieranie zasad kopii zapasowych

Należy pobrać odpowiedni obiekt zasad przy użyciu polecenia [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). To polecenie cmdlet służy do wyświetlania zasad skojarzonych z typem obciążenia lub do uzyskiwania określonych zasad.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Pobieranie zasad dla typu obciążenia

Poniższy przykład pobiera zasady dla typu obciążenia **migracji pamięci**:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Dane wyjściowe będą podobne do następujących:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Strefa czasowa pola **nieprzerwanego** działania w programie PowerShell jest w formacie UTC. Gdy w Azure Portal zostanie wyświetlony czas tworzenia kopii zapasowej, czas jest dostosowywany do lokalnej strefy czasowej.

#### <a name="retrieve-a-specific-policy"></a>Pobieranie określonych zasad

Następujące zasady pobierają zasady kopii zapasowej o nazwie **dailyafs**:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Włączanie ochrony i stosowanie zasad

Włącz ochronę za pomocą polecenia [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Po skojarzeniu zasad z magazynem kopie zapasowe są wyzwalane zgodnie z harmonogramem zasad.

Poniższy przykład umożliwia ochronę udziału plików platformy Azure **testAzureFileShare** na koncie magazynu **testStorageAcct**przy użyciu zasad **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka na zakończenie zadania konfigurowania ochrony i daje dane wyjściowe podobne do poniższego przykładu:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Aby uzyskać więcej informacji na temat pobierania listy udziałów plików dla konta magazynu, zobacz [ten artykuł](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Ważna Uwaga: Identyfikacja elementu kopii zapasowej

W tej części przedstawiono ważne zmiany w kopiach zapasowych udziałów plików platformy Azure w celu zapewnienia ogólnej dostępności.

Podczas włączania tworzenia kopii zapasowej dla udziałów plików platformy Azure użytkownik uzyskuje klientowi nazwę udziału plików jako nazwę jednostki, a tworzony jest element kopii zapasowej. Nazwa elementu kopii zapasowej jest unikatowym identyfikatorem, który tworzy usługa Azure Backup. Zazwyczaj identyfikator jest przyjazny dla użytkownika. Jednak aby obsłużyć scenariusz usuwania nietrwałego, w którym można usunąć udział plików, a inny udział plików może być utworzony o tej samej nazwie, unikatowa tożsamość udziału plików platformy Azure jest teraz IDENTYFIKATORem.

Aby znać unikatowy identyfikator każdego elementu, uruchom polecenie **Get-AzRecoveryServicesBackupItem** z odpowiednimi filtrami dla **backupManagementType** i **obciążeniatype** , aby uzyskać wszystkie odpowiednie elementy. Następnie zaobserwuj pole Nazwa w zwracanym obiekcie/odpowiedzi programu PowerShell.

Zalecamy, aby wyświetlić listę elementów, a następnie pobrać ich unikatową nazwę z pola Nazwa w odpowiedzi. Użyj tej wartości, aby odfiltrować elementy o parametrze *name* . W przeciwnym razie użyj parametru *FriendlyName* , aby pobrać element z jego identyfikatorem.

> [!IMPORTANT]
> Upewnij się, że program PowerShell został uaktualniony do minimalnej wersji (AZ. RecoveryServices 2.6.0) na potrzeby tworzenia kopii zapasowych udziałów plików platformy Azure. W tej wersji filtr *FriendlyName* jest dostępny dla polecenia **Get-AzRecoveryServicesBackupItem** .
>
> Przekaż nazwę udziału plików platformy Azure do parametru *FriendlyName* . Jeśli przekażesz nazwę udziału plików do parametru *name* , ta wersja zgłasza ostrzeżenie w celu przekazania nazwy do parametru *FriendlyName* .
>
> Zainstalowanie minimalnej wersji może spowodować niepowodzenie istniejących skryptów. Zainstaluj minimalną wersję programu PowerShell przy użyciu następującego polecenia:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Użyj [Narzędzia Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) , aby uruchomić kopię zapasową na żądanie dla chronionego udziału plików platformy Azure:

1. Pobierz konto magazynu z kontenera w magazynie, który przechowuje dane kopii zapasowej za pomocą polecenia [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Aby rozpocząć zadanie tworzenia kopii zapasowej, Uzyskaj informacje o udziale plików platformy Azure za pomocą polecenia [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom kopię zapasową na żądanie za pomocą polecenia [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Wykonaj kopię zapasową na żądanie w następujący sposób:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Polecenie zwraca zadanie z IDENTYFIKATORem, który ma być śledzony, jak pokazano w następującym przykładzie:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Migawki udziałów plików platformy Azure są używane podczas wykonywania kopii zapasowych. Zwykle zadanie kończy się przez czas, gdy polecenie zwraca te dane wyjściowe.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [tworzenia kopii zapasowych Azure Files w Azure Portal](backup-afs.md).
* Zaplanuj tworzenie kopii zapasowych Azure Automation za pomocą [przykładowego skryptu w witrynie GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) .
