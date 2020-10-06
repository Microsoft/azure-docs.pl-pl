---
title: Microsoft Azure StorSimple konfiguracja serwera iSCSI macierzy wirtualnej | Microsoft Docs
description: W tym artykule opisano, jak przeprowadzić instalację początkową, zarejestrować serwer iSCSI StorSimple i ukończyć konfigurację urządzenia.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 81799e6ec366c7429fdb29b85b4ff65d353a8fba
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742421"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Wdróż macierz wirtualną StorSimple — Skonfiguruj jako serwer iSCSI za pośrednictwem Azure Portal

![przepływ procesu konfiguracji iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ten samouczek wdrażania ma zastosowanie do Microsoft Azure StorSimple macierzy wirtualnej. W tym samouczku opisano, jak przeprowadzić konfigurację początkową, zarejestrować serwer iSCSI StorSimple, zakończyć konfigurację urządzenia, a następnie utworzyć, zainstalować, zainicjować i sformatować woluminy w macierzy wirtualnej StorSimple skonfigurowany jako serwer iSCSI. 

Aby ukończyć procedury opisane w tym miejscu, należy wykonać około 30 minut. Informacje publikowane w tym artykule dotyczą tylko tablic wirtualnych StorSimple.

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji

Przed skonfigurowaniem i skonfigurowaniem macierzy wirtualnej StorSimple upewnij się, że:

* Zainicjowano obsługę macierzy wirtualnej i połączono z nią, zgodnie z opisem w artykule [wdrażanie StorSimple wirtualnej macierzy — inicjowanie macierzy wirtualnej w funkcji Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) lub [wdrażanie wirtualnej macierzy StorSimple — Inicjowanie obsługi macierzy wirtualnej w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi StorSimple Menedżer urządzeń, który został utworzony w celu zarządzania macierzami wirtualnymi StorSimple. Aby uzyskać więcej informacji, zobacz **krok 2. Pobieranie klucza rejestracji usługi w artykule** [wdrażanie StorSimple Virtual Array — przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Jeśli jest to druga lub kolejna tablica wirtualna, która jest rejestrowana w istniejącej usłudze StorSimple Menedżer urządzeń, należy mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany, gdy pierwsze urządzenie zostało pomyślnie zarejestrowane w tej usłudze. Jeśli ten klucz został utracony, zobacz **Pobieranie klucza szyfrowania danych usługi** w programie [Korzystanie z interfejsu użytkownika sieci Web w celu administrowania wirtualną tablicą StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Konfiguracja krok po kroku

Wykonaj następujące instrukcje krok po kroku, aby skonfigurować i skonfigurować macierz wirtualną StorSimple:

* [Krok 1. ukończenie instalacji lokalnego interfejsu użytkownika sieci Web i zarejestrowanie urządzenia](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2. ukończenie wymaganej konfiguracji urządzenia
* [Krok 3. Dodawanie woluminu](#step-3-add-a-volume)
* [Krok 4. Instalowanie, inicjowanie i formatowanie woluminu](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1. ukończenie instalacji lokalnego interfejsu użytkownika sieci Web i zarejestrowanie urządzenia

#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby ukończyć instalację i zarejestrować urządzenie

1. Otwórz okno przeglądarki. Aby nawiązać połączenie z typem interfejsu użytkownika sieci Web:
   
    `https://<ip-address of network interface>`
   
    Użyj adresu URL połączenia zanotowanego w poprzednim kroku. Zostanie wyświetlony komunikat o błędzie z informacją, że wystąpił problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj na tej stronie sieci Web**.
   
    ![Błąd certyfikatu zabezpieczeń](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia zmienione w kroku 3: uruchamianie urządzenia wirtualnego w [Wdróż StorSimple Virtual Array — Inicjowanie obsługi urządzenia wirtualnego w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [wdrażanie StorSimple wirtualnej macierzy — Inicjowanie obsługi urządzenia wirtualnego w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Strona logowania](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Nastąpi przekierowanie do strony **głównej** . Ta strona zawiera opis różnych ustawień wymaganych do skonfigurowania i zarejestrowania urządzenia wirtualnego za pomocą usługi StorSimple Menedżer urządzeń. Należy pamiętać, że **Ustawienia sieci**, **Ustawienia serwera proxy sieci Web**i **Ustawienia czasu** są opcjonalne. Jedyne wymagane ustawienia to **Ustawienia urządzenia** i **Ustawienia chmury**.
   
    ![Strona główna](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na stronie **Ustawienia sieci** w obszarze **interfejsy sieciowe**zostaną automatycznie skonfigurowane dane 0. Każdy interfejs sieciowy jest domyślnie ustawiany do automatycznego uzyskiwania adresu IP (DHCP). W związku z tym adres IP, podsieć i Brama zostaną automatycznie przypisane (dla protokołów IPv4 i IPv6).
   
    Planując wdrożenie urządzenia jako serwera iSCSI (w celu udostępnienia magazynu blokowego), zalecamy wyłączenie opcji **Pobierz adres IP automatycznie** i skonfigurowanie statycznych adresów IP.
   
    ![Strona Ustawienia sieci](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Jeśli podczas aprowizacji urządzenia dodano więcej niż jeden interfejs sieciowy, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejs sieciowy można skonfigurować tylko jako IPv4 lub jako IPv4 i IPv6. Tylko konfiguracje protokołu IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są używane, gdy urządzenie próbuje skontaktować się z dostawcami usług magazynu w chmurze lub rozpoznać urządzenie według nazwy, jeśli jest ono skonfigurowane jako serwer plików. Na stronie **Ustawienia sieciowe** w obszarze **serwery DNS**:
   
   1. Podstawowy i pomocniczy serwer DNS zostaną automatycznie skonfigurowane. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, możesz określić serwery DNS. Aby zapewnić wysoką dostępność, zalecamy skonfigurowanie podstawowego i pomocniczego serwera DNS.
   2. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i zweryfikowanie ustawień sieci.
6. Na stronie **Ustawienia urządzenia** :
   
   1. Przypisz unikatową **nazwę** do urządzenia. Nazwa może składać się z 1-15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij ikonę **serwera** iSCSI ikona ![ serwera iSCSI ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) dla tworzonego **typu** urządzenia. Serwer iSCSI umożliwi udostępnienie magazynu blokowego.
   3. Określ, czy to urządzenie ma zostać przyłączone do domeny. Jeśli urządzenie jest serwerem iSCSI, przyłączenie do domeny jest opcjonalne. Jeśli zdecydujesz się nie przyłączyć serwera iSCSI do domeny, kliknij przycisk **Zastosuj**, zaczekaj na zastosowanie ustawień, a następnie przejdź do następnego kroku.
      
       Jeśli chcesz przyłączyć urządzenie do domeny. Wprowadź **nazwę domeny**, a następnie kliknij przycisk **Zastosuj**.
      
      > [!NOTE]
      > W przypadku przyłączania serwera iSCSI do domeny należy się upewnić, że tablica wirtualna należy do własnej jednostki organizacyjnej (OU) dla Microsoft Azure Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO).
      > 
      > 
   4. Zostanie wyświetlone okno dialogowe. Wprowadź poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Poświadczenia domeny zostaną zweryfikowane. Jeśli poświadczenia są nieprawidłowe, zostanie wyświetlony komunikat o błędzie.
      
       ![poświadczenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i zweryfikowanie ustawień urządzenia.
7. (Opcjonalnie) skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu.
   
    ![Konfigurowanie serwera proxy sieci Web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na stronie **serwer proxy sieci Web** :
   
   1. Podaj **adres URL serwera proxy sieci Web** w tym formacie: *http: \/ /host-IP adres* lub *FQDN: numer portu*. Należy zauważyć, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelnianie** jako **podstawowa** lub **Brak**.
   3. W przypadku korzystania z uwierzytelniania należy również podać **nazwę użytkownika** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikowanie i zastosowanie skonfigurowanych ustawień serwera proxy sieci Web.
8. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, takie jak strefa czasowa i serwery NTP serwerów głównych i dodatkowych. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
   
    ![Ustawienia czasu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na stronie **Ustawienia czasu** :
   
   1. Z listy rozwijanej wybierz **strefę czasową** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
   2. Określ **podstawowy serwer NTP** dla urządzenia lub Zaakceptuj wartość domyślną Time.Windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
   3. Opcjonalnie można określić **pomocniczy serwer NTP** dla urządzenia.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikowanie i zastosowanie skonfigurowanych ustawień czasu.
9. Skonfiguruj ustawienia chmury dla urządzenia. W tym kroku zakończysz konfigurację urządzenia lokalnego, a następnie zarejestrujesz urządzenie w usłudze StorSimple Menedżer urządzeń.
   
   1. Wprowadź **klucz rejestracji usługi** uzyskany w **kroku 2. Pobieranie klucza rejestracji usługi** w temacie [wdrażanie StorSimple Virtual Array — przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Jeśli nie jest to pierwsze urządzenie, które jest zarejestrowana w ramach tej usługi, musisz podać **klucz szyfrowania danych usługi**. Ten klucz jest wymagany w przypadku klucza rejestracji usługi w celu zarejestrowania dodatkowych urządzeń w usłudze StorSimple Menedżer urządzeń. Aby uzyskać więcej informacji, zobacz [Pobieranie klucza szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) w lokalnym interfejsie użytkownika sieci Web.
   3. Kliknij pozycję **Zarejestruj**. Spowoduje to ponowne uruchomienie urządzenia. Przed pomyślnym zarejestrowaniem urządzenia może być konieczne odczekanie przez 2-3 minut. Po ponownym uruchomieniu urządzenia nastąpi przekierowanie do strony logowania.
      
      ![Zarejestruj urządzenie](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Wróć do witryny Azure Portal.
11. Przejdź do bloku **urządzenia** usługi. Jeśli masz dużo zasobów, kliknij pozycję **wszystkie zasoby**, kliknij nazwę usługi (Wyszukaj ją w razie potrzeby), a następnie kliknij pozycję **urządzenia**.
12. W bloku **urządzenia** Sprawdź, czy urządzenie pomyślnie nawiązał połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Gotowe do skonfigurowania**.
    
    ![Wdróż urządzenie](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2. Konfigurowanie urządzenia jako serwera iSCSI

Wykonaj następujące kroki w Azure Portal, aby zakończyć wymaganą konfigurację urządzenia.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Aby skonfigurować urządzenie jako serwer iSCSI

1. Przejdź do usługi StorSimple Menedżer urządzeń, a następnie przejdź do pozycji **zarządzanie > urządzeń**. W bloku **urządzenia** wybierz właśnie utworzone urządzenie. To urządzenie będzie widoczne jako **gotowe do skonfigurowania**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kliknij urządzenie i zostanie wyświetlony komunikat transparent wskazujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kliknij pozycję **Konfiguruj** na pasku poleceń urządzenia. Spowoduje to otwarcie bloku **Konfiguracja** . W bloku **Konfigurowanie** wykonaj następujące czynności:
   
   * Nazwa serwera iSCSI jest automatycznie wypełniana.
   * Upewnij się, że szyfrowanie magazynu w chmurze jest ustawione na **włączone**. Dzięki temu dane wysyłane z urządzenia do chmury są szyfrowane.
   * Określ klucz szyfrowania 32-znakowego i zarejestruj go w aplikacji do zarządzania kluczami do użytku w przyszłości.
   * Wybierz konto magazynu, które ma być używane z Twoim urządzeniem. W tej subskrypcji możesz wybrać istniejące konto magazynu lub kliknąć przycisk **Dodaj** , aby wybrać konto z innej subskrypcji.
     
     ![Konfigurowanie urządzenia jako serwera iSCSI 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kliknij pozycję **Konfiguruj** , aby zakończyć konfigurowanie serwera iSCSI.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Zostanie wyświetlone powiadomienie o tym, że tworzenie serwera iSCSI jest w toku. Po pomyślnym utworzeniu serwera iSCSI zostanie zaktualizowany blok **urządzenia** , a odpowiadający mu stan urządzenia jest w **trybie online**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3. Dodawanie woluminu

1. W bloku **urządzenia** wybierz urządzenie, które zostało właśnie skonfigurowane jako serwer iSCSI. Kliknij przycisk **...** (kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Dodaj wolumin**. Możesz również kliknąć pozycję **+ Dodaj wolumin** na pasku poleceń. Spowoduje to otwarcie bloku **Dodawanie woluminu** .
   
    ![Dodaj wolumin](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. W bloku **Dodaj wolumin** wykonaj następujące czynności:
   
   * W polu **Nazwa woluminu** wprowadź unikatową nazwę woluminu. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
   * Z listy rozwijanej **Typ** wybierz, czy chcesz utworzyć wolumin **przypięty** **warstwowy** lub lokalnie. W przypadku obciążeń wymagających lokalnych gwarancji, małych opóźnień i większej wydajności Wybierz **wolumin** **przypięty lokalnie** . Dla wszystkich innych danych wybierz opcję **wolumin** **warstwowy** .
   * W polu **pojemność** Określ rozmiar woluminu. Wolumin warstwowy musi zawierać się w przedziale od 500 GB do 5 TB, a wolumin przypięty lokalnie musi mieć wartość z zakresu od 50 GB do 500 GB.
     
     Wolumin przypięty lokalnie jest elastycznie zainicjowany i zapewnia, że dane podstawowe w woluminie pozostają na urządzeniu i nie zostaną przelane do chmury.
     
     Wolumin warstwowy z drugiej strony jest alokowany elastycznie. Podczas tworzenia woluminu warstwowego zostanie zainicjowana około 10% miejsca w warstwie lokalnej i zostanie zainicjowana 90% miejsca w chmurze. Na przykład jeśli Zainicjowano obsługę woluminu o pojemności 1 TB, 100 GB będzie znajdować się w miejscu lokalnym, a w chmurze zostanie użyta 900 GB. To z kolei oznacza, że w przypadku braku całego lokalnego miejsca na urządzeniu nie można zainicjować obsługi administracyjnej udziałów warstwowych (ponieważ 10% nie będzie dostępny).
     
     ![Dodaj wolumin 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kliknij pozycję **połączone hosty**, wybierz rekord kontroli dostępu (ACR) odpowiadający inicjatorowi iSCSI, który ma zostać połączony z tym woluminem, a następnie kliknij pozycję **Wybierz**. <br><br> 
3. Aby dodać nowy połączony host, kliknij przycisk **Dodaj nowy**, wprowadź nazwę hosta i jego kwalifikowaną nazwę iSCSI (IQN), a następnie kliknij przycisk **Dodaj**. Jeśli nie masz nazwy IQN, przejdź do [załącznika A: pobieranie nazwy IQN hosta z systemem Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Dodaj wolumin 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po zakończeniu konfigurowania woluminu kliknij przycisk **OK**. Zostanie utworzony wolumin z określonymi ustawieniami i zostanie wyświetlone powiadomienie. Domyślnie monitorowanie i wykonywanie kopii zapasowych będzie włączone dla woluminu.
   
     ![Dodaj wolumin 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Aby upewnić się, że wolumin został pomyślnie utworzony, przejdź do bloku **woluminy** . Powinien zostać wyświetlony wymieniony wolumin.
   
   ![Dodaj wolumin 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4. Instalowanie, inicjowanie i formatowanie woluminu

Wykonaj następujące kroki, aby zainstalować, zainicjować i sformatować woluminy StorSimple na hoście z systemem Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Aby zainstalować, zainicjować i sformatować wolumin

1. Otwórz aplikację **inicjatora iSCSI** na odpowiednim serwerze.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Odnajdowanie** kliknij pozycję **Odnajdź portal**.
   
    ![Odnajdź Portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. W oknie dialogowym **Odnajdowanie portalu obiektu docelowego** podaj adres IP interfejsu sieci iSCSI, a następnie kliknij przycisk **OK**.
   
    ![Adres IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. W oknie **Właściwości inicjatora iSCSI** na karcie **Obiekty docelowe** zlokalizuj wartości **Wykryte obiekty docelowe**. (Każdy wolumin będzie odnaleziony jako docelowy). Stan urządzenia powinien być wyświetlany jako **nieaktywny**.
   
    ![wykryte cele](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Wybierz urządzenie docelowe, a następnie kliknij przycisk **Połącz**. Po połączeniu urządzenia stan powinien zmienić się na **Połączone**. (Aby uzyskać więcej informacji na temat korzystania z inicjatora iSCSI firmy Microsoft, zobacz [Instalowanie i Konfigurowanie inicjatora iSCSI firmy Microsoft][1].
   
    ![Wybierz urządzenie docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hoście systemu Windows naciśnij klawisz z logo systemu Windows + X, a następnie kliknij pozycję **Uruchom**.
7. W oknie dialogowym **Uruchom** wpisz **Diskmgmt.msc**. Kliknij przycisk **OK**. Zostanie wyświetlone okno **Zarządzanie dyskami**. W prawym okienku zostaną wyświetlone woluminy na hoście.
8. W oknie **Zarządzanie dyskami** zostaną wyświetlone zainstalowane woluminy (tak jak na poniższym rysunku). Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.
   
    ![Zarządzanie dyskami](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kliknij prawym przyciskiem myszy i wybierz opcję **zainicjuj dysk**.
   
    ![Zainicjuj dysk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. W oknie dialogowym Wybierz dyski do zainicjowania, a następnie kliknij przycisk **OK**.
    
    ![Zainicjuj dysk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Zostanie uruchomiony Kreator nowych woluminów prostych. Wybierz rozmiar dysku, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Przypisz literę dysku do woluminu, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Wprowadź parametry do formatowania woluminu. **W systemie Windows Server obsługiwany jest tylko system plików NTFS.** Ustaw rozmiar jednostki alokacji na 64 KB. Podaj etykietę dla danego woluminu. Zaleca się, aby ta nazwa była taka sama jak nazwa woluminu podana w macierzy wirtualnej StorSimple. Kliknij przycisk **Dalej**.
    
    ![Kreator nowego woluminu 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Sprawdź wartości w woluminie, a następnie kliknij przycisk **Zakończ**.
    
    ![Kreator nowego woluminu 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Woluminy będą wyświetlane w **trybie online** na stronie **Zarządzanie dyskami** .
    
    ![woluminy online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać wirtualną tablicą StorSimple](storsimple-ova-web-ui-admin.md)za pomocą lokalnego interfejsu użytkownika sieci Web.

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Dodatek A: pobieranie nazwy IQN hosta z systemem Windows Server

Wykonaj poniższe kroki, aby pobrać kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows, na którym uruchomiono system Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Aby pobrać nazwę IQN hosta z systemem Windows

1. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
   
    ![Właściwości inicjatora iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Zapisz ten ciąg.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



