---
title: Inicjowanie obsługi administracyjnej macierzy wirtualnej StorSimple w funkcji Hyper-V | Microsoft Docs
description: Ten drugi samouczek wdrożenia macierzy wirtualnej StorSimple obejmuje Inicjowanie obsługi macierzy wirtualnej w funkcji Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070589"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Wdrażanie wirtualnej macierzy StorSimple — Inicjowanie obsługi administracyjnej w funkcji Hyper-V
![Diagram przedstawiający kroki wymagane do wdrożenia macierzy wirtualnej. Pierwsza część drugiego kroku ma etykietę udostępnianie w funkcji Hyper-V i jest wyróżniona.](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym samouczku opisano sposób udostępniania macierzy wirtualnej StorSimple w systemie hosta z uruchomioną funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. Ten artykuł dotyczy wdrażania tablic wirtualnych StorSimple w chmurze Azure Portal i Microsoft Azure Government.

Musisz mieć uprawnienia administratora, aby udostępnić i skonfigurować tablicę wirtualną. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne dotyczące aprowizacji
W tym miejscu znajdują się wymagania wstępne dotyczące aprowizacji macierzy wirtualnej w systemie hosta z uruchomioną funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie portalu do macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Pobrano obraz macierzy wirtualnej dla funkcji Hyper-V z Azure Portal. Aby uzyskać więcej informacji, zobacz **krok 3. Pobieranie obrazu macierzy wirtualnej** [przygotowanie portalu do StorSimple Virtual Array Guide](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Oprogramowanie uruchomione w macierzy wirtualnej StorSimple może być używane tylko z usługą StorSimple Menedżer urządzeń.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Dla macierzy wirtualnej StorSimple
Przed wdrożeniem macierzy wirtualnej upewnij się, że:

* Użytkownik ma dostęp do systemu hosta z uruchomioną funkcją Hyper-V w systemie Windows Server 2008 R2 lub nowszym, który może być używany w celu udostępnienia urządzenia.
* System hosta może przeznaczyć następujące zasoby, aby udostępnić tablicę wirtualną:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB dla danych.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem zapoznaj się z wymaganiami dotyczącymi sieci, aby wdrożyć macierz wirtualną StorSimple i odpowiednio skonfigurować sieć centrum danych. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci wirtualnej macierzy StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Inicjowanie obsługi krok po kroku
Aby zainicjować obsługę administracyjną macierzy wirtualnej i nawiązać z nią połączenie, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby do spełnienia minimalnych wymagań macierzy wirtualnej.
2. Zainicjuj obsługę macierzy wirtualnej w funkcji hypervisor.
3. Uruchom macierz wirtualną i uzyskaj adres IP.

Każdy z tych kroków został wyjaśniony w poniższych sekcjach.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1. Upewnij się, że system hosta spełnia minimalne wymagania dotyczące macierzy wirtualnej
Aby utworzyć tablicę wirtualną, potrzebne są:

* Rola funkcji Hyper-V zainstalowana w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2 SP1.
* Menedżer funkcji Microsoft Hyper-V na kliencie z systemem Microsoft Windows połączonym z hostem.

Upewnij się, że podstawowy sprzęt (system hosta), na którym tworzysz macierz wirtualną, jest w stanie przeznaczyć następujące zasoby do macierzy wirtualnej:

* Co najmniej 4 rdzenie.
* Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
* Jeden interfejs sieciowy.
* Dysk wirtualny 500 GB dla danych systemu.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2. Inicjowanie obsługi macierzy wirtualnej w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie w funkcji hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Aby zainicjować obsługę macierzy wirtualnej
1. Na hoście z systemem Windows Server Skopiuj obraz macierzy wirtualnej na dysk lokalny. Ten obraz został pobrany (VHD lub VHDX) za pomocą Azure Portal. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.
2. Otwórz **Menedżera serwera**. W prawym górnym rogu kliknij przycisk **Narzędzia** i wybierz pozycję **Menedżer funkcji Hyper-V**.

   ![Zrzut ekranu przedstawiający Menedżer serwera pokazujący rozwinięte menu narzędzi z wyróżnionym elementem Menedżera funkcji Hyper-V.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   W przypadku korzystania z systemu Windows Server 2008 R2 otwórz Menedżera funkcji Hyper-V. W Menedżer serwera kliknij pozycję **role > funkcji Hyper-v > Menedżerze funkcji Hyper-v**.
3. W **Menedżerze funkcji Hyper-V** w okienku zakresu kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie kliknij kolejno pozycje **Nowy** > **Maszyna wirtualna**.

   ![Zrzut ekranu Menedżera funkcji Hyper-V z menu skrótów węzła systemowego z wyróżnionymi elementami New i Virtual Machine.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stronie **Przed rozpoczęciem** w kreatorze nowej maszyny wirtualnej kliknij przycisk **Dalej**.
5. Na stronie **Określanie nazwy i lokalizacji** Podaj **nazwę** macierzy wirtualnej. Kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający stronę Określanie nazwy i lokalizacji, która zawiera pole Nazwa wypełnione i wyróżniono przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na stronie **Określanie generacji** wybierz typ obrazu urządzenia, a następnie kliknij przycisk **dalej**. Ta strona nie jest wyświetlana, jeśli używasz systemu Windows Server 2008 R2.

   * Wybierz opcję **generacja 2** , jeśli pobrano obraz VHDX dla systemu Windows Server 2012 lub nowszego.
   * Wybierz opcję **generacja 1** , jeśli pobrano obraz VHD dla systemu Windows Server 2008 R2 lub nowszego.

   ![Zrzut ekranu przedstawiający stronę Określanie generacji z wybraną opcją generacja 2 i podświetloną przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stronie **Przypisywanie pamięci** w polu **Pamięć początkowa** wprowadź wartość nie mniejszą niż **8192 MB**, nie włączaj pamięci dynamicznej, a następnie kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający stronę przypisywanie pamięci z wartością 8192 w polu pamięć początkowa. Przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stronie **Konfigurowanie sieci** określ przełącznik wirtualny połączony z Internetem, a następnie kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający stronę Konfigurowanie sieci pokazującą element przełącznika wirtualnego funkcji Hyper-V wybrany w polu połączenie i wyróżniony przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na stronie **Podłączanie wirtualnego dysku twardego** wybierz opcję **Użyj istniejącego wirtualnego dysku twardego**, określ lokalizację obrazu macierzy wirtualnej (VHDX lub VHD), a następnie kliknij przycisk **dalej**.

   ![Zrzut ekranu przedstawiający stronę łączenie wirtualnego dysku twardego z wybraną opcją Użyj istniejącego wirtualnego dysku twardego, wprowadzoną lokalizację i wyróżniony przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Przejrzyj informacje na stronie **Podsumowanie**, a następnie kliknij pozycję **Zakończ**, aby utworzyć maszynę wirtualną.

    ![Zrzut ekranu przedstawiający stronę kreatora kończenia nowej maszyny wirtualnej z opisem maszyny wirtualnej. Przycisk Zakończ jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Aby spełnić wymagania minimalne, potrzebujesz 4 rdzeni. Aby dodać 4 procesory wirtualne, wybierz swój system hosta w oknie **Menedżera funkcji Hyper-V**. Na liście **Maszyny wirtualne** w okienku po prawej stronie znajdź utworzoną przed chwilą maszynę wirtualną. Zaznacz i kliknij prawym przyciskiem myszy nazwę maszyny, a następnie wybierz pozycję **Ustawienia**.

    ![Zrzut ekranu okna Menedżera funkcji Hyper-V. Na liście jest zaznaczony jeden komputer, a element ustawień zostanie wyróżniony w menu skrótów tego komputera.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stronie **Ustawienia** w lewym okienku kliknij pozycję **Procesor**. W okienku po prawej stronie ustaw wartość 4 lub większą w polu **Liczba procesorów wirtualnych**. Kliknij pozycję **Zastosuj**.

    ![Zrzut ekranu przedstawiający stronę ustawień z wyróżnionym elementem procesora, liczbę procesorów wirtualnych ustawionych na cztery i wyróżniony przycisk Zastosuj.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby spełnić wymagania minimalne, należy również dodać wirtualny dysk danych 500 GB. Na stronie **Ustawienia**:

    1. W okienku po lewej stronie wybierz pozycję **Kontroler SCSI**.
    2. W okienku po prawej stronie wybierz pozycję **Dysk twardy** i kliknij przycisk **Dodaj**.

    ![Zrzut ekranu strony ustawień z wybranym elementem kontrolera SCSI, wybranego elementu dysk twardy i przycisk Dodaj.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stronie **Dysk twardy** wybierz opcję **Wirtualny dysk twardy**, a następnie kliknij przycisk **Nowy**. Zostanie uruchomiony **Kreator nowego wirtualnego dysku twardego**.

    ![Zrzut ekranu strony ustawień z wybranym pozycją dysk twardy, wybrano opcję wirtualny dysk twardy i przycisk Nowy wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stronie **Przed rozpoczęciem** w kreatorze nowego wirtualnego dysku twardego kliknij przycisk **Dalej**.
16. Na stronie **Wybieranie formatu dysku** zaakceptuj domyślną opcję formatu **VHDX**. Kliknij przycisk **Dalej**. Ten ekran nie jest wyświetlany, jeśli jest uruchomiony system Windows Server 2008 R2.

    ![Zrzut ekranu przedstawiający stronę Wybieranie formatu dysku z wybraną opcją VHDX i wyróżnioną przycisk Dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stronie **Wybieranie typu dysku** ustaw typ wirtualnego dysku twardego **Dynamicznie powiększający się** (zalecane). Dysk **O stałym rozmiarze** również byłby odpowiedni, ale jego tworzenie mogłoby zająć więcej czasu. Nie zalecamy używania opcji **Różnicowy**. Kliknij przycisk **Dalej**. W systemach Windows Server 2012 R2 i Windows Server 2012, **dynamicznie powiększający** się jest opcja domyślna w systemie windows Server 2008 R2, wartość domyślna to **stały rozmiar**.

    ![Zrzut ekranu przedstawiający stronę Wybieranie typu dysku z wybraną opcją dynamicznego powiększania i wyróżnioną przyciskiem dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stronie **Określanie nazwy i lokalizacji** podaj **nazwę** dysku danych oraz jego **lokalizację** (możesz skorzystać z opcji Przeglądaj). Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Określanie nazwy i lokalizacji z wartościami w polach Nazwa i lokalizacja. Przycisk dalej zostanie wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na stronie **Konfigurowanie dysku** wybierz opcję **Utwórz nowy pusty wirtualny dysk twardy** i określ rozmiar **500 GB** (lub więcej). Chociaż 500 GB jest minimalnym wymaganiem, zawsze można zainicjować obsługę większą liczbę dysków. Należy pamiętać, że nie można rozbudować lub zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Aby uzyskać więcej informacji na temat rozmiaru dysku do aprowizacji, zapoznaj się z sekcją ustalanie rozmiaru w [dokumencie Best Practices](storsimple-ova-best-practices.md). Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Konfigurowanie dysku z wybranym nowym pustym wirtualnym dyskiem twardym, rozmiarem ustawionym na 500 i wyróżnionym przyciskiem dalej.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Sprawdź dane dysku wirtualnego na stronie **Podsumowanie** i, jeśli wszystkie dane są prawidłowe, kliknij przycisk **Zakończ**, aby utworzyć dysk. Kreator zostanie zamknięty, a wirtualny dysk twardy zostanie dodany do maszyny.

    ![Zrzut ekranu przedstawiający stronę kreatora kończenia nowego wirtualnego dysku twardego z opisem wirtualnego dysku twardego. Przycisk Zakończ jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Wróć do strony **Ustawienia**. Kliknij przycisk **OK**, aby zamknąć stronę **Ustawienia**, a następnie wróć do okna Menedżera funkcji Hyper-V.

    ![Zrzut ekranu przedstawiający stronę ustawień. Przycisk OK jest wyróżniony.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3. uruchomienie macierzy wirtualnej i uzyskanie adresu IP
Wykonaj następujące kroki, aby uruchomić macierz wirtualną i nawiązać z nią połączenie.

#### <a name="to-start-the-virtual-array"></a>Aby uruchomić macierz wirtualną
1. Uruchom macierz wirtualną.

   ![Zrzut ekranu okna Menedżera funkcji Hyper-V. Na liście wybrano nową tablicę, a element początkowy zostanie wyróżniony w menu skrótów tej tablicy.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Po uruchomieniu urządzenia wybierz je, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Połącz**.

   ![Zrzut ekranu okna Menedżera funkcji Hyper-V. Wybrano nową tablicę, jej stan jest uruchomiony, a element Connect zostanie wyróżniony w menu skrótów.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Może być konieczne odczekanie 5-10 minut, aż urządzenie będzie gotowe. W konsoli zostanie wyświetlony komunikat o stanie, wskazujący postęp procesu. Gdy urządzenie będzie gotowe, przejdź do pozycji **Akcja**. Naciśnij klawisz `Ctrl + Alt + Delete` , aby zalogować się do macierzy wirtualnej. Domyślny użytkownik to *StorSimpleAdmin* , a hasło domyślne to *Password1*.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere z nieokreślonymi znakami w polu logowania.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere. Tekst na stronie informuje o tym, że należy zmienić hasło.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać co najmniej 3 z następujących 4 elementów: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Otrzymasz powiadomienie, że hasło zostało zmienione.

   ![Zrzut ekranu przedstawiający kartę Konsola kliencka vSphere. Tekst na stronie wskazuje, że hasło zostało zmienione.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po pomyślnym zmianie hasła tablica wirtualna może zostać ponownie uruchomiona. Poczekaj na uruchomienie urządzenia.

   ![Zrzut ekranu przedstawiający stronę głównej StorSimpleAdmin. Tekst na stronie prosi użytkownika o zaczekanie na usługę powiadamiania o zdarzeniach systemowych.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zostanie wyświetlona konsola programu Windows PowerShell urządzenia wraz z paskiem postępu.

   ![Zrzut ekranu przedstawiający okno konsoli z paskiem postępu. W oknie zostanie wyświetlona informacja, że początkowa konfiguracja jest ciągła, i prosi użytkownika o oczekiwanie.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie zostanie uruchomione w środowisku innym niż DHCP, zobaczysz następujący ekran.

   ![Zrzut ekranu przedstawiający okno konsoli z tekstem opisującym urządzenie. Wiersz polecenia odczytuje "kontroler" i wydaje się być gotowy do wprowadzania danych.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Następnie skonfiguruj sieć.
7. Użyj `Get-HcsIpAddress` polecenia, aby wyświetlić listę interfejsów sieciowych włączonych w macierzy wirtualnej. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![Zrzut ekranu przedstawiający okno konsoli z danymi wyjściowymi polecenia Get-HcsIpAddress. "Ethernet" jest wymieniony jako nazwa urządzenia.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Zrzut ekranu przedstawiający okno konsoli z danymi wyjściowymi polecenia Get-Help Set-HcsIpAddress i poprawnego użycia polecenia Set-HcsIpAddress.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Użyj tego adresu IP, aby nawiązać połączenie z interfejsem użytkownika sieci Web macierzy wirtualnej i ukończyć konfigurację lokalną i rejestrację.

   ![Zrzut ekranu przedstawiający okno konsoli z tekstem baneru urządzenia. Ten tekst zawiera adres i adres IP urządzenia.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Obowiązkowe Wykonaj ten krok tylko wtedy, gdy wdrażasz urządzenie w chmurze dla instytucji rządowych. Na urządzeniu zostanie teraz włączony tryb Stany Zjednoczone FIPS (Federal Information Processing Standard). Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użycia przez Federalne systemy komputerowe dla instytucji rządowych USA do ochrony poufnych danych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS należy ponownie uruchomić urządzenie, aby zmiany kryptograficzne zaczęły obowiązywać.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Przełączanie urządzenia do trybu FIPS i niezgodnego z trybem FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, zobaczysz następujący błąd w tekście baneru (pokazanym poniżej). Zmodyfikuj konfigurację urządzenia tak, aby zapewnić zasoby zgodne z wymaganiami minimalnymi. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z minimalnymi wymaganiami dotyczącymi konfiguracji w kroku 1: Upewnij się, że system hosta spełnia minimalne wymagania dotyczące macierzy wirtualnej.

![Zrzut ekranu przedstawiający okno konsoli z tekstem baneru urządzenia. Ten tekst zawiera komunikat o błędzie, który zawiera adres URL służący do rozwiązywania problemu.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Jeśli wystąpi jakikolwiek inny błąd podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web, zapoznaj się z następującymi przepływami pracy:

* Uruchom testy diagnostyczne, aby [rozwiązać problemy z konfiguracją interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generuj pakiet dzienników i Wyświetl pliki dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
