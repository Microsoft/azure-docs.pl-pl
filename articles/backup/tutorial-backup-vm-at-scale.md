---
title: Samouczek — Tworzenie kopii zapasowych wielu maszyn wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak utworzyć magazyn Recovery Services, zdefiniować zasady tworzenia kopii zapasowych i jednocześnie utworzyć kopię zapasową wielu maszyn wirtualnych.
ms.date: 01/31/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: f9306f2ef5c4b2a53dcba17cafca9ea13b8dab43
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80245246"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Tworzenie kopii zapasowych wielu maszyn wirtualnych za pomocą witryny Azure Portal

Kopie zapasowe danych na platformie Azure są przechowywane w zasobie platformy Azure nazywanym magazynem usługi Recovery Services. Zasób magazynu usługi Recovery Services jest dostępny w menu Ustawienia większości usług platformy Azure. Korzyści wynikające z używania magazynu Recovery Services w menu Ustawienia większości usług platformy Azure ułatwiają tworzenie kopii zapasowych danych. Jednak praca z poszczególnymi bazami danych lub maszynami wirtualnymi w firmie jest niewygodna. Co zrobić, jeśli trzeba utworzyć kopię zapasową danych wszystkich maszyn wirtualnych w dziale lub w lokalizacji? Można łatwo utworzyć kopię zapasową wielu maszyn wirtualnych, tworząc zasady tworzenia kopii zapasowych i stosując je do odpowiednich maszyn wirtualnych. W tym samouczku wyjaśniono:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalanie zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services zawiera dane kopii zapasowych i zasady tworzenia kopii zapasowych stosowane do chronionych maszyn wirtualnych. Tworzenie kopii zapasowych maszyn wirtualnych jest procesem lokalnym. Nie można utworzyć kopii maszyn wirtualnych z jednej lokalizacji w magazynie usługi Recovery Services w innej lokalizacji. Z tego powodu w każdej lokalizacji platformy Azure zawierającej maszyny wirtualne, których kopie zapasowe mają być wykonywane, musi istnieć co najmniej jeden magazyn usługi Recovery Services.

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**, a następnie na liście usług wpisz *Recovery Services*. Podczas pisania odbywa się filtrowanie listy zasobów. Gdy dostrzeżesz na liście pozycję magazynów usługi Recovery Services, wybierz ją, aby otworzyć menu magazynów usługi Recovery Services.

    ![Otwieranie menu magazynu usługi Recovery Services](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png)

2. W menu **Magazyny usługi Recovery Services** kliknij pozycję **Dodaj**, aby otworzyć menu magazynu usługi Recovery Services.

    ![Otwieranie menu magazynu](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. W menu Magazyn usługi Recovery Services:

    * Wpisz *myRecoveryServicesVault* w polu **Nazwa**.
    * Identyfikator bieżącej subskrypcji znajduje się w polu **Subskrypcja**. Jeśli masz dodatkowe subskrypcje, możesz wybrać inną subskrypcję dla nowego magazynu.
    * W sekcji **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie pozycję *myResourceGroup*. Jeśli grupa *zasobów* nie istnieje, wybierz pozycję **Utwórz nowy** i wpisz Grupa *zasobów*.
    * W menu rozwijanym **Lokalizacja** wybierz pozycję *Europa Zachodnia*.
    * Kliknij przycisk **Utwórz**, aby utworzyć magazyn usługi Recovery Services.

Magazyn usługi Recovery Services musi znajdować się w tej samej lokalizacji co chronione maszyny wirtualne. Jeśli Twoje maszyny wirtualne znajdują się w wielu regionach, utwórz magazyn usługi Recovery Services w każdym regionie. W tym samouczku tworzony jest magazyn usługi Recovery Services w regionie *Europa Zachodnia*, ponieważ w tym właśnie regionie utworzono maszynę *myVM* (maszynę wirtualną utworzoną za pomocą przewodnika Szybki start).

Utworzenie magazynu usług Recovery Services może potrwać kilka minut. Monitoruj powiadomienia o stanie wyświetlane w prawej górnej części obszaru portalu. Po utworzeniu magazynu pojawi się on na liście magazynów Usług odzyskiwania.

W przypadku tworzenia magazynu usługi Recovery Services ma on domyślnie magazyn geograficznie nadmiarowy. Aby zapewnić odporność danych, magazyn geograficznie nadmiarowy replikuje dane wielokrotnie w dwóch regionach platformy Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Konfigurowanie zasad tworzenia kopii zapasowych, aby chronić maszyny wirtualne

Po utworzeniu magazynu usługi Recovery Services następnym krokiem jest skonfigurowanie magazynu pod kątem typu danych i ustawienie zasad tworzenia kopii zapasowych. Zasady tworzenia kopii zapasowych są harmonogramem określającym częstotliwość i czas tworzenia punktów odzyskiwania. Zasady zawierają także zakres przechowywania dla punktów odzyskiwania. Na potrzeby tego samouczka przyjmujemy, że Twoja firma to sportowa złożona z hotelu, Stadium, restauracje i koncesje oraz że chronisz dane na maszynach wirtualnych. Poniższe kroki pozwalają utworzyć zasady tworzenia kopii zapasowych danych finansowych.

1. Wybierz magazyn **myRecoveryServicesVault** z listy magazynów usługi Recovery Services, aby otworzyć jego pulpit nawigacyjny.

   ![Otwieranie menu scenariusza](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. W menu pulpitu nawigacyjnego magazynu kliknij pozycję **Kopia zapasowa**, aby otworzyć menu Kopia zapasowa.

3. W menu Cel kopii zapasowej wybierz pozycję *Azure* z menu rozwijanego **Gdzie jest uruchomione Twoje obciążenie**. Z listy rozwijanej **Co ma zawierać kopia zapasowa** wybierz pozycję *Maszyna wirtualna*, a następnie kliknij pozycję **Kopia zapasowa**.

    Te akcje spowodują przygotowanie magazynu usługi Recovery Services do interakcji z maszyną wirtualną. Magazyny usługi Recovery Services mają domyślne zasady tworzące punkt przywracania każdego dnia i przechowujące punkty przywracania przez 30 dni.

    ![Otwieranie menu scenariusza](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Aby utworzyć nowe zasady, w menu zasad tworzenia kopii zapasowych z menu rozwijanego **Wybierz zasady tworzenia kopii zapasowej** wybierz pozycję *Utwórz nowe*.

    ![Wybór obciążenia](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. W menu **Zasady kopii zapasowych** w polu **Nazwa zasad** wpisz *Finance* (Finansowe). Wprowadź następujące zmiany dotyczące zasad tworzenia kopii zapasowych:
   * W obszarze **Częstotliwość tworzenia kopii zapasowych** ustaw strefę czasową *Czas środkowy*. Ponieważ kompleks sportowy znajduje się w Teksasie, właściciel chce, aby czas był lokalny. Pozostaw dla częstotliwości tworzenia kopii zapasowych ustawienie: codziennie o 3:30.
   * W obszarze **Przechowywanie codziennego punktu kopii zapasowej** ustaw okres 90 dni.
   * W obszarze **Przechowywanie tygodniowego punktu kopii zapasowej** użyj punktu przywracania *Poniedziałek* przechowywanego przez 52 tygodnie.
   * W obszarze **Przechowywanie miesięcznego punktu kopii zapasowej** użyj punktu przywracania od pierwszej niedzieli miesiąca przechowywanego przez 36 miesięcy.
   * Anuluj wybór opcji **Przechowywanie rocznego punktu kopii zapasowej**. Kierownik ds. finansów nie chce przechowywać danych dłużej niż 36 miesięcy.
   * Kliknij pozycję **OK**, aby utworzyć zasady kopii zapasowych.

     ![Wybór obciążenia](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Po utworzeniu zasad tworzenia kopii zapasowych należy skojarzyć je z maszynami wirtualnymi.

6. W oknie dialogowym **Wybieranie maszyn wirtualnych** wybierz pozycję *myVM* , a następnie kliknij przycisk **OK** , aby wdrożyć zasady tworzenia kopii zapasowych na maszynach wirtualnych.

    Zostaną wyświetlone wszystkie maszyny wirtualne, które znajdują się w tej samej lokalizacji i nie są jeszcze skojarzone z żadnymi zasadami tworzenia kopii zapasowych. Maszyny *myVMH1* i *myVMR1* są wybrane do skojarzenia z zasadami *Finance* (Finansowe).

    ![Wybór obciążenia](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    Po ukończeniu wdrażania zostanie wyświetlone powiadomienie o pomyślnym ukończeniu wdrażania.

## <a name="initial-backup"></a>Początkowa kopia zapasowa

Włączono tworzenie kopii zapasowych dla magazynów usługi Recovery Services, ale nie została utworzona początkowa kopia zapasowa. Jednym z najlepszych rozwiązań w zakresie odzyskiwania po awarii jest wyzwolenie tworzenia pierwszej kopii zapasowej w celu zapewnienia ochrony danych.

Aby uruchomić zadanie tworzenia kopii zapasowej na żądanie:

1. Na pulpicie nawigacyjnym magazynu kliknij liczbę **3** w obszarze Elementy kopii zapasowej, aby otworzyć menu **Elementy kopii zapasowej**.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Zostanie otwarte menu **Elementy kopii zapasowej**.

2. W menu **Elementy kopii zapasowej** kliknij pozycję **Maszyna wirtualna platformy Azure**, aby otworzyć listę maszyn wirtualnych skojarzonych z magazynem.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Zostanie otwarta lista **Elementy kopii zapasowej**.

    ![Zadanie tworzenia kopii zapasowej zostało wyzwolone](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Na liście **Elementy kopii zapasowej** kliknij przycisk z wielokropkiem **...**, aby otworzyć menu Kontekst.

4. W menu kontekstowym wybierz pozycję **Utwórz teraz kopię zapasową**.

    ![Menu Kontekst](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Zostanie otwarte menu Utwórz teraz kopię zapasową.

5. W menu Utwórz teraz kopię zapasową wprowadź ostatni dzień zachowania punktu odzyskiwania i kliknij pozycję **Kopia zapasowa**.

    ![ustawianie ostatniego dnia okresu zachowywania punktu odzyskiwania Utwórz teraz punkt odzyskiwania](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Powiadomienia dotyczące wdrożenia informują o wyzwoleniu zadania tworzenia kopii zapasowej, dzięki czemu możesz monitorować postęp zadania na stronie zadań tworzenia kopii zapasowych. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

    Po ukończeniu zadania tworzenia początkowej kopii zapasowej jego stan pojawi się w menu zadań kopii zapasowej. Zadanie tworzenia kopii zapasowej na żądanie spowodowało utworzenie początkowego punktu przywracania dla maszyny *myVM*. Jeśli chcesz utworzyć kopie zapasowe innych maszyn wirtualnych, powtórz te czynności dla każdej z nich.

    ![Kafelek Zadania tworzenia kopii zapasowej](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuowanie pracy z kolejnymi samouczkami, nie usuwaj zasobów utworzonych w tym samouczku. Jeśli nie planujesz kontynuowania pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka.

1. Na pulpicie nawigacyjnym magazynu **myRecoveryServicesVault** kliknij liczbę **3** w obszarze **Elementy kopii zapasowej**, aby otworzyć menu Elementy kopii zapasowej.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

2. W menu **Elementy kopii zapasowej** kliknij pozycję **Maszyna wirtualna platformy Azure**, aby otworzyć listę maszyn wirtualnych skojarzonych z magazynem.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Zostanie otwarta lista **Elementy kopii zapasowej**.

3. W menu **Elementy kopii zapasowej** kliknij przycisk z wielokropkiem, aby otworzyć menu kontekstowe.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. W menu kontekstowym wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej** , aby otworzyć menu Zatrzymaj kopię zapasową.

    ![Ikona Ustawienia](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. W menu **Zatrzymaj kopię zapasową** wybierz górne menu rozwijane i wybierz pozycję **Usuń dane kopii zapasowej**.

6. W oknie dialogowym **Wpisywanie nazwy elementu kopii zapasowej** wpisz *myVM*.

7. Po zweryfikowaniu elementu kopii zapasowej (zostanie wyświetlony znacznik wyboru) przycisk **Zatrzymaj tworzenie kopii zapasowej** jest włączony. Kliknij przycisk **Zatrzymaj kopię zapasową**, aby zatrzymać zasady i usunąć punkty przywracania.

    ![kliknij przycisk Zatrzymaj kopię zapasową, aby usunąć magazyn](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. W menu **myRecoveryServicesVault** kliknij pozycję **Usuń**.

    ![kliknij przycisk Zatrzymaj kopię zapasową, aby usunąć magazyn](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Po usunięciu magazynu wrócisz do listy magazynów usługi Recovery Services.

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
