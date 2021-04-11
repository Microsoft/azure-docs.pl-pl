---
title: Obsługa warstwy archiwum (wersja zapoznawcza)
description: Dowiedz się więcej o obsłudze warstwy Archiwum dla Azure Backup
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 7a42b8702cfdda14a18aa3cdd4e084ed78767b0a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012152"
---
# <a name="archive-tier-support-preview"></a>Obsługa warstwy archiwum (wersja zapoznawcza)

Klienci korzystają z Azure Backup do przechowywania danych kopii zapasowej, w tym ich Long-Term przechowywania (LTR), a dane kopii zapasowej są definiowane przez reguły zgodności organizacji. W większości przypadków starsze dane kopii zapasowej są rzadko używane i są przechowywane tylko w celu zapewnienia zgodności.

Azure Backup obsługuje tworzenie kopii zapasowych długoterminowych punktów przechowywania w warstwie archiwum, oprócz migawek i warstwy Standardowa.

## <a name="scope-for-preview"></a>Zakres dla wersji zapoznawczej

Obsługiwane obciążenia:

- Maszyny wirtualne platformy Azure
  - Tylko miesięczne i roczne punkty odzyskiwania. Codzienne i cotygodniowe punkty odzyskiwania nie są obsługiwane.
  - Wiek >= 3 miesiące w warstwie Vault-Standard
  - Zatrzymywanie w lewo >= 6 miesięcy
  - Brak aktywnych codziennych i tygodniowych zależności
- SQL Server w usłudze Azure Virtual Machines
  - Tylko pełne punkty odzyskiwania. Dzienniki i różnice nie są obsługiwane.
  - Wiek >= 45 dni w warstwie Vault-Standard
  - Zatrzymywanie w lewo >= 6 miesięcy
  - Brak zależności

Obsługiwani klienci:

- Możliwość jest dostępna przy użyciu programu PowerShell

>[!NOTE]
>Obsługa warstwy Archiwum dla maszyn wirtualnych platformy Azure i SQL Server na maszynach wirtualnych platformy Azure jest w ograniczonej publicznej wersji zapoznawczej z ograniczoną liczbą rejestracji. Aby utworzyć konto do obsługi archiwum, użyj tego [linku](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Wprowadzenie do programu PowerShell

1. Pobierz [najnowszą](https://github.com/PowerShell/PowerShell/releases) wersję programu PowerShell z usługi GitHub.

1. Uruchom następujące polecenie w programie PowerShell:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Połącz się z platformą Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Zaloguj się do subskrypcji:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Pobierz magazyn:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Pobierz listę elementów kopii zapasowej:

    - W przypadku maszyn wirtualnych platformy Azure:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - SQL Server w usłudze Azure Virtual Machines:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. Pobierz element kopii zapasowej.

    - W przypadku maszyn wirtualnych platformy Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - SQL Server w usłudze Azure Virtual Machines:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

### <a name="check-archivable-recovery-points"></a>Sprawdź punkty odzyskiwania z archiwum

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Spowoduje to wyświetlenie listy wszystkich punktów odzyskiwania skojarzonych z określonym elementem kopii zapasowej, które są gotowe do przeniesienia do archiwum.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Sprawdź, dlaczego punkt odzyskiwania nie może zostać przeniesiony do archiwum

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Gdzie `$rp[0]` jest punktem odzyskiwania, dla którego chcesz sprawdzić, dlaczego nie jest on archiwizowany.

Przykładowe dane wyjściowe:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Sprawdź zalecany zestaw punktów archiwizowania (tylko dla maszyn wirtualnych platformy Azure)

Punkty odzyskiwania skojarzone z maszyną wirtualną mają charakter przyrostowy. Gdy określony punkt odzyskiwania jest przenoszony do archiwum, jest konwertowany na pełną kopię zapasową, a następnie przenoszony do archiwum. Dzięki temu oszczędności związane z przenoszeniem do archiwum są zależne od zmian źródła danych.

Dlatego Azure Backup zostało dołączone do zalecanego zestawu punktów odzyskiwania, które mogą spowodować oszczędność kosztów, jeśli są one przenoszone razem.

>[!NOTE]
>Oszczędności kosztów są zależne od różnych powodów i mogą nie być takie same dla żadnego z dwóch wystąpień.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Przenieś do archiwum

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

To polecenie przenosi punkt odzyskiwania archiwizowany do archiwum. Zwraca zadanie, które może służyć do śledzenia operacji przenoszenia zarówno z portalu, jak i programu PowerShell.

### <a name="view-archived-recovery-points"></a>Wyświetlanie zarchiwizowanych punktów odzyskiwania

To polecenie zwraca wszystkie zarchiwizowane punkty odzyskiwania.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Przywracanie za pomocą programu PowerShell

W przypadku punktów odzyskiwania w archiwum Azure Backup zapewnia zintegrowaną metodologię przywracania.

Zintegrowane przywracanie jest procesem dwuetapowym. Pierwszy krok obejmuje ponownego wypełniania punktów odzyskiwania przechowywanych w archiwum i tymczasowe przechowywanie ich w warstwie Standardowa magazynu przez czas trwania (znany również jako czas odzyskiwania) od okresu od 10 do 30 dni. Wartość domyślna to 15 dni. Istnieją dwa różne priorytety uzupełniania — standardowy i wysoki priorytet. Dowiedz się więcej o [priorytecie](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)ponownego uzupełniania.

>[!NOTE]
>
>- Nie można zmienić czasu trwania ponownego wypełniania, a odzyskane punkty odzyskiwania pozostają w warstwie Standardowa za czas odzyskiwania.
>- Dodatkowy krok uzupełniania jest kosztem.

Aby uzyskać więcej informacji na temat różnych metod przywracania dla usługi Azure Virtual Machines, zobacz [przywracanie maszyny wirtualnej platformy Azure za pomocą programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Aby przywrócić SQL Server, wykonaj następujące [kroki](backup-azure-sql-automation.md#restore-sql-dbs). Wymagane są dodatkowe parametry **RehydrationPriority** i **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Wyświetlanie zadań z programu PowerShell

Aby wyświetlić zadania przenoszenia i przywracania, użyj następującego polecenia cmdlet programu PowerShell:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Używanie portalu

### <a name="check-archived-recovery-point"></a>Sprawdź zarchiwizowany punkt odzyskiwania

Teraz można wyświetlić wszystkie punkty odzyskiwania, które zostały przeniesione do archiwum.

![Wszystkie punkty odzyskiwania.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Przywracanie w portalu

Dla punktów odzyskiwania, które zostały przeniesione do archiwum, funkcja Restore wymaga dodania parametrów w celu odzyskania czasu trwania i odzyskania.

![Przywracanie w portalu.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Wyświetlanie zadań w portalu

![Wyświetl zadania w portalu.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Modyfikowanie ochrony

Istnieją dwa sposoby modyfikacji ochrony źródła danych:

- Modyfikowanie istniejącej zasady
- Ochrona źródła danych przy użyciu nowych zasad

W obu przypadkach nowe zasady są stosowane do wszystkich starszych punktów odzyskiwania, które znajdują się w warstwie Standardowa, oraz w warstwach archiwum. Tak więc starsze punkty odzyskiwania mogą zostać usunięte w przypadku zmiany zasad.

Po przeniesieniu punktów odzyskiwania do archiwum zostaną one objęte okresem wczesnego usunięcia wynoszącym 180 dni. Opłaty są naliczane proporcjonalnie. Jeśli punkt odzyskiwania, który nie został jeszcze zarchiwizowany przez 180 dni, zostanie usunięty, nastąpi obniżenie kosztów równy 180 minus liczba dni spędzonych w warstwie Standardowa.

Punkty odzyskiwania, które nie były archiwizowane przez co najmniej sześć miesięcy, spowodują naliczenie opłaty za wczesne usunięcie.

## <a name="stop-protection-and-delete-data"></a>Zatrzymywanie ochrony i usuwanie danych

Zatrzymaj ochronę i Usuń dane usuwa wszystkie punkty odzyskiwania. W przypadku punktów odzyskiwania w archiwum, które nie miały czasu trwania 180 dni w warstwie archiwum, usuwanie punktów odzyskiwania będzie prowadzić do ponoszenia kosztów wczesnego usuwania.

## <a name="error-codes-and-troubleshooting-steps"></a>Kody błędów i kroki rozwiązywania problemów

Istnieje kilka kodów błędów, które są dostępne, gdy nie można przenieść punktu odzyskiwania do archiwum.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Komunikat o błędzie** — typ Recovery-Point nie kwalifikuje się do przeniesienia Archiwum

**Opis** — ten kod błędu jest wyświetlany, gdy wybrany typ punktu odzyskiwania nie kwalifikuje się do przeniesienia do archiwum.

**Zalecana akcja** — Sprawdź uprawnienia punktu odzyskiwania [tutaj](#scope-for-preview)

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Komunikat o błędzie** — Recovery-Point z aktywnymi zależnościami dla przywracania nie kwalifikują się do przeniesienia Archiwum

**Opis —** Wybrany punkt odzyskiwania ma aktywne zależności i dlatego nie można go przenieść do archiwum.

**Zalecana akcja** — Sprawdź uprawnienia punktu odzyskiwania [tutaj](#scope-for-preview)

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Komunikat o błędzie** — Recovery-Point nie kwalifikuje się do przechodzenia do archiwum jako cykl życia w magazynie — warstwa standardowa jest mniejsza niż wymagana wartość minimalna

**Opis** — punkt odzyskiwania musi pozostać w warstwie Standardowa przez co najmniej trzy miesiące w przypadku maszyn wirtualnych platformy azure i 45 dni dla SQL Server na maszynach wirtualnych platformy Azure

**Zalecana akcja** — Sprawdź uprawnienia punktu odzyskiwania [tutaj](#scope-for-preview)

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Komunikat o błędzie** — Recovery-Point pozostała wartość jest mniejsza niż wymagana wartość minimalna.

**Opis** — minimalny cykl życia wymagany dla punktu odzyskiwania w celu przeniesienia do archiwum wynosi sześć miesięcy.

**Zalecana akcja** — Sprawdź uprawnienia punktu odzyskiwania [tutaj](#scope-for-preview)

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Komunikat o błędzie** — Recovery-Point nie kwalifikuje się do przeniesienia archiwum, ponieważ został on już przeniesiony do warstwy Archiwum

**Opis** — wybrany punkt odzyskiwania jest już w archiwum. Dlatego nie kwalifikuje się do przeniesienia do archiwum.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Komunikat o błędzie** — typ DataSource nie kwalifikuje się do interfejsu API rekomendacji.

**Opis** — interfejs API rekomendacji ma zastosowanie tylko do maszyn wirtualnych platformy Azure. Nie dotyczy wybranego typu źródła danych.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Komunikat o błędzie** — punkt odzyskiwania został już przeodwodniony. Uzupełnianie nie jest dozwolone w tym RP.

**Opis** — wybrany punkt odzyskiwania został już poddany dehydratacji.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Komunikat o błędzie** — punkt odzyskiwania nie kwalifikuje się do przeniesienia archiwum.

**Opis** — wybrany punkt odzyskiwania nie kwalifikuje się do przeniesienia archiwum.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

 **Komunikat** błędu — punkt odzyskiwania archiwum nie został ododwodniony. Ponów próbę przywrócenia po zakończeniu ponownego wypełniania w archiwum RP.

**Opis** — punkt odzyskiwania nie jest poddany dehydratacji. Spróbuj przywrócić po ponownego wypełniania punktu odzyskiwania.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Komunikat** **o błędzie** — funkcja uzupełniania jest obsługiwana tylko w przypadku punktów odzyskiwania archiwum — funkcja uzupełniania jest obsługiwana tylko dla punktów odzyskiwania archiwum.

**Opis** — odzyskanie nie jest dozwolone dla wybranego punktu odzyskiwania.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Komunikat o błędzie** — funkcja uzupełniania jest już In-Progress dla punktu odzyskiwania archiwum.

**Opis** — uzupełnianie dla wybranego punktu odzyskiwania jest już w toku.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Komunikat o błędzie** — nie można przenieść punktu odzyskiwania do warstwy archiwum ze względu na niewystarczający czas przechowywania określony w zasadach

**Zalecana akcja** — Aktualizuj zasady dotyczące chronionego elementu przy użyciu odpowiedniego ustawienia przechowywania i spróbuj ponownie.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Komunikat o błędzie** — nadal określamy, czy można przenieść ten punkt odzyskiwania.

**Opis** — należy jeszcze określić gotowość do przeniesienia punktu odzyskiwania.

**Zalecana akcja** — Sprawdź ponownie po upływie pewnego czasu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Co będzie miało miejsce archiwizowanie punktów odzyskiwania, jeśli zatrzymasz ochronę i zachowasz dane?

Punkt odzyskiwania będzie nadal w archiwum. Aby uzyskać więcej informacji, zobacz [wpływ zatrzymania ochrony w punktach odzyskiwania](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Następne kroki

- [Cennik usługi Azure Backup](azure-backup-pricing.md)