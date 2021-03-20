---
title: Przywróć wszystkie pliki w woluminie przy użyciu MARS
description: Dowiedz się, jak przywrócić wszystkie pliki w woluminie przy użyciu agenta MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613973"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Przywracanie wszystkich plików w woluminie przy użyciu agenta MARS

W tym artykule wyjaśniono, jak przywrócić wszystkie pliki kopii zapasowej w całym woluminie przy użyciu Kreatora odzyskiwania danych w agencie Microsoft Azure Recovery Services (MARS). Oto co możesz zrobić:

- Przywróć wszystkie pliki kopii zapasowej w woluminie na tym samym komputerze, na którym zostały wykonane kopie zapasowe.
- Przywróć wszystkie pliki kopii zapasowej w woluminie do alternatywnej maszyny.

>[!TIP]
>Opcja **woluminu** odzyskuje wszystkie dane kopii zapasowej w określonym woluminie. Ta opcja zapewnia szybsze szybkości transferu (do 40 MB/s) i jest zalecane do odzyskiwania danych o dużych rozmiarach lub całych woluminów.
>
>**Opcja poszczególne pliki i foldery** umożliwia szybki dostęp do danych punktów odzyskiwania. Jest to odpowiednie do odzyskiwania poszczególnych plików i jest zalecane w przypadku całkowitego rozmiaru mniejszego niż 80 GB. Umożliwia transfer lub kopiowanie przyspiesza do 6 MB/s podczas odzyskiwania.

## <a name="volume-level-restore-to-the-same-machine"></a>Przywracanie na poziomie woluminu do tego samego komputera

Poniższe kroki pomogą odzyskać wszystkie pliki kopii zapasowej w woluminie:

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie zainstalowano przystawkę, przeszukaj komputer lub serwer pod kątem **Microsoft Azure Backup**. Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

1. Wybierz pozycję **Odzyskaj dane** , aby uruchomić kreatora.

    ![Menu Odzyskaj dane](./media/restore-all-files-volume-mars/recover.png)

1. Na stronie **wprowadzenie** , aby przywrócić dane na ten sam serwer lub na tym samym komputerze, wybierz pozycję **ten serwer (nazwa serwera)**  >  **dalej**.

    ![Strona Wprowadzenie](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **wolumin**  >  **dalej**.

    ![Wybierz tryb odzyskiwania](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stronie **Wybierz wolumin i datę** Wybierz wolumin, który chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w jednej dacie jest dostępnych wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

     ![Wybierz wolumin i datę](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stronie **Określ opcje odzyskiwania** Skonfiguruj zachowanie przywracania.
    1. Wybierz miejsce docelowe odzyskiwania:
        - **Oryginalna lokalizacja**: Przywracanie danych do oryginalnej ścieżki.
        - **Inna lokalizacja**: Określ alternatywną lokalizację, w której mają zostać przywrócone dane.
    1. Wybierz zachowanie w **przypadku, gdy elementy w kopii zapasowej znajdują się już w miejscu docelowym odzyskiwania**:
        - **Utwórz kopie, aby mieć obie wersje**: Jeśli plik o takiej samej nazwie już istnieje, dane w punkcie odzyskiwania zostaną przywrócone jako kopia. Kopia będzie mieć zlokalizowany prefiks nazwy pliku przy użyciu lokalnego czasu zadania przywracania w jednym z następujących formatów:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Zastąp istniejące wersje odzyskanymi wersjami**: Jeśli plik o takiej samej nazwie już istnieje, zawartość zostanie zamieniona na dane w punkcie odzyskiwania.
        - **Nie Odzyskuj elementów, które już istnieją w miejscu docelowym odzyskiwania**: Jeśli plik o takiej samej nazwie już istnieje, zostanie pominięty.
    1. **Włącz opcję Przywróć uprawnienia listy kontroli dostępu (ACL) do odzyskiwanego pliku lub folderu** , jeśli plik ma zostać przywrócony z oryginalnymi uprawnieniami w punkcie odzyskiwania.
        ![Określ opcje odzyskiwania](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Sprawdź szczegóły odzyskiwania w okienku **potwierdzenia** i wybierz polecenie **Odzyskaj**.

    ![Szczegóły potwierdzenia](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na stronie **postęp odzyskiwania** Monitoruj postęp zadania odzyskiwania. Kreator może być również bezpiecznie zamknięty, a operacja odzyskiwania będzie kontynuowana w tle. Postęp można wyświetlić ponownie, klikając dwukrotnie zadanie odzyskiwania na pulpicie nawigacyjnym.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Przywracanie na poziomie woluminu do alternatywnej maszyny

Poniższe kroki pomogą odzyskać wszystkie pliki kopii zapasowej w woluminie do alternatywnej maszyny. Poniższe kroki służą do odzyskiwania danych z Azure Backup, jeśli cały serwer zostanie utracony.

Te kroki obejmują następującą terminologię:

- *Maszyna źródłowa* — oryginalna maszyna, z której wykonano kopię zapasową, która jest obecnie niedostępna.
- *Maszyna docelowa* — maszyna, do której dane są odzyskiwane.
- *Przykładowy magazyn* — magazyn Recovery Services, do którego zarejestrowano maszynę źródłową i maszynę docelową.

> [!NOTE]
> Kopie zapasowe nie mogą zostać przywrócone na komputerze docelowym, na którym działa Starsza wersja systemu operacyjnego. Na przykład kopię zapasową wykonaną z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 7 (lub nowszym). Nie można przywrócić kopii zapasowej z komputera z systemem Windows 10 do komputera z systemem Windows 7.

1. Otwórz przystawkę **Microsoft Azure Backup** na maszynie docelowej.

1. Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowani do tego samego magazynu Recovery Services.

1. Wybierz pozycję **Odzyskaj dane** , aby otworzyć **Kreatora odzyskiwania danych**.

    ![Zrzut ekranu przedstawiający Azure Backup z wyróżnionym odzyskiwaniem danych (przywracanie do komputera alternatywnego)](./media/backup-azure-restore-windows-server/recover.png)

1. Na stronie **wprowadzenie** wybierz **inny serwer**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych Wprowadzenie stronie (przywracanie do komputera alternatywnego)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Podaj plik poświadczeń magazynu, który odnosi się do przykładowego magazynu, i wybierz pozycję **dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), [Pobierz nowy plik poświadczeń magazynu z przykładowego magazynu](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) w Azure Portal. Po podaniu prawidłowego poświadczenia magazynu zostanie wyświetlona nazwa odpowiedniego magazynu kopii zapasowych.

1. Na stronie **Wybierz serwer kopii zapasowej** wybierz maszynę źródłową z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych — strona wybierz serwer kopii zapasowej (przywracanie do komputera alternatywnego)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **wolumin**  >  **dalej**.

    ![Wybierz tryb odzyskiwania](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stronie **Wybierz wolumin i datę** Wybierz wolumin, który chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w jednej dacie jest dostępnych wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

     ![Wybierz wolumin i datę](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stronie **Określ opcje odzyskiwania** Skonfiguruj zachowanie przywracania.
    1. Wybierz miejsce docelowe odzyskiwania:
        - **Oryginalna lokalizacja**: Przywracanie danych do oryginalnej ścieżki.
        - **Inna lokalizacja**: Określ alternatywną lokalizację, w której mają zostać przywrócone dane.
    1. Wybierz zachowanie w **przypadku, gdy elementy w kopii zapasowej znajdują się już w miejscu docelowym odzyskiwania**:
        - **Utwórz kopie, aby mieć obie wersje**: Jeśli plik o takiej samej nazwie już istnieje, dane w punkcie odzyskiwania zostaną przywrócone jako kopia. Kopia będzie mieć zlokalizowany prefiks nazwy pliku przy użyciu lokalnego czasu zadania przywracania w jednym z następujących formatów:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Zastąp istniejące wersje odzyskanymi wersjami**: Jeśli plik o takiej samej nazwie już istnieje, zawartość zostanie zamieniona na dane w punkcie odzyskiwania.
        - **Nie Odzyskuj elementów, które już istnieją w miejscu docelowym odzyskiwania**: Jeśli plik o takiej samej nazwie już istnieje, zostanie pominięty.
    1. **Włącz opcję Przywróć uprawnienia listy kontroli dostępu (ACL) do odzyskiwanego pliku lub folderu** , jeśli plik ma zostać przywrócony z oryginalnymi uprawnieniami w punkcie odzyskiwania.
        ![Określ opcje odzyskiwania](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Sprawdź szczegóły odzyskiwania w okienku **potwierdzenia** i wybierz polecenie **Odzyskaj**.

    ![Szczegóły potwierdzenia](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na stronie **postęp odzyskiwania** Monitoruj postęp zadania odzyskiwania. Kreator może być również bezpiecznie zamknięty, a operacja odzyskiwania będzie kontynuowana w tle. Postęp można wyświetlić ponownie, klikając dwukrotnie zadanie odzyskiwania na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy odzyskasz pliki i foldery, możesz [zarządzać kopiami zapasowymi](backup-azure-manage-windows-server.md).
- Znajdź [typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md).
