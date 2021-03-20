---
title: Samouczek — Tworzenie kopii zapasowych wielu maszyn wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak utworzyć magazyn Recovery Services, zdefiniować zasady tworzenia kopii zapasowych i jednocześnie utworzyć kopię zapasową wielu maszyn wirtualnych.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180579"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Tworzenie kopii zapasowych wielu maszyn wirtualnych za pomocą witryny Azure Portal

Kopie zapasowe danych na platformie Azure są przechowywane w zasobie platformy Azure nazywanym magazynem usługi Recovery Services. Zasób magazynu usługi Recovery Services jest dostępny w menu Ustawienia większości usług platformy Azure. Zaletą korzystania z magazynu Recovery Servicesgo zintegrowanego z menu Ustawienia większości usług platformy Azure jest łatwość tworzenia kopii zapasowych danych. Jednak Praca pojedyncza z każdą bazą danych lub maszyną wirtualną w firmie jest żmudnym. Co zrobić, jeśli trzeba utworzyć kopię zapasową danych wszystkich maszyn wirtualnych w dziale lub w lokalizacji? Można łatwo utworzyć kopię zapasową wielu maszyn wirtualnych, tworząc zasady tworzenia kopii zapasowych i stosując te zasady do odpowiednich maszyn wirtualnych. W tym samouczku wyjaśniono:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalanie zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services zawiera dane kopii zapasowych i zasady tworzenia kopii zapasowych stosowane do chronionych maszyn wirtualnych. Tworzenie kopii zapasowych maszyn wirtualnych jest procesem lokalnym. Nie można utworzyć kopii zapasowej maszyny wirtualnej z jednej lokalizacji w magazynie Recovery Services w innej lokalizacji. Z tego powodu w każdej lokalizacji platformy Azure zawierającej maszyny wirtualne, których kopie zapasowe mają być wykonywane, musi istnieć co najmniej jeden magazyn usługi Recovery Services.

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. W oknie dialogowym **Wszystkie usługi** wprowadź frazę *Recovery Services*. Lista zasobów jest filtrowana zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **Magazyny usługi Recovery Services**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/tutorial-backup-vm-at-scale/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

1. Na pulpicie nawigacyjnym **Magazyny usługi Recovery Services** wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu usługi Recovery Services](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. W menu Magazyn usługi Recovery Services:

    * Wpisz *myRecoveryServicesVault* w polu **Nazwa**.
    * Identyfikator bieżącej subskrypcji znajduje się w polu **Subskrypcja**. Jeśli masz dodatkowe subskrypcje, możesz wybrać inną subskrypcję dla nowego magazynu.
    * W sekcji **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie pozycję *myResourceGroup*. Jeśli grupa *zasobów* nie istnieje, wybierz pozycję **Utwórz nowy** i wpisz Grupa *zasobów*.
    * W menu rozwijanym **Lokalizacja** wybierz pozycję *Europa Zachodnia*.

    ![Recovery Services wartości magazynu](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Magazyn usługi Recovery Services musi znajdować się w tej samej lokalizacji co chronione maszyny wirtualne. Jeśli Twoje maszyny wirtualne znajdują się w wielu regionach, utwórz magazyn usługi Recovery Services w każdym regionie. W tym samouczku przedstawiono magazyn Recovery Services w *Europie zachodniej* , ponieważ *myVM* (maszyna wirtualna utworzona przy użyciu szybkiego startu) została utworzona.

1. Gdy wszystko będzie gotowe do utworzenia magazynu usługi Recovery Services, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Utworzenie magazynu usługi Recovery Services może zająć trochę czasu. Monitoruj powiadomienia o stanie wyświetlane w obszarze **Powiadomienia** w prawym górnym rogu okna portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów usługi Recovery Services. Jeśli magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/tutorial-backup-vm-at-scale/refresh-button.png)

W przypadku tworzenia magazynu usługi Recovery Services ma on domyślnie magazyn geograficznie nadmiarowy. Aby zapewnić odporność danych, magazyn geograficznie nadmiarowy replikuje dane wielokrotnie w dwóch regionach platformy Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Konfigurowanie zasad tworzenia kopii zapasowych, aby chronić maszyny wirtualne

Po utworzeniu magazynu usługi Recovery Services następnym krokiem jest skonfigurowanie magazynu pod kątem typu danych i ustawienie zasad tworzenia kopii zapasowych. Zasady tworzenia kopii zapasowych są harmonogramem określającym częstotliwość i czas tworzenia punktów odzyskiwania. Zasady zawierają także zakres przechowywania dla punktów odzyskiwania. Na potrzeby tego samouczka przyjmujemy, że Twoja firma to sportowa złożona z hotelu, Stadium, restauracje i koncesje oraz ochrona danych na maszynach wirtualnych. Poniższe kroki pozwalają utworzyć zasady tworzenia kopii zapasowych danych finansowych.

1. Wybierz magazyn **myRecoveryServicesVault** z listy magazynów usługi Recovery Services, aby otworzyć jego pulpit nawigacyjny.

   ![Otwieranie menu scenariusza](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **kopia zapasowa** , aby otworzyć menu kopia zapasowa.

1. W menu Cel kopii zapasowej wybierz pozycję *Azure* z menu rozwijanego **Gdzie jest uruchomione Twoje obciążenie**. Z listy rozwijanej **co chcesz utworzyć kopię zapasową** wybierz pozycję *maszyna wirtualna*, a następnie wybierz pozycję **kopia zapasowa**.

    Te akcje spowodują przygotowanie magazynu usługi Recovery Services do interakcji z maszyną wirtualną. Magazyny usługi Recovery Services mają domyślne zasady tworzące punkt przywracania każdego dnia i przechowujące punkty przywracania przez 30 dni.

    ![Cel kopii zapasowej](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Aby utworzyć nowe zasady, w menu rozwijanym **Wybierz zasady tworzenia kopii** zapasowej wybierz pozycję *Utwórz nowe zasady*.

    ![Utwórz nowe zasady](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Zostanie otwarte okienko **zasady tworzenia kopii zapasowej** . Wypełnij następujące informacje:
   * W obszarze **Nazwa zasad** wpisz *finanse*. Wprowadź następujące zmiany dotyczące zasad tworzenia kopii zapasowych:
   * W obszarze **Częstotliwość tworzenia kopii zapasowych** ustaw strefę czasową *Czas środkowy*. Ponieważ kompleks sportowy znajduje się w Teksasie, właściciel chce, aby czas był lokalny. Pozostaw dla częstotliwości tworzenia kopii zapasowych ustawienie: codziennie o 3:30.
   * W obszarze **Przechowywanie codziennego punktu kopii zapasowej** ustaw okres 90 dni.
   * W obszarze **Przechowywanie tygodniowego punktu kopii zapasowej** użyj punktu przywracania *Poniedziałek* przechowywanego przez 52 tygodnie.
   * W obszarze **Przechowywanie miesięcznego punktu kopii zapasowej** użyj punktu przywracania od pierwszej niedzieli miesiąca przechowywanego przez 36 miesięcy.
   * Anuluj wybór opcji **Przechowywanie rocznego punktu kopii zapasowej**. Kierownik ds. finansów nie chce przechowywać danych dłużej niż 36 miesięcy.
   * Wybierz **przycisk OK** , aby utworzyć zasady kopii zapasowych.

     ![Ustawienia zasad kopii zapasowych](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Po utworzeniu zasad tworzenia kopii zapasowych należy skojarzyć je z maszynami wirtualnymi.

1. W obszarze **Virtual Machines** wybierz pozycję **Dodaj**.

     ![Dodaj maszyny wirtualne](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Zostanie otwarte okienko **Wybieranie maszyn wirtualnych** . Wybierz pozycję *myVM* i wybierz pozycję **OK** , aby wdrożyć zasady tworzenia kopii zapasowych na maszynach wirtualnych.

    Wyświetlane są wszystkie maszyny wirtualne znajdujące się w tej samej lokalizacji i nie są one jeszcze skojarzone z zasadami tworzenia kopii zapasowych. Maszyny *myVMH1* i *myVMR1* są wybrane do skojarzenia z zasadami *Finance* (Finansowe).

    ![Wybierz Maszyny wirtualne do ochrony](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Po wybraniu maszyn wirtualnych wybierz pozycję **Włącz kopię zapasową**.

    Po zakończeniu wdrożenia otrzymasz powiadomienie o pomyślnym zakończeniu wdrażania.

## <a name="initial-backup"></a>Początkowa kopia zapasowa

Włączono tworzenie kopii zapasowych dla magazynów Recovery Services, ale nie utworzono początkowej kopii zapasowej. Najlepszym rozwiązaniem w zakresie odzyskiwania po awarii jest wyzwolenie pierwszej kopii zapasowej, dzięki czemu dane są chronione.

Aby uruchomić zadanie tworzenia kopii zapasowej na żądanie:

1. Na pulpicie nawigacyjnym magazynu wybierz pozycję **3** w obszarze **elementy kopii zapasowej**, aby otworzyć menu elementy kopii zapasowej.

    ![Elementy kopii zapasowej](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Zostanie otwarte menu **Elementy kopii zapasowej**.

1. W menu **elementy kopii zapasowej** wybierz pozycję **maszyna wirtualna platformy Azure** , aby otworzyć listę maszyn wirtualnych skojarzonych z magazynem.

    ![Lista maszyn wirtualnych](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. Zostanie otwarta lista **Elementy kopii zapasowej**.

    ![Zadanie tworzenia kopii zapasowej zostało wyzwolone](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. Na liście **elementy kopii zapasowej** wybierz wielokropek **...** , aby otworzyć menu kontekstowe.

1. W menu kontekstowym wybierz pozycję **Utwórz teraz kopię zapasową**.

    ![Menu kontekstowe — wybierz pozycję Utwórz kopię zapasową teraz](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Zostanie otwarte menu Utwórz teraz kopię zapasową.

1. W menu Utwórz teraz kopię zapasową wprowadź ostatni dzień zachowywania punktu odzyskiwania, a następnie wybierz **przycisk OK**.

    ![Ustaw ostatni dzień zachowywania punktu odzyskiwania kopii zapasowej teraz](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Powiadomienia dotyczące wdrożenia informują o wyzwoleniu zadania tworzenia kopii zapasowej, dzięki czemu możesz monitorować postęp zadania na stronie zadań tworzenia kopii zapasowych. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

    Po ukończeniu zadania tworzenia początkowej kopii zapasowej jego stan pojawi się w menu zadań kopii zapasowej. Zadanie tworzenia kopii zapasowej na żądanie spowodowało utworzenie początkowego punktu przywracania dla maszyny *myVM*. Jeśli chcesz utworzyć kopie zapasowe innych maszyn wirtualnych, powtórz te czynności dla każdej z nich.

    ![Kafelek Zadania tworzenia kopii zapasowej](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, nie czyść zasobów utworzonych w tym samouczku. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka w Azure Portal.

1. Na pulpicie nawigacyjnym **myRecoveryServicesVault** wybierz pozycję **3** w obszarze **elementy kopii zapasowej** , aby otworzyć menu elementy kopii zapasowej.

    ![Otwórz menu elementów kopii zapasowej](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. W menu **elementy kopii zapasowej** wybierz pozycję **maszyna wirtualna platformy Azure** , aby otworzyć listę maszyn wirtualnych skojarzonych z magazynem.

    ![Lista maszyn wirtualnych](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Zostanie otwarta lista **Elementy kopii zapasowej**.

1. W menu **elementy kopii zapasowej** wybierz wielokropek, aby otworzyć menu kontekstowe.

    ![W menu elementy kopii zapasowej Otwórz menu kontekstowe](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. W menu kontekstowym wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej** , aby otworzyć menu Zatrzymaj kopię zapasową.

    ![Menu Zatrzymaj kopię zapasową](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. W menu **Zatrzymaj kopię zapasową** wybierz górne menu rozwijane i wybierz pozycję **Usuń dane kopii zapasowej**.

1. W oknie dialogowym **Wpisywanie nazwy elementu kopii zapasowej** wpisz *myVM*.

1. Po zweryfikowaniu elementu kopii zapasowej (zostanie wyświetlony znacznik wyboru) przycisk **Zatrzymaj tworzenie kopii zapasowej** jest włączony. Wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej** , aby zatrzymać zasady i usunąć punkty przywracania.

    ![Wybierz pozycję Zatrzymaj tworzenie kopii zapasowej w celu usunięcia magazynu](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Usunięte elementy są zachowywane w stanie usuwania nietrwałego przez 14 dni. Usunięcie magazynu będzie możliwe dopiero po upływie tego okresu. Aby uzyskać więcej informacji, zobacz [Usuwanie magazynu Recovery Services Azure Backup](backup-azure-delete-vault.md).

1. Jeśli w magazynie nie ma więcej elementów, wybierz pozycję **Usuń**.

    ![Wybierz pozycję Usuń](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Po usunięciu magazynu powrócisz do listy magazynów Recovery Services.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem wykonano następujące czynności przy użyciu witryny Azure Portal:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Konfigurowanie magazynu pod kątem ochrony maszyn wirtualnych
> * Tworzenie niestandardowych zasad tworzenia i przechowywania kopii zapasowych
> * Przypisywanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalanie tworzenia kopii zapasowej na żądanie dla maszyn wirtualnych

Przejdź do następnego samouczka, aby przywrócić maszynę wirtualną platformy Azure z dysku.

> [!div class="nextstepaction"]
> [Przywracanie maszyn wirtualnych za pomocą interfejsu wiersza polecenia](./tutorial-restore-disk.md)
