---
title: Tworzenie kopii zapasowej plików w usłudze Azure Files — często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące ochrony udziałów plików platformy Azure za pomocą usługi Azure Backup.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105645"
---
# <a name="questions-about-backing-up-azure-files"></a>Pytania dotyczące tworzenia kopii zapasowej plików w usłudze Azure Files

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej plików usługi Azure Files. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Pytania dotyczące usługi Azure Backup można zadawać także na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurowanie zadania tworzenia kopii zapasowej dla usługi Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Dlaczego nie widzę niektórych kont magazynu, które chcę chronić, które zawierają prawidłowe udziały plików platformy Azure?

Zapoznaj się z [macierzą pomocy technicznej dla kopii zapasowych udziałów plików platformy Azure](azure-file-share-support-matrix.md) , aby upewnić się, że konto magazynu należy do jednego z obsługiwanych typów kont magazynu. Istnieje również możliwość, że konto magazynu, którego szukasz, jest już chronione lub zarejestrowane w innym magazynie. [Wyrejestruj konto magazynu](manage-afs-backup.md#unregister-a-storage-account) z magazynu, aby odnaleźć konto magazynu w innych magazynach do ochrony.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Gdy próbuję skonfigurować kopię zapasową, nie widzę niektórych udziałów plików platformy Azure na koncie magazynu. Dlaczego?

Sprawdź, czy udział pliku platformy Azure nie jest już chroniony w tym samym magazynie usługi Recovery Services lub nie został ostatnio usunięty.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Czy mogę chronić udziały plików połączonych z grupą synchronizacji w usłudze Azure Files Sync?

Tak. Ochrona udziałów plików platformy Azure połączonych z grupami synchronizacji jest włączona.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Podczas próby utworzenia kopii zapasowej udziałów plików konto usługi Storage zostało kliknięte w celu odnalezienia w nim udziałów plików. Nie zostały one jednak objęte ochroną. Jak mogę chronić te udziały plików w innym magazynie?

Podczas próby utworzenia kopii zapasowej, wybranie konta magazynu w celu odnalezienia udziałów plików w nim spowoduje zarejestrowanie konta magazynu w magazynie, z którego to zrobiono. W przypadku wybrania opcji ochrony udziałów plików przy użyciu innego magazynu [Wyrejestruj](manage-afs-backup.md#unregister-a-storage-account) wybrane konto magazynu z tego magazynu.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Czy mogę zmienić magazyn, do którego należy utworzyć kopię zapasową moich udziałów plików?

Tak. Należy jednak [zatrzymać ochronę udziału plików](manage-afs-backup.md#stop-protection-on-a-file-share) z połączonego magazynu, [wyrejestrować](manage-afs-backup.md#unregister-a-storage-account) to konto magazynu, a następnie chronić je w innym magazynie.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Ile udziałów plików platformy Azure można chronić w magazynie?

Udziały plików platformy Azure można chronić z maksymalnie 50 kont magazynu na magazyn. Maksymalna liczba udziałów plików platformy Azure w jednym magazynie wynosi 200.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Czy można chronić dwa różne udziały plików z poziomu tego samego konta usługi Storage w różnych magazynach?

Nie. Wszystkie udziały plików na koncie usługi Storage mogą być chronione tylko przez ten sam magazyn.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Co należy zrobić, jeśli moje kopie zapasowe zaczynają kończyć się niepowodzeniem z powodu błędu maksymalnego limitu?

W dowolnym momencie możesz mieć maksymalnie 200 migawek na jeden udział plików. Limit obejmuje migawki wykonane przez usługę Azure Backup na podstawie zasad. Jeśli wykonywanie kopii zapasowych zakończy się niepowodzeniem po osiągnięciu limitu, usuń migawki na żądanie, aby pomyślnie tworzyć kopie zapasowe w przyszłości.

## <a name="restore"></a>Przywracanie

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Czy można odzyskać dane z usuniętego udziału plików platformy Azure?

Po usunięciu udziału plików platformy Azure zostanie wyświetlona lista kopii zapasowych, które zostaną usunięte i wymagane jest potwierdzenie. Obecnie nie można przywrócić usuniętego udziału plików platformy Azure.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Czy można przywrócić dane z kopii zapasowych, jeśli zatrzymano ochronę udziału plików platformy Azure?

Tak. Jeśli podczas zatrzymywania ochrony została wybrana opcja **Zachowaj dane kopii zapasowej**, można przywrócić dane z wszystkich istniejących punktów przywracania.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie w przypadku anulowania trwającego zadania przywracania?

Jeśli trwające zadanie przywracania zostanie anulowane, proces przywracania zostanie zatrzymany i wszystkie pliki zostaną przywrócone przed anulowaniem, pozostać w skonfigurowanym miejscu docelowym (w lokalizacji oryginalnej lub alternatywnej) bez żadnych wycofywania.

## <a name="manage-backup"></a>Zarządzanie kopią zapasową

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Czy można konfigurować i przywracać kopie zapasowe udziałów plików platformy Azure oraz zarządzać nimi za pomocą programu PowerShell?

Tak. Zapoznaj się ze szczegółową dokumentacją [tutaj](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Czy mogę uzyskać dostęp do migawek wykonanych przez kopie zapasowe platformy Azure i zainstalować je?

Dostęp do wszystkich migawek wykonanych przez Azure Backup można uzyskać, wyświetlając migawki w portalu, programie PowerShell lub interfejsie wiersza polecenia. Aby dowiedzieć się więcej na temat migawek udziału Azure Files, zobacz [Omówienie migawek udziałów dla Azure Files](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaki jest maksymalny czas przechowywania, który można skonfigurować dla kopii zapasowych?

Szczegółowe informacje na temat maksymalnego przechowywania można znaleźć w [macierzy obsługi](azure-file-share-support-matrix.md) . Azure Backup wykonuje obliczenia liczby migawek w czasie rzeczywistym podczas wprowadzania wartości przechowywania podczas konfigurowania zasad tworzenia kopii zapasowych. Gdy tylko liczba migawek odpowiadających zdefiniowanym wartościom przechowywania przekracza 200, w portalu zostanie wyświetlone ostrzeżenie z prośbą o dostosowanie wartości przechowywania. Jest tak dlatego, że nie zostanie przekroczony limit maksymalnej liczby migawek obsługiwanych przez Azure Files dla każdego udziału plików w dowolnym momencie.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Co się stanie, jeśli zmienię zasady tworzenia kopii zapasowych udziału plików platformy Azure?

Po zastosowaniu nowych zasad do udziału plików będzie obowiązywał harmonogram i okres przechowywania określony przez te nowe zasady. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania są one oznaczane do oczyszczenia w ramach następnego zadania oczyszczania i usuwane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych obszarach Azure Backup, zobacz niektóre z tych innych często zadawanych pytań dotyczących tworzenia kopii zapasowych:

- [Recovery Services vault FAQ (Magazyn usługi Recovery Services — często zadawane pytania)](backup-azure-backup-faq.md)
- [Azure VM backup FAQ (Tworzenie kopii zapasowych maszyn wirtualnych — często zadawane pytania)](backup-azure-vm-backup-faq.md)
- [Azure Backup agent FAQ (Agent usługi Azure Backup — często zadawane pytania)](backup-azure-file-folder-backup-faq.md)
