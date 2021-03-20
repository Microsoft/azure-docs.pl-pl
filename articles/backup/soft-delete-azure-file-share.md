---
title: Przypadkowe usuwanie ochrony udziałów plików platformy Azure
description: Dowiedz się, jak usuwanie nietrwałe może chronić udziały plików platformy Azure przed przypadkowym usunięciem.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89179916"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Przypadkowe usuwanie ochrony udziałów plików platformy Azure przy użyciu Azure Backup

Aby zapewnić ochronę przed cyberattacks lub przypadkowym usunięciem, funkcja [usuwania nietrwałego](../storage/files/storage-files-prevent-file-share-deletion.md) jest włączana dla wszystkich udziałów plików na koncie magazynu podczas konfigurowania kopii zapasowej dowolnego udziału plików na odpowiednim koncie magazynu. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa udział plików, zawartość udziału plików i punkty odzyskiwania (migawki) są zachowywane przez co najmniej 14 dodatkowych dni, co pozwala na odzyskiwanie udziałów plików bez utraty danych.  Usuwanie nietrwałe jest obsługiwane dla kont magazynu w warstwach Standardowa i Premium, a ustawienie jest włączone, Azure Backup dla wszystkich kont magazynu obsługujących kopie zapasowe udziałów plików.

Poniższy wykres przepływu przedstawia różne kroki i Stany elementu kopii zapasowej po włączeniu usuwania nietrwałego dla udziałów plików na koncie magazynu:

 ![Wykres nietrwałego przepływu usuwania](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Po włączeniu usuwania nietrwałego dla udziałów plików na koncie magazynu?

W przypadku skonfigurowania kopii zapasowej po raz pierwszy dla dowolnego udziału plików na koncie magazynu usługa Azure Backup umożliwia usuwanie nietrwałe dla wszystkich udziałów plików na odpowiednim koncie magazynu.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Czy można skonfigurować liczbę dni, dla których moje migawki i punkty przywracania będą przechowywane w stanie nieusuniętym, po usunięciu udziału plików?

Tak, można ustawić okres przechowywania zgodnie z wymaganiami. W [tym dokumencie](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) opisano kroki konfigurowania okresu przechowywania. W przypadku kont magazynu z udziałami plików z kopią zapasową minimalne ustawienie przechowywania powinno wynosić 14 dni.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Czy Azure Backup zresetować moje ustawienie przechowywania, ponieważ skonfigurowano je do mniej niż 14 dni?

Z punktu widzenia zabezpieczeń zaleca się minimalne przechowywanie przez 14 dni w przypadku kont magazynu z kopią zapasową udziałów plików. W przypadku każdego uruchomienia zadania tworzenia kopii zapasowej, jeśli Azure Backup określa, że ustawienie będzie mniejsze niż 14 dni, resetuje je do 14 dni.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Jaki jest koszt ponoszony w okresie przechowywania?

W okresie nietrwałym usunięty koszt chronionego wystąpienia i koszt magazynu migawek pozostaną w stanie takim, w jakim się znajdują.  Ponadto opłata zostanie naliczona za użytą moc dla standardowych udziałów plików i szybkość magazynu migawek dla udziałów plików w warstwie Premium.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Czy można wykonać operację przywracania, gdy moje dane są w stanie usunięte nietrwałe?

Musisz najpierw cofnąć usunięcie nietrwałego udziału plików, aby wykonać operacje przywracania. Operacja cofnięcia usunięcia spowoduje przeniesienie udziału plików do stanu kopii zapasowej, w którym można przywrócić do dowolnego punktu w czasie. Aby dowiedzieć się, jak anulować usuwanie udziału plików, odwiedź [ten link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) lub zobacz [skrypt cofania usuwania plików](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Jak można przeczyścić dane udziału plików na koncie magazynu, które ma co najmniej jeden chroniony udział plików?

Jeśli masz co najmniej jeden chroniony udział plików na koncie magazynu, oznacza to, że usuwanie nietrwałe jest włączone dla wszystkich udziałów plików na tym koncie, a dane będą przechowywane przez 14 dni po operacji usuwania. Ale jeśli chcesz natychmiast przeczyścić dane i nie chcesz ich zachować, wykonaj następujące czynności:

1. Jeśli udział plików został już usunięty, podczas gdy jest włączona funkcja usuwania nietrwałego, najpierw usuń ten udział plików z [portalu plików](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) lub za pomocą [skryptu cofania usuwania plików](./scripts/backup-powershell-script-undelete-file-share.md).
2. Wyłącz usuwanie nietrwałe dla udziałów plików na koncie magazynu, wykonując czynności opisane w [tym dokumencie](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete).
3. Teraz Usuń udział plików, którego zawartość ma zostać natychmiast przeczyszczona.

>[!NOTE]
>Należy wykonać krok 2 przed rozpoczęciem następnego zaplanowanego zadania tworzenia kopii zapasowej w ramach chronionego udziału plików na koncie magazynu. Ponieważ przy każdym uruchomieniu zadania tworzenia kopii zapasowej, umożliwia ono ponowne włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.

>[!WARNING]
>Po wyłączeniu usuwania nietrwałego w kroku 2 wszystkie operacje usuwania wykonane względem udziałów plików są trwałą operacją usuwania. Dlatego jeśli przypadkowo usuniesz udział plików z kopią zapasową po wyłączeniu usuwania nietrwałego, utracisz wszystkie migawki i nie będzie można odzyskać danych.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>Jakie zmiany są Azure Backup w kontekście ustawienia usuwania nietrwałego udziału plików? czy Wyrejestruj konto magazynu?

Podczas wyrejestrowywania Azure Backup sprawdza ustawienia okresu przechowywania udziałów plików, a jeśli jest większa niż 14 dni lub mniej niż 14 dni, pozostawia przechowywanie w postaci, w jakiej jest. Jeśli jednak czas przechowywania wynosi 14 dni, uważamy go za włączany przez Azure Backup i uniemożliwiamy wyłączenie usuwania nietrwałego podczas procesu wyrejestrowywania. Jeśli chcesz wyrejestrować konto magazynu przy zachowaniu ustawienia przechowywania, należy włączyć je ponownie z poziomu okienka konta magazynu po zakończeniu wyrejestrowywania. Można odwołać się do [tego linku](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) w celu wykonania czynności konfiguracyjnych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak tworzyć kopie zapasowe udziałów plików platformy Azure z Azure Portal](backup-afs.md)
