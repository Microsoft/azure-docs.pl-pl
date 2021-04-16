---
title: Rozwiązywanie problemów z kopią zapasową udziału plików platformy Azure
description: W tym artykule znajdują się informacje dotyczące rozwiązywania problemów występujących podczas ochrony udziałów plików platformy Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515077"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Rozwiązywanie problemów podczas kopii zapasowej udziałów plików platformy Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów, które można rozwiązać podczas konfigurowania kopii zapasowej lub przywracania udziałów plików platformy Azure przy użyciu usługi Azure Backup Service.

## <a name="common-configuration-issues"></a>Typowe problemy z konfiguracją

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Nie można odnaleźć konta magazynu w celu skonfigurowania kopii zapasowej udziału plików platformy Azure

- Poczekaj na zakończenie odnajdywania.
- Sprawdź, czy jakikolwiek udział plików w ramach konta magazynu jest już chroniony za pomocą innego magazynu usługi Recovery Services.

  >[!NOTE]
  >wszystkie udziały plików konta magazynu mogą być chronione w ramach jednego magazynu usługi Recovery Services. Ten skrypt [umożliwia znalezienie](scripts/backup-powershell-script-find-recovery-services-vault.md) magazynu usługi Recovery Services, w którym zarejestrowano konto magazynu.

- Upewnij się, że udział plików nie znajduje się na żadnym z nieobsługiwanych kont magazynu. Aby znaleźć obsługiwane konta magazynu, zapoznaj się z [macierzą obsługi](azure-file-share-support-matrix.md) kopii zapasowych udziałów plików platformy Azure.
- Upewnij się, że łączna długość nazwy konta magazynu i nazwy grupy zasobów nie przekracza 84 znaków w przypadku nowych kont magazynu i 77 znaków w przypadku klasycznych kont magazynu.
- Sprawdź ustawienia zapory konta magazynu, aby upewnić się, że opcja zezwalania zaufanym usługom firmy Microsoft na dostęp do konta magazynu jest włączona.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Błąd w portalu z informacją o niepowodzeniu odnajdywania kont magazynu

Jeśli masz subskrypcję partnera (z włączoną obsługą programu CSP), zignoruj błąd. Jeśli Twoja subskrypcja nie jest włączona dla CSP i nie można wykryć kont magazynu, skontaktuj się z pomocą techniczną.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Wybrana weryfikacja lub rejestracja konta magazynu nie powiodła się

Ponów próbę rejestracji. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Nie można wyświetlić listy lub odnaleźć udziałów plików na wybranym koncie magazynu

- Upewnij się, że konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione po ostatniej weryfikacji lub rejestracji w magazynie.
- Upewnij się, że udział plików, który chcesz chronić, nie został usunięty.
- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na wypadek tworzenia kopii zapasowej udziału plików. Aby znaleźć obsługiwane konta magazynu, zapoznaj się z [macierzą obsługi](azure-file-share-support-matrix.md) kopii zapasowych udziałów plików platformy Azure.
- Sprawdź, czy udział plików jest już chroniony w tym samym magazynie usługi Recovery Services.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Konfiguracja udziału plików kopii zapasowej (lub konfiguracja zasad ochrony) nieudana

- Spróbuj ponownie wykonać konfigurację, aby sprawdzić, czy problem będzie się powtarzać.
- Upewnij się, że udział plików, który chcesz chronić, nie został usunięty.
- Jeśli próbujesz chronić wiele udziałów plików jednocześnie, a niektóre udziały plików nie powiodły się, spróbuj ponownie skonfigurować kopię zapasową dla udziałów plików, które zakończyły się niepowodzeniem.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Nie można usunąć magazynu usługi Recovery Services po usunięciu ochrony udziału plików

W Azure Portal magazynu otwórz konta **magazynu** infrastruktury kopii  >  **zapasowych** magazynu  >  . Wybierz **pozycję Wyrejestruj,** aby usunąć konta magazynu z magazynu usługi Recovery Services.

>[!NOTE]
>Magazyn usługi Recovery Services można usunąć tylko po wyrejestrowyniu wszystkich kont magazynu zarejestrowanych w magazynie.

## <a name="common-backup-or-restore-errors"></a>Typowe błędy tworzenia kopii zapasowej lub przywracania

>[!NOTE]
>Zapoznaj się z [tym dokumentem,](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) aby upewnić się, że masz wystarczające uprawnienia do wykonywania operacji tworzenia kopii zapasowej lub przywracania.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound — operacja nie powiodła się, ponieważ nie znaleziono udziału plików

Kod błędu: FileShareNotFound

Komunikat o błędzie: Operacja nie powiodła się, ponieważ nie znaleziono udziału plików

Upewnij się, że udział plików, który chcesz chronić, nie został usunięty.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable — konto magazynu nie znaleziono lub nie jest obsługiwane

Kod błędu: UserErrorFileShareEndpointUnreachable

Komunikat o błędzie: Nie znaleziono konta magazynu lub nie jest obsługiwane

- Upewnij się, że konto magazynu istnieje w grupie zasobów i nie zostało usunięte z grupy zasobów po ostatniej weryfikacji.

- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na wypadek tworzenia kopii zapasowej udziału plików.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached — osiągnięto maksymalny limit migawek dla tego udziału plików; Po wygaśnięciu starszych wersji będzie można ich więcej

Kod błędu: AFSMaxSnapshotReached

Komunikat o błędzie: Osiągnięto maksymalny limit migawek dla tego udziału plików; Po wygaśnięciu starszych wersji będzie można ich więcej.

- Ten błąd może wystąpić podczas tworzenia wielu kopii zapasowych na żądanie dla udziału plików.
- Istnieje limit 200 migawek na udział plików, w tym migawki wykonane przez Azure Backup. Starsze zaplanowane kopie zapasowych (lub migawki) są czyszczone automatycznie. Kopie zapasowe na żądanie (lub migawki) muszą zostać usunięte po osiągnięciu maksymalnego limitu.

Usuń kopie zapasowe na żądanie (migawki udziałów plików platformy Azure) z portalu usługi Azure Files.

>[!NOTE]
> punkty odzyskiwania zostaną utracone w przypadku usunięcia migawek utworzonych za pomocą usługi Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound — operacja nie powiodła się, ponieważ określone konto magazynu już nie istnieje

Kod błędu: UserErrorStorageAccountNotFound

Komunikat o błędzie: Operacja nie powiodła się, ponieważ określone konto magazynu już nie istnieje.

Upewnij się, że konto magazynu nadal istnieje i nie jest usuwane.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound — podane szczegóły konta magazynu są nieprawidłowe

Kod błędu: UserErrorDTSStorageAccountNotFound

Komunikat o błędzie: Podane szczegóły konta magazynu są nieprawidłowe.

Upewnij się, że konto magazynu nadal istnieje i nie jest usuwane.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound — grupa zasobów nie istnieje

Kod błędu: UserErrorResourceGroupNotFound

Komunikat o błędzie: Grupa zasobów nie istnieje

Wybierz istniejącą grupę zasobów lub utwórz nową grupę zasobów.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest — zadanie tworzenia kopii zapasowej jest już w toku dla tego udziału plików

Kod błędu: ParallelSnapshotRequest

Komunikat o błędzie: Zadanie tworzenia kopii zapasowej jest już w toku dla tego udziału plików.

- Kopia zapasowa udziału plików nie obsługuje równoległych żądań migawek względem tego samego udziału plików.

- Poczekaj na zakończenie istniejącego zadania tworzenia kopii zapasowej, a następnie spróbuj ponownie. Jeśli nie możesz znaleźć zadania tworzenia kopii zapasowej w magazynie usługi Recovery Services, sprawdź inne magazyny usługi Recovery Services w tej samej subskrypcji.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>Udział plikówBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling — tworzenie kopii zapasowej lub przywracanie udziału plików nie powiodło się z powodu ograniczenia usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu

Kod błędu: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Komunikat o błędzie: Tworzenie kopii zapasowej lub przywracanie udziału plików nie powiodło się z powodu ograniczenia usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu.

Spróbuj wykonać operację tworzenia/przywracania kopii zapasowej w późniejszym czasie.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound — nie znaleziono docelowego udziału plików

Kod błędu: TargetFileShareNotFound

Komunikat o błędzie: Nie znaleziono docelowego udziału plików.

- Upewnij się, że wybrane konto magazynu istnieje, a docelowy udział plików nie został usunięty.

- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na wypadek tworzenia kopii zapasowej udziału plików.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked — zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym

Kod błędu: UserErrorStorageAccountIsLocked

Komunikat o błędzie: Zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym.

Usuń blokadę konta magazynu lub użyj  blokady **usuwania** zamiast blokady odczytu, a następnie spróbuj ponownie wykonać operację tworzenia kopii zapasowej lub przywracania.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached — odzyskiwanie nie powiodło się, ponieważ liczba plików, które zakończyły się niepowodzeniem, przekracza wartość progową

Kod błędu: DataTransferServiceCoFLimitReached

Komunikat o błędzie: Odzyskiwanie nie powiodło się, ponieważ liczba plików, które się nie powiodły, przekracza wartość progową.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka podaną w szczegółach zadania). Należy rozwiązać te błędy i ponowić próbę wykonania operacji przywracania tylko dla plików, których działanie zakończyło się niepowodzeniem.

- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, które nie powiodły się, są obecnie w użyciu
  - Katalog o takiej samej nazwie jak plik, który zakończył się niepowodzeniem, istnieje w katalogu nadrzędnym.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover — odzyskiwanie nie powiodło się, ponieważ nie można odzyskać żadnego pliku

Kod błędu: DataTransferServiceAllFilesFailedToRecover

Komunikat o błędzie: Odzyskiwanie nie powiodło się, ponieważ nie można odzyskać żadnego pliku.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka podaną w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem.

- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, które nie powiodły się, są obecnie w użyciu
  - Katalog o takiej samej nazwie jak plik, który zakończył się niepowodzeniem, istnieje w katalogu nadrzędnym.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid — przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje

Kod błędu: DataTransferServiceSourceUriNotValid

Komunikat o błędzie: Przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje.

- Wybrane elementy nie istnieją w danych punktu odzyskiwania. Aby odzyskać pliki, udostępnij poprawną listę plików.
- Migawka udziału plików odpowiadająca punktowi odzyskiwania została ręcznie usunięta. Wybierz inny punkt odzyskiwania, a następnie spróbuj ponownie wykonać operację przywracania.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked — zadanie odzyskiwania jest w trakcie przetwarzania w tym samym miejscu docelowym

Kod błędu: UserErrorDTSDestLocked

Komunikat o błędzie: Zadanie odzyskiwania jest w trakcie przetwarzania w tym samym miejscu docelowym.

- Kopia zapasowa udziału plików nie obsługuje odzyskiwania równoległego do tego samego docelowego udziału plików.

- Poczekaj na zakończenie trwającej operacji odzyskiwania, a następnie spróbuj ponownie. Jeśli nie możesz znaleźć zadania odzyskiwania w magazynie usługi Recovery Services, sprawdź inne magazyny usługi Recovery Services w tej samej subskrypcji.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull — operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny

Kod błędu: UserErrorTargetFileShareFull

Komunikat o błędzie: Operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny.

Zwiększ limit przydziału rozmiaru docelowego udziału plików, aby obsłużyć dane przywracania, i spróbuj ponownie wykonać operację przywracania.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient — docelowy udział plików nie ma wystarczającego limitu przydziału rozmiaru magazynu do przywrócenia

Kod błędu: UserErrorTargetFileShareQuotaNotSufficient

Komunikat o błędzie: Docelowy udział plików nie ma wystarczającego limitu przydziału rozmiaru magazynu do przywrócenia

Zwiększ limit przydziału rozmiaru docelowego udziału plików, aby obsłużyć dane przywracania, i spróbuj ponownie wykonać operację

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed — operacja przywracania nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przywracania wstępnego na zasobach usługi File Sync Service skojarzonych z docelowym udziałem plików

Kod błędu: File Sync PreRestoreFailed

Komunikat o błędzie: Operacja przywracania nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przywracania wstępnego File Sync Service skojarzonych z docelowym udziałem plików.

Spróbuj przywrócić dane w późniejszym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress — wykrywanie zmian Azure File Sync service jest w toku dla docelowego udziału plików. Wykrywanie zmian zostało wyzwolone przez poprzednie przywrócenie do docelowego udziału plików

Kod błędu: AzureFileSyncChangeDetectionInProgress

Komunikat o błędzie: Azure File Sync wykrywania zmian usługi jest w toku dla docelowego udziału plików. Wykrywanie zmian zostało wyzwolone przez poprzednie przywrócenie do docelowego udziału plików.

Użyj innego docelowego udziału plików. Możesz też poczekać na ukończenie wykrywania zmian Azure File Sync Service dla docelowego udziału plików przed ponowieniem próby przywrócenia.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored — nie można pomyślnie odzyskać co najmniej jednego pliku. Aby uzyskać więcej informacji, sprawdź listę plików, które zakończyły się niepowodzeniem, w podanej powyżej ścieżce

Kod błędu: UserErrorAFSRecoverySomeFilesNotRestored

Komunikat o błędzie: Nie można pomyślnie odzyskać co najmniej jednego pliku. Aby uzyskać więcej informacji, sprawdź listę plików zakończonych niepowodzeniem w ramach podanej powyżej ścieżki.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka w szczegółach zadania). Należy rozwiązać przyczyny i ponowić próbę wykonania operacji przywracania tylko dla plików, których działanie zakończyło się niepowodzeniem.
- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, które nie powiodły się, są obecnie w użyciu
  - Katalog o takiej samej nazwie jak plik, który zakończył się niepowodzeniem, istnieje w katalogu nadrzędnym.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound — nie można odnaleźć migawki udziału plików platformy Azure odpowiadającej punktowi odzyskiwania

Kod błędu: UserErrorAFSSourceSnapshotNotFound

Komunikat o błędzie: Nie można odnaleźć migawki udziału plików platformy Azure odpowiadającej punktowi odzyskiwania

- Upewnij się, że migawka udziału plików odpowiadająca punktowi odzyskiwania, który próbujesz użyć do odzyskiwania, nadal istnieje.

  >[!NOTE]
  >Jeśli usuniesz migawkę udziału plików, która została utworzona przez Azure Backup, odpowiednie punkty odzyskiwania staną się bezużytelne. Nie zalecamy usuwania migawek w celu zapewnienia gwarantowanego odzyskiwania.

- Spróbuj wybrać inny punkt przywracania, aby odzyskać dane.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget — trwa kolejne zadanie przywracania dla tego samego docelowego udziału plików

Kod błędu: UserErrorAnotherRestoreInProgressOnSameTarget

Komunikat o błędzie: Inne zadanie przywracania jest w toku dla tego samego docelowego udziału plików

Użyj innego docelowego udziału plików. Alternatywnie możesz anulować swoją operację lub poczekać na zakończenie innej operacji przywracania.

## <a name="common-modify-policy-errors"></a>Typowe błędy zasad modyfikowania

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation — inna operacja konfigurowania ochrony jest w toku dla tego elementu

Kod błędu: BMSUserErrorConflictingProtectionOperation

Komunikat o błędzie: Inna operacja konfigurowania ochrony jest w toku dla tego elementu.

Poczekaj na zakończenie poprzedniej operacji modyfikowania zasad i spróbuj ponownie później.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked — inna operacja jest w toku dla wybranego elementu

Kod błędu: BMSUserErrorObjectLocked

Komunikat o błędzie: Inna operacja jest w toku dla wybranego elementu.

Poczekaj na zakończenie innej operacji w toku i spróbuj ponownie później.

## <a name="common-soft-delete-related-errors"></a>Typowe błędy związane z usuwaniem nie soft-delete

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState — ten punkt przywracania nie jest dostępny, ponieważ migawka skojarzona z tym punktem znajduje się w udziałach plików, które są w stanie usunięcia nietrwistego

Kod błędu: UserErrorRestoreAFSInSoftDeleteState

Komunikat o błędzie: Ten punkt przywracania nie jest dostępny, ponieważ migawka skojarzona z tym punktem znajduje się w udziału plików, który jest w stanie usunięcia nieukreślone.

Nie można wykonać operacji przywracania, gdy udział plików jest w stanie usunięcia nie soft. Cofniesz przywrócenie udziału plików z [](scripts/backup-powershell-script-undelete-file-share.md) portalu usługi Files lub za pomocą skryptu Cofniej przywrócenie, a następnie spróbuj go przywrócić.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState — wymienione punkty przywracania nie są dostępne, ponieważ skojarzony udział plików zawierający migawki punktów przywracania został trwale usunięty

Kod błędu: UserErrorRestoreAFSInDeleteState

Komunikat o błędzie: Wymienione punkty przywracania nie są dostępne, ponieważ skojarzony udział plików zawierający migawki punktów przywracania został trwale usunięty.

Sprawdź, czy udział plików kopii zapasowej został usunięty. Jeśli był on w stanie usunięcia nieu programowego, sprawdź, czy okres przechowywania usuwania nieukońcowego został ugodowy i nie został odzyskany. W każdym z tych przypadków utracisz wszystkie migawki trwale i nie będzie można odzyskać danych.

>[!NOTE]
> Zalecamy, aby nie usuwać kopii zapasowej udziału plików lub, jeśli jest on w stanie usunięcia nie soft, cofnąć usunięcie przed zakończeniem okresu przechowywania usuwania nie soft, aby uniknąć utraty wszystkich punktów przywracania.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState — tworzenie kopii zapasowej nie powiodło się, ponieważ udział plików platformy Azure jest w stanie usunięcia nie soft

Kod błędu: UserErrorBackupAFSInSoftDeleteState

Komunikat o błędzie: Tworzenie kopii zapasowej nie powiodło się, ponieważ udział plików platformy Azure jest w stanie usunięcia nieużytego

Cofniesz usunięcie udziału plików z portalu [](scripts/backup-powershell-script-undelete-file-share.md) **Plików** lub za pomocą skryptu Cofń usunięcie, aby kontynuować tworzenie kopii zapasowej i zapobiec trwałemu usunięciu danych.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState — tworzenie kopii zapasowej nie powiodło się, ponieważ skojarzony udział plików platformy Azure został trwale usunięty

Kod błędu: UserErrorBackupAFSInDeleteState

Komunikat o błędzie: Tworzenie kopii zapasowej nie powiodło się, ponieważ skojarzony udział plików platformy Azure został trwale usunięty

Sprawdź, czy udział plików kopii zapasowej został trwale usunięty. Jeśli tak, zatrzymaj tworzenie kopii zapasowej udziału plików, aby uniknąć powtarzających się niepowodzeń tworzenia kopii zapasowej. Aby dowiedzieć się, jak zatrzymać ochronę, zobacz [Zatrzymywanie ochrony udziału plików platformy Azure](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kopii zapasowej udziałów plików platformy Azure, zobacz:

- [Tworzenie kopii zapasowej udziałów plików platformy Azure](backup-afs.md)
- [Często zadawane pytania dotyczące kopii zapasowej udziału plików platformy Azure](backup-azure-files-faq.yml)
