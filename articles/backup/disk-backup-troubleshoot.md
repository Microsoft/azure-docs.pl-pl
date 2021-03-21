---
title: Rozwiązywanie problemów z błędami kopii zapasowych na dysku Azure
description: Informacje o rozwiązywaniu problemów z błędami kopii zapasowych w usłudze Kopia zapasowa Azure Disk
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737731"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Rozwiązywanie problemów z błędami kopii zapasowych na dysku platformy Azure (wersja zapoznawcza)

>[!IMPORTANT]
>Kopia zapasowa Azure Disk jest w wersji zapoznawczej bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać dostęp do regionu, zobacz [Macierz obsługi](disk-backup-support-matrix.md).
>
>[Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) , aby utworzyć konto w wersji zapoznawczej.

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych i przywracania z użyciem dysku platformy Azure. Aby uzyskać więcej informacji na temat dostępności obszaru [kopii zapasowej na dysku platformy Azure](disk-backup-overview.md) , obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Typowe problemy związane z tworzeniem kopii zapasowych na dysku Azure

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Kod błędu: UserErrorSnapshotRGSubscriptionMismatch

Komunikat o błędzie: wybrano nieprawidłową subskrypcję dla magazynu danych migawek

Zalecana akcja: dyski i migawki dysków są przechowywane w tej samej subskrypcji. Możesz wybrać dowolną grupę zasobów do przechowywania migawek dysków w ramach subskrypcji. Wybierz tę samą subskrypcję co dysk źródłowy. Aby uzyskać więcej informacji, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Kod błędu: UserErrorSnapshotRGNotFound

Komunikat o błędzie: nie można wykonać operacji, ponieważ Grupa zasobów magazynu danych migawek nie istnieje.

Zalecana akcja: Utwórz grupę zasobów i podaj do niej wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kopii zapasowej](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Kod błędu: UserErrorManagedDiskNotFound

Komunikat o błędzie: nie można wykonać operacji, ponieważ dysk zarządzany już nie istnieje.

Zalecana akcja: kopie zapasowe będą nadal kończyć się niepowodzeniem, ponieważ dysk źródłowy może zostać usunięty lub przeniesiony do innej lokalizacji. Użyj istniejącego punktu przywracania, aby przywrócić dysk, jeśli został on usunięty przez pomyłkę. Jeśli dysk zostanie przeniesiony do innej lokalizacji, skonfiguruj kopię zapasową dla tego dysku.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Kod błędu: UserErrorNotEnoughPermissionOnDisk

Komunikat o błędzie: usługa Azure Backup wymaga dodatkowych uprawnień na dysku w celu wykonania tej operacji.

Zalecana akcja: Udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień na dysku. Zapoznaj się z [dokumentacją](backup-managed-disks.md) , aby dowiedzieć się, jakie uprawnienia są wymagane przez tożsamość zarządzaną magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Kod błędu: UserErrorNotEnoughPermissionOnSnapshotRG

Komunikat o błędzie: usługa Azure Backup wymaga dodatkowych uprawnień do grupy zasobów magazynu danych migawek, aby wykonać tę operację.

Zalecana akcja: Udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień w grupie zasobów magazynu danych migawek. Grupa zasobów magazynu danych migawek to lokalizacja, w której są przechowywane migawki dysków. Zapoznaj się z [dokumentacją](backup-managed-disks.md) , aby zrozumieć, która jest grupą zasobów, jakie uprawnienia są wymagane przez tożsamość zarządzaną magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Kod błędu: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Komunikat o błędzie: nieprawidłowy dysk lub usługa Azure Backup wymaga dodatkowych uprawnień na dysku w celu wykonania tej operacji

Zalecana akcja: kopie zapasowe będą nadal kończyć się niepowodzeniem, ponieważ dysk źródłowy może zostać usunięty lub przeniesiony do innej lokalizacji. Użyj istniejącego punktu przywracania, aby przywrócić dysk, jeśli został on usunięty przez pomyłkę. Jeśli dysk zostanie przeniesiony do innej lokalizacji, skonfiguruj kopię zapasową dla tego dysku. Jeśli dysk nie zostanie usunięty lub przeniesiony, udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień na dysku. Zapoznaj się z dokumentacją, aby dowiedzieć się [,](backup-managed-disks.md) jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Kod błędu: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Komunikat o błędzie: nie można wykonać operacji, ponieważ Grupa zasobów magazynu danych migawek już nie istnieje. Lub usługa Azure Backup wymaga dodatkowych uprawnień do grupy zasobów magazynu danych migawek, aby wykonać tę operację.

Zalecana akcja: Utwórz grupę zasobów i Udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień w grupie zasobów magazynu danych migawek. Grupa zasobów magazynu danych migawek to lokalizacja, w której są przechowywane migawki dysków. Zapoznaj się z [dokumentacją](backup-managed-disks.md) , aby dowiedzieć się, co to jest Grupa zasobów, jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Kod błędu: UserErrorDiskBackupAuthorizationFailed

Komunikat o błędzie: tożsamość zarządzana magazynu kopii zapasowych nie ma wystarczających uprawnień do wykonania tej operacji.

Zalecana akcja: Przyznaj tożsamości zarządzanej magazynu kopii zapasowych odpowiednie uprawnienia na dysku, którego kopia zapasowa ma zostać utworzona, oraz w grupie zasobów magazyn danych migawek, w której są przechowywane migawki. Zapoznaj się z dokumentacją, aby dowiedzieć się [,](backup-managed-disks.md) jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Kod błędu: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Komunikat o błędzie: nie można wykonać operacji, ponieważ Grupa zasobów magazynu danych migawek już nie istnieje. Lub usługa Azure Backup wymaga dodatkowych uprawnień do grupy zasobów magazynu danych migawek, aby wykonać tę operację.

Zalecana akcja: Utwórz grupę zasobów i nadaj zarządzanej tożsamości magazynu kopii zapasowych odpowiednie uprawnienia w grupie zasobów magazynu danych migawek. Grupa zasobów magazynu danych migawek to lokalizacja, w której są przechowywane migawki. Zapoznaj się z [dokumentacją](backup-managed-disks.md) , aby dowiedzieć się, co to jest Grupa zasobów, jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Kod błędu: UserErrorOperationalStoreParametersNotProvided

Komunikat o błędzie: nie można wykonać operacji, ponieważ nie podano parametru grupy zasobów magazynu danych migawek

Zalecana akcja: Podaj parametr grupy zasobów magazynu danych migawek. Grupa zasobów magazynu danych migawek to lokalizacja, w której są przechowywane migawki dysków. Aby uzyskać więcej informacji, zapoznaj [się z dokumentacją](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Kod błędu: UserErrorInvalidOperationalStoreResourceGroup

Komunikat o błędzie: podano nieprawidłową grupę zasobów magazynu danych migawek

Zalecana akcja: Podaj prawidłową grupę zasobów dla magazynu danych migawek. Grupa zasobów magazynu danych migawek to lokalizacja, w której są przechowywane migawki dysków. Aby uzyskać więcej informacji, zapoznaj [się z dokumentacją](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Kod błędu: UserErrorDiskBackupDiskTypeNotSupported

Komunikat o błędzie: nieobsługiwany typ dysku

Zalecana akcja: Zapoznaj się z [matrycą pomocy technicznej w przypadku](disk-backup-support-matrix.md) nieobsługiwanych scenariuszy i ograniczeń.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Kod błędu: UserErrorSameNameDiskAlreadyExists

Komunikat o błędzie: nie można przywrócić, ponieważ dysk o tej samej nazwie już istnieje w wybranej docelowej grupie zasobów

Zalecana akcja: Podaj inną nazwę dysku na potrzeby przywracania. Aby uzyskać więcej informacji, zobacz [przywracanie platformy Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Kod błędu: UserErrorRestoreTargetRGNotFound

Komunikat o błędzie: operacja nie powiodła się, ponieważ docelowa Grupa zasobów nie istnieje.

Zalecana akcja: Podaj prawidłową grupę zasobów do przywrócenia. Aby uzyskać więcej informacji, zobacz [przywracanie platformy Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Kod błędu: UserErrorNotEnoughPermissionOnTargetRG

Komunikat o błędzie: usługa Azure Backup wymaga dodatkowych uprawnień w docelowej grupie zasobów do wykonania tej operacji.

Zalecana akcja: Udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień w docelowej grupie zasobów. Docelowa Grupa zasobów to wybrana lokalizacja, w której ma zostać przywrócony dysk. Zapoznaj się z [dokumentacją dotyczącą przywracania](restore-managed-disks.md) , aby dowiedzieć się, jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Kod błędu: UserErrorSubscriptionDiskQuotaLimitReached

Komunikat o błędzie: operacja nie powiodła się, ponieważ osiągnięto maksymalny limit przydziału dysku w subskrypcji.

Zalecana akcja: Zapoznaj się z dokumentacją dotyczącą [limitów subskrypcji i usług oraz dokumentacji przydziału](../azure-resource-manager/management/azure-subscription-service-limits.md) lub pomoc techniczna firmy Microsoft kontaktu, aby uzyskać dalsze wskazówki.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Kod błędu: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Komunikat o błędzie: operacja nie powiodła się, ponieważ docelowa Grupa zasobów nie istnieje. Lub usługa Azure Backup wymaga dodatkowych uprawnień do docelowej grupy zasobów, aby wykonać tę operację.

Zalecana akcja: Podaj prawidłową grupę zasobów do przywrócenia i Udziel zarządzanej tożsamości magazynu kopii zapasowych odpowiednich uprawnień w docelowej grupie zasobów. Docelowa Grupa zasobów to wybrana lokalizacja, w której ma zostać przywrócony dysk. Zapoznaj się z [dokumentacją dotyczącą przywracania](restore-managed-disks.md) , aby dowiedzieć się, jakie uprawnienia są wymagane przez zarządzaną tożsamość magazynu kopii zapasowych i jak ją udostępnić.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Kod błędu: UserErrorDESKeyVaultKeyDisabled

Komunikat o błędzie: klucz magazynu kluczy używany na potrzeby zestawu szyfrowania dysków nie jest w stanie włączenia.

Zalecana akcja: Włącz klucz magazynu kluczy używany na potrzeby zestawu szyfrowania dysków. Zapoznaj się z ograniczeniami w [macierzy obsługi](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Kod błędu: UserErrorMSIPermissionsNotPresentOnDES

Komunikat o błędzie: usługa Azure Backup potrzebuje uprawnienia dostępu do zestawu szyfrowania dysku używanego z dyskiem.

Zalecana akcja: zapewnianie dostępu czytelnikowi do zarządzanej tożsamości magazynu kopii zapasowych do zestawu szyfrowania dysków (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Kod błędu: UserErrorDESKeyVaultKeyNotAvailable

Komunikat o błędzie: klucz magazynu kluczy używany na potrzeby zestawu szyfrowania dysków nie jest dostępny.

Zalecana akcja: Upewnij się, że klucz magazynu kluczy używany na potrzeby zestawu szyfrowania dysków nie jest wyłączony ani usunięty.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Kod błędu: UserErrorDiskSnapshotNotFound

Komunikat o błędzie: migawka dysku dla tego punktu przywracania została usunięta.

Zalecana akcja: migawki są przechowywane w grupie zasobów magazynu danych migawek w ramach Twojej subskrypcji. Istnieje możliwość, że migawka związana z wybranym punktem przywracania mogła zostać usunięta lub przeniesiona z tej grupy zasobów. Rozważ użycie innego punktu odzyskiwania w celu przywrócenia. Ponadto postępuj zgodnie z zalecanymi wskazówkami dotyczącymi wybierania grupy zasobów migawek wymienionej w [dokumentacji przywracania](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Kod błędu: UserErrorSnapshotMetadataNotFound

Komunikat o błędzie: metadane migawki dysku dla tego punktu przywracania zostały usunięte

Zalecana akcja: należy rozważyć użycie innego punktu odzyskiwania w celu przywrócenia. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą przywracania](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Kod błędu: BackupAgentPluginHostValidateProtectionError

Komunikat o błędzie: kopia zapasowa dysku nie jest jeszcze dostępna w regionie magazynu kopii zapasowych, w którym jest podejmowana próba skonfigurowania ochrony.

Zalecana akcja: Magazyn kopii zapasowych musi znajdować się w regionie obsługiwanym w wersji zapoznawczej. Aby uzyskać dostęp do regionu, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Kod błędu: UserErrorDppDatasourceAlreadyHasBackupInstance

Komunikat o błędzie: dysk, dla którego próbujesz skonfigurować kopię zapasową, jest już chroniony. Dysk jest już skojarzony z wystąpieniem kopii zapasowej w magazynie kopii zapasowych.

Zalecana akcja: ten dysk jest już skojarzony z wystąpieniem kopii zapasowej w magazynie kopii zapasowych. Jeśli chcesz ponownie chronić ten dysk, Usuń wystąpienie kopii zapasowej z magazynu kopii zapasowych, w którym jest obecnie chronione, i ponownie Włącz ochronę dysku w innym magazynie.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Kod błędu: UserErrorDppDatasourceAlreadyProtected

Komunikat o błędzie: dysk, dla którego próbujesz skonfigurować kopię zapasową, jest już chroniony. Dysk jest już skojarzony z wystąpieniem kopii zapasowej w magazynie kopii zapasowych.

Zalecana akcja: ten dysk jest już skojarzony z wystąpieniem kopii zapasowej w magazynie kopii zapasowych. Jeśli chcesz ponownie chronić ten dysk, Usuń wystąpienie kopii zapasowej z magazynu kopii zapasowych, w którym jest obecnie chronione, i ponownie Włącz ochronę dysku w innym magazynie.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Kod błędu: UserErrorMaxConcurrentOperationLimitReached

Komunikat o błędzie: nie można rozpocząć operacji, ponieważ osiągnięto maksymalną dozwoloną liczbę równoczesnych kopii zapasowych.

Zalecana akcja: Poczekaj na zakończenie poprzedniej uruchomionej kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi kopii zapasowych dysków na platformie Azure](disk-backup-support-matrix.md)