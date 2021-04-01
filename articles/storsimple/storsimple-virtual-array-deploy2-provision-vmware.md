---
title: Inicjowanie obsługi administracyjnej macierzy wirtualnej StorSimple w oprogramowaniu VMware
description: Ten drugi samouczek w serii StorSimple Virtual Array Deployment obejmuje Inicjowanie obsługi urządzenia wirtualnego w oprogramowaniu VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9810a34021aa039354aad24f84aff373229c0190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87021481"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Wdrażanie wirtualnej macierzy StorSimple — Inicjowanie obsługi administracyjnej w oprogramowaniu VMware
![Diagram przedstawiający kroki wymagane do wdrożenia macierzy wirtualnej. Druga część drugiego kroku ma etykietę udostępnianie w oprogramowaniu VMware i jest wyróżniona.](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym samouczku opisano sposób aprowizacji i nawiązywania połączenia z tablicą wirtualną StorSimple w systemie hosta z systemem VMware ESXi 5,0, 5,5, 6,0 lub 6,5. Ten artykuł dotyczy wdrażania tablic wirtualnych StorSimple w Azure Portal i w chmurze Microsoft Azure Government.

Do aprowizowania urządzenia wirtualnego i nawiązania z nim połączenia wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne dotyczące aprowizacji
Wymagania wstępne dotyczące aprowizacji urządzenia wirtualnego w systemie hosta z systemem VMware ESXi 5,0, 5,5, 6,0 lub 6,5 są następujące.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie portalu do macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Pobrano obraz urządzenia wirtualnego dla programu VMware z Azure Portal. Aby uzyskać więcej informacji, zobacz **krok 3. Pobieranie obrazu urządzenia wirtualnego** [Przygotuj Portal for StorSimple Virtual Array Guide](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Dla urządzenia wirtualnego StorSimple
Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do systemu hosta z uruchomioną funkcją Hyper-V (2008 R2 lub nowszą), którego można użyć w celu udostępnienia urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB dla danych systemu.

### <a name="for-the-network-in-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem upewnij się, że:

* Zawarto przegląd wymagań sieciowych w celu wdrożenia urządzenia wirtualnego StorSimple i skonfigurowania sieci centrum danych zgodnie z wymaganiami. 

## <a name="step-by-step-provisioning"></a>Inicjowanie obsługi krok po kroku
Aby zapewnić obsługę administracyjną urządzenia wirtualnego i nawiązać z nim połączenie, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby do spełnienia minimalnych wymagań dotyczących urządzeń wirtualnych.
2. Inicjowanie obsługi administracyjnej urządzenia wirtualnego w funkcji hypervisor.
3. Uruchom urządzenie wirtualne i uzyskaj adres IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1. Upewnij się, że system hosta spełnia minimalne wymagania dotyczące urządzenia wirtualnego
Aby utworzyć urządzenie wirtualne, potrzebne są:

* Dostęp do systemu hosta z systemem VMware ESXi Server 5,0, 5,5, 6,0 lub 6,5.
* Klient VMware vSphere w systemie na potrzeby zarządzania hostem ESXi.

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu. Minimalna przepustowość internetowa powinna wynosić 5 MB/s, aby zapewnić optymalną pracę z urządzeniem.
  * Dysk wirtualny 500 GB dla danych.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2. Inicjowanie obsługi urządzenia wirtualnego w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie wirtualne w funkcji hypervisor.

1. Skopiuj obraz urządzenia wirtualnego do swojego systemu. Ten obraz wirtualny został pobrany za pomocą Azure Portal.

   1. Upewnij się, że pobrano najnowszy plik obrazu. Jeśli wcześniej pobrano obraz, Pobierz go ponownie, aby upewnić się, że masz najnowszy obraz. Najnowszy obraz ma dwa pliki (zamiast jednego).
   2. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Zaloguj się na serwerze ESXi przy użyciu klienta vSphere. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![Zrzut ekranu przedstawiający stronę logowania klienta vSphere. Pola adres IP, nazwa użytkownika i hasło zawierają wartości, a przycisk Zaloguj zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. W kliencie vSphere w sekcji spis w okienku po lewej stronie wybierz serwer ESXi.

   ![Zrzut ekranu przedstawiający stronę główną klienta vSphere. W sekcji spis zostanie wyróżniony serwer ESXi.](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Przekaż dysk VMDK na serwer ESXi. Przejdź do karty **Konfiguracja** w okienku po prawej stronie. W obszarze **sprzęt** wybierz pozycję **Magazyn**.

   ![Zrzut ekranu przedstawiający kartę Konfiguracja klienta vSphere. W sekcji sprzęt zostanie wyróżniony magazyn.](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. W okienku po prawej stronie, w obszarze **Datastores** (Magazyny danych), wybierz magazyn danych, do którego chcesz przekazać dysk VMDK. Magazyn danych musi mieć wystarczającą ilość wolnego miejsca dla systemu operacyjnego i dysków z danymi.

   ![Zrzut ekranu przedstawiający stronę Magazyn klienta vSphere. Karta magazyny danych jest otwarta i zawiera listę magazynów danych. Wybrano jeden magazyn danych.](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kliknij prawym przyciskiem myszy i wybierz polecenie **Browse Datastore** (Przeglądaj magazyn danych).

   ![Zrzut ekranu przedstawiający menu skrótów wybranego magazynu danych. Wybrano element Przeglądaj elementy magazynu danych.](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zostanie wyświetlone okno **Datastore Browser** (Przeglądarka magazynu danych).

   ![Zrzut ekranu przedstawiający przeglądarkę magazynu danych. Foldery w magazynie danych są widoczne.](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na pasku narzędzi kliknij ikonę, :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png"::: Aby utworzyć nowy folder. Określ nazwę folderu i zanotuj ją. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (zalecane najlepsze rozwiązanie). Kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający przeglądarkę magazynu danych z wyróżnioną ikoną nowego folderu. Okno dialogowe ma wypełnioną nazwę folderu i przycisk OK.](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nowy folder zostanie wyświetlony w okienku po lewej stronie okna **Datastore Browser** (Przeglądarka magazynu danych).

   ![Zrzut ekranu przeglądarki datastore z nowym folderem widocznym w hierarchii folderów.](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kliknij ikonę Przekaż :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png"::: i wybierz pozycję **Przekaż plik**.

    ![Zrzut ekranu przedstawiający menu skrótów ikony przekazywania. Wybrano element Przekaż plik.](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Wskaż pobrane pliki VMDK. Pliki są dwa. Wybierz plik do przekazania.

    ![Zrzut ekranu przedstawiający okno dialogowe zawierające foldery i dwa pliki M D KB. Jeden z plików jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kliknij przycisk **Otwórz**. Rozpocznie się przekazywanie pliku VMDK do określonego magazynu danych. Przekazanie pliku może zająć kilka minut.
13. Po zakończeniu przekazywania plik będzie widoczny w magazynie danych w utworzonym folderze.

    ![Zrzut ekranu przedstawiający przeglądarkę magazynu danych. Nowy folder zostanie wyróżniony w hierarchii folderów, a przekazany plik jest widoczny w tym folderze.](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teraz przekaż drugi plik VMDK do tego samego magazynu danych.
14. Wróć do okna klienta vSphere. Po wybraniu serwera ESXi kliknij prawym przyciskiem myszy i wybierz pozycję **Nowa maszyna wirtualna**.

    ![Zrzut ekranu przedstawiający menu skrótów serwera ESXi. Wybrano nowy element maszyny wirtualnej.](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zostanie wyświetlone okno **Utwórz nową maszynę wirtualną** . Na stronie **Konfiguracja** wybierz opcję **niestandardowa** . Kliknij przycisk **Dalej**.
    ![Zrzut ekranu strony Konfiguracja okna Utwórz nową maszynę wirtualną. Wybrana jest opcja niestandardowa, a przycisk Dalej jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stronie **Nazwa i lokalizacja** Określ nazwę swojej maszyny wirtualnej. Ta nazwa powinna być zgodna z nazwą folderu (zalecane najlepsze rozwiązanie) określoną wcześniej w kroku 8.

    ![Zrzut ekranu przedstawiający stronę Nazwa i lokalizacja okna Utwórz nową maszynę wirtualną. Pole Nazwa jest wypełnione, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stronie **Magazyn** wybierz magazyn danych, którego chcesz użyć do aprowizacji maszyny wirtualnej.

    ![Zrzut ekranu strony magazyn okna Utwórz nową maszynę wirtualną. Wybrano magazyn danych, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stronie **wersja maszyny wirtualnej** wybierz pozycję **wersja maszyny wirtualnej: 8**.

    ![Zrzut ekranu przedstawiający stronę wersji maszyny wirtualnej. Wybrana jest opcja maszyny wirtualnej w wersji 8, a przycisk Dalej jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stronie **system operacyjny gościa** wybierz **system operacyjny gościa** jako **system Windows**. W polu **wersja**, z listy rozwijanej wybierz pozycję **Microsoft Windows Server 2012 (64-bit)**.

    ![Zrzut ekranu strony systemu operacyjnego gościa z wybranym systemem Windows, wersja ustawiona na Microsoft Windows Server 2012 (64-bitowe) i dalej wyróżniona.](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stronie **procesory** Dostosuj **liczbę gniazd wirtualnych** i **liczbę rdzeni na gniazdo wirtualne** , aby **łączna liczba rdzeni** wynosić 4 (lub więcej). Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę procesorów CPU przedstawiającą jedno gniazdo wirtualne, cztery rdzenie na gniazdo wirtualne i cztery łączne rdzenie. Przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stronie **pamięć** Określ 8 GB pamięci RAM (lub więcej). Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę pamięci. Wartość 8 GB jest wypełniona dla rozmiaru pamięci.](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stronie **Sieć** Określ liczbę interfejsów sieciowych. Minimalnym wymaganiem jest jeden interfejs sieciowy.

    ![Zrzut ekranu strony sieci. Liczba interfejsów sieciowych jest ustawiona na jeden, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stronie **kontroler SCSI** Zaakceptuj domyślny **kontroler LSI Logic SAS**.

    ![Zrzut ekranu przedstawiający stronę kontrolera SCSI. Wybrana jest opcja "s" I "s", a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stronie **Wybierz dysk** wybierz opcję **Użyj istniejącego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Wybieranie dysku z wybraną opcją Użyj istniejącego dysku wirtualnego i podświetloną przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stronie **Wybierz istniejący dysk** w obszarze **ścieżka pliku dysku** kliknij przycisk **Przeglądaj**. Spowoduje to otwarcie okna dialogowego **Przeglądaj magazyny** danych. Przejdź do lokalizacji, w której został przekazany dysk VMDK. W magazynie danych jest teraz widoczny tylko jeden plik, w ramach którego zostały scalone dwa pliki, które zostały wcześniej przekazane. Wybierz plik, a następnie kliknij przycisk **OK**. Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Wybieranie istniejącego dysku. Przycisk przeglądania jest wyróżniony, a okno dialogowe zawiera jeden plik i wyróżniony przycisk OK.](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stronie **Opcje zaawansowane** Zaakceptuj ustawienie domyślne i kliknij przycisk **dalej**.

    ![Zrzut ekranu strony opcji zaawansowanych. Przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stronie **Ready to Complete** (Gotowe do ukończenia) przejrzyj wszystkie ustawienia skojarzone z nową maszyną wirtualną. **Przed ukończeniem Sprawdź ustawienia maszyny wirtualnej**. Kliknij przycisk **Kontynuuj**.

    ![Zrzut ekranu przedstawiający stronę gotowy do ukończenia z wyróżnionym przyciskiem Kontynuuj. Zaznaczona jest opcja Edytuj ustawienia maszyny wirtualnej przed ukończeniem.](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stronie **właściwości Virtual Machines** na karcie **sprzęt** zlokalizuj sprzęt urządzenia. Wybierz pozycję **nowy dysk twardy**. Kliknij pozycję **Dodaj**.

    ![Zrzut ekranu karty sprzęt na stronie właściwości Virtual Machines. Na liście sprzęt jest wybrany nowy dysk twardy. Przycisk Dodaj zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobaczysz okno **Dodaj sprzęt** . Na stronie **Typ urządzenia** w obszarze **Wybierz typ urządzenia, które chcesz dodać** wybierz pozycję **dysk twardy**, a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawiający stronę typ urządzenia w oknie Dodawanie sprzętu. Wybrano urządzenie dysk twardy, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stronie **Wybierz dysk** wybierz opcję **Utwórz nowy dysk wirtualny**. Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Wybieranie dysku. Zaznaczona jest opcja Utwórz nowy dysk wirtualny, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stronie **Tworzenie dysku** Zmień **rozmiar dysku** na 500 GB (lub więcej). Chociaż 500 GB jest minimalnym wymaganiem, zawsze można zainicjować obsługę większą liczbę dysków. Należy pamiętać, że nie można rozbudować lub zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Aby uzyskać więcej informacji na temat rozmiaru dysku do aprowizacji, zapoznaj się z sekcją ustalanie rozmiaru w [dokumencie Best Practices](storsimple-ova-best-practices.md). W obszarze **Inicjowanie obsługi dysku** wybierz pozycję **alokowanie elastyczne**. Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Tworzenie dysku. Rozmiar dysku wynosi 500 GB, opcja alokowania elastycznego jest zaznaczona, a przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stronie **Opcje zaawansowane** Zaakceptuj wartość domyślną.

    ![Zrzut ekranu strony opcji zaawansowanych. Węzeł urządzenia wirtualnego jest ustawiony na SCSI (0:0), a przycisk Dalej jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stronie **gotowy do ukończenia** przejrzyj opcje dysku. Kliknij przycisk **Finish** (Zakończ).

    ![Zrzut ekranu przedstawiający stronę gotowy do wykonania. Widoczne jest podsumowanie opcji dysku, a przycisk Zakończ jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Wróć do strony właściwości maszyny wirtualnej. Do maszyny wirtualnej zostanie dodany nowy dysk twardy. Kliknij przycisk **Finish** (Zakończ).

    ![Zrzut ekranu przedstawiający stronę właściwości maszyny wirtualnej. Lista sprzęt zawiera nowy dysk twardy, a przycisk Zakończ jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Po wybraniu maszyny wirtualnej w okienku po prawej stronie przejdź do karty **Podsumowanie** . Przejrzyj ustawienia dla swojej maszyny wirtualnej.

    ![Zrzut ekranu przedstawiający kartę Podsumowanie klienta vSphere. Nowa maszyna wirtualna zostanie wyróżniona, a jej zasoby i właściwości ogólne są widoczne.](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Maszyna wirtualna jest teraz aprowizowana. Następnym krokiem jest uruchomienie tej maszyny i uzyskanie adresu IP.

> [!NOTE]
> Firma Microsoft zaleca, aby nie instalować narzędzi VMware w macierzy wirtualnej (zgodnie z powyższym przydziałem). Zainstalowanie narzędzi VMware spowoduje powstanie nieobsługiwanej konfiguracji.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3. uruchomienie urządzenia wirtualnego i pobieranie adresu IP
Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W vSphere Configuration Manager w lewym okienku wybierz urządzenie i kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe. Wybierz pozycję **Power** (Zasilanie), a następnie pozycję **Power on** (Włącz). Maszyna wirtualna powinna zostać włączona. Stan można wyświetlić w okienku **ostatnie zadania** w dolnej części klienta vSphere.

   ![Zrzut ekranu przedstawiający menu skrótów urządzenia. Wybrano element potęgi. Sąsiadujące menu jest widoczne z wybranym elementem Włącz włączony.](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Ukończenie zadań instalacyjnych potrwa kilka minut. Gdy urządzenie jest uruchomione, przejdź do karty **konsoli** . Aby zalogować się do urządzenia, naciśnij klawisze CTRL + ALT + DELETE. Alternatywnie możesz wskazać kursor w oknie konsoli i nacisnąć klawisze CTRL + ALT + INSERT. Domyślny użytkownik to *StorSimpleAdmin* , a hasło domyślne to *Password1*.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere. Pole hasła jest puste.](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere. Tekst na stronie informuje o tym, że należy zmienić hasło.](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać 3 z 4 tych wymagań: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Zostanie wyświetlone powiadomienie o zmianie hasła.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere. Tekst na stronie wskazuje, że hasło zostało zmienione.](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po pomyślnym zmianie hasła urządzenie wirtualne może zostać ponownie rozruchowe. Poczekaj na zakończenie ponownego rozruchu. Konsola programu Windows PowerShell urządzenia może być wyświetlana wraz z paskiem postępu.

   ![Zrzut ekranu przedstawiający okno konsoli z paskiem postępu. W oknie zostanie wyświetlona informacja, że początkowa konfiguracja jest ciągła, i prosi użytkownika o oczekiwanie.](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie zostanie uruchomione w środowisku innym niż DHCP, zobaczysz następujący ekran.

   ![Zrzut ekranu przedstawiający okno konsoli z tekstem opisującym urządzenie. Wiersz polecenia odczytuje "kontroler" i wydaje się być gotowy do wprowadzania danych.](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Następnie skonfiguruj sieć.
7. Użyj `Get-HcsIpAddress` polecenia, aby wyświetlić listę interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![Zrzut ekranu przedstawiający okno konsoli z danymi wyjściowymi polecenia Get-HcsIpAddress. "Ethernet" jest wymieniony jako nazwa urządzenia.](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Przykład przedstawiono poniżej:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Zrzut ekranu przedstawiający okno konsoli z danymi wyjściowymi polecenia Get-Help Set-HcsIpAddress i poprawnego użycia polecenia Set-HcsIpAddress.](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Ten adres IP będzie używany do nawiązywania połączenia z interfejsem użytkownika sieci Web urządzenia wirtualnego i dokończ konfigurację lokalną i rejestrację.

   ![Zrzut ekranu przedstawiający okno konsoli z tekstem baneru urządzenia. Ten tekst zawiera adres i adres IP urządzenia.](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Obowiązkowe Wykonaj ten krok tylko wtedy, gdy wdrażasz urządzenie w chmurze dla instytucji rządowych. Na urządzeniu zostanie teraz włączony tryb Stany Zjednoczone FIPS (Federal Information Processing Standard). Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użycia przez Federalne systemy komputerowe dla instytucji rządowych USA do ochrony poufnych danych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS należy ponownie uruchomić urządzenie, aby zmiany kryptograficzne zaczęły obowiązywać.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Przełączanie urządzenia do trybu FIPS i niezgodnego z trybem FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście baneru zostanie wyświetlony błąd (widoczny poniżej). Trzeba zmodyfikować konfigurację urządzenia tak, aby zapewnić zasoby spełniające minimalne wymagania. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Minimalne wymagania konfiguracji opisano w sekcji [Krok 1. Sprawdzanie, czy system hosta spełnia minimalne wymagania dotyczące urządzenia wirtualnego](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![Zrzut ekranu przedstawiający okno konsoli z tekstem baneru urządzenia. Ten tekst zawiera komunikat o błędzie, który zawiera adres URL służący do rozwiązywania problemu.](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Jeśli wystąpi jakikolwiek inny błąd podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web, zapoznaj się z następującymi przepływami pracy:

* Uruchom testy diagnostyczne, aby [rozwiązać problemy z konfiguracją interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generuj pakiet dzienników i Wyświetl pliki dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
