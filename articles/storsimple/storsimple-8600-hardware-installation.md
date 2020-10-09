---
title: Zainstaluj urządzenie Microsoft Azure StorSimple 8600
description: W tym artykule opisano sposób rozpakowywania, montowania w stojaku i kabla urządzenia z StorSimple 8600 przed wdrożeniem i skonfigurowaniem oprogramowania.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871817"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Rozpakowywanie, montowanie w stojaku i Podłączanie kabli do urządzenia z systemem StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple 8600 to urządzenie dwuobudówowe, które składa się z podstawowej i EBOD obudowy. W tym samouczku wyjaśniono, jak rozpakować sprzęt urządzenia z systemem StorSimple 8600 i podłączyć go do niego przed skonfigurowaniem oprogramowania StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozpakowywanie urządzenia z urządzeniem z StorSimple 8600
Poniższe kroki zawierają szczegółowe instrukcje dotyczące rozpakowywania urządzenia magazynującego z systemem StorSimple 8600. To urządzenie jest dostarczane w dwóch polach — jeden dla obudowy podstawowej i drugi dla obudowy EBOD. Te dwa pola są następnie umieszczane w jednym polu.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Przed rozpakowaniem urządzenia zapoznaj się z poniższymi informacjami.

![](./media/storsimple-safety/IC740879.png)![Ostrzeżenie ikony dużej grubości ikony ostrzeżenia ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

1. Upewnij się, że masz dwie osoby, aby zarządzać wagą urządzenia, jeśli jest ono obsługiwane ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj następujące kroki, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. [Skontaktuj się z pomocą techniczną pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby ocenić, czy urządzenie jest w dobrym stanie.
2. Otwórz pole zewnętrzne, a następnie wypełnij dwa pola odpowiadające podstawowym i EBOD obudów. Teraz można rozpakować obudowy podstawowe i EBOD. Na poniższej ilustracji przedstawiono niepakowany widok jednej z obudów.
   
    ![Rozpakowywanie urządzenia magazynującego](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Niepakowany widok urządzenia magazynującego**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Pole pakowania |
   |   2 |Kable SAS (w akcesoriach i zasobniku kabli) |
   |   3 |Dolna pianka |
   |   4 |Urządzenie |
   |   5 |Górna pianka |
   |   6 |Pole akcesoriów |
3. Po rozpakowaniu dwóch pól upewnij się, że masz:
   
   * 1 obudowa podstawowa (obudowa podstawowa i obudowa EBOD znajdują się w dwóch oddzielnych ramkach)
   * 1 obudowa EBOD
   * 4 przewody zasilające, 2 w każdym polu
   * 2 kable SAS (aby połączyć podstawową obudowę z obudową EBOD)
   * 1 kabel sieci Ethernet typu skrzyżowanie
   * 2 okablowania konsoli szeregowej
   * 1 konwerter szeregowy USB na potrzeby dostępu szeregowego
   * 4 karty/QSFP-SFP + do użycia z interfejsami sieciowymi 10 GbE
   * 2 zestawy instalacji stojaków (4 szyny boczne z montażem sprzętu, 2 każdy dla obudowy podstawowej i obudowy EBOD), 1 w każdym polu
   * Wprowadzenie — dokumentacja
     
     Jeśli nie otrzymano żadnego z wymienionych powyżej elementów, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).  

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8600-device"></a>Stojak — Instalowanie urządzenia z systemem StorSimple 8600
Postępuj zgodnie z poniższymi instrukcjami, aby zainstalować urządzenie magazynujące z systemem StorSimple 8600 w standardowym stojaku 19-calowym z przodu i z tyłu. To urządzenie zawiera dwie obudowy: obudowę podstawową i obudowę EBOD. Oba te elementy muszą być zainstalowane w stojaku.

Instalacja składa się z wielu kroków, z których każda została omówiona w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple muszą być zainstalowane w stojaku w celu zapewnienia odpowiedniej operacji.
> 
> 

### <a name="site-preparation"></a>Przygotowanie witryny
Obudowy muszą być zainstalowane w standardowym stojaku 19-calowym, które ma zarówno wpisy przód, jak i tylne. Aby przygotować się do instalacji stelażowej, należy wykonać poniższą procedurę.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokację do instalacji stojaka
1. Upewnij się, że obudowy podstawowe i EBOD są bezpiecznie przechowywane na płaskiej, stabilnej i poziomej powierzchni roboczej (lub podobnej).
2. Upewnij się, że lokacja, w której zamierzasz skonfigurować, ma moc standardową AC od niezależnego źródła lub z jednostki dystrybucji zasilania w stojaku (PDU) z zasilaczem UPS.
3. Upewnij się, że jedno gniazdo 4U (2 X 2U) jest dostępne w stojaku, w którym zamierzasz zainstalować obudowy.

![](./media/storsimple-safety/IC740879.png)![Ostrzeżenie ikony dużej grubości ikony ostrzeżenia ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **!**

 Upewnij się, że masz dwie osoby, które mają być dostępne do zarządzania wagą, jeśli ręcznie obsługujesz konfigurację urządzenia. W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów).

### <a name="rack-prerequisites"></a>Wymagania wstępne dotyczące stojaka
Obudowy są przeznaczone do instalacji w standardowym pliku w języku 19 cala z:

* Minimalna głębokość 27,84 cala z wpisu stojaka do opublikowania
* Maksymalna waga 32 kg dla urządzenia
* Maksymalne ciśnienie z 5 Pascalów (miernik wody 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Montaż zestawu szynowego w stojaku
Zestaw szyn montażowych zostanie udostępniony do użycia z 19-calowym szafą w stojaku. Szyny zostały przetestowane w celu obsłużenia maksymalnej wagi obudowy. Te szyny umożliwiają również instalację wielu obudów bez utraty miejsca w stojaku. Najpierw zainstaluj obudowę EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Aby zainstalować obudowę EBOD na szynach
1. Wykonaj ten krok tylko wtedy, gdy na urządzeniu nie zainstalowano wewnętrznych szyn. Zazwyczaj szyny wewnętrzne są instalowane w fabryce. Jeśli szyny nie są zainstalowane, a następnie zainstaluje slajdy z lewej szyną i z prawej strony szyny po bokach obudowy obudowy. Dołączą one przy użyciu sześciu wkrętów metryk na każdej stronie. Aby pomóc w orientacji, slajdy szyny są oznaczone jako **LH – Front** i **RH – Front**, a koniec, który jest umieszczany w tylnej części obudowy, ma zbieżny koniec.
   
    ![Dołączanie slajdów szyny do obudowy obudowy](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Dołączanie slajdów szyny do boków obudowy**
   
   | Etykieta | Opis |
   | --- | --- |
   |  1 |M 3x4 — wkręty z przyciskiem |
   |  2 |Slajdy obudowy |
2. Dołącz lewe szyny i zespoły kolejek z prawej strony do szafy stelażowej w pionie. Nawiasy są oznaczone jako **LH**, **RH**i **tej samej strony** , aby poprowadzić Cię przez właściwą orientację.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Zwiększ poziom szyny, aby dopasować się do wpisów w stojaku i wstawić numery PIN do elementów członkowskich publikowania w stojaku do przodu i do tyłu. Upewnij się, że zestaw szyny jest poziom.
4. Zabezpiecz zestaw szyny do pionowych elementów członkowskich stojaka, używając dwóch z dostarczonych wartości. Użyj jednego gwintu na wierzchu i po jednym z tyłu.
5. Powtórz te kroki dla drugiego zestawu szyn.
   
     ![Dołączanie slajdów szyny do szafy stelażowej](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie zestawów szyn do stojaka**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Zawężanie złącza |
   |   2 |Otwór na stojaku w stelażu |
   |   3 |Numery PIN z lewej strony |
   |   4 |Zawężanie złącza |
   |   5 |Numery PIN z lewej strony do przodu |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Instalowanie obudowy EBOD w stojaku
Przy użyciu szyn stojaków, które zostały właśnie zainstalowane, wykonaj następujące kroki, aby zainstalować obudowę EBOD w stojaku.

#### <a name="to-mount-the-ebod-enclosure"></a>Aby zainstalować obudowę EBOD
1. Przy użyciu Asystenta Unieś obudowę i Wyrównaj ją do szyn stojaków.
2. Starannie wkładaj obudowę do szyn, a następnie Wypchnij ją całkowicie do szafy stelażowej.
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie obudowy w stojaku**
3. Usuń lewe i prawe czołowe kołnierze, pobierając bezpłatnie wielkie litery. Naciśnięcia kołnierza po prostu Przyciągnij na kołnierze.
4. Zabezpiecz obudowę w stojaku, instalując jeden z Phillipsych w każdym kołnierzu, w lewo i w prawo.
5. Zainstaluj naciski na siebie, naciskając je do położenia i przyciągania do miejsca.
   
     ![Instalowanie Cap kołnierza](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie Cap kołnierza**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Złączenie mocujące obudowy |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Instalowanie obudowy podstawowej w stojaku
Po zakończeniu instalowania obudowy EBOD konieczne będzie zainstalowanie podstawowej obudowy, wykonując te same kroki.

> [!NOTE]
> * Istnieje możliwość, że w stojaku między obudową podstawową a obudową EBOD istnieje kilka pustych gniazd.
> * Użyj dostarczonego kabla SAS 2 mln, aby połączyć podstawową obudowę z obudową EBOD.
> * Brak ograniczeń względem względnego położenia jednostki głównej do jednostki EBOD. W związku z tym obudowa podstawowa może zostać umieszczona w górnym gnieździe i w EBOD poniżej — lub odwrotnie.
> 
> 

Następnym krokiem jest podłączenie urządzenia do sieci i dostępu szeregowego.

## <a name="cable-your-storsimple-8600-device"></a>Podłączanie kabli do urządzenia StorSimple 8600
Poniższe procedury wyjaśniają, jak podłączyć urządzenie z StorSimple 8600 do połączeń z urządzeniami, sieciami i połączeniami seryjnymi.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem nawiązać połączenia z urządzeniem należy:

* Obudowa podstawowa i obudowa EBOD, całkowicie rozpakowane
* 4 kable zasilające (2 każdy dla obudowy podstawowej i EBOD) dołączonego do urządzenia
* 2 kable SAS dostarczone z urządzeniem w celu połączenia obudowy EBOD z obudową podstawową
* Dostęp do 2 jednostek dystrybucji (zalecane)
* Kable sieciowe
* Dostarczone kable szeregowe
* Konwerter serial-USB z odpowiednim sterownikiem zainstalowanym na komputerze (w razie potrzeby)
* Dostarczone karty 4 QSFP-SFP + do użycia z interfejsami sieciowymi 10 GbE
* [Sprzęt obsługiwany w przypadku interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS i okablowanie
Urządzenie ma zarówno obudowę podstawowy, jak i obudowę EBOD. Wymaga to okablowania połączonego z łącznością SAS (Serial Attached SCSI) i mocą.

Konfigurując to urządzenie po raz pierwszy, najpierw wykonaj kroki dla okablowania SAS, a następnie wykonaj kroki dla okablowania.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieciowe
Urządzenie jest w konfiguracji aktywnego stanu gotowości: w dowolnym momencie jeden moduł kontrolera jest aktywny i przetwarza wszystkie operacje na dyskach i sieciach, gdy drugi moduł kontrolera jest w stanie wstrzymania. Jeśli wystąpi awaria kontrolera, kontroler gotowości natychmiast aktywuje i kontynuuje wszystkie operacje na dyskach i w sieci.

Aby można było obsługiwać ten nadmiarowy kontroler przełączania do trybu failover, należy podłączyć sieć urządzenia, jak pokazano w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Do kabla dla połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: 4 1 GB/s i 2 10 GB/s. Zapoznaj się z poniższą ilustracją, aby zidentyfikować porty danych na urządzeniu.
   
     ![Płyta montażowa urządzeń z 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tył urządzenia z informacjami o portach danych**
   
   | Etykieta | Opis |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE interfejsów sieciowych |
   |   2, 3 |Interfejsy sieciowe 10 GbE |
   |   6 |Porty szeregowe |
2. Na poniższym diagramie przedstawiono okablowanie sieciowe. (Minimalna konfiguracja sieci jest pokazywana przez pełne niebieskie linie. Aby zapewnić wysoką dostępność i wydajność, wymagana jest dodatkowa konfiguracja, która jest wyświetlana przez kropkowane linie.)

![Podłączanie urządzenia w postaci kabla do sieci](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Okablowanie sieci dla urządzenia**

| Etykieta | Opis |
| --- | --- |
| A |Sieć LAN z dostępem do Internetu |
| B |Kontroler 0 |
| C |PCM 0 |
| D |Kontroler 1 |
| E |PCM 1 |
| F |Kontroler EBOD 0 |
| G |Kontroler EBOD 1 |
| H, I |Hosty (na przykład serwery plików) |
| 0-5 |Interfejsy sieciowe |
| 6 |Obudowa podstawowa |
| 7 |Obudowa EBOD |

W przypadku okablowania urządzenia wymagana jest minimalna konfiguracja:

* Co najmniej dwa interfejsy sieciowe połączone na każdym kontrolerze z jednym na potrzeby dostępu do chmury i jeden dla iSCSI. Port DATA 0 jest automatycznie włączany i konfigurowany za pośrednictwem konsoli szeregowej urządzenia. Oprócz danych 0 inny port danych należy również skonfigurować za pośrednictwem klasycznego portalu Azure. W takim przypadku Połącz port DATA 0 z podstawową siecią LAN (sieć z dostępem do Internetu). Inne porty danych mogą być połączone z segmentem sieci SAN/iSCSI (VLAN), w zależności od zamierzonej roli.
* Identyczne interfejsy na każdym kontrolerze podłączonym do tej samej sieci w celu zapewnienia dostępności w przypadku przejścia do trybu failover kontrolera. Na przykład jeśli zdecydujesz się połączyć dane 0 i 3 dla jednego z kontrolerów, musisz połączyć odpowiednie dane 0 i 3 na drugim kontrolerze.

Pamiętaj o wysokiej dostępności i wydajności:

* Jeśli to możliwe, należy skonfigurować parę interfejsów sieciowych na potrzeby dostępu do chmury (1 GbE) i inną parę dla iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, podłącz interfejsy sieciowe z każdego kontrolera do dwóch różnych przełączników, aby zapewnić dostępność przed awarią przełącznika. Na rysunku przedstawiono interfejsy sieciowe 2 10 GbE, dane 2 i dane 3 z każdego kontrolera podłączonego do dwóch różnych przełączników. Aby uzyskać więcej informacji, zapoznaj się z **interfejsami sieciowymi** w [wymaganiach dotyczących wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> W przypadku używania SFP + nadajników z interfejsami sieciowymi 10 GbE Użyj dostarczonych kart QSFP-SFP +. Aby uzyskać więcej informacji, przejdź do [obsługiwanego sprzętu dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portów szeregowych
Aby nawiązać połączenie z portem szeregowym, wykonaj następujące czynności.

#### <a name="to-cable-for-serial-connection"></a>Do kabla dla połączenia szeregowego
1. Urządzenie ma port szeregowy na każdym kontrolerze, który jest identyfikowany przez ikonę klucza. Aby zlokalizować porty szeregowe, zapoznaj się z ilustracją przedstawiającą porty danych z tyłu urządzenia.
2. Zidentyfikuj aktywny kontroler na urządzeniu. Migające niebieskie diody LED wskazują, że kontroler jest aktywny.
3. Użyj dostarczonego kabla szeregowego (jeśli to konieczne, konwertera USB-Serial dla laptopa) i Podłącz konsolę lub komputer (z emulacją terminalu do urządzenia) do portu szeregowego aktywnego kontrolera.
4. Zainstaluj sterowniki serial-USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób:
   
   * 115 200 bodów
   * 8 bitów danych
   * 1 bit stopu
   * Bez parzystości
   * Sterowanie przepływem ustawione na **wartość None**
6. Sprawdź, czy połączenie działa, naciskając klawisz ENTER w konsoli programu. Powinien pojawić się menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie światłami:** Gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pokoju komputerowym z ograniczonym dostępem, należy się upewnić, że połączenia szeregowe z obydwoma kontrolerami są zawsze połączone z przełącznikiem konsoli szeregowej lub podobnym sprzętem. Umożliwia to poza pasmem zdalne sterowanie i obsługę operacji w przypadku przerw w działaniu sieci lub nieoczekiwanych awarii.
> 
> 

Zakończono okablowanie urządzenia w celu uzyskania dostępu do sieci i połączenia szeregowego. Następnym krokiem jest skonfigurowanie oprogramowania na urządzeniu.

## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do [wdrażania i konfigurowania lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

