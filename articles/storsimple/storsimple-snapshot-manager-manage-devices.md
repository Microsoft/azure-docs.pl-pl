---
title: Zarządzanie urządzeniami za pomocą StorSimple Snapshot Manager | Microsoft Docs
description: Opisuje sposób używania przystawki StorSimple Snapshot Manager MMC do łączenia urządzeń StorSimple i zarządzania nimi.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054854"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Używanie Snapshot Manager StorSimple do nawiązywania połączeń z urządzeniami StorSimple i zarządzania nimi
## <a name="overview"></a>Omówienie
Za pomocą węzłów w okienku StorSimple Snapshot Manager **SCOPE** można sprawdzić zaimportowane dane urządzenia StorSimple i odświeżyć połączone urządzenia magazynujące. Ponadto po kliknięciu węzła **urządzenia** można wyświetlić listę połączonych urządzeń i odpowiednie informacje o stanie w okienku **wyników** .

![Połączone urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Rysunek 1: StorSimple Snapshot Manager podłączonym urządzeniu** 

W zależności **od wybranych opcji** w okienku **wyników** zostaną wyświetlone następujące informacje dotyczące poszczególnych urządzeń. (Aby uzyskać więcej informacji na temat konfigurowania widoku, przejdź do [menu Widok](storsimple-use-snapshot-manager.md#view-menu).

| Kolumna wyników | Opis |
|:--- |:--- |
| Nazwa |Nazwa urządzenia zgodnie z konfiguracją w klasycznym portalu Azure |
| Model |Numer modelu urządzenia |
| Wersja |Wersja oprogramowania zainstalowanego na urządzeniu |
| Stan |Czy urządzenie jest dostępne |
| Ostatnia synchronizacja |Data i godzina ostatniej synchronizacji urządzenia |
| Numer seryjny |Numer seryjny urządzenia |

Jeśli klikniesz prawym przyciskiem myszy węzeł **urządzenia** w okienku **zakres** , możesz wybrać jedną z następujących akcji:

* Dodawanie lub zastępowanie urządzenia
* Podłącz urządzenie i sprawdź, czy Importy
* Odśwież podłączone urządzenia

Jeśli klikniesz węzeł **urządzenia** , a następnie klikniesz prawym przyciskiem myszy nazwę urządzenia w okienku **wyników** , możesz wybrać jedną z następujących akcji:

* Uwierzytelnianie urządzenia
* Wyświetlanie szczegółów urządzenia
* Odświeżanie urządzenia
* Usuń konfigurację urządzenia
* Zmiana hasła urządzenia

> [!NOTE]
> Wszystkie te akcje są również dostępne w okienku **Akcje** .


W tym samouczku wyjaśniono, jak używać Snapshot Manager StorSimple do łączenia urządzeń i zarządzania nimi oraz wykonywania następujących zadań:

* Dodawanie lub zastępowanie urządzenia
* Podłącz urządzenie i sprawdź, czy Importy
* Odśwież podłączone urządzenia
* Uwierzytelnianie urządzenia
* Wyświetlanie szczegółów urządzenia
* Odświeżanie poszczególnych urządzeń
* Usuń konfigurację urządzenia
* Zmienianie wygasłego hasła urządzenia
* Zastępowanie urządzenia zakończonego niepowodzeniem

> [!NOTE]
> Aby uzyskać ogólne informacje na temat korzystania z interfejsu StorSimple Snapshot Manager, przejdź do [StorSimple Snapshot Manager interfejs użytkownika](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Dodawanie lub zastępowanie urządzenia
Aby dodać lub zastąpić urządzenie StorSimple, należy wykonać poniższą procedurę.

#### <a name="to-add-or-replace-a-device"></a>Aby dodać lub zamienić urządzenie
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy węzeł **urządzenia** , a następnie kliknij polecenie **Konfiguruj urządzenie**. Zostanie wyświetlone okno dialogowe **Konfigurowanie urządzenia** .
   
    ![Konfigurowanie urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. W polu listy rozwijanej **urządzenie** wybierz adres IP urządzenia lub urządzenia wirtualnego. 
4. W polu tekstowym **hasło** wpisz hasło StorSimple Snapshot Manager utworzone dla urządzenia w klasycznym portalu Azure. Kliknij przycisk **OK**. StorSimple Snapshot Manager wyszukuje zidentyfikowane urządzenie. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie.
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** , aby zamknąć komunikat o błędzie, a następnie kliknij przycisk **Anuluj** , aby zamknąć okno dialogowe **Konfigurowanie urządzenia** .

## <a name="connect-a-device-and-verify-imports"></a>Podłącz urządzenie i sprawdź, czy Importy
Aby połączyć urządzenie z systemem StorSimple i sprawdzić, czy są importowane wszystkie istniejące grupy woluminów, które mają skojarzone kopie zapasowe, należy wykonać poniższą procedurę.

#### <a name="to-connect-a-device-and-verify-imports"></a>Aby połączyć urządzenie i zweryfikować Importy
1. Aby podłączyć urządzenie do StorSimple Snapshot Manager, postępuj zgodnie z instrukcjami w temacie Dodawanie lub zastępowanie urządzenia. Po nawiązaniu połączenia z urządzeniem StorSimple Snapshot Manager reaguje w następujący sposób:
   
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Po wybraniu urządzenia pojawia się ono w okienku **wyników** , a pole Stan wskazuje, że urządzenie jest **dostępne**. StorSimple Snapshot Manager importuje wszystkie grupy woluminów skonfigurowane dla urządzenia, pod warunkiem, że grupy woluminów mają skojarzone kopie zapasowe. Zasady tworzenia kopii zapasowych nie są importowane. Grupy woluminów, które nie mają skojarzonych kopii zapasowych, nie są importowane.
2. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
3. Kliknij prawym przyciskiem myszy górny węzeł w okienku **zakres** , a następnie kliknij pozycję **Przełącz Importy**.
   
    ![Wybierz pozycję Przełącz Importy](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Wyświetlane jest okno dialogowe **Przełącz Importy** , pokazujące stan zaimportowanych grup woluminów i kopii zapasowych. Kliknij przycisk **OK**.

Po pomyślnym zaimportowaniu grup woluminów i kopii zapasowych możesz zarządzać nimi za pomocą StorSimple Snapshot Manager, tak jak w przypadku zarządzania grupami woluminów i kopiami zapasowymi utworzonymi i skonfigurowanymi przy użyciu StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Odśwież podłączone urządzenia
Poniższa procedura umożliwia zsynchronizowanie podłączonych urządzeń StorSimple z usługą StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Aby odświeżyć połączone urządzenia
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy pozycję **urządzenia**, a następnie kliknij polecenie **Odśwież urządzenia**. Spowoduje to zsynchronizowanie połączonych urządzeń z StorSimple Snapshot Manager, aby można było wyświetlić grupy woluminów i kopie zapasowe, w tym wszystkie najnowsze dodatki. 
   
    ![Odświeżanie urządzeń StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Akcja **Odśwież urządzenia** pobiera wszystkie nowe grupy woluminów i wszystkie skojarzone z nimi kopie zapasowe z połączonych urządzeń. W przeciwieństwie do akcji **Skanuj woluminy** dostępne dla węzła **woluminy** , **odświeżanie urządzenia** nie przywraca rejestru kopii zapasowej.

## <a name="authenticate-a-device"></a>Uwierzytelnianie urządzenia
Poniższa procedura umożliwia uwierzytelnienie urządzenia StorSimple przy użyciu StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Aby uwierzytelnić urządzenie
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij pozycję **urządzenia**.
3. W okienku **wyników** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij pozycję **Uwierzytelnij**.
4. Zostanie wyświetlone okno dialogowe **uwierzytelnianie** . Wpisz hasło urządzenia, a następnie kliknij przycisk **OK**.
   
    ![Uwierzytelnianie — okno dialogowe](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Wyświetlanie szczegółów urządzenia
Użyj następującej procedury, aby wyświetlić szczegóły urządzenia StorSimple i, w razie potrzeby, ponownie zsynchronizuj urządzenie z StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Aby wyświetlić i ponownie zsynchronizować szczegóły urządzenia
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij pozycję **urządzenia**.
3. W okienku **wyników** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij pozycję **szczegóły**.

4. pojawi się okno dialogowe **szczegóły urządzenia** . W tym polu jest wyświetlana nazwa, model, wersja, numer seryjny, stan, docelowa kwalifikowana nazwa iSCSI (IQN) oraz Data i godzina ostatniej synchronizacji.

* Kliknij pozycję **ponowna synchronizacja** , aby zsynchronizować urządzenie.
* Kliknij przycisk **OK** lub **Anuluj** , aby zamknąć okno dialogowe.
  
  ![Szczegóły urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Odświeżanie poszczególnych urządzeń
Użyj poniższej procedury, aby ponownie synchronizować pojedyncze urządzenie StorSimple z usługą StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Aby odświeżyć urządzenie
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager. 
2. W okienku **zakres** kliknij pozycję **urządzenia**. 
3. W okienku **wyników** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Odśwież urządzenie**. Spowoduje to zsynchronizowanie urządzenia z StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Usuń konfigurację urządzenia
Aby usunąć pojedynczą konfigurację urządzenia StorSimple z usługi StorSimple Snapshot Manager, należy wykonać czynności opisane w poniższej procedurze.

#### <a name="to-delete-a-device-configuration"></a>Aby usunąć konfigurację urządzenia
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij pozycję **urządzenia**. 
3. W okienku **wyników** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Usuń**. 
4. Zostanie wyświetlony następujący komunikat. Kliknij przycisk **tak** , aby usunąć konfigurację, lub kliknij przycisk **nie** , aby anulować usunięcie.
   
    ![Usuwanie konfiguracji urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Zmienianie wygasłego hasła urządzenia
Należy wprowadzić hasło w celu uwierzytelnienia urządzenia z systemem StorSimple za pomocą StorSimple Snapshot Manager. To hasło należy skonfigurować podczas konfigurowania urządzenia za pomocą interfejsu programu Windows PowerShell. Hasło może jednak wygasnąć. W takim przypadku można zmienić hasło przy użyciu klasycznego portalu Azure. Następnie, ponieważ urządzenie zostało skonfigurowane w StorSimple Snapshot Manager przed wygaśnięciem hasła, należy ponownie uwierzytelnić urządzenie w usłudze StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Aby zmienić wygasłe hasło
1. W klasycznym portalu Azure Uruchom usługę StorSimple Manager.
2. Kliknij pozycję **urządzenia**  >  **Skonfiguruj** dla urządzenia.
3. Przewiń w dół do sekcji StorSimple Snapshot Manager. Wprowadź hasło o długości 14-15 znaków. Upewnij się, że hasło zawiera kombinację wielkich, małych liter, cyfr i znaków specjalnych.
4. Wprowadź ponownie hasło, aby je potwierdzić.
5. Kliknij przycisk **Zapisz** w dolnej części strony.

#### <a name="to-re-authenticate-the-device"></a>Aby ponownie uwierzytelnić urządzenie
1. Uruchom StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij pozycję **urządzenia**. W okienku **wyników** zostanie wyświetlona lista skonfigurowanych urządzeń.
3. Wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Uwierzytelnij**.
4. W oknie **uwierzytelnianie** wprowadź nowe hasło.
5. Wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Odśwież urządzenie**. Spowoduje to zsynchronizowanie urządzenia z StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Zastępowanie urządzenia zakończonego niepowodzeniem
Jeśli urządzenie StorSimple ulegnie awarii i zostanie zastąpione przez urządzenie w trybie failover (praca awaryjna), wykonaj następujące kroki, aby nawiązać połączenie z nowym urządzeniem i wyświetlić skojarzone kopie zapasowe.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Aby nawiązać połączenie z nowym urządzeniem po przejściu w tryb failover
1. Skonfiguruj ponownie połączenie iSCSI z nowym urządzeniem. Aby uzyskać instrukcje, przejdź do lokalizacji "krok 7. Instalowanie, inicjowanie i formatowanie woluminu" w [sekcji Wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Jeśli nowe urządzenie StorSimple ma taki sam adres IP jak stary, może być możliwe połączenie starej konfiguracji.


1. Zatrzymaj usługę Microsoft StorSimple Management:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   3. W oknie **usługi** wybierz **usługę Microsoft StorSimple Management**.
   4. W prawym okienku w obszarze **usługi Microsoft StorSimple Management**kliknij pozycję **Zatrzymaj usługę**.
2. Usuń informacje o konfiguracji dotyczące starego urządzenia:
   
   1. W Eksploratorze plików przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Usuń pliki w folderze BACatalog.
3. Uruchom ponownie usługę Microsoft StorSimple Management:
   
   1. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   2. W oknie **usługi** wybierz **usługę Microsoft StorSimple Management**.
   3. W prawym okienku w obszarze **usługi Microsoft StorSimple Management**kliknij pozycję **Uruchom ponownie usługę**.
4. Uruchom StorSimple Snapshot Manager.
5. Aby skonfigurować nowe urządzenie StorSimple, wykonaj kroki opisane w sekcji Krok 2. Łączenie urządzenia StorSimple w programie [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Kliknij prawym przyciskiem myszy węzeł najwyższego poziomu w okienku **zakres** (StorSimple Snapshot Manager w przykładzie), a następnie kliknij pozycję **Przełącz Importy**. 
7. Komunikat pojawia się, gdy zaimportowane grupy woluminów i kopie zapasowe są widoczne w StorSimple Snapshot Manager. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [używać Snapshot Manager StorSimple do wyświetlania woluminów i zarządzania nimi](storsimple-snapshot-manager-manage-volumes.md).

