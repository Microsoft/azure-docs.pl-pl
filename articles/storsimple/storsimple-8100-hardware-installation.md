---
title: Zainstaluj urządzenie Microsoft Azure StorSimple 8100
description: W tym artykule opisano sposób rozpakowywania, montowania w stojaku i kabla urządzenia z StorSimple 8100 przed wdrożeniem i skonfigurowaniem oprogramowania.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84699119"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Rozpakowywanie, montowanie w stojaku i Podłączanie kabli do urządzenia z systemem StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple 8100 to jedno obudowa, czyli urządzenie zainstalowane w stojaku. W tym samouczku wyjaśniono, jak rozpakować sprzęt urządzenia z systemem StorSimple 8100 i podłączyć go do niego przed skonfigurowaniem i wdrożeniem urządzenia StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozpakowywanie urządzenia z urządzeniem z StorSimple 8100
Poniższe kroki zawierają szczegółowe instrukcje dotyczące rozpakowywania urządzenia magazynującego z usługą StorSimple 8100. To urządzenie jest dostarczane w jednym pudełku.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Przed rozpakowaniem urządzenia zapoznaj się z poniższymi informacjami.

![](./media/storsimple-safety/IC740879.png)![Ostrzeżenie ikony dużej grubości ikony ostrzeżenia ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

1. Upewnij się, że masz dwie osoby, aby zarządzać wagą obudowy, jeśli jest ona obsługiwana ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj następujące kroki, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. [Skontaktuj się z pomocą techniczną pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby ocenić, czy urządzenie jest w dobrym stanie.
2. Rozpakuj zawartość pudełka. Na poniższej ilustracji przedstawiono niepakowany widok urządzenia StorSimple.
   
     ![Rozpakowywanie urządzenia magazynującego](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Niepakowany widok urządzenia magazynującego**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Pole pakowania |
   |   2 |Dolna pianka |
   |   3 |Urządzenie |
   |   4 |Górna pianka |
   |   5 |Pole akcesoriów |
3. Po rozpakowaniu upewnij się, że masz:
   
   * 1 pojedyncze urządzenie obudów
   * 2 przewody zasilające
   * 1 kabel sieci Ethernet typu skrzyżowanie
   * 2 okablowania konsoli szeregowej
   * 1 konwerter szeregowy USB na potrzeby dostępu szeregowego
   * 1 Śrubokręt T10
   * 4 karty/QSFP-SFP + do użycia z interfejsami sieciowymi 10 GbE
   * 1 zestaw do montażu w stojaku (dwie szyny boczne z sprzętem montażowym)
   * Dokumentacja Wprowadzenie
     
     Jeśli nie otrzymano żadnego z wymienionych powyżej elementów, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8100-device"></a>Stojak — Instalowanie urządzenia z systemem StorSimple 8100
Postępuj zgodnie z poniższymi instrukcjami, aby zainstalować urządzenie magazynujące z systemem StorSimple 8100 w standardowym stojaku 19-calowym z przodu i z tyłu. Urządzenie z systemem StorSimple 8100 ma jeden podstawowy załącznik.

Instalacja składa się z wielu kroków, z których każda została omówiona w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple muszą być zainstalowane w stojaku w celu zapewnienia odpowiedniej operacji.
> 
> 

### <a name="prepare-the-site"></a>Przygotuj lokację
Urządzenie musi być zainstalowane w standardowym stojaku 19-calowym, które ma zarówno wpisy frontonu, jak i tyłu. Aby przygotować się do instalacji stelażowej, należy wykonać poniższą procedurę.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokację do instalacji stojaka
1. Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej (lub podobnej).
2. Upewnij się, że lokacja, w której zamierzasz skonfigurować, ma moc standardową AC od niezależnego źródła lub z jednostki dystrybucji zasilania w stojaku (PDU) z zasilaczem UPS.
3. Upewnij się, że jedno gniazdo 2U jest dostępne w stojaku, w którym zamierzasz zainstalować urządzenie.

![](./media/storsimple-safety/IC740879.png)![Ostrzeżenie ikony dużej grubości ikony ostrzeżenia ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

Upewnij się, że masz dwie osoby, które mają być dostępne do zarządzania wagą, jeśli ręcznie obsługujesz konfigurację urządzenia. W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów).

### <a name="rack-prerequisites"></a>Wymagania wstępne dotyczące stojaka
Obudowa 8100 została zaprojektowana do instalacji w standardowym szafie montażowym 19 cali z:

* Minimalna głębokość 27,84 cala od wpisu stojaka do opublikowania.
* Maksymalna waga 32 kg dla urządzenia
* Maksymalne ciśnienie powrotu 5 Pascalów (0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Montaż zestawu szynowego w stojaku
Zestaw szyn montażowych jest dostępny do użycia z 19-calowym szafą w stojaku. Szyny zostały przetestowane w celu obsłużenia maksymalnej wagi obudowy. Te szyny umożliwiają również instalację wielu obudów bez utraty miejsca w stojaku.

#### <a name="to-install-the-device-on-the-rails"></a>Aby zainstalować urządzenie na szynach
1. Wykonaj ten krok tylko wtedy, gdy na urządzeniu nie zainstalowano wewnętrznych szyn. Zazwyczaj szyny wewnętrzne są instalowane w fabryce. Jeśli szyny nie są zainstalowane, a następnie zainstaluje slajdy z lewej szyną i z prawej strony szyny po bokach obudowy obudowy. Dołączą one przy użyciu sześciu wkrętów metryk na każdej stronie. Aby pomóc w orientacji, slajdy szyny są oznaczone jako **LH – Front** i **RH – Front**, a koniec, który jest umieszczany w tylnej części obudowy, ma zbieżny koniec.<br/>
   
    ![Dołączanie slajdów szyny do obudowy obudowy](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Dołączanie wewnętrznych slajdów szyny do boków obudowy**
   
    Etykieta | Opis
    ----- | -----------
    1     | M 3x4 — wkręty z przyciskiem
    2     | Slajdy obudowy

2. Dołączaj zewnętrzne i zewnętrzne zespoły kolejek do szafy w stojaku do pionowej składowej szafy. Nawiasy są oznaczone jako **LH**, **RH**i **tej samej strony** , aby przekierować użytkownika do odpowiedniej orientacji.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Zwiększ poziom szyny, aby dopasować się do wpisów w stojaku i wstawić numery PIN do elementów członkowskich publikowania w stojaku do przodu i do tyłu. Upewnij się, że zestaw szyny jest poziom.
4. Aby zabezpieczyć zestaw szyny do pionowych elementów członkowskich stojaka, należy użyć dwóch z dostarczonych wartości. Użyj jednego gwintu na wierzchu i po jednym z tyłu.
5. Powtórz te kroki dla drugiego zestawu szyn.<br/>
   
     ![Dołączanie slajdów szyny do szafy stelażowej](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie zewnętrznych zestawów szyn do stojaka**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Zawężanie złącza |
   |   2 |Otwór na stojaku w stelażu |
   |   3 |Numery PIN z lewej strony szyny |
   |   4 |Zawężanie złącza |
   |   5 |Numery PIN z lewej strony szyny |

### <a name="mounting-the-device-in-the-rack"></a>Instalowanie urządzenia w stojaku
Przy użyciu szyn stojaków, które zostały właśnie zainstalowane, wykonaj następujące kroki, aby zainstalować urządzenie w stojaku.

#### <a name="to-mount-the-device"></a>Aby zainstalować urządzenie
1. Przy użyciu Asystenta Unieś obudowę i Wyrównaj ją do szyn stojaków.
2. Starannie wkładaj urządzenie do szyn, a następnie wypchnij je całkowicie do szafy stelażowej.<br/>
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie urządzenia w stojaku**
3. Usuń lewe i prawe czołowe kołnierze, pobierając bezpłatnie wielkie litery. Naciśnięcia kołnierza po prostu Przyciągnij na kołnierze.
4. Zabezpiecz obudowę w stojaku, instalując jeden z podanych Phillips, po lewej stronie i w prawo.
5. Zainstaluj naciski na siebie, naciskając je do położenia i przyciągania w miejscu.<br/>
   
     ![Instalowanie Cap kołnierza](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie Cap kołnierza**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Złączenie mocujące obudowy |

Następnym krokiem jest podłączenie urządzenia do sieci i dostępu szeregowego.

## <a name="cable-your-storsimple-8100-device"></a>Podłączanie kabli do urządzenia StorSimple 8100
Poniższe procedury wyjaśniają, jak podłączyć urządzenie z StorSimple 8100 do połączeń z urządzeniami, sieciami i połączeniami seryjnymi.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem podłączania kabli urządzenia będą potrzebne następujące elementy:

* Urządzenie magazynujące, całkowicie rozpakowane i zamontowane w stojaku.
* 2 kable zasilające dołączone do urządzenia
* Dostęp do 2 jednostek dystrybucji (zalecane).
* Kable sieciowe
* Dostarczone kable szeregowe
* Konwerter szeregowy USB z odpowiednim sterownikiem zainstalowanym na komputerze (w razie potrzeby)
* Dostarczone karty 4 QSFP-SFP + do użycia z interfejsami sieciowymi 10 GbE
* [Sprzęt obsługiwany w przypadku interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Okablowanie sieciowe
Urządzenie obejmuje nadmiarowe moduły mocy i chłodzenia (PCMs). Aby zapewnić wysoką dostępność, muszą być zainstalowane i połączone z różnymi źródłami zasilanymi przez PCMs.

Wykonaj następujące kroki, aby podłączyć urządzenie do urządzeń.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieciowe
Urządzenie jest konfiguracją aktywnego stanu gotowości: w dowolnym momencie jeden moduł kontrolera jest aktywny i przetwarza wszystkie operacje na dyskach i sieciach, gdy drugi moduł kontrolera jest w stanie wstrzymania. Jeśli kontroler zakończy się niepowodzeniem, kontroler gotowości zostanie aktywowany natychmiast i będzie kontynuował wszystkie operacje na dyskach i w sieci.

Aby można było obsługiwać ten nadmiarowy kontroler przełączania do trybu failover, należy podłączyć sieć urządzenia zgodnie z opisem w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Do kabla dla połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: 4 1 GB/s i 2 10 GB/s. Zidentyfikuj różne porty danych na planie dla urządzenia.
   
    ![Płyta montażowa urządzeń z 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Z tyłu urządzenia pokazującego porty danych**
   
   | Etykieta | Opis |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE interfejsów sieciowych |
   |   2, 3 |Interfejsy sieciowe 10 GbE |
   |   6 |Porty szeregowe |
2. Na poniższym diagramie przedstawiono okablowanie sieciowe. (Minimalna konfiguracja sieci jest pokazywana przez pełne niebieskie linie. Dodatkowa konfiguracja wymagana w celu zapewnienia wysokiej dostępności i wydajności jest pokazywana przez linie kropkowane.)

    ![Podłączanie urządzenia do 2U w sieci](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Okablowanie sieci dla urządzenia**

   |Etykieta | Opis |
   |----- | ----------- |
   | A    | Sieć LAN z dostępem do Internetu |
   | B    | Kontroler 0 |
   | C    | PCM 0 |
   | D    | Kontroler 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Interfejsy sieciowe |



W przypadku okablowania urządzenia wymagana jest minimalna konfiguracja:

* Co najmniej dwa interfejsy sieciowe połączone na każdym kontrolerze z jednym na potrzeby dostępu do chmury i jeden dla iSCSI. Port DATA 0 jest automatycznie włączany i konfigurowany za pośrednictwem konsoli szeregowej urządzenia. Oprócz danych 0 inny port danych należy również skonfigurować za pośrednictwem klasycznego portalu Azure. W takim przypadku Połącz port DATA 0 z podstawową siecią LAN (sieć z dostępem do Internetu). Inne porty danych mogą być połączone z segmentem sieci SAN/iSCSI (VLAN), w zależności od zamierzonej roli.
* Identyczne interfejsy na każdym kontrolerze podłączonym do tej samej sieci w celu zapewnienia dostępności w przypadku przejścia do trybu failover kontrolera. Na przykład jeśli zdecydujesz się połączyć dane 0 i 3 dla jednego z kontrolerów, musisz połączyć odpowiednie dane 0 i 3 na drugim kontrolerze.

Pamiętaj o wysokiej dostępności i wydajności:

* Jeśli to możliwe, należy skonfigurować parę interfejsów sieciowych na potrzeby dostępu do chmury (1 GbE) i inną parę dla iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, podłącz interfejsy sieciowe z każdego kontrolera do dwóch różnych przełączników, aby zapewnić dostępność przed awarią przełącznika. Na rysunku przedstawiono interfejsy sieciowe 2 10 GbE, dane 2 i dane 3 z każdego kontrolera podłączonego do dwóch różnych przełączników.

Aby uzyskać więcej informacji, zapoznaj się z **interfejsami sieciowymi** w [wymaganiach dotyczących wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli używasz urządzeń nadawczych SFP + z interfejsami sieciowymi 10 GbE, Użyj dostarczonych kart QSFP-SFP +. Aby uzyskać więcej informacji, przejdź do [obsługiwanego sprzętu dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portów szeregowych
Aby nawiązać połączenie z portem szeregowym, wykonaj następujące czynności.

#### <a name="to-cable-for-serial-connection"></a>Do kabla dla połączenia szeregowego
1. Urządzenie ma port szeregowy na każdym kontrolerze, który jest identyfikowany przez ikonę klucza. Zapoznaj się z ilustracją w sekcji [okablowanie sieciowe](#network-cabling) , aby znaleźć porty szeregowe na urządzeniu.
2. Zidentyfikuj aktywny kontroler na urządzeniu. Migające niebieskie diody LED wskazują, że kontroler jest aktywny.
3. Użyj dostarczonych kabli szeregowych (jeśli to konieczne, konwertera USB-Serial dla laptopa) i Podłącz konsolę lub komputer (z emulacją terminalu do urządzenia) do portu szeregowego aktywnego kontrolera.
4. Zainstaluj sterowniki serial-USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób: 115 200 bodów, 8 bitów danych, 1 bit stop, bez parzystości i sterowanie przepływem ustawione na wartość Brak.
6. Sprawdź, czy połączenie działa, naciskając klawisz ENTER w konsoli programu. Powinien pojawić się menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie światłami**: w przypadku zainstalowania urządzenia w zdalnym centrum danych lub w pokoju komputerowym z ograniczonym dostępem upewnij się, że połączenia szeregowe z obydwoma kontrolerami są zawsze połączone z przełącznikiem konsoli szeregowej lub podobnym sprzętem. Umożliwia to poza pasmem zdalne sterowanie i obsługę operacji, jeśli występują zakłócenia sieci lub nieoczekiwane awarie.
> 
> 

Urządzenie jest teraz przewodowe w celu zapewnienia mocy, dostępu do sieci i połączenia szeregowego. Następnym krokiem jest skonfigurowanie oprogramowania i wdrożenie urządzenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak wdrożyć i skonfigurować lokalne urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

