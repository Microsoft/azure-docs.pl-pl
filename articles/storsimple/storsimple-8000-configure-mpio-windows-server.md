---
title: Konfigurowanie funkcji MPIO dla urządzenia StorSimple | Microsoft Docs
description: Opisuje sposób konfigurowania wielościeżkowego we/wy (MPIO) dla urządzenia StorSimple podłączonego do hosta z systemem Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: cc88d5b7a458c3666cdb4469d7021917d27115f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514342"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple

W tym samouczku opisano kroki, które należy wykonać, aby zainstalować i używać funkcji wielościeżkowego wejścia/wyjścia (MPIO) na hoście z systemem Windows Server 2012 R2 i połączonym z urządzeniem fizycznym StorSimple. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń fizycznych z serii StorSimple 8000. Funkcja MPIO nie jest obecnie obsługiwana w urządzeniu w chmurze StorSimple.

Wbudowana obsługa funkcji wielościeżkowego we/wy (MPIO) w systemie Windows Server w celu ułatwienia tworzenia konfiguracji sieci iSCSI o wysokiej dostępności i odpornych na błędy. Funkcja MPIO używa nadmiarowych składników ścieżki fizycznej — kart, kabli i przełączników — do tworzenia ścieżek logicznych między serwerem a urządzeniem magazynującym. Jeśli wystąpi awaria składnika, powodując niepowodzenie ścieżki logicznej, logika wielościeżkowa używa ścieżki alternatywnej dla operacji we/wy, dzięki czemu aplikacje mogą nadal uzyskiwać dostęp do swoich danych. Ponadto w zależności od konfiguracji funkcja MPIO może również zwiększyć wydajność przez ponowne zrównoważenie obciążenia we wszystkich tych ścieżkach. Aby uzyskać więcej informacji, zobacz [MPIO — Omówienie](https://technet.microsoft.com/library/cc725907.aspx "Omówienie i funkcje MPIO").

Aby zapewnić wysoką dostępność rozwiązania StorSimple, na urządzeniu StorSimple należy skonfigurować funkcję MPIO. Gdy funkcja MPIO jest zainstalowana na serwerach hosta z systemem Windows Server 2012 R2, serwery mogą wówczas tolerować awarie łącza, sieci lub interfejsu.

## <a name="mpio-configuration-summary"></a>Podsumowanie konfiguracji MPIO

MPIO to opcjonalna funkcja w systemie Windows Server i nie jest instalowana domyślnie. Powinno być instalowane jako funkcja za pomocą Menedżera serwera.

Wykonaj następujące kroki, aby skonfigurować wielościeżkowe wejście/wyjście na urządzeniu StorSimple:

* Krok 1. Instalowanie funkcji MPIO na hoście z systemem Windows Server
* Krok 2. Konfigurowanie funkcji MPIO dla woluminów StorSimple
* Krok 3. Instalowanie woluminów StorSimple na hoście
* Krok 4. Konfigurowanie funkcji MPIO w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

Każdy z powyższych kroków został omówiony w poniższych sekcjach.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1. Instalowanie funkcji MPIO na hoście z systemem Windows Server

Aby zainstalować tę funkcję na hoście z systemem Windows Server, należy wykonać poniższą procedurę.

#### <a name="to-install-mpio-on-the-host"></a>Aby zainstalować funkcję MPIO na hoście

1. Otwórz Menedżer serwera na hoście z systemem Windows Server. Domyślnie Menedżer serwera jest uruchamiany, gdy członek grupy Administratorzy zaloguje się na komputerze z systemem Windows Server 2012 R2 lub Windows Server 2012. Jeśli Menedżer serwera nie jest jeszcze otwarta, kliknij przycisk **Start > Menedżer serwera**.
   
   ![Menedżer serwera](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kliknij **Menedżer serwera > pulpit nawigacyjny > Dodaj role i funkcje**. Spowoduje to uruchomienie kreatora **dodawania ról i funkcji** .
   
   ![Kreator dodawania ról i funkcji 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. W kreatorze **dodawania ról i funkcji** wykonaj następujące czynności:
   
   1. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
   2. Na stronie **Wybieranie typu instalacji** zaakceptuj domyślne ustawienie instalacji opartej **na rolach lub funkcjach** . Kliknij przycisk **Dalej**.
   
       ![Kreator dodawania ról i funkcji 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na stronie **Wybieranie serwera docelowego** wybierz **opcję Wybierz serwer z puli serwerów**. Serwer hosta powinien być wykrywany automatycznie. Kliknij przycisk **Dalej**.
   4. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
   5. Na stronie **Wybieranie funkcji** wybierz pozycję **wielościeżkowe we/wy**, a następnie kliknij przycisk **dalej**.
   
       ![Kreator dodawania ról i funkcji 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na stronie **Potwierdzanie opcji instalacji** Potwierdź wybór, a następnie w **razie potrzeby wybierz opcję ponownie uruchom serwer docelowy**, jak pokazano poniżej. Kliknij przycisk **Zainstaluj**.
   
       ![Kreator dodawania ról i funkcji 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Otrzymasz powiadomienie po zakończeniu instalacji. Kliknij przycisk **Zamknij**, aby zamknąć kreatora.
   
       ![Kreator dodawania ról i funkcji 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2. Konfigurowanie funkcji MPIO dla woluminów StorSimple

Funkcja MPIO musi być skonfigurowana do identyfikowania woluminów StorSimple. Aby skonfigurować wielościeżkowe wejście/wyjście do rozpoznawania woluminów StorSimple, wykonaj następujące czynności.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Aby skonfigurować wielościeżkowe wejście/wyjście dla woluminów StorSimple

1. Otwórz **konfigurację MPIO**. Kliknij **Menedżer serwera > pulpit nawigacyjny > narzędzia > MPIO**.
2. W oknie dialogowym **Właściwości MPIO** wybierz kartę **odnajdywanie** wielościeżkowe.
3. Wybierz pozycję **Dodaj obsługę urządzeń iSCSI**, a następnie kliknij przycisk **Dodaj**.  
   ![Właściwości MPIO odnajdywania wiele ścieżek](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Po wyświetleniu monitu uruchom ponownie serwer.
5. W oknie dialogowym **Właściwości MPIO** kliknij kartę **urządzenia MPIO** . kliknij przycisk **Dodaj**.
    </br>![Właściwości MPIO — urządzenia z funkcją MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. W oknie dialogowym **Dodawanie obsługi MPIO** , w obszarze **Identyfikator sprzętu urządzenia**wprowadź numer seryjny urządzenia. Aby uzyskać numer seryjny urządzenia, uzyskaj dostęp do usługi StorSimple Menedżer urządzeń. Przejdź do **urządzeń > pulpicie nawigacyjnym**. Numer seryjny urządzenia jest wyświetlany **w okienku po** prawej stronie pulpitu nawigacyjnego urządzenia.
    </br>
    ![Dodawanie obsługi MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Po wyświetleniu monitu uruchom ponownie serwer.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3. Instalowanie woluminów StorSimple na hoście

Po skonfigurowaniu funkcji MPIO w systemie Windows Server woluminy utworzone na urządzeniu StorSimple można zainstalować, a następnie skorzystać z funkcji MPIO w celu zapewnienia nadmiarowości. Aby zainstalować wolumin, wykonaj następujące czynności.

#### <a name="to-mount-volumes-on-the-host"></a>Aby zainstalować woluminy na hoście

1. Otwórz okno **Właściwości inicjatora iSCSI** na hoście systemu Windows Server. Kliknij **Menedżer serwera > pulpit nawigacyjny > narzędzia > inicjatorze iSCSI**.
2. W oknie dialogowym **Właściwości inicjatora iSCSI** kliknij kartę odnajdywanie, a następnie kliknij pozycję **odkryj Portal docelowy**.
3. W oknie dialogowym **odnajdywanie portalu docelowego** wykonaj następujące czynności:
   
   1. Wprowadź adres IP portu danych urządzenia StorSimple (na przykład wprowadź dane 0).
   2. Kliknij przycisk **OK** , aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI** .
     
      > [!IMPORTANT]
      > **Jeśli używasz sieci prywatnej do połączeń iSCSI, wprowadź adres IP portu danych połączonego z siecią prywatną.**
    
4. Powtórz kroki 2-3 dla drugiego interfejsu sieciowego (na przykład dane 1) na urządzeniu. Należy pamiętać, że te interfejsy powinny być włączone dla protokołu iSCSI. Aby uzyskać więcej informacji, zobacz [Modyfikowanie interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Wybierz kartę **obiekty docelowe** w oknie dialogowym **Właściwości inicjatora iSCSI** . Powinna zostać wyświetlona nazwa IQN urządzenia StorSimple Target w obszarze **odnalezione cele**.

   ![Karta obiektów docelowych właściwości inicjatora iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknij przycisk **Połącz** , aby nawiązać sesję iSCSI z urządzeniem StorSimple. Zostanie wyświetlone okno dialogowe **łączenie z miejscem docelowym** .
7. W oknie dialogowym **łączenie z celem** zaznacz pole wyboru **Włącz obsługę wielu ścieżek** . Kliknij pozycję **Zaawansowane**.
8. W oknie dialogowym **Ustawienia zaawansowane** wykonaj następujące czynności:
   
   1. Z listy rozwijanej **karta lokalna** wybierz **inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej adres **IP inicjatora** wybierz adres IP hosta.
   3. Z listy rozwijanej adres IP **portalu docelowego** wybierz pozycję adres IP interfejsu urządzenia.
   4. Kliknij przycisk **OK** , aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI** .
9. Kliknij pozycję **Właściwości**. W oknie dialogowym **Właściwości** kliknij przycisk **Dodaj sesję**.
10. W oknie dialogowym **łączenie z celem** zaznacz pole wyboru **Włącz obsługę wielu ścieżek** . Kliknij pozycję **Zaawansowane**.
11. W oknie dialogowym **Ustawienia zaawansowane** :

    1. Z listy rozwijanej **karta lokalna** wybierz inicjator iSCSI firmy Microsoft.
    2. Na liście rozwijanej adres **IP inicjatora** wybierz adres IP odpowiadający hostowi. W takim przypadku łączysz dwa interfejsy sieciowe na urządzeniu z pojedynczym interfejsem sieciowym na hoście. W związku z tym ten interfejs jest taki sam jak w przypadku pierwszej sesji.
    3. Na liście rozwijanej **adres IP portalu docelowego** wybierz adres IP dla drugiego interfejsu danych włączonego na urządzeniu.
    4. Kliknij przycisk **OK** , aby powrócić do okna dialogowego Właściwości inicjatora iSCSI. Dodano drugą sesję do celu.
12. Otwórz **przystawkę Zarządzanie komputerem** , przechodząc do **Menedżer serwera > pulpitu nawigacyjnego > Zarządzanie komputerem**. W lewym okienku kliknij pozycję **magazyn > Zarządzanie dyskami**. Wolumin utworzony na urządzeniu StorSimple widoczny dla tego hosta jest wyświetlany w obszarze **Zarządzanie dyskami** jako nowe dyski.
13. Zainicjuj dysk i Utwórz nowy wolumin. W trakcie procesu formatowania wybierz rozmiar bloku 64 KB.
    
    ![Zarządzanie dyskami](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. W obszarze **Zarządzanie dyskami**kliknij prawym przyciskiem myszy **dysk** , a następnie wybierz polecenie **Właściwości**.
15. W oknie dialogowym właściwości StorSimple modelu # # # # **Path** kliknij kartę **MPIO** .
    
    ![DeviceProp StorSimple 8100 dla wielu ścieżek dysków.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. W sekcji **Nazwa DSM** kliknij **szczegóły** i sprawdź, czy parametry są ustawione na domyślne parametry. Parametry domyślne to:
    
    * Okres weryfikacji ścieżki = 30
    * Liczba ponownych prób = 3
    * Okres usuwania PDO = 20
    * Interwał ponawiania prób = 1
    * Sprawdzanie ścieżki włączone = niezaznaczone.

> [!NOTE]
> **Nie należy modyfikować parametrów domyślnych.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4. Konfigurowanie funkcji MPIO w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

W przypadku wysokiej dostępności opartej na wielu ścieżkach i równoważenia obciążenia należy ręcznie dodać wiele sesji, aby zadeklarować różne dostępne ścieżki. Na przykład jeśli host ma dwa interfejsy połączone z siecią iSCSI, a urządzenie ma dwa interfejsy połączone z siecią iSCSI, potrzebne są cztery sesje skonfigurowane z prawidłowymi permutacjami ścieżki (tylko dwie sesje będą wymagane, jeśli każdy interfejs danych i interfejs hosta znajdują się w innej podsieci IP i nie ma routingu).

**Zalecamy posiadanie co najmniej 8 aktywnych sesji równoległych między urządzeniem i hostem aplikacji.** Można to osiągnąć przez włączenie 4 interfejsów sieciowych w systemie Windows Server. Używaj fizycznych interfejsów sieciowych lub interfejsów wirtualnych za pośrednictwem technologii wirtualizacji sieci na poziomie sprzętu lub systemu operacyjnego na hoście z systemem Windows Server. W przypadku dwóch interfejsów sieciowych na urządzeniu ta konfiguracja spowodowałaby 8 aktywnych sesji. Ta konfiguracja ułatwia optymalizację przepływności urządzenia i chmury.

> [!IMPORTANT]
> **Zalecamy, aby nie mieszać 1 GbE ani interfejsów sieciowych 10 GbE. Jeśli używasz dwóch interfejsów sieciowych, oba interfejsy powinny być tego samego typu.**

Poniższa procedura opisuje sposób dodawania sesji, gdy urządzenie StorSimple z dwoma interfejsami sieciowymi jest połączone z hostem z dwoma interfejsami sieciowymi. Zapewnia to tylko 4 sesje. Tej samej procedury należy użyć z urządzeniem StorSimple z dwoma interfejsami sieciowymi podłączonymi do hosta z czterema interfejsami sieciowymi. Należy skonfigurować 8 zamiast 4 sesji opisanych tutaj.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Aby skonfigurować wielościeżkowe wejście/wyjście w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

1. Wykonaj odnajdywanie elementu docelowego: w oknie dialogowym **Właściwości inicjatora iSCSI** na karcie **odnajdywanie** kliknij pozycję **Odnajdź Portal**.
2. W oknie dialogowym **łączenie z celem** wprowadź adres IP jednego z interfejsów sieciowych urządzeń.
3. Kliknij przycisk **OK** , aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI** .
4. W oknie dialogowym **Właściwości inicjatora iSCSI** wybierz kartę **obiekty docelowe** , zaznacz odnaleziony element docelowy, a następnie kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe **łączenie z miejscem docelowym** .
5. W oknie dialogowym **łączenie z celem** :
   
   1. Pozostaw domyślne ustawienie wybrane dla opcji **Dodaj to połączenie** do listy ulubionych elementów docelowych. Powoduje to, że urządzenie automatycznie spróbuje ponownie uruchomić połączenie za każdym razem, gdy ten komputer zostanie uruchomiony ponownie.
   2. Zaznacz pole wyboru **Włącz obsługę wielu ścieżek** .
   3. Kliknij pozycję **Zaawansowane**.
6. W oknie dialogowym **Ustawienia zaawansowane** :
   
   1. Z listy rozwijanej **karta lokalna** wybierz **inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej adres **IP inicjatora** wybierz adres IP odpowiadający pierwszemu interfejsowi na hoście (interfejs iSCSI).
   3. Na liście rozwijanej **adres IP portalu docelowego** wybierz adres IP dla pierwszego interfejsu danych włączonego na urządzeniu.
   4. Kliknij przycisk **OK** , aby powrócić do okna dialogowego Właściwości inicjatora iSCSI.
7. Kliknij pozycję **Właściwości**, a następnie w oknie dialogowym **Właściwości** kliknij przycisk **Dodaj sesję**.
8. W oknie dialogowym **łączenie z celem** zaznacz pole wyboru **Włącz obsługę wielu ścieżek** , a następnie kliknij przycisk **Zaawansowane**.
9. W oknie dialogowym **Ustawienia zaawansowane** :
   
   1. Z listy rozwijanej **karta lokalna** wybierz **inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej adres **IP inicjatora** wybierz adres IP odpowiadający drugiemu interfejsowi iSCSI na hoście.
   3. Na liście rozwijanej **adres IP portalu docelowego** wybierz adres IP dla drugiego interfejsu danych włączonego na urządzeniu.
   4. Kliknij przycisk **OK** , aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI** . Dodano już drugą sesję do obiektu docelowego.
10. Powtórz kroki 8-10, aby dodać kolejne sesje (ścieżki) do obiektu docelowego. W przypadku dwóch interfejsów na hoście i dwóch na urządzeniu można dodać łącznie cztery sesje.
11. Po dodaniu żądanych sesji (ścieżek) w oknie dialogowym **Właściwości inicjatora iSCSI** wybierz obiekt docelowy, a następnie kliknij przycisk **Właściwości**. Na karcie Sesje okna dialogowego **Właściwości** , należy zwrócić uwagę na cztery identyfikatory sesji, które odpowiadają możliwym permutacjom ścieżki. Aby anulować sesję, zaznacz pole wyboru obok identyfikatora sesji, a następnie kliknij przycisk **Rozłącz**.
12. Aby wyświetlić urządzenia prezentowane w ramach sesji, wybierz kartę **urządzenia** . Aby skonfigurować zasady MPIO dla wybranego urządzenia, kliknij pozycję **MPIO**. Zostanie wyświetlone okno dialogowe **szczegóły urządzenia** . Na karcie **MPIO** możesz wybrać odpowiednie ustawienia **zasad równoważenia obciążenia** . Można również wyświetlić typ ścieżki **aktywnego** lub **wstrzymania** .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [korzystaniu z usługi StorSimple Menedżer urządzeń, aby zmodyfikować konfigurację urządzenia StorSimple](storsimple-8000-modify-device-config.md).

