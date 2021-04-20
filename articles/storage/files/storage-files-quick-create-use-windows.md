---
title: Tworzenie i używanie udziału Azure Files maszyn wirtualnych z systemem Windows
description: Utwórz udział Azure Files i użyj go w Azure Portal. Połącz go z maszyną wirtualną z systemem Windows, połącz z udziałem Pliki i przekaż plik do udziału Pliki.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718237"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Szybki start: tworzenie i zarządzanie udziałami Azure Files maszynami wirtualnymi z systemem Windows

W tym artykule przedstawiono podstawowe kroki tworzenia i używania udziału usługi Azure Files. W tym przewodniku Szybki start położono nacisk na szybkie konfigurowanie udziału usługi Azure Files, aby można było sprawdzić, jak działa ta usługa. Jeśli potrzebujesz bardziej szczegółowych instrukcji dotyczących tworzenia i używania udziałów plików platformy Azure we własnym środowisku, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

W tym przewodniku Szybki start skonfigurujesz następujące elementy:

- Konto magazynu i udział plików platformy Azure
- Maszyna wirtualna z systemem Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Przed rozpoczęciem pracy z udziałem plików platformy Azure musisz utworzyć konto magazynu platformy Azure. Konto magazynu ogólnego przeznaczenia w wersji 2 zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek i tabel. W tym samouczku przedstawiono tworzenie konta magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne. Konto magazynu może zawierać nieograniczoną liczbę udziałów. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Następnie utwórz udział plików.

1. Po wdrożeniu konta magazynu platformy Azure wybierz pozycję **Przejdź do zasobu**.
1. Wybierz **pozycję Udziały** plików w okienku konta magazynu.

    ![Wybierz pozycję Udziały plików.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Wybierz pozycję **+ Udział plików**.

    ![Wybierz pozycję + udział plików, aby utworzyć nowy udział plików.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nazwij nowy udział plików *qsfileshare,* wprowadź wartość "1" dla opcji **Limit** przydziału, pozostaw zaznaczoną pozycję Zoptymalizowane **transakcje** i wybierz **pozycję Utwórz.** Limit przydziału może być maksymalnie 5 TiB (100 TiB z włączonymi dużymi udziałami plików), ale na potrzeby tego przewodnika Szybki start potrzebujesz tylko 1 GiB.
1. Utwórz nowy plik tekstowy o nazwie *qsTestFile* na komputerze lokalnym.
1. Wybierz nowy udział plików, a następnie w lokalizacji udziału plików wybierz pozycję **Przekaż**.

    ![Przekaż plik.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Przejdź do lokalizacji, w której utworzono plik txt, wybierz plik *qsTestFile.txt* i wybierz pozycję **Przekaż**.

Na razie na platformie Azure utworzono konto magazynu i udział plików zawierający jeden plik. Następnie utworzysz maszynę wirtualną platformy Azure z systemem Windows Server 2016 Datacenter do reprezentowania serwera lokalnego w ramach tego przewodnika Szybki start.

### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

1. Następnie rozwiń menu po lewej stronie portalu i wybierz pozycję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal.
1. W polu wyszukiwania nad listą zasobów **Azure Marketplace** wyszukaj i wybierz pozycję **Windows Server 2016 Datacenter.**
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu** wybierz grupę zasobów utworzoną w ramach tego przewodnika Szybki start.

   ![Wprowadź podstawowe informacje o maszynie wirtualnej w bloku portalu.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. W obszarze **Szczegóły wystąpienia** wprowadź nazwę maszyny wirtualnej: *qsVM*.
1. Pozostaw wartości domyślne w polach **Region**, **Opcje dostępności**, **Obraz** i **Rozmiar**.
1. W **obszarze Konto administratora** dodaj nazwę **użytkownika** i wprowadź **hasło** dla maszyny wirtualnej.
1. W obszarze **Reguły portów wejściowych** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz **RDP (3389)** i **HTTP** z listy rozwijanej.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**. Tworzenie nowej maszyny wirtualnej potrwa kilka minut.

1. Po zakończeniu wdrażania maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.

Na tym etapie utworzono nową maszynę wirtualną i dołączono dysk z danymi. Teraz musisz nawiązać połączenie z maszyną wirtualną.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz pozycję **Połącz** na stronie właściwości maszyny wirtualnej.

   ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą **adresu IP** przez **numer portu** *3389* i wybierz pozycję **Pobierz plik RDP**.
1. Otwórz pobrany plik RDP i wybierz polecenie **Połącz**, gdy wyświetli się odpowiedni monit.
1. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika w formacie *localhost\nazwa_użytkownika*, gdzie &lt;nazwa_użytkownika&gt; jest nazwą użytkownika administratora maszyny wirtualnej utworzoną dla danej maszyny wirtualnej. Wprowadź hasło utworzone dla maszyny wirtualnej, a następnie wybierz pozycję **OK**.

   ![Więcej opcji](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak** lub **Kontynuuj**, aby utworzyć połączenie.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapowanie udziału plików platformy Azure na dysk systemu Windows

1. W witrynie Azure Portal przejdź do udziału plików *qsfileshare* i wybierz pozycję **Połącz**.
1. Wybierz literę dysku, a następnie skopiuj zawartość drugiego pola i wklej ją w **Notatniku**.

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Zrzut ekranu przedstawiający zawartość pola, które należy skopiować i wkleić w Notatniku." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. Na maszynie wirtualnej otwórz **program PowerShell** i wklej zawartość **Notatnika,** a następnie naciśnij klawisz Enter, aby uruchomić polecenie. Powinien on zamapować dysk.

## <a name="create-a-share-snapshot"></a>Tworzenie migawki udziału

Teraz, gdy dysk został zamapowany, możesz utworzyć migawkę.

1. W portalu przejdź do udziału plików, wybierz pozycję **Migawki,** a następnie wybierz **pozycję + Dodaj migawkę.**

   ![Wybierz pozycję migawki w sekcji operacje, a następnie wybierz pozycję Dodaj migawkę.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na maszynie wirtualnej otwórz plik *qstestfile.txt* i wpisz ciąg „ten plik został zmodyfikowany”, a następnie zapisz i zamknij plik.
1. Utwórz kolejną migawkę.

## <a name="browse-a-share-snapshot"></a>Przeglądanie migawek udziału

1. W swoim udziałze plików wybierz pozycję **Migawki.**
1. W bloku **Migawki** wybierz pierwszą migawkę z listy.

   ![Wybrana migawka na liście sygnatur czasowych](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Otwórz tę migawkę i wybierz pozycję *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Przywracanie z migawki

1. W bloku migawki udziału plików kliknij prawym przyciskiem myszy pozycję *qsTestFile* i wybierz przycisk **Przywróć**.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Zrzut ekranu przedstawiający blok migawki, wybrany jest plik qstestfile, a przywracanie jest wyróżnione.":::

1. Wybierz pozycję **Zastąp oryginalny plik**.

   ![Zrzut ekranu przedstawiający wyskakujące okienko przywracania z zaznaczonym zastępowaniem oryginalnego pliku.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na maszynie wirtualnej otwórz plik. Przywrócono niezmodyfikowaną wersję.

## <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału

1. W swoim udziałze plików wybierz pozycję **Migawki.**
1. W bloku **Migawki** wybierz ostatnią migawkę z listy i wybierz pozycję **Usuń.**

   ![Zrzut ekranu przedstawiający blok migawek, ostatnia wybrana migawka, wyróżniony przycisk Usuń.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Używanie migawki udziału w systemie Windows

Podobnie jak w przypadku migawek usługi VSS w środowisku lokalnym, możesz wyświetlać migawki z zainstalowanego udziału plików platformy Azure za pomocą karty Poprzednie wersje.

1. W Eksploratorze plików znajdź zainstalowany udział.

   ![Zainstalowany udział w Eksploratorze plików](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Wybierz plik *qsTestFile.txt*, kliknij go prawym przyciskiem myszy i wybierz pozycję **Właściwości** z menu.

   ![Menu dla wybranego katalogu wyświetlane po kliknięciu prawym przyciskiem myszy](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Wybierz pozycję **Poprzednie wersje**, aby wyświetlić listę migawek udziału dla tego katalogu.

1. Wybierz pozycję **Otwórz**, aby otworzyć migawkę.

   ![Karta Poprzednie wersje](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Uaktualnianie z poprzedniej wersji

1. Wybierz pozycję **Przywróć**. Ta akcja rekursywnie kopiuje zawartość całego katalogu do oryginalnej lokalizacji w momencie utworzenia migawki udziału.

   ![Przycisk Przywróć w komunikacie ostrzegawczym](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Jeśli plik nie uległ zmianie, nie zobaczysz poprzedniej wersji tego pliku, ponieważ jest on w tej samej wersji co migawka. Jest to zgodne z tym, jak to działa na serwerze plików systemu Windows.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md)
