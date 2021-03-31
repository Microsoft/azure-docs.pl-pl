---
title: Wdróż StorSimple Snapshot Manager | Microsoft Docs
description: Dowiedz się, jak pobrać i zainstalować Snapshot Manager StorSimple, przystawkę programu MMC do zarządzania funkcjami ochrony danych StorSimple i tworzenia kopii zapasowych.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003875"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Wdróż przystawkę StorSimple Snapshot Manager MMC

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Program StorSimple Snapshot Manager umożliwia zarządzanie Microsoft Azure StorSimple lokalnymi i magazynem w chmurze tak samo, jak w pełni zintegrowany system pamięci masowej, co upraszcza proces tworzenia kopii zapasowych i przywracania oraz zmniejszania kosztów. 

W tym samouczku opisano wymagania dotyczące konfiguracji oraz procedury instalowania, usuwania i uaktualniania StorSimple Snapshot Manager.

> [!NOTE]
> * Nie można użyć Snapshot Manager StorSimple do zarządzania Microsoft Azure StorSimplemi macierzami wirtualnymi (znanymi również jako StorSimple lokalne urządzenia wirtualne).
> * Jeśli planujesz zainstalować aktualizację StorSimple Update 2 na urządzeniu StorSimple, pamiętaj, aby pobrać najnowszą wersję StorSimple Snapshot Manager i zainstalować ją **przed zainstalowaniem StorSimple Update 2**. Najnowsza wersja StorSimple Snapshot Manager jest zgodna z poprzednią wersją i współpracuje ze wszystkimi wersjami Microsoft Azure StorSimple. Jeśli używasz wcześniejszej wersji programu StorSimple Snapshot Manager, musisz ją zaktualizować (nie musisz odinstalować poprzedniej wersji przed zainstalowaniem nowej wersji).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple instalacji Snapshot Manager
StorSimple Snapshot Manager można zainstalować na komputerach z systemem operacyjnym Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub Windows Server 2012 R2. Na serwerach z systemem Windows 2008 R2 należy również zainstalować systemy Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3,0.

Przed zainstalowaniem lub uaktualnieniem przystawki StorSimple Snapshot Manager dla programu Microsoft Management Console (MMC) Upewnij się, że urządzenie Microsoft Azure StorSimple i serwer hosta są poprawnie skonfigurowane.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Poniższe kroki zapewniają ogólny przegląd zadań konfiguracyjnych, które należy wykonać przed zainstalowaniem Snapshot Manager StorSimple. Aby uzyskać pełną konfigurację Microsoft Azure StorSimple i informacje dotyczące konfiguracji, w tym wymagania systemowe i instrukcje krok po kroku, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Przed rozpoczęciem zapoznaj się z [listą kontrolną konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) i [wymaganiami wstępnymi](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) dotyczącymi wdrażania w temacie [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Przed zainstalowaniem StorSimple Snapshot Manager
1. Rozpakowywanie, Instalowanie i łączenie Microsoft Azure StorSimple urządzenia zgodnie z opisem w temacie [Instalowanie urządzenia z programem StorSimple 8100](storsimple-8100-hardware-installation.md) lub [instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że na komputerze hosta jest uruchomiony jeden z następujących systemów operacyjnych:
   
   * Windows Server 2008 R2 (na serwerach z systemem Windows 2008 R2 należy również zainstalować systemy Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3,0)
   * Windows Server 2012
   * Windows Server 2012 z dodatkiem R2
     
     W przypadku urządzenia wirtualnego StorSimple host musi być maszyną wirtualną Microsoft Azure.
3. Upewnij się, że spełniono wszystkie wymagania dotyczące konfiguracji Microsoft Azure StorSimple. Aby uzyskać szczegółowe informacje, przejdź do sekcji [wymagania wstępne dotyczące wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Podłącz urządzenie do hosta i wykonaj konfigurację początkową. Aby uzyskać szczegółowe informacje, przejdź do sekcji [kroki wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Utwórz woluminy na urządzeniu, przypisz je do hosta i sprawdź, czy host może go zainstalować i używać. StorSimple Snapshot Manager obsługuje następujące typy woluminów:
   
   * Woluminy podstawowe
   * Woluminy proste
   * Woluminy dynamiczne
   * Dublowane woluminy dynamiczne (RAID 1)
   * Woluminy udostępnione klastra
     
     Aby uzyskać informacje na temat tworzenia woluminów na urządzeniu StorSimple lub urządzeniu wirtualnym StorSimple, przejdź do [kroku 6. Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)w obszarze [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Zainstaluj nowy Snapshot Manager StorSimple
Przed zainstalowaniem Snapshot Manager StorSimple upewnij się, że woluminy utworzone na urządzeniu StorSimple lub StorSimple urządzenie wirtualne są zainstalowane, zainicjowane i sformatowane zgodnie z opisem w temacie [Konfigurowanie wymagań wstępnych](#configure-prerequisites).

> [!IMPORTANT]
> * W przypadku urządzenia wirtualnego StorSimple host musi być maszyną wirtualną Microsoft Azure. 
> * Na hoście musi być uruchomiony system Windows 2008 R2, Windows Server 2012 lub Windows Server 2012 R2. Jeśli na serwerze jest uruchomiony system Windows Server 2008 R2, należy również zainstalować systemy Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3,0.
> * Aby można było podłączyć urządzenie do usługi StorSimple Snapshot Manager, należy skonfigurować połączenie iSCSI z hosta na urządzenie StorSimple.

Wykonaj następujące kroki, aby ukończyć nową instalację StorSimple Snapshot Manager. W przypadku instalowania uaktualnienia przejdź do pozycji [Uaktualnij lub ponownie zainstaluj program StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1. Instalowanie StorSimple Snapshot Manager 
* Krok 2. Łączenie StorSimple Snapshot Manager z urządzeniem 
* Krok 3. Weryfikowanie połączenia z urządzeniem 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1. Instalowanie StorSimple Snapshot Manager
Wykonaj następujące kroki, aby zainstalować StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Aby zainstalować StorSimple Snapshot Manager
1. Pobierz oprogramowanie StorSimple Snapshot Manager (przejdź do [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) w centrum pobierania Microsoft) i Zapisz je lokalnie na hoście.
2. W Eksploratorze plików kliknij prawym przyciskiem myszy folder skompresowany, a następnie kliknij polecenie **Wyodrębnij wszystko**.
3. W oknie **Wyodrębnij skompresowane foldery (spakowane)** w polu **Wybierz miejsce docelowe i Wyodrębnij pliki** wpisz lub przejdź do ścieżki, w której chcesz umieścić plik do wyodrębnienia.
   
    > [!IMPORTANT]
    > Na dysku C: należy zainstalować StorSimple Snapshot Manager.
    
4. Zaznacz pole wyboru **Pokaż wyodrębnione pliki po zakończeniu** , a następnie kliknij pozycję **Wyodrębnij**.
   
    ![Okno dialogowe Wyodrębnianie plików](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po zakończeniu wyodrębniania zostanie otwarty folder docelowy. Kliknij dwukrotnie ikonę konfiguracja aplikacji, która pojawia się w folderze docelowym.
6. Po wyświetleniu komunikatu o **pomyślnym zakończeniu instalacji** kliknij przycisk **Zamknij**. Na pulpicie powinna zostać wyświetlona ikona StorSimple Snapshot Manager.
   
    ![ikona pulpitu](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2. Łączenie StorSimple Snapshot Manager z urządzeniem
Aby nawiązać połączenie z usługą StorSimple Snapshot Manager z urządzeniem StorSimple, wykonaj następujące czynności.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Aby połączyć Snapshot Manager StorSimple z urządzeniem
1. Kliknij ikonę Snapshot Manager StorSimple na pulpicie. Zostanie wyświetlone okno StorSimple Snapshot Manager. Okno zawiera okienko **zakres** , okienko **wyników** i okienko **akcji** . 
   
    ![Interfejs użytkownika Snapshot Manager StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Okienko **zakres** (lewe okienko) zawiera listę węzłów zorganizowanych w strukturze drzewa. Niektóre węzły można rozwinąć, aby wybrać widok lub konkretne dane powiązane z tym węzłem. Kliknij ikonę strzałki, aby rozwinąć lub zwinąć węzeł. Kliknij prawym przyciskiem myszy element w okienku **zakres** , aby wyświetlić listę akcji dostępnych dla tego elementu.
   * Okienko **wyników** (środkowe okienko) zawiera szczegółowe informacje o stanie węzła, widoku lub danych, które zostały wybrane w okienku **zakres** .
   * Okienko **Akcje** zawiera listę operacji, które można wykonać w węźle, widoku lub danych, które zostały wybrane w okienku **zakres** .
     
     Pełny opis interfejsu użytkownika StorSimple Snapshot Manager można znaleźć w temacie [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).
2. W okienku **zakres** kliknij prawym przyciskiem myszy węzeł **urządzenia** , a następnie kliknij polecenie **Konfiguruj urządzenie**. Zostanie wyświetlone okno dialogowe **Konfigurowanie urządzenia** .
   
    ![Konfigurowanie urządzenia](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. W polu Lista **urządzeń** wybierz adres IP urządzenia Microsoft Azure StorSimple lub urządzenia wirtualnego. W polu tekstowym **hasło** wpisz StorSimple Snapshot Manager hasło, które zostało utworzone dla urządzenia w Azure Portal. Kliknij przycisk **OK**.
4. StorSimple Snapshot Manager wyszukuje zidentyfikowane urządzenie. Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Możesz [sprawdzić połączenie z urządzeniem](#to-verify-the-connection) , aby upewnić się, że połączenie zostało pomyślnie dodane.
   
    Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** , aby zamknąć komunikat o błędzie, a następnie kliknij przycisk **Anuluj** , aby zamknąć okno dialogowe **Konfigurowanie urządzenia** .
5. Po nawiązaniu połączenia z urządzeniem StorSimple Snapshot Manager importuje każdą grupę woluminów skonfigurowaną dla tego urządzenia, pod warunkiem, że grupa woluminów ma skojarzone kopie zapasowe. Grupy woluminów, które nie mają skojarzonych kopii zapasowych, nie są importowane. Ponadto zasady tworzenia kopii zapasowych utworzone dla grupy woluminów nie są importowane. Aby wyświetlić zaimportowane grupy, kliknij prawym przyciskiem myszy węzeł **grupy** najwyższego poziomu w okienku **zakres** , a następnie kliknij pozycję **Przełącz zaimportowane grupy**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3. Weryfikowanie połączenia z urządzeniem
Wykonaj następujące kroki, aby sprawdzić, czy StorSimple Snapshot Manager jest podłączony do urządzenia StorSimple.

#### <a name="to-verify-the-connection"></a>Aby zweryfikować połączenie
1. W okienku **zakres** kliknij węzeł **urządzenia** .
   
    ![Stan urządzenia StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Sprawdź okienko **wyników** : 
   
   * Jeśli na ikonie urządzenia zostanie wyświetlony zielony wskaźnik i będzie on **dostępny** w kolumnie **stan** , urządzenie jest połączone. 
   * Jeśli na ikonie urządzenia pojawi się czerwony wskaźnik, a w kolumnie **stan** jest niedostępna, urządzenie nie jest połączone. 
   * Jeśli **odświeżanie** pojawi się w kolumnie **stan** , StorSimple Snapshot Manager Pobiera grupy woluminów i skojarzone kopie zapasowe dla podłączonego urządzenia.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uaktualnij lub ponownie zainstaluj program StorSimple Snapshot Manager
Należy odinstalować StorSimple Snapshot Manager całkowicie przed uaktualnieniem lub ponownym zainstalowaniem oprogramowania. 

Przed ponownym zainstalowaniem Snapshot Manager StorSimple wykonaj kopię zapasową istniejącej bazy danych StorSimple Snapshot Manager na komputerze-hoście. Spowoduje to zapisanie zasad tworzenia kopii zapasowych i informacji o konfiguracji, dzięki czemu można łatwo przywrócić te dane z kopii zapasowej.

Wykonaj następujące kroki, Jeśli uaktualniasz lub ponownie instalujesz StorSimple Snapshot Manager:

* Krok 1. odinstalowanie Snapshot Manager StorSimple 
* Krok 2. Tworzenie kopii zapasowej bazy danych StorSimple Snapshot Manager 
* Krok 3. ponowne zainstalowanie StorSimple Snapshot Manager i przywrócenie bazy danych 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1. odinstalowanie Snapshot Manager StorSimple
Aby odinstalować StorSimple Snapshot Manager, wykonaj następujące czynności.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Aby odinstalować StorSimple Snapshot Manager
1. Na komputerze-hoście Otwórz **Panel sterowania**, kliknij pozycję **programy**, a następnie kliknij pozycję **programy i funkcje**.
2. W lewym okienku kliknij pozycję **Odinstaluj lub zmień program**.
3. Kliknij prawym przyciskiem myszy pozycję **StorSimple Snapshot Manager**, a następnie kliknij pozycję **Odinstaluj**.
4. Spowoduje to uruchomienie programu instalacyjnego Snapshot Manager StorSimple. Kliknij przycisk **Modyfikuj Instalatora**, a następnie kliknij przycisk **Odinstaluj**.
   
   > [!NOTE]
   > Jeśli w tle są uruchomione jakiekolwiek procesy programu MMC, takie jak StorSimple Snapshot Manager lub Zarządzanie dyskami, dezinstalacja zakończy się niepowodzeniem i zostanie wyświetlony komunikat informujący o zamknięciu wszystkich wystąpień programu MMC przed podjęciem próby odinstalowania programu. Wybierz pozycję **Automatycznie zamknij aplikacje i spróbuj uruchomić je ponownie po zakończeniu instalacji**, a następnie kliknij przycisk **OK**.
   > 
   > 
5. Po zakończeniu procesu odinstalowywania zostanie wyświetlony komunikat **Instalacja powiodła** się. Kliknij przycisk **Zamknij**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2. Tworzenie kopii zapasowej bazy danych StorSimple Snapshot Manager
Wykonaj następujące kroki, aby utworzyć i zapisać kopię bazy danych StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Aby utworzyć kopię zapasową bazy danych
1. Zatrzymaj usługę Microsoft StorSimple Management:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   3. Na stronie **usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Zatrzymaj usługę**.
      
        ![Zatrzymaj usługę StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
  
3. Znajdź plik XML katalogu, skopiuj plik i Zapisz kopię w bezpiecznej lokalizacji lub w chmurze.
   
    ![Plik wykazu kopii zapasowej StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę Microsoft StorSimple Management: 
   
   1. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   2. Na stronie **usługi** wybierz **usługę Microsoft StorSimple Management**.
   3. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Uruchom ponownie usługę**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3. ponowne zainstalowanie StorSimple Snapshot Manager i przywrócenie bazy danych
Aby ponownie zainstalować program StorSimple Snapshot Manager, wykonaj kroki opisane w temacie [Install a New StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Następnie użyj poniższej procedury, aby przywrócić bazę danych StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Aby przywrócić bazę danych
1. Zatrzymaj usługę Microsoft StorSimple Management:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   3. Na stronie **usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Zatrzymaj usługę**.
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
   > 
   > 
3. Usuń plik XML katalogu i zastąp go wersją zapisaną wcześniej.
4. Uruchom ponownie usługę Microsoft StorSimple Management: 
   
   1. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   2. Na stronie **usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   3. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Uruchom ponownie usługę**.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat StorSimple Snapshot Manager, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Aby dowiedzieć się więcej o interfejsie użytkownika StorSimple Snapshot Manager, przejdź do [StorSimple Snapshot Manager interfejs użytkownika](storsimple-use-snapshot-manager.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi StorSimple Snapshot Manager, przejdź do pozycji [Użyj StorSimple Snapshot Manager w celu administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).

