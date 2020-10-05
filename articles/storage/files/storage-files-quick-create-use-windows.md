---
title: Tworzenie i używanie udziału Azure Files na maszynach wirtualnych z systemem Windows
description: Utwórz i użyj udziału Azure Files w Azure Portal. Połącz ją z maszyną wirtualną z systemem Windows, Połącz się z udziałem plików, a następnie Przekaż plik do udziału plików.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0cbbcc822765097f83fcd1a52ce775c69faba9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87836432"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Szybki Start: Tworzenie udziału Azure Files i zarządzanie nim przy użyciu maszyn wirtualnych z systemem Windows

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
1. Wybierz pozycję **Pliki** w okienku konta magazynu.

    ![Wybieranie pozycji Pliki](./media/storage-files-quick-create-use-windows/click-files.png)

1. Wybierz pozycję **udział plików**.

    ![Wybieranie przycisku Dodaj udział plików](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nadaj nowemu udziałowi plików nazwę *qsfileshare*, wprowadź wartość „1” w polu **Limit przydziału**, a następnie wybierz pozycję **Utwórz**. Maksymalny limit przydziału to 5 TiB, ale w tym przewodniku Szybki start potrzebujesz tylko 1 GiB.
1. Utwórz nowy plik tekstowy o nazwie *qsTestFile* na komputerze lokalnym.
1. Wybierz nowy udział plików, a następnie w lokalizacji udziału plików wybierz pozycję **Przekaż**.

    ![Przekazywanie pliku](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Przejdź do lokalizacji, w której utworzono plik txt, wybierz plik *qsTestFile.txt* i wybierz pozycję **Przekaż**.

Na razie na platformie Azure utworzono konto magazynu i udział plików zawierający jeden plik. Następnie utworzysz maszynę wirtualną platformy Azure z systemem Windows Server 2016 Datacenter do reprezentowania serwera lokalnego w ramach tego przewodnika Szybki start.

### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

1. Następnie rozwiń menu po lewej stronie portalu i wybierz pozycję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal.
1. W polu wyszukiwania powyżej listy zasobów **portalu Azure Marketplace** Wyszukaj i wybierz pozycję **Windows Server 2016 Datacenter**, a następnie wybierz pozycję **Utwórz**.
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu** wybierz grupę zasobów utworzoną w ramach tego przewodnika Szybki start.

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. W obszarze **Szczegóły wystąpienia** wprowadź nazwę maszyny wirtualnej: *qsVM*.
1. Pozostaw wartości domyślne w polach **Region**, **Opcje dostępności**, **Obraz** i **Rozmiar**.
1. W obszarze **Konto administratora** dodaj wartość *VMadmin* w polu **Nazwa użytkownika** i wprowadź **hasło** dla maszyny wirtualnej.
1. W obszarze **Reguły portów wejściowych** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz **RDP (3389)** i **HTTP** z listy rozwijanej.
1. Wybierz pozycję **Przeglądanie + tworzenie**.
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
1. Skopiuj zawartość drugiego pola, a następnie wklej ją w **Notatniku**.

   ![Ścieżka UNC z okienka Połącz usługi Azure Files](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Na maszynie wirtualnej otwórz **Eksploratora plików** i w oknie wybierz pozycję **Ten komputer**. Ten wybór spowoduje zmianę menu dostępnego na wstążce. W menu **Komputer** wybierz pozycję **Mapuj dysk sieciowy**.
1. Wybierz literę dysku i wprowadź ścieżkę UNC. Jeśli zostały wykonane sugestie dotyczące nazewnictwa w tym przewodniku Szybki Start, skopiuj * \\ qsstorageacct. plik. Core. Windows. Net\qsfileshare* z **Notatnika**.

   Upewnij się, że oba pola wyboru są zaznaczone.

   ![Zrzut ekranu przedstawiający okno dialogowe „Mapowanie dysku sieciowego”](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Wybierz pozycję **Zakończ**.
1. W oknie dialogowym **Zabezpieczenia systemu Windows**:

   - Skopiuj nazwę konta magazynu poprzedzoną ciągiem AZURE\ z Notatnika, a następnie wklej ją w oknie dialogowym **Zabezpieczenia systemu Windows** jako nazwę użytkownika. Jeśli nadawane nazwy były zgodne z sugestiami w tym przewodniku Szybki start, skopiuj wartość *AZURE\qsstorageacct*.
   - Skopiuj klucz konta magazynu z Notatnika, a następnie wklej go w oknie dialogowym **Zabezpieczenia systemu Windows** jako hasło.

      ![Ścieżka UNC z okienka Połącz usługi Azure Files](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Tworzenie migawki udziału

Teraz, gdy dysk został zamapowany, możesz utworzyć migawkę.

1. W portalu przejdź do swojego udziału plików i wybierz pozycję **Utwórz migawkę**.

   ![Tworzenie migawki](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na maszynie wirtualnej otwórz plik *qstestfile.txt* i wpisz ciąg „ten plik został zmodyfikowany”, a następnie zapisz i zamknij plik.
1. Utwórz kolejną migawkę.

## <a name="browse-a-share-snapshot"></a>Przeglądanie migawek udziału

1. W udziale plików wybierz pozycję **Wyświetl migawki**.
1. W okienku **Migawki udziału plików** wybierz pierwszą migawkę na liście.

   ![Wybrana migawka na liście sygnatur czasowych](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. W okienku dla tej migawki wybierz plik *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Przywracanie z migawki

1. W bloku migawki udziału plików kliknij prawym przyciskiem myszy pozycję *qsTestFile* i wybierz przycisk **Przywróć**.
1. Wybierz pozycję **Zastąp oryginalny plik**.

   ![Przyciski Pobierz i Przywróć](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na maszynie wirtualnej otwórz plik. Przywrócono niezmodyfikowaną wersję.

## <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału

1. W udziale plików wybierz pozycję **Wyświetl migawki**.
1. W okienku **Migawki udziału plików** wybierz ostatnią migawkę na liście i kliknij przycisk **Usuń**.

   ![Przycisk Usuń](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

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

   ![Przycisk Przywróć w komunikacie ostrzegawczym ](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) Uwaga: Jeśli plik nie został zmieniony, nie zostanie wyświetlona Poprzednia wersja tego pliku, ponieważ ten plik jest w tej samej wersji co migawka. Jest to zgodne z tym, jak to działa na serwerze plików z systemem Windows.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md)
