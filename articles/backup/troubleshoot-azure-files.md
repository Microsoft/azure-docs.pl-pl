---
title: Rozwiązywanie problemów z usługą Azure File Share
description: W tym artykule znajdują się informacje dotyczące rozwiązywania problemów występujących podczas ochrony udziałów plików platformy Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89376982"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Rozwiązywanie problemów podczas tworzenia kopii zapasowych udziałów plików platformy Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów w celu rozwiązania problemów występujących podczas konfigurowania kopii zapasowej lub przywracania udziałów plików platformy Azure przy użyciu usługi Azure Backup.

## <a name="common-configuration-issues"></a>Typowe problemy z konfiguracją

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Nie można odnaleźć konta magazynu w celu skonfigurowania kopii zapasowej udziału plików platformy Azure

- Poczekaj na zakończenie odnajdywania.
- Sprawdź, czy udział plików w ramach konta magazynu jest już chroniony przez inny magazyn Recovery Services.

  >[!NOTE]
  >wszystkie udziały plików konta magazynu mogą być chronione w ramach jednego magazynu usługi Recovery Services. Za pomocą [tego skryptu](scripts/backup-powershell-script-find-recovery-services-vault.md) można znaleźć magazyn Recovery Services, w którym zarejestrowano konto magazynu.

- Upewnij się, że udział plików nie znajduje się na żadnym z nieobsługiwanych kont magazynu. Aby znaleźć obsługiwane konta magazynu, można zapoznać się z [macierzą pomocy technicznej dla kopii zapasowej udziału plików platformy Azure](azure-file-share-support-matrix.md) .
- Upewnij się, że łączna długość nazwy konta magazynu i nazwy grupy zasobów nie przekracza 84 znaków w przypadku nowych kont magazynu i 77 znaków w przypadku klasycznych kont magazynu.
- Sprawdź ustawienia zapory konta magazynu, aby upewnić się, że opcja zezwalania zaufanym usługom firmy Microsoft na dostęp do konta magazynu jest włączona.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Błąd w portalu odnajdywanie kont magazynu nie powiodło się

Jeśli masz subskrypcję partnera (z włączoną obsługą dostawcy usług kryptograficznych), zignoruj błąd. Jeśli subskrypcja nie jest włączona przez dostawcę CSP i nie można odnaleźć kont magazynu, skontaktuj się z pomocą techniczną.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Nie można przeprowadzić walidacji lub rejestracji konta magazynu

Ponów próbę rejestracji. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Nie można wyświetlić lub znaleźć udziałów plików na wybranym koncie magazynu

- Upewnij się, że konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione po ostatniej weryfikacji lub rejestracji w magazynie.
- Upewnij się, że udział plików, który chcesz chronić, nie został usunięty.
- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na potrzeby tworzenia kopii zapasowych udziałów plików. Aby znaleźć obsługiwane konta magazynu, można zapoznać się z [macierzą pomocy technicznej dla kopii zapasowej udziału plików platformy Azure](azure-file-share-support-matrix.md) .
- Sprawdź, czy udział plików jest już chroniony w tym samym magazynie Recovery Services.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Konfiguracja udziału plików kopii zapasowej (lub konfiguracja zasad ochrony) kończy się niepowodzeniem

- Spróbuj ponownie wykonać konfigurację, aby sprawdzić, czy problem będzie się powtarzać.
- Upewnij się, że udział plików, który chcesz chronić, nie został usunięty.
- Jeśli próbujesz chronić wiele udziałów plików jednocześnie, a niektóre udziały plików kończą się niepowodzeniem, spróbuj ponownie skonfigurować kopie zapasowe dla niezakończonych udziałów plików.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Nie można usunąć magazynu Recovery Services po wyłączeniu ochrony udziału plików

W Azure Portal Otwórz **Vault**  >  konta magazynu**infrastruktury kopii zapasowej**magazynu  >  **Storage accounts**. Wybierz pozycję **Wyrejestruj** , aby usunąć konta magazynu z magazynu Recovery Services.

>[!NOTE]
>Magazyn Recovery Services można usunąć tylko po wyrejestrowaniu wszystkich kont magazynu zarejestrowanych w magazynie.

## <a name="common-backup-or-restore-errors"></a>Typowe błędy tworzenia kopii zapasowej lub przywracania

>[!NOTE]
>Zapoznaj się z [tym dokumentem](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) , aby upewnić się, że masz wystarczające uprawnienia do wykonywania operacji tworzenia kopii zapasowej lub przywracania.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound — operacja nie powiodła się, ponieważ nie odnaleziono udziału plików

Kod błędu: FileShareNotFound

Komunikat o błędzie: operacja nie powiodła się, ponieważ nie odnaleziono udziału plików

Upewnij się, że udział plików, który próbujesz chronić, nie został usunięty.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable — nie znaleziono konta magazynu lub nie jest ono obsługiwane

Kod błędu: UserErrorFileShareEndpointUnreachable

Komunikat o błędzie: nie znaleziono konta magazynu lub nie jest ono obsługiwane

- Upewnij się, że konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani usunięte z grupy zasobów po ostatniej weryfikacji.

- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na potrzeby tworzenia kopii zapasowych udziałów plików.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached — Osiągnięto maksymalny limit migawek dla tego udziału plików; po wygaśnięciu starszych wersji będzie możliwe przejęcie dalszych prób

Kod błędu: AFSMaxSnapshotReached

Komunikat o błędzie: Osiągnięto maksymalny limit migawek dla tego udziału plików; po upływie tego czasu starsze wersje będą mogły upłynąć.

- Ten błąd może wystąpić, gdy tworzysz wiele kopii zapasowych na żądanie dla udziału plików.
- Istnieje limit 200 migawek na udział plików, łącznie z tymi pobranymi przez Azure Backup. Starsze zaplanowane kopie zapasowych (lub migawki) są czyszczone automatycznie. Kopie zapasowe na żądanie (lub migawki) muszą zostać usunięte po osiągnięciu maksymalnego limitu.

Usuń kopie zapasowe na żądanie (migawki udziałów plików platformy Azure) z portalu usługi Azure Files.

>[!NOTE]
> punkty odzyskiwania zostaną utracone w przypadku usunięcia migawek utworzonych za pomocą usługi Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound — operacja nie powiodła się, ponieważ określone konto magazynu już nie istnieje

Kod błędu: UserErrorStorageAccountNotFound

Komunikat o błędzie: operacja nie powiodła się, ponieważ określone konto magazynu już nie istnieje.

Upewnij się, że konto magazynu nadal istnieje i nie jest usuwane.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound — podane szczegóły konta magazynu są nieprawidłowe.

Kod błędu: UserErrorDTSStorageAccountNotFound

Komunikat o błędzie: podane szczegóły konta magazynu są nieprawidłowe.

Upewnij się, że konto magazynu nadal istnieje i nie jest usuwane.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound — Grupa zasobów nie istnieje

Kod błędu: UserErrorResourceGroupNotFound

Komunikat o błędzie: Grupa zasobów nie istnieje

Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest — zadanie tworzenia kopii zapasowej jest już w toku dla tego udziału plików

Kod błędu: ParallelSnapshotRequest

Komunikat o błędzie: zadanie tworzenia kopii zapasowej jest już w toku dla tego udziału plików.

- Kopia zapasowa udziału plików nie obsługuje równoległych żądań migawek względem tego samego udziału plików.

- Poczekaj na zakończenie istniejącego zadania tworzenia kopii zapasowej, a następnie spróbuj ponownie. Jeśli nie możesz znaleźć zadania tworzenia kopii zapasowej w magazynie Recovery Services, zaznacz inne magazyny Recovery Services w tej samej subskrypcji.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling — tworzenie kopii zapasowej udziału plików lub przywracanie nie powiodło się ze względu na ograniczenie usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu.

Kod błędu: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Komunikat o błędzie: wykonywanie kopii zapasowej udziału plików lub przywracanie nie powiodło się ze względu na ograniczenie usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu.

Spróbuj wykonać operację tworzenia kopii zapasowej/przywracania w późniejszym czasie.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-docelowy udział plików nie został znaleziony

Kod błędu: TargetFileShareNotFound

Komunikat o błędzie: nie znaleziono docelowego udziału plików.

- Upewnij się, że wybrane konto magazynu istnieje i docelowy udział plików nie został usunięty.

- Upewnij się, że konto magazynu jest obsługiwanym kontem magazynu na potrzeby tworzenia kopii zapasowych udziałów plików.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked — zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym

Kod błędu: UserErrorStorageAccountIsLocked

Komunikat o błędzie: zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym.

Usuń blokadę konta magazynu lub Użyj **blokady usuwania** zamiast **blokady odczytu** i spróbuj wykonać operację tworzenia kopii zapasowej lub przywracania.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached — odzyskiwanie nie powiodło się, ponieważ liczba plików z błędami jest większa niż wartość progowa

Kod błędu: DataTransferServiceCoFLimitReached

Komunikat o błędzie: odzyskiwanie nie powiodło się, ponieważ liczba plików z błędami jest większa niż wartość progowa.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka podana w szczegółach zadania). Zaadresuj błędy i spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem.

- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, których nie powiodło się, są obecnie używane
  - w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover — odzyskiwanie nie powiodło się, ponieważ nie można odzyskać żadnego pliku

Kod błędu: DataTransferServiceAllFilesFailedToRecover

Komunikat o błędzie: odzyskiwanie nie powiodło się, ponieważ nie można odzyskać żadnego pliku.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem.

- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, których nie powiodło się, są obecnie używane
  - w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid — przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje

Kod błędu: DataTransferServiceSourceUriNotValid

Komunikat o błędzie: Przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje.

- Wybrane elementy nie istnieją w danych punktu odzyskiwania. Aby odzyskać pliki, udostępnij poprawną listę plików.
- Migawka udziału plików odpowiadająca punktowi odzyskiwania została ręcznie usunięta. Wybierz inny punkt odzyskiwania, a następnie spróbuj ponownie wykonać operację przywracania.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked — zadanie odzyskiwania jest w toku do tego samego miejsca docelowego

Kod błędu: UserErrorDTSDestLocked

Komunikat o błędzie: zadanie odzyskiwania jest w toku do tego samego miejsca docelowego.

- Kopia zapasowa udziału plików nie obsługuje odzyskiwania równoległego do tego samego docelowego udziału plików.

- Poczekaj na zakończenie trwającej operacji odzyskiwania, a następnie spróbuj ponownie. Jeśli nie możesz znaleźć zadania odzyskiwania w magazynie Recovery Services, zaznacz inne magazyny Recovery Services w tej samej subskrypcji.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull — operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny

Kod błędu: UserErrorTargetFileShareFull

Komunikat o błędzie: operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny.

Zwiększ limit przydziału rozmiaru docelowego udziału plików, aby pomieścić dane przywracania, a następnie spróbuj ponownie wykonać operację przywracania.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient — docelowy udział plików nie ma wystarczającego limitu przydziału rozmiaru magazynu na potrzeby przywracania

Kod błędu: UserErrorTargetFileShareQuotaNotSufficient

Komunikat o błędzie: docelowy udział plików nie ma wystarczającego limitu przydziału rozmiaru magazynu na potrzeby przywracania

Zwiększ limit przydziału rozmiaru docelowego udziału plików, aby uwzględnić przywracanie danych, a następnie spróbuj ponownie wykonać operację.

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed — operacja przywracania nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przed przywróceniem zasobów usługi File Sync skojarzonych z docelowym udziałem plików

Kod błędu: File Sync PreRestoreFailed

Komunikat o błędzie: operacja przywracania nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przed przywróceniem na File Sync zasobów usługi skojarzonych z docelowym udziałem plików.

Spróbuj przywrócić dane w późniejszym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>Trwa Wykrywanie zmiany usługi AzureFileSyncChangeDetectionInProgress Azure File Sync w docelowym udziale plików. Wykrycie zmiany zostało wyzwolone przez poprzednie przywracanie do docelowego udziału plików

Kod błędu: AzureFileSyncChangeDetectionInProgress

Komunikat o błędzie: wykrycie zmiany usługi Azure File Sync w docelowym udziale plików jest w toku. Wykrywanie zmian zostało wyzwolone przez poprzednie przywracanie do docelowego udziału plików.

Użyj innego docelowego udziału plików. Alternatywnie możesz poczekać na zakończenie wykrywania zmiany usługi Azure File Sync dla docelowego udziału plików przed ponowną próbą przywrócenia.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored — nie można pomyślnie odzyskać co najmniej jednego pliku. Aby uzyskać więcej informacji, sprawdź listę plików zakończonych niepowodzeniem w ścieżce podanej powyżej.

Kod błędu: UserErrorAFSRecoverySomeFilesNotRestored

Komunikat o błędzie: pomyślnie odzyskano co najmniej jeden plik. Aby uzyskać więcej informacji, sprawdź listę plików zakończonych niepowodzeniem w ramach podanej powyżej ścieżki.

- Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka podana w szczegółach zadania). Rozwiąż przyczyny i spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem.
- Typowe przyczyny niepowodzeń przywracania plików:

  - pliki, których nie powiodło się, są obecnie używane
  - w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound — nie można odnaleźć migawki udziału plików platformy Azure odpowiadającej punktowi odzyskiwania

Kod błędu: UserErrorAFSSourceSnapshotNotFound

Komunikat o błędzie: nie można odnaleźć migawki udziału plików platformy Azure odpowiadającej punktowi odzyskiwania

- Upewnij się, że migawka udziału plików odpowiadająca punktowi odzyskiwania, którego próbujesz użyć do odzyskiwania, nadal istnieje.

  >[!NOTE]
  >W przypadku usunięcia migawki udziału plików, która została utworzona przez Azure Backup, odpowiednie punkty odzyskiwania staną się niedostępne. Nie zalecamy usuwania migawek, aby zapewnić gwarantowane odzyskiwanie.

- Spróbuj wybrać inny punkt przywracania, aby odzyskać dane.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget — inne zadanie przywracania jest w toku w tym samym docelowym udziale plików

Kod błędu: UserErrorAnotherRestoreInProgressOnSameTarget

Komunikat o błędzie: inne zadanie przywracania jest w toku w tym samym docelowym udziale plików

Użyj innego docelowego udziału plików. Alternatywnie możesz anulować lub poczekać na zakończenie innych operacji przywracania.

## <a name="common-modify-policy-errors"></a>Typowe błędy zasad modyfikacji

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation — inna operacja konfigurowania ochrony jest w toku dla tego elementu

Kod błędu: BMSUserErrorConflictingProtectionOperation

Komunikat o błędzie: inna operacja konfigurowania ochrony jest w toku dla tego elementu.

Poczekaj na zakończenie poprzedniej operacji modyfikacji zasad i spróbuj ponownie później.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked — inna operacja jest w toku dla wybranego elementu

Kod błędu: BMSUserErrorObjectLocked

Komunikat o błędzie: inna operacja jest w toku dla wybranego elementu.

Poczekaj na zakończenie innej operacji w toku i spróbuj ponownie później.

## <a name="common-soft-delete-related-errors"></a>Typowe błędy związane z usuwaniem nietrwałego

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState — ten punkt przywracania nie jest dostępny, ponieważ migawka skojarzona z tym punktem znajduje się w udziale plików, który jest w stanie nieusuniętym.

Kod błędu: UserErrorRestoreAFSInSoftDeleteState

Komunikat o błędzie: ten punkt przywracania nie jest dostępny, ponieważ migawka skojarzona z tym punktem znajduje się w udziale plików, który jest w stanie nieusuniętym.

Nie można wykonać operacji przywracania, gdy udział plików jest w stanie usunięte nietrwałe. Cofnij usunięcie udziału plików z portalu plików lub za pomocą [skryptu cofania usunięcia](scripts/backup-powershell-script-undelete-file-share.md) , a następnie spróbuj przywrócić.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>Punkty przywracania wymienione na liście UserErrorRestoreAFSInDeleteState nie są dostępne, ponieważ skojarzony udział plików zawierający migawki punktu przywracania został trwale usunięty

Kod błędu: UserErrorRestoreAFSInDeleteState

Komunikat o błędzie: wymienione punkty przywracania nie są dostępne, ponieważ skojarzony udział plików zawierający migawki punktu przywracania został trwale usunięty.

Sprawdź, czy kopia zapasowa udziału plików jest usuwana. Jeśli był w stanie nietrwałego usunięcia, sprawdź, czy okres przechowywania nieprzerwanego usuwania jest wyższy i nie został odzyskany ponownie. W każdym z tych przypadków wszystkie migawki zostaną trwale utracone i nie będzie można odzyskać danych.

>[!NOTE]
> Zalecamy, aby nie usuwać kopii zapasowej udziału plików lub jeśli jest w stanie nietrwałego usunięcia, cofanie usunięcia przed zakończeniem usuwania nietrwałego okresu przechowywania, aby uniknąć utraty wszystkich punktów przywracania.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState — tworzenie kopii zapasowej nie powiodło się, ponieważ udział plików platformy Azure jest w stanie usuwania nietrwałego

Kod błędu: UserErrorBackupAFSInSoftDeleteState

Komunikat o błędzie: wykonywanie kopii zapasowej nie powiodło się, ponieważ udział plików platformy Azure jest w stanie usuwania nietrwałego

Cofnij usunięcie udziału plików z **portalu plików** lub za pomocą [skryptu cofania usuwania](scripts/backup-powershell-script-undelete-file-share.md) , aby kontynuować tworzenie kopii zapasowej i uniemożliwić trwałe usunięcie danych.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState — tworzenie kopii zapasowej nie powiodło się, ponieważ skojarzony udział plików platformy Azure został trwale usunięty

Kod błędu: UserErrorBackupAFSInDeleteState

Komunikat o błędzie: wykonywanie kopii zapasowej nie powiodło się, ponieważ skojarzony udział plików platformy Azure został trwale usunięty

Sprawdź, czy kopia zapasowa udziału plików została trwale usunięta. Jeśli tak, Zatrzymaj tworzenie kopii zapasowej udziału plików, aby uniknąć błędów kopii zapasowych. Aby dowiedzieć się, jak zatrzymać ochronę, zobacz [Zatrzymywanie ochrony udziału plików platformy Azure](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych udziałów plików platformy Azure, zobacz:

- [Tworzenie kopii zapasowej udziałów plików platformy Azure](backup-afs.md)
- [Tworzenie kopii zapasowej udziału plików platformy Azure — często zadawane pytania](backup-azure-files-faq.md)
