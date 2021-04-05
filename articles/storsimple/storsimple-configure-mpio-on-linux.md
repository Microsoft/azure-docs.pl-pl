---
title: Konfigurowanie funkcji MPIO na hoście StorSimple systemu Linux
description: Informacje o krokach wymaganych do skonfigurowania wielościeżkowego wejścia/wyjścia (MPIO) na serwerze hosta z systemem StorSimple Linux (CentOS 6,6).
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 2b7ddf6423db4c471ee2065635f4e3e89f7eb7b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745737"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurowanie funkcji MPIO na hoście StorSimple z systemem CentOS
W tym artykule opisano kroki wymagane do skonfigurowania wielościeżkowego wejścia/wyjścia (MPIO) na serwerze hosta z systemem CentOS 6,6. Serwer hosta jest połączony z urządzeniem Microsoft Azure StorSimple, aby zapewnić wysoką dostępność za pośrednictwem inicjatorów iSCSI. Szczegółowo opisano automatyczne odnajdowanie urządzeń wielościeżkowych i konkretnej konfiguracji tylko dla woluminów StorSimple.

Ta procedura ma zastosowanie do wszystkich modeli urządzeń z serii StorSimple 8000.

> [!NOTE]
> Nie można użyć tej procedury dla urządzenia w chmurze StorSimple. Aby uzyskać więcej informacji, zobacz Jak skonfigurować serwery hosta dla urządzenia w chmurze.

## <a name="about-multipathing"></a>Informacje o wielu ścieżkach
Funkcja wielościeżkowego umożliwia skonfigurowanie wielu ścieżek we/wy między serwerem hosta a urządzeniem magazynującym. Te ścieżki we/wy są fizycznymi połączeniami sieci SAN, które mogą obejmować oddzielne kable, przełączniki, interfejsy sieciowe i kontrolery. Funkcja wielościeżkowego agreguje ścieżki we/wy, aby skonfigurować nowe urządzenie skojarzone ze wszystkimi zagregowanymi ścieżkami.

Przeznaczenie wielościeżkowe:

* **Wysoka dostępność**: zapewnia alternatywną ścieżkę, jeśli jakikolwiek element ścieżki we/wy (na przykład kabel, przełącznik, interfejs sieciowy lub kontroler) zakończy się niepowodzeniem.
* **Równoważenie obciążenia**: w zależności od konfiguracji urządzenia magazynującego można zwiększyć wydajność dzięki wykrywaniu obciążeń we wszystkich ścieżkach we/wy i dynamicznie ponownie zrównoważeniu tych obciążeń.

### <a name="about-multipathing-components"></a>Informacje o składnikach wielościeżkowych
Wielościeżkowe w systemie Linux obejmuje składniki jądra i składniki obszaru użytkownika, jak pokazano poniżej.

* **Jądro**: główny składnik to *Mapowanie urządzenia* , które przekieruje we/wy i obsługuje tryb failover dla ścieżek i grup ścieżek.

* **Przestrzeń użytkownika**: są to *Narzędzia wielościeżkowe* , które zarządzają urządzeniami wielościeżkowymi przez nakazanie modułu wielościeżkowego mapowania urządzeń. Narzędzia składają się z:
   
   * **Wielościeżkowe**: Wyświetla listę i konfiguruje urządzenia wielościeżkowe.
   * **Wielościeżkowe**: Demon, który wykonuje wielościeżkowe i monitoruje ścieżki.
   * **Devmap-Name**: zawiera zrozumiałą nazwę urządzenia do udev dla devmaps.
   * **Kpartx**: mapuje liniowy devmaps na partycje urządzeń, aby mapowania wielościeżkowego były podzielone na partycje.
   * **Wielościeżkowe. conf**: plik konfiguracyjny demona wielościeżkowego, która jest używana do zastępowania wbudowanej tabeli konfiguracyjnej.

### <a name="about-the-multipathconf-configuration-file"></a>Informacje o pliku konfiguracji wielościeżkowego. conf
Plik konfiguracji `/etc/multipath.conf` udostępnia wiele funkcji wielościeżkowych, które użytkownik konfigurowalne. `multipath`Polecenie i demon jądra `multipathd` używają informacji znajdujących się w tym pliku. Ten plik jest konsultowany tylko podczas konfigurowania urządzeń wielościeżkowych. Przed uruchomieniem polecenia upewnij się, że wszystkie zmiany zostały wprowadzone `multipath` . W przypadku zmodyfikowania pliku później należy zatrzymać i ponownie uruchomić wielościeżkową zmianę, aby zmiany zaczęły obowiązywać.

Wielościeżkowe. conf ma pięć sekcji:

- **Wartości domyślne na poziomie systemu** *(domyślne)*: można przesłonić wartości domyślne na poziomie systemu.
- **Urządzenia zabronione** *(zabroniony)*: można określić listę urządzeń, które nie powinny być kontrolowane przez funkcję mapowania urządzeń.
- **Wyjątki zabronionych** elementów *(blacklist_exceptions)*: można zidentyfikować konkretne urządzenia, które mają być traktowane jako urządzenia wielościeżkowe, nawet jeśli są wymienione w listy blokowania.
- **Ustawienia specyficzne dla kontrolera magazynu** *(urządzenia)*: można określić ustawienia konfiguracji, które zostaną zastosowane do urządzeń, które mają informacje o dostawcy i o produkcie.
- **Ustawienia specyficzne dla urządzenia** *(wielościeżkowe)*: możesz użyć tej sekcji, aby dostosować ustawienia konfiguracji poszczególnych jednostek LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurowanie wielościeżkowego StorSimple połączonego z hostem systemu Linux
Urządzenie StorSimple połączone z hostem systemu Linux można skonfigurować pod kątem wysokiej dostępności i równoważenia obciążenia. Na przykład jeśli Host systemu Linux ma dwa interfejsy podłączone do sieci SAN, a urządzenie ma dwa interfejsy podłączone do sieci SAN, tak że te interfejsy znajdują się w tej samej podsieci, dostępne są 4 ścieżki. Jeśli jednak każdy interfejs danych na urządzeniu i interfejsie hosta znajdują się w innej podsieci IP (bez routingu), będą dostępne tylko 2 ścieżki. Można skonfigurować wielościeżkowe, aby automatycznie wykrywać wszystkie dostępne ścieżki, wybrać algorytm równoważenia obciążenia dla tych ścieżek, zastosować określone ustawienia konfiguracji dla woluminów StorSimple, a następnie włączyć i zweryfikować wielościeżkowe.

Poniższa procedura opisuje sposób konfigurowania wielościeżkowego, gdy urządzenie StorSimple z dwoma interfejsami sieciowymi jest połączone z hostem z dwoma interfejsami sieciowymi.

## <a name="prerequisites"></a>Wymagania wstępne
Ta sekcja zawiera szczegółowe informacje o wymaganiach wstępnych dotyczących konfiguracji serwera CentOS i urządzenia StorSimple.

### <a name="on-centos-host"></a>Na hoście CentOS
1. Upewnij się, że host CentOS ma włączone 2 interfejsy sieciowe. Wpisz:
   
    `ifconfig`
   
    Poniższy przykład przedstawia dane wyjściowe, gdy `eth0` `eth1` na hoście znajdują się dwa interfejsy sieciowe (i).
   
    ```output
    [root@centosSS ~]# ifconfig
    eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

    eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

    loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
    ```
1. Zainstaluj narzędzia *inicjatora iSCSI* na serwerze CentOS. Wykonaj następujące kroki, aby zainstalować narzędzia *inicjatora iSCSI*.
   
   1. Zaloguj się `root` do swojego hosta CentOS.
   1. Zainstaluj narzędzia *inicjatora iSCSI*. Wpisz:
      
       `yum install iscsi-initiator-utils`
   1. Po pomyślnym zainstalowaniu narzędzia *inicjatora iSCSI* należy uruchomić usługę iSCSI. Wpisz:
      
       `service iscsid start`
      
       Zdarza się to, `iscsid` że program może nie zostać uruchomiony i `--force` może być wymagana opcja
   1. Aby zapewnić włączenie inicjatora iSCSI w czasie rozruchu, użyj `chkconfig` polecenia, aby włączyć usługę.
      
       `chkconfig iscsi on`
   1. Aby sprawdzić, czy program został prawidłowo skonfigurowany, uruchom polecenie:
      
       `chkconfig --list | grep iscsi`
      
       Poniżej pokazano przykładowe dane wyjściowe.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       Z powyższego przykładu można zobaczyć, że środowisko iSCSI zostanie uruchomione na czas rozruchu w przypadku uruchamiania poziomów 2, 3, 4 i 5.
1. Instalowanie funkcji *mapowania urządzeń-wielu ścieżek*. Wpisz:
   
    `yum install device-mapper-multipath`
   
    Rozpocznie się instalacja. Wpisz **Y** , aby kontynuować po wyświetleniu monitu o potwierdzenie.

### <a name="on-storsimple-device"></a>Na urządzeniu StorSimple
Urządzenie StorSimple powinno mieć następujące:

* Co najmniej dwa interfejsy włączone dla interfejsu iSCSI. Aby sprawdzić, czy na urządzeniu StorSimple są włączone dwa interfejsy, wykonaj następujące kroki w klasycznym portalu Azure dla urządzenia StorSimple:
  
  1. Zaloguj się do klasycznego portalu urządzenia StorSimple.
  1. Wybierz usługę StorSimple Manager, kliknij pozycję **urządzenia** i wybierz określone urządzenie StorSimple. Kliknij pozycję **Konfiguruj** i sprawdź ustawienia interfejsu sieciowego. Poniżej przedstawiono zrzut ekranu z dwoma interfejsami sieciowymi z obsługą iSCSI. W tym miejscu dane 2 i dane 3, oba interfejsy 10 GbE są włączone dla protokołu iSCSI.
     
      ![Konfiguracja MPIO StorsSimple DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Konfiguracja MPIO StorSimple DATA 3](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na stronie **Konfigurowanie**
     
     1. Upewnij się, że oba interfejsy sieciowe są włączone przy włączonej technologii iSCSI. Dla pola z **obsługą iSCSI** należy ustawić **wartość tak**.
     1. Upewnij się, że interfejsy sieciowe mają tę samą szybkość, obie powinny być 1 GbE lub 10 GbE.
     1. Zanotuj adresy IPv4 interfejsów z obsługą interfejsu iSCSI i Zapisz do późniejszego użycia na hoście.
* Interfejsy iSCSI na urządzeniu StorSimple powinny być dostępne z serwera CentOS.
      Aby to sprawdzić, należy podać adresy IP interfejsów sieciowych z obsługą interfejsu iSCSI StorSimple na serwerze hosta. Poniżej przedstawiono polecenia używane i odpowiadające im dane wyjściowe o wartość DANE2 (10.126.162.25) i DATA3 (10.126.162.26):
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Konfiguracja sprzętu
Zalecamy połączenie dwóch interfejsów sieciowych iSCSI na oddzielnych ścieżkach w celu zapewnienia nadmiarowości. Na poniższym rysunku przedstawiono zalecaną konfigurację sprzętu w celu zapewnienia wysokiej dostępności i wielościeżkowego równoważenia obciążenia dla serwera CentOS i urządzenia StorSimple.

![Konfiguracja sprzętowa MPIO dla hosta StorSimple z systemem Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak pokazano na powyższym rysunku:

* Urządzenie StorSimple jest w konfiguracji aktywne-pasywne z dwoma kontrolerami.
* Dwie przełączniki sieci SAN są połączone z kontrolerami urządzeń.
* Na urządzeniu StorSimple są włączone dwa inicjatory iSCSI.
* Na hoście CentOS są włączone dwa interfejsy sieciowe.

Powyższa konfiguracja spowoduje przekazanie 4 oddzielnych ścieżek między urządzeniem i hostem, Jeśli host i interfejsy danych są trasowane.

> [!IMPORTANT]
> * Zalecamy, aby nie mieszać 1 GbE i 10 GbE interfejsów sieciowych do wielościeżkowego. W przypadku korzystania z dwóch interfejsów sieciowych oba interfejsy powinny być identycznego typu.
> * Na urządzeniu z systemem StorSimple, DATA0, DANE1, DATA4 i DATA5 są 1 GbE Interfaces, a DANE2 i DATA3 to 10 interfejsów sieciowych. |
> 
> 

## <a name="configuration-steps"></a>Kroki konfiguracji
Kroki konfiguracji dla wielu ścieżek obejmują Konfigurowanie dostępnych ścieżek do automatycznego odnajdywania, Określanie algorytmu równoważenia obciążenia, który ma być używany, Włączanie wielościeżkowego i Sprawdzanie konfiguracji. Każdy z tych kroków został szczegółowo omówiony w poniższych sekcjach.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1. Konfigurowanie wielu ścieżek na potrzeby automatycznego odnajdywania
Urządzenia z obsługą wielu ścieżek mogą być automatycznie odnajdywane i konfigurowane.

1. Zainicjuj `/etc/multipath.conf` plik. Wpisz:
   
     `mpathconf --enable`
   
    Powyższe polecenie spowoduje utworzenie `sample/etc/multipath.conf` pliku.
1. Uruchom usługę wielościeżkową. Wpisz:
   
    `service multipathd start`
   
    Zostaną wyświetlone następujące dane wyjściowe:
   
    `Starting multipathd daemon:`
1. Włącz automatyczne odnajdowanie wielu ścieżek. Wpisz:
   
    `mpathconf --find_multipaths y`
   
    Spowoduje to modyfikację sekcji wartości domyślne `multipath.conf` w sposób pokazany poniżej:
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2. Konfigurowanie wielu ścieżek dla woluminów StorSimple
Domyślnie wszystkie urządzenia są blocklisted w pliku wielościeżkowym. conf i zostaną pominięte. Należy utworzyć wyjątki listy blokowania, aby umożliwić obsługę wielu ścieżek dla woluminów z urządzeń StorSimple.

1. Edytuj plik `/etc/mulitpath.conf`. Wpisz:
   
    `vi /etc/multipath.conf`
1. Znajdź sekcję blacklist_exceptions w pliku wielościeżkowego. conf. Urządzenie StorSimple musi być wymienione jako wyjątek listy blokowania w tej sekcji. Możesz usunąć komentarz z odpowiednich wierszy w tym pliku, aby zmodyfikować go jak pokazano poniżej (Użyj tylko określonego modelu używanego urządzenia):
   
    ```config
    blacklist_exceptions {
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8100*"
        }
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8600*"
        }
    }
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3. Konfigurowanie wielościeżkowego działania okrężnego
Ten algorytm równoważenia obciążenia używa wszystkich dostępnych wielu ścieżek do aktywnego kontrolera w zrównoważonej, okrężnej sposób.

1. Edytuj plik `/etc/multipath.conf`. Wpisz:
   
    `vi /etc/multipath.conf`
1. W `defaults` sekcji Ustaw wartość `path_grouping_policy` na `multibus` . `path_grouping_policy`Określa domyślne zasady grupowania ścieżek, które mają być stosowane do nieokreślonych wielościeżkowych. Zostanie wyświetlona sekcja wartości domyślne, jak pokazano poniżej.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> Najbardziej typowe wartości `path_grouping_policy` obejmują:
> 
> * Tryb failover = 1 ścieżka na grupę priorytetów
> * Multibus = wszystkie prawidłowe ścieżki w 1 grupie priorytetów
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4. Włączanie wielościeżkowego
1. Uruchom ponownie `multipathd` demona. Wpisz:
   
    `service multipathd restart`
1. Dane wyjściowe będą jak pokazano poniżej:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>Krok 5. Weryfikacja wielu ścieżek
1. Najpierw upewnij się, że połączenie iSCSI zostało nawiązane z urządzeniem StorSimple w następujący sposób:
   
   a. Odkryj Urządzenie StorSimple. Wpisz:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    Dane wyjściowe, gdy adres IP dla DATA0 to 10.126.162.25, a port 3260 jest otwarty na urządzeniu StorSimple dla ruchu wychodzącego iSCSI, jest tak jak pokazano poniżej:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Skopiuj nazwę IQN urządzenia StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` z poprzednich danych wyjściowych.

   b. Połącz się z urządzeniem przy użyciu docelowej nazwy IQN. Urządzenie StorSimple jest miejscem docelowym iSCSI. Wpisz:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    Poniższy przykład przedstawia dane wyjściowe z docelową IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . Dane wyjściowe wskazują, że pomyślnie nawiązano połączenie z dwoma interfejsami sieciowymi obsługującymi iSCSI na urządzeniu.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Jeśli w tym miejscu widzisz tylko jeden interfejs hosta i dwie ścieżki, należy włączyć oba interfejsy na hoście dla iSCSI. Można postępować zgodnie z [szczegółowymi instrukcjami w dokumentacji systemu Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Wolumin jest udostępniany serwerowi CentOSemu z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [krok 6. Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) za pośrednictwem Azure Portal na urządzeniu StorSimple.

1. Sprawdź dostępne ścieżki. Wpisz:

    `multipath -l`

      Poniższy przykład przedstawia dane wyjściowe dla dwóch interfejsów sieciowych na urządzeniu StorSimple podłączonym do jednego interfejsu sieciowego hosta z dwiema dostępnymi ścieżkami.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    Poniższy przykład przedstawia dane wyjściowe dla dwóch interfejsów sieciowych na urządzeniu StorSimple podłączonym do dwóch interfejsów sieciowych hosta z czterema dostępnymi ścieżkami.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Po skonfigurowaniu ścieżek zapoznaj się z określonymi instrukcjami w systemie operacyjnym hosta (CentOS 6,6), aby zainstalować i sformatować ten wolumin.

## <a name="troubleshoot-multipathing"></a>Rozwiązywanie problemów z funkcją wielościeżkowego
Ta sekcja zawiera kilka przydatnych porad w przypadku napotkania problemów podczas konfiguracji wielościeżkowej.

PYTANIE: Zmiany wprowadzone w pliku nie są widoczne `multipath.conf` .

A. Jeśli wprowadzono jakiekolwiek zmiany w `multipath.conf` pliku, należy ponownie uruchomić usługę wielościeżkowego. Wpisz następujące polecenie:

`service multipathd restart`

PYTANIE: Na urządzeniu StorSimple włączono dwa interfejsy sieciowe i dwa interfejsy sieciowe na hoście. Po wyświetleniu listy dostępnych ścieżek widzę tylko dwie ścieżki. Oczekiwane są cztery dostępne ścieżki.

A. Upewnij się, że dwie ścieżki znajdują się w tej samej podsieci i obsługują Routing. Jeśli interfejsy sieciowe znajdują się w różnych sieciach vLAN i nie obsługują routingu, zobaczysz tylko dwie ścieżki. Jednym ze sposobów sprawdzenia, czy można uzyskać dostęp do interfejsów hosta z interfejsu sieciowego na urządzeniu StorSimple. Musisz [skontaktować się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , ponieważ ta weryfikacja może zostać wykonana tylko za pośrednictwem sesji obsługi.

PYTANIE: Gdy wyświetlam listę dostępnych ścieżek, nie widzę żadnych danych wyjściowych.

A. Zazwyczaj nie widzisz żadnych ścieżek wielościeżkowych sugerujących problem z demonem wielościeżkowym i najprawdopodobniej występują problemy z tym `multipath.conf` plikiem.

Warto również sprawdzić, czy po nawiązaniu połączenia z elementem docelowym można zobaczyć pewne dyski, ponieważ nie ma odpowiedzi z list wielościeżkowych, co oznacza, że nie masz żadnych dysków.

* Użyj następującego polecenia, aby ponownie przeskanować magistralę SCSI:
  
    `$ rescan-scsi-bus.sh` (część pakietu sg3_utils)
* Wpisz następujące polecenia:
  
    `$ dmesg | grep sd*`
     
     Lub
  
    `$ fdisk -l`
  
    Zostaną zwrócone szczegóły ostatnio dodanych dysków.
* Aby określić, czy jest to dysk StorSimple, użyj następujących poleceń:
  
    `cat /sys/block/<DISK>/device/model`
  
    Spowoduje to zwrócenie ciągu, który określi, czy jest to dysk StorSimple.

Mniej prawdopodobne, ale możliwe przyczyny to również nieodświeżony Identyfikator PID iSCSI. Użyj następującego polecenia, aby wylogować się z sesji iSCSI:

`iscsiadm -m node --logout -p <Target_IP>`

Powtórz to polecenie dla wszystkich podłączonych interfejsów sieciowych w obiekcie docelowym iSCSI, który jest urządzeniem StorSimple. Po wylogowaniu ze wszystkich sesji iSCSI Użyj nazwy IQN obiektu docelowego iSCSI, aby ponownie nawiązać sesję iSCSI. Wpisz następujące polecenie:

`iscsiadm -m node --login -T <TARGET_IQN>`


PYTANIE: Nie mam pewności, czy urządzenie jest dozwolone.

A. Aby sprawdzić, czy urządzenie jest dozwolone, użyj następującego polecenia rozwiązywania problemów:

```console
multipathd -k
multipathd> show devices
available block devices:
ram0 devnode blacklisted, unmonitored
ram1 devnode blacklisted, unmonitored
ram2 devnode blacklisted, unmonitored
ram3 devnode blacklisted, unmonitored
ram4 devnode blacklisted, unmonitored
ram5 devnode blacklisted, unmonitored
ram6 devnode blacklisted, unmonitored
ram7 devnode blacklisted, unmonitored
ram8 devnode blacklisted, unmonitored
ram9 devnode blacklisted, unmonitored
ram10 devnode blacklisted, unmonitored
ram11 devnode blacklisted, unmonitored
ram12 devnode blacklisted, unmonitored
ram13 devnode blacklisted, unmonitored
ram14 devnode blacklisted, unmonitored
ram15 devnode blacklisted, unmonitored
loop0 devnode blacklisted, unmonitored
loop1 devnode blacklisted, unmonitored
loop2 devnode blacklisted, unmonitored
loop3 devnode blacklisted, unmonitored
loop4 devnode blacklisted, unmonitored
loop5 devnode blacklisted, unmonitored
loop6 devnode blacklisted, unmonitored
loop7 devnode blacklisted, unmonitored
sr0 devnode blacklisted, unmonitored
sda devnode whitelisted, monitored
dm-0 devnode blacklisted, unmonitored
dm-1 devnode blacklisted, unmonitored
dm-2 devnode blacklisted, unmonitored
sdb devnode whitelisted, monitored
sdc devnode whitelisted, monitored
dm-3 devnode blacklisted, unmonitored
```


Aby uzyskać więcej informacji, przejdź do [rozwiązywania problemów z wieloma ścieżkami](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lista przydatnych poleceń
| Typ | Polecenie | Opis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Uruchom usługę iSCSI |
| &nbsp; |`service iscsid stop` |Zatrzymaj usługę iSCSI |
| &nbsp; |`service iscsid restart` |Uruchom ponownie usługę iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Odnajdź dostępne elementy docelowe w określonym adresie |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Logowanie do obiektu docelowego iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Wylogowywanie z obiektu docelowego iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nazwa inicjatora iSCSI drukowania |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Sprawdź stan sesji iSCSI i woluminu odnalezionego na hoście |
| &nbsp; |`iscsi -m session` |Pokazuje wszystkie sesje iSCSI ustanowione między hostem a urządzeniem StorSimple |
|  | | |
| **Wiele ścieżek** |`service multipathd start` |Uruchom demona wielościeżkowa |
| &nbsp; |`service multipathd stop` |Zatrzymywanie wielościeżkowego demona |
| &nbsp; |`service multipathd restart` |Uruchom ponownie demona wielościeżkowa |
| &nbsp; |`chkconfig multipathd on` </br> LUB </br> `mpathconf -with_chkconfig y` |Włączenie demona wielościeżkowego do uruchamiania w czasie rozruchu |
| &nbsp; |`multipathd -k` |Uruchom konsolę interaktywną w celu rozwiązywania problemów |
| &nbsp; |`multipath -l` |Wyświetlanie listy połączeń i urządzeń z wielościeżkowej ścieżki |
| &nbsp; |`mpathconf --enable` |Utwórz przykładowy plik mulitpath. conf w `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Następne kroki
Podczas konfigurowania wielościeżkowego wejścia/wyjścia na hoście z systemem Linux może być również konieczne zaodwoływanie się do następujących dokumentów w systemie CentoS 6,6:

* [Konfigurowanie funkcji MPIO na CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Przewodnik uczenia systemu Linux](http://linux-training.be/linuxsys.pdf)
