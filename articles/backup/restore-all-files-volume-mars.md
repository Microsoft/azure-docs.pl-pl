---
title: Przywracanie wszystkich plików w woluminie za pomocą marsa
description: Dowiedz się, jak przywrócić wszystkie pliki w woluminie przy użyciu agenta MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516505"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Przywracanie wszystkich plików w woluminie przy użyciu agenta MARS

W tym artykule wyjaśniono, jak przywrócić wszystkie pliki kopii zapasowej w całym woluminie przy użyciu kreatora odzyskiwania danych w agencie usługi Microsoft Azure Recovery Services (MARS). Oto co możesz zrobić:

- Przywróć wszystkie pliki kopii zapasowej w woluminie na tym samym komputerze, z którego zostały wykonane kopie zapasowe.
- Przywróć wszystkie pliki kopii zapasowej w woluminie na maszynie alternatywnej.

>[!TIP]
>Opcja **Wolumin** umożliwia odzyskanie wszystkich kopii zapasowej danych na określonym woluminie. Ta opcja zapewnia większą szybkość transferu (do 40 MB/s) i jest zalecana do odzyskiwania dużych ilości danych lub całych woluminów.
>
>Opcja **Pojedyncze pliki i foldery** umożliwia szybki dostęp do danych punktu odzyskiwania. Jest odpowiedni do odzyskiwania poszczególnych plików i jest zalecany do całkowitego rozmiaru mniejszego niż 80 GB. Oferuje szybkość transferu lub kopiowania do 6 MB/s podczas odzyskiwania.

## <a name="volume-level-restore-to-the-same-machine"></a>Przywracanie na tym samym komputerze na poziomie woluminu

Poniższe kroki ułatwiają odzyskanie wszystkich plików kopii zapasowej w woluminie:

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie została zainstalowana przystawka, wyszukaj na komputerze lub serwerze **Microsoft Azure Backup**. Aplikacja desktop powinna pojawić się w wynikach wyszukiwania.

1. Wybierz **pozycję Odzyskaj** dane, aby uruchomić kreatora.

    ![Menu Odzyskiwanie danych](./media/restore-all-files-volume-mars/recover.png)

1. Na **Wprowadzenie,** aby przywrócić dane na tym samym serwerze lub komputerze, wybierz pozycję Ten **serwer (nazwa serwera)**  >  **Dalej.**

    ![Strona Wprowadzenie](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **Wolumin**  >  **dalej.**

    ![Wybieranie trybu odzyskiwania](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stronie **Wybierz wolumin i datę** wybierz wolumin, który chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubiona** data wskazuje dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty jest dostępnych wiele punktów  odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego Godzina.

     ![Wybieranie woluminu i daty](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stronie **Określanie opcji odzyskiwania** skonfiguruj zachowanie przywracania.
    1. Wybierz miejsce docelowe odzyskiwania:
        - **Oryginalna lokalizacja:** przywróć dane do oryginalnej ścieżki.
        - **Inna lokalizacja:** określ alternatywną lokalizację, do których mają zostać przywrócone dane.
    1. Wybierz zachowanie dla opcji **Gdy elementy w kopii zapasowej znajdują się już w miejscu docelowym odzyskiwania:**
        - **Utwórz kopie, aby mieć obie** wersje: jeśli plik o takiej samej nazwie już istnieje, dane w punkcie odzyskiwania zostaną przywrócone jako kopia. Kopia będzie mieć zlokalizowany prefiks nazwy pliku przy użyciu czasu lokalnego zadania przywracania w jednym z następujących formatów:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Zastąp istniejące wersje odzyskanymi** wersjami: jeśli plik o takiej samej nazwie już istnieje, zawartość zostanie zastąpiona danymi w punkcie odzyskiwania.
        - **Nie należy odzyskiwać elementów, które już** istnieją w miejscu docelowym odzyskiwania: jeśli plik o takiej samej nazwie już istnieje, zostanie pominięty.
    1. Włącz uprawnienia przywróć listę kontroli dostępu **(ACL)** do odzyskiwanych plików lub folderów, jeśli plik powinien zostać przywrócony z oryginalnymi uprawnieniami w punkcie odzyskiwania.
        ![Określanie opcji odzyskiwania](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Sprawdź szczegóły odzyskiwania w okienku **Potwierdzenie** i wybierz pozycję **Odzyskaj.**

    ![Szczegóły potwierdzenia](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na **stronie Postęp odzyskiwania** monitoruj postęp zadania odzyskiwania. Kreator można również bezpiecznie zamknąć, a operacja odzyskiwania będzie kontynuowana w tle. Możesz ponownie wyświetlić postęp, klikając dwukrotnie zadanie odzyskiwania na pulpicie nawigacyjnym.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Przywracanie na poziomie woluminu na maszynie alternatywnej

Poniższe kroki ułatwiają odzyskanie wszystkich plików kopii zapasowej w woluminie na maszynie alternatywnej. Te kroki mogą pomóc w odzyskaniu danych z Azure Backup w przypadku zgubinia całego serwera.

Te kroki obejmują następującą terminologię:

- *Maszyna źródłowa* — oryginalna maszyna, z której została podjęta kopia zapasowa, a która jest obecnie niedostępna.
- *Maszyna docelowa* — maszyna, do której są odzyskiwane dane.
- *Przykładowy magazyn* — magazyn usługi Recovery Services, w którym zarejestrowano maszynę źródłową i docelową.

> [!NOTE]
> Kopii zapasowych nie można przywrócić na maszynie docelowej, na których działa wcześniejsza wersja systemu operacyjnego. Na przykład kopię zapasową z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 7 (lub nowszym). Nie można przywrócić kopii zapasowej Windows 10 komputera z systemem Windows 7.

1. Otwórz **przystawkę Microsoft Azure Backup** na maszynie docelowej.

1. Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowane w tym samym magazynie usługi Recovery Services.

1. Wybierz **pozycję Odzyskaj** dane, aby otworzyć **Kreatora odzyskiwania danych.**

    ![Zrzut ekranu przedstawiający Azure Backup z wyróżnionem daniem Odzyskaj dane (przywróć na inną maszynę)](./media/backup-azure-restore-windows-server/recover.png)

1. Na stronie **Wprowadzenie** wybierz pozycję **Inny serwer.**

    ![Zrzut ekranu przedstawiający stronę kreatora odzyskiwania Wprowadzenie danych (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Podaj plik poświadczeń magazynu odpowiadający przykładowemu magazynowi, a następnie wybierz pozycję **Dalej.**

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), [pobierz](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) nowy plik poświadczeń magazynu z przykładowego magazynu w Azure Portal. Po poświadczeniu prawidłowego magazynu zostanie wyświetlona nazwa odpowiedniego magazynu kopii zapasowych.

1. Na stronie **Wybieranie serwera kopii** zapasowej wybierz maszynę źródłową z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Kreator odzyskiwania danych Wybieranie serwera kopii zapasowej (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **Wolumin**  >  **dalej.**

    ![Wybieranie trybu odzyskiwania](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stronie **Wybierz wolumin i datę** wybierz wolumin, który chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty jest dostępnych wiele punktów  odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego Godzina.

     ![Wybieranie woluminu i daty](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stronie **Określanie opcji odzyskiwania** skonfiguruj zachowanie przywracania.
    1. Wybierz miejsce docelowe odzyskiwania:
        - **Oryginalna lokalizacja:** przywróć dane do oryginalnej ścieżki.
        - **Inna lokalizacja:** określ alternatywną lokalizację, do których mają zostać przywrócone dane.
    1. Wybierz zachowanie dla opcji **Gdy elementy w kopii zapasowej znajdują się już w miejscu docelowym odzyskiwania:**
        - **Utwórz kopie, aby mieć obie** wersje: jeśli plik o takiej samej nazwie już istnieje, dane w punkcie odzyskiwania zostaną przywrócone jako kopia. Kopia będzie mieć zlokalizowany prefiks nazwy pliku przy użyciu czasu zadania przywracania lokalnego w jednym z następujących formatów:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Zastąp istniejące wersje odzyskanymi** wersjami: jeśli plik o takiej samej nazwie już istnieje, zawartość zostanie zastąpiona danymi w punkcie odzyskiwania.
        - **Nie należy odzyskiwać elementów, które już** istnieją w miejscu docelowym odzyskiwania: jeśli plik o takiej samej nazwie już istnieje, zostanie pominięty.
    1. Włącz uprawnienia przywróć listę kontroli dostępu **(ACL)** do odzyskiwanych plików lub folderów, jeśli plik powinien zostać przywrócony z oryginalnymi uprawnieniami w punkcie odzyskiwania.
        ![Określanie opcji odzyskiwania](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Sprawdź szczegóły odzyskiwania w okienku **Potwierdzenie** i wybierz pozycję **Odzyskaj.**

    ![Szczegóły potwierdzenia](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na **stronie Postęp odzyskiwania** monitoruj postęp zadania odzyskiwania. Kreator można również bezpiecznie zamknąć, a operacja odzyskiwania będzie kontynuowana w tle. Możesz ponownie wyświetlić postęp, klikając dwukrotnie zadanie odzyskiwania na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

- Teraz, po odzyskaniu plików i folderów, możesz [zarządzać kopiami zapasami](backup-azure-manage-windows-server.md).
- Znajdź [często zadawane pytania dotyczące kopii zapasowej plików i folderów.](backup-azure-file-folder-backup-faq.yml)
