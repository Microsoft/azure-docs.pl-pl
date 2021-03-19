---
title: Tworzenie kopii zapasowej plików w usłudze Azure Files — często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące ochrony udziałów plików platformy Azure za pomocą usługi Azure Backup.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91940839"
---
# <a name="questions-about-backing-up-azure-files"></a>Pytania dotyczące tworzenia kopii zapasowej plików w usłudze Azure Files

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej plików usługi Azure Files. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Możesz również ogłosić pytania dotyczące usługi Azure Backup na stronie pytań i odpowiedzi [Microsoft Q&na potrzeby dyskusji](/answers/topics/azure-backup.html).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurowanie zadania tworzenia kopii zapasowej dla usługi Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Dlaczego nie widzę niektórych kont magazynu, które chcę chronić, które zawierają prawidłowe udziały plików platformy Azure?

Zapoznaj się z [macierzą pomocy technicznej dla kopii zapasowych udziałów plików platformy Azure](azure-file-share-support-matrix.md) , aby upewnić się, że konto magazynu należy do jednego z obsługiwanych typów kont magazynu. Istnieje również możliwość, że konto magazynu, którego szukasz, jest już chronione lub zarejestrowane w innym magazynie. [Wyrejestruj konto magazynu](manage-afs-backup.md#unregister-a-storage-account) z magazynu, aby odnaleźć konto magazynu w innych magazynach do ochrony.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Gdy próbuję skonfigurować kopię zapasową, nie widzę niektórych udziałów plików platformy Azure na koncie magazynu. Dlaczego?

Sprawdź, czy udział pliku platformy Azure nie jest już chroniony w tym samym magazynie usługi Recovery Services lub nie został ostatnio usunięty.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Czy mogę chronić udziały plików połączonych z grupą synchronizacji w usłudze Azure Files Sync?

Tak. Ochrona udziałów plików platformy Azure połączonych z grupami synchronizacji jest włączona.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Podczas próby utworzenia kopii zapasowej udziałów plików wybrano konto magazynu w celu odnalezienia znajdujących się w nim udziałów plików. Nie zostały one jednak objęte ochroną. Jak mogę chronić te udziały plików w innym magazynie?

Podczas próby utworzenia kopii zapasowej, wybranie konta magazynu w celu odnalezienia udziałów plików w nim spowoduje zarejestrowanie konta magazynu w magazynie, z którego to zrobiono. W przypadku wybrania opcji ochrony udziałów plików przy użyciu innego magazynu [Wyrejestruj](manage-afs-backup.md#unregister-a-storage-account) wybrane konto magazynu z tego magazynu.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Dlaczego nie mogę zmienić magazynu w celu skonfigurowania kopii zapasowej udziału plików?

Jeśli konto magazynu jest już zarejestrowane w magazynie lub inne udziały plików na koncie magazynu są chronione za pomocą magazynu, nie ma możliwości jego zmiany. Wszystkie udziały plików na koncie magazynu mogą być chronione tylko przez ten sam magazyn. Jeśli chcesz zmienić magazyn, musisz [zatrzymać ochronę wszystkich udziałów plików na koncie magazynu](manage-afs-backup.md#stop-protection-on-a-file-share) z połączonego magazynu, [wyrejestrować](manage-afs-backup.md#unregister-a-storage-account) konto magazynu, a następnie wybrać inny magazyn do ochrony.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Czy mogę zmienić magazyn, do którego należy utworzyć kopię zapasową moich udziałów plików?

Tak. Należy jednak [zatrzymać ochronę udziału plików](manage-afs-backup.md#stop-protection-on-a-file-share) z połączonego magazynu, [wyrejestrować](manage-afs-backup.md#unregister-a-storage-account) to konto magazynu, a następnie chronić je w innym magazynie.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Czy można chronić dwa różne udziały plików z poziomu tego samego konta usługi Storage w różnych magazynach?

Nie. Wszystkie udziały plików na koncie usługi Storage mogą być chronione tylko przez ten sam magazyn.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Co należy zrobić, jeśli moje kopie zapasowe zaczynają kończyć się niepowodzeniem z powodu błędu maksymalnego limitu?

W dowolnym momencie możesz mieć maksymalnie 200 migawek na jeden udział plików. Limit obejmuje migawki wykonane przez usługę Azure Backup na podstawie zasad. Jeśli wykonywanie kopii zapasowych zakończy się niepowodzeniem po osiągnięciu limitu, usuń migawki na żądanie, aby pomyślnie tworzyć kopie zapasowe w przyszłości.

## <a name="restore"></a>Przywracanie

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Czy można odzyskać dane z usuniętego udziału plików platformy Azure?

Jeśli udział plików jest w stanie usunięte nietrwałe, musisz najpierw cofnąć usunięcie udziału plików, aby wykonać operację przywracania. Operacja cofnięcia usunięcia spowoduje udostępnienie udziału plików w stanie aktywnym, w którym można przywrócić wszystkie punkty w czasie. Aby dowiedzieć się, jak anulować usuwanie udziału plików, odwiedź [ten link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) lub zobacz [skrypt cofania usuwania plików](./scripts/backup-powershell-script-undelete-file-share.md). Jeśli udział plików zostanie trwale usunięty, nie będzie można przywrócić zawartości i migawek.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Czy można przywrócić dane z kopii zapasowych, jeśli zatrzymano ochronę udziału plików platformy Azure?

Tak. Jeśli podczas zatrzymywania ochrony została wybrana opcja **Zachowaj dane kopii zapasowej**, można przywrócić dane z wszystkich istniejących punktów przywracania.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie w przypadku anulowania trwającego zadania przywracania?

Jeśli trwające zadanie przywracania zostanie anulowane, proces przywracania zostanie zatrzymany i wszystkie pliki zostaną przywrócone przed anulowaniem, pozostać w skonfigurowanym miejscu docelowym (w lokalizacji oryginalnej lub alternatywnej) bez żadnych wycofywania.

## <a name="manage-backup"></a>Zarządzanie kopią zapasową

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Czy można konfigurować i przywracać kopie zapasowe udziałów plików platformy Azure oraz zarządzać nimi za pomocą programu PowerShell?

Tak. Zapoznaj się ze szczegółową dokumentacją [tutaj](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Czy mogę uzyskać dostęp do migawek wykonanych przez kopie zapasowe platformy Azure i zainstalować je?

Dostęp do wszystkich migawek wykonanych przez Azure Backup można uzyskać, wyświetlając migawki w portalu, programie PowerShell lub interfejsie wiersza polecenia. Aby dowiedzieć się więcej na temat migawek udziału Azure Files, zobacz [Omówienie migawek udziałów dla Azure Files](../storage/files/storage-snapshots-files.md).

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>Co się stanie po przeniesieniu kopii zapasowej udziału plików na inną subskrypcję?

Gdy udział plików zostanie przeniesiony do innej subskrypcji, jest traktowany jako nowy udział plików przez Azure Backup. Poniżej przedstawiono zalecane czynności:
 
Scenariusz: Załóżmy, że masz udział plików *FS1* w subskrypcji *S1* i jest on chroniony przy użyciu magazynu *V1* . Teraz chcesz przenieść udział plików do subskrypcji *S2*.
 
1.  Przenieś odpowiednie konto magazynu i udział plików (FS1) do innej subskrypcji (S2).
2.  W magazynie V1 Wyzwól operację Zatrzymaj ochronę za pomocą operacji usuwania danych dla FS1.
3.  Wyrejestruj hosting konta magazynu FS1 z magazynu w wersji 1.
4.  Skonfiguruj ponownie kopię zapasową usługi FS1, która została przeniesiona do warstwy S2 z magazynem (v2) w ramach subskrypcji S2. 
 
Należy pamiętać, że po ponownym skonfigurowaniu kopii zapasowej za pomocą wersji 2 migawki utworzone przy użyciu wersji 1 nie będą już zarządzane przez Azure Backup. Należy więc ręcznie usunąć te migawki zgodnie z wymaganiami.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Czy mogę przenieść kopię zapasową udziału plików do innej grupy zasobów?
 
Tak. udział plików kopii zapasowej można przenieść do innej grupy zasobów. Należy jednak ponownie skonfigurować kopię zapasową udziału plików, ponieważ będzie on traktowany jako nowy zasób przez Azure Backup. Ponadto migawki utworzone przed przeniesieniem grupy zasobów nie będą już zarządzane przez usługę Azure Backup. Należy więc ręcznie usunąć te migawki zgodnie z wymaganiami.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaki jest maksymalny czas przechowywania, który można skonfigurować dla kopii zapasowych?

Szczegółowe informacje na temat maksymalnego przechowywania można znaleźć w [macierzy obsługi](azure-file-share-support-matrix.md) . Azure Backup wykonuje obliczenia liczby migawek w czasie rzeczywistym podczas wprowadzania wartości przechowywania podczas konfigurowania zasad tworzenia kopii zapasowych. Gdy tylko liczba migawek odpowiadających zdefiniowanym wartościom przechowywania przekracza 200, w portalu zostanie wyświetlone ostrzeżenie z prośbą o dostosowanie wartości przechowywania. Jest tak dlatego, że nie zostanie przekroczony limit maksymalnej liczby migawek obsługiwanych przez Azure Files dla każdego udziału plików w dowolnym momencie.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Jaki jest wpływ istniejących punktów odzyskiwania i migawek podczas modyfikowania zasad tworzenia kopii zapasowej udziału plików platformy Azure w celu przełączenia się z "codziennych zasad" do "zasad GFS"?

W przypadku zmodyfikowania dziennych zasad tworzenia kopii zapasowych do zasad GFS (co pozwala na dodanie cotygodniowych/miesięcznych/rocznych okresów przechowywania) zachowanie jest następujące:

- **Przechowywanie**: w przypadku dodawania tygodniowego/miesięcznego/rocznego przechowywania w ramach modyfikowania zasad wszystkie przyszłe punkty odzyskiwania utworzone w ramach zaplanowanej kopii zapasowej będą znakowane zgodnie z nowymi zasadami. Wszystkie istniejące punkty odzyskiwania będą nadal uznawane za okresowe punkty odzyskiwania, więc nie będą znakowane co tydzień/co miesiąc/rok.

- **Migawki i czyszczenie punktów odzyskiwania**:

  - Jeśli dzienne przechowywanie zostanie rozszerzone, Data wygaśnięcia istniejących punktów odzyskiwania zostanie zaktualizowana zgodnie z dzienną wartością przechowywania skonfigurowaną w nowych zasadach.
  - W przypadku skrócenia okresu przechowywania istniejące punkty odzyskiwania i migawki są oznaczane do usunięcia w kolejnym uruchomieniu zadania oczyszczania zgodnie z wartością dziennego przechowywania skonfigurowaną w nowych zasadach, a następnie usunięta.

Oto przykład tego, jak to działa:

#### <a name="existing-policy-p1"></a>Istniejące zasady [P1]

|Typ przechowywania |Zaplanuj |Przechowywanie  |
|---------|---------|---------|
|Codziennie    |    Codziennie o godzinie 8 PM    |  100 dni       |

#### <a name="new-policy-modified-p1"></a>Nowe zasady [zmodyfikowano P1]

| Typ przechowywania | Zaplanuj                       | Przechowywanie |
| -------------- | ------------------------------ | --------- |
| Codziennie          | Codziennie o godzinie 9 PM              | 50 dni   |
| Co tydzień         | W niedzielę o godzinie 9 PM              | 3 tygodnie   |
| Miesięcznie        | W ubiegłym poniedziałek o godzinie 9 PM         | 1 miesiąc   |
| Rocznie         | W sty w trzeciej niedzielę o godzinie 9 PM | 4 lata   |

#### <a name="impact"></a>Wpływ

1. Data wygaśnięcia istniejących punktów odzyskiwania zostanie skorygowana w zależności od dziennej wartości przechowywania nowych zasad: to jest, 50 dni. W związku z tym wszystkie punkty odzyskiwania starsze niż 50 dni zostaną oznaczone do usunięcia.

2. Istniejące punkty odzyskiwania nie będą otagowane co tydzień/miesięcznie/rocznie na podstawie nowych zasad.

3. Wszystkie przyszłe kopie zapasowe zostaną wyzwolone zgodnie z nowym harmonogramem: to jest, o godzinie 9 PM.

4. Data wygaśnięcia wszystkich przyszłych punktów odzyskiwania będzie wyrównana do nowych zasad.

>[!NOTE]
>Zmiany zasad będą dotyczyć tylko punktów odzyskiwania utworzonych w ramach zaplanowanego uruchomienia zadania tworzenia kopii zapasowej. W przypadku kopii zapasowych na żądanie czas przechowywania jest określany przez wartość **Zachowaj** do w czasie wykonywania kopii zapasowej.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Jakie znaczenie ma wpływ na istniejące punkty odzyskiwania, gdy Modyfikuję istniejące zasady GFS?

Po zastosowaniu nowych zasad w udziałach plików wszystkie przyszłe zaplanowane kopie zapasowe będą wykonywane zgodnie z harmonogramem skonfigurowanym w zmodyfikowanych zasadach.  Przechowywanie wszystkich istniejących punktów odzyskiwania jest wyrównane zgodnie z nowymi skonfigurowanymi wartościami przechowywania. Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania zostaną oznaczone jako przechowywane zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

Oto przykład tego, jak to działa:

#### <a name="existing-policy-p2"></a>Istniejące zasady [P2]

| Typ przechowywania | Zaplanuj           | Przechowywanie |
| -------------- | ------------------ | --------- |
| Codziennie          | Codziennie o godzinie 8 PM | 50 dni   |
| Co tydzień         | W poniedziałek o godzinie 8 PM  | 3 tygodnie   |

#### <a name="new-policy-modified-p2"></a>Nowe zasady [zmodyfikowano P2]

| Typ przechowywania | Zaplanuj               | Przechowywanie |
| -------------- | ---------------------- | --------- |
| Codziennie          | Codziennie o godzinie 9 PM     | 10 dni   |
| Co tydzień         | W poniedziałek o godzinie 9 PM      | 2 tygodnie   |
| Miesięcznie        | W ubiegłym poniedziałek o godzinie 9 PM | 2 miesiące  |

#### <a name="impact-of-change"></a>Wpływ zmiany

1. Data wygaśnięcia istniejących codziennych punktów odzyskiwania będzie wyrównana zgodnie z nową dzienną wartością przechowywania, czyli 10 dni. Dlatego każdy codzienny punkt odzyskiwania jest starszy niż 10 dni, zostanie usunięty.

2. Data wygaśnięcia istniejących tygodniowych punktów odzyskiwania będzie wyrównana zgodnie z nową tygodniową wartością przechowywania, która wynosi dwa tygodnie. Każdy tygodniowy punkt odzyskiwania starsze niż dwa tygodnie zostanie usunięty.

3. Miesięczne punkty odzyskiwania będą tworzone tylko w ramach przyszłych kopii zapasowych w oparciu o nową konfigurację zasad.

4. Data wygaśnięcia wszystkich przyszłych punktów odzyskiwania będzie wyrównana do nowych zasad.

>[!NOTE]
>Zmiany zasad będą dotyczyć tylko punktów odzyskiwania utworzonych w ramach zaplanowanej kopii zapasowej. W przypadku kopii zapasowych na żądanie czas przechowywania jest określany przez wartość **Zachowaj** do w momencie tworzenia kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów podczas tworzenia kopii zapasowych udziałów plików platformy Azure](troubleshoot-azure-files.md)
