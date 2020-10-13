---
title: Narzędzia do oceny wydajności w systemie Linux
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak rozwiązywać typowe problemy podczas korzystania z usługi Azure Kubernetes Service (AKS) przy użyciu narzędzi do oceny wydajności systemu Linux.
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90982039"
---
# <a name="linux-performance-troubleshooting"></a>Rozwiązywanie problemów z wydajnością systemu Linux

Wyczerpanie zasobów na komputerach z systemem Linux jest typowym problemem i może być manifestem przez wiele różnych symptomów. Ten dokument zawiera ogólne omówienie dostępnych narzędzi, które ułatwiają diagnozowanie problemów.

Wiele z tych narzędzi akceptuje interwał, w którym można generować wycofywane dane wyjściowe. Ten format danych wyjściowych zwykle sprawia, że wzorce wykrywania trendów są znacznie prostsze. W przypadku zaakceptowania przykładowego wywołania zostanie uwzględnione `[interval]` .

Wiele z tych narzędzi ma obszerną historię i szeroki zestaw opcji konfiguracji. Ta strona zawiera tylko prosty podzbiór wywołań do wyróżnienia typowych problemów. Kanoniczne źródło informacji jest zawsze dokumentacją referencyjną dla każdego określonego narzędzia. Ta dokumentacja będzie znacznie bardziej dokładna niż podano w tym miejscu.

## <a name="guidance"></a>Wskazówki

Należy przeprowadzić systematyczne podejście do badania problemów z wydajnością. UŻYWANE są dwa popularne podejścia (użycie, nasycenie, błędy) i RED (stawka, błędy, czas trwania). CZERWONY jest zwykle używany w kontekście usług do monitorowania opartego na żądaniach. Użycie jest zwykle używane w przypadku monitorowania zasobów: dla każdego zasobu na komputerze, monitorowania wykorzystania, nasycenia i błędów. Cztery główne rodzaje zasobów na dowolnym komputerze to procesor CPU, pamięć, dysk i sieć. Wysokie użycie, nasycenie lub częstotliwości błędów dla dowolnego z tych zasobów wskazuje możliwy problem z systemem. Gdy występuje problem, zbadaj główną przyczynę: Dlaczego opóźnienie operacji we/wy dysku jest wysokie? Czy są ograniczane dyski lub jednostki SKU maszyny wirtualnej? Jakie procesy są zapisywane na urządzeniach i w jakich plikach?

Przykłady typowych problemów i wskaźników w celu ich zdiagnozowania:
- Ograniczanie liczby operacji we/wy: Użyj iostat do mierzenia liczby operacji we/wy na urządzenie. Upewnij się, że żaden dysk nie przekracza jego limitu, a suma dla wszystkich dysków jest mniejsza niż limit dla maszyny wirtualnej.
- Ograniczanie przepustowości: Użyj iostat jako dla operacji IOPS, ale mierząc przepływność odczytu i zapisu. Upewnij się, że przepustowość dla poszczególnych urządzeń i agregowania jest mniejsza niż wartość limitu przepustowości.
- Wyczerpanie połączenia z ruchem: może to spowodować zalogowanie się jako wysoce aktywne (wychodzące) w programie SAR. 
- Utrata pakietów: Ta wartość może być mierzona przez serwer proxy za pomocą liczby ponownych transmisji protokołu TCP względem liczby wysłanych/odebranych. Obie `sar` `netstat` te informacje.

## <a name="general"></a>Ogólne

Te narzędzia są ogólnego przeznaczenia i obejmują podstawowe informacje o systemie. Są to dobry punkt wyjścia do dalszej analizy.

### <a name="uptime"></a>czas

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

czas przestoju zapewnia czas systemowy i 1, 5 i 15 minut średnie obciążenia. Te średnie obciążenia są w przybliżeniu odpowiadają wątkom wykonującym pracę lub oczekującym na ukończenie pracy awaryjnej. Bezwzględnie te liczby mogą być trudne do zinterpretowania, ale mierzone w czasie, które mogą powiedzieć nam przydatne informacje:

- Średnia 1-minutowa średnia > 5 minut oznacza zwiększenie obciążenia.
- Średnia 1-minutowa średnia < 5 minut oznacza zmniejszenie obciążeń.

czas pracy może również mieć wpływ na to, dlaczego informacje są niedostępne: problem mógł zostać rozwiązany samodzielnie lub przez ponowne uruchomienie komputera, zanim użytkownik będzie mógł uzyskać do niego dostęp.

Średnia obciążenia większa niż liczba dostępnych wątków procesora może wskazywać na problem z wydajnością dla danego obciążenia.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg zrzuca bufor jądra. Zdarzenia, takie jak OOMKill, dodają wpis do buforu jądra. Znajdowanie OOMKill lub innych komunikatów wyczerpania zasobów w dziennikach dmesg jest silnym wskaźnikiem problemu.

### <a name="top"></a>top (pierwsze)

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` zawiera szeroki przegląd bieżącego stanu systemu. Nagłówki zawierają przydatne informacje zagregowane:

- stan zadań: uruchomiony, uśpiony, zatrzymany.
- Użycie procesora CPU w tym przypadku przede wszystkim przedstawia czas bezczynności.
- całkowita, bezpłatna i używana pamięć systemowa.

`top` może pominąć procesy krótkotrwałe; alternatywy takie jak `htop` i `atop` zapewniają podobne interfejsy przy rozwiązywaniu niektórych wad.

## <a name="cpu"></a>Procesor CPU

Te narzędzia zapewniają informacje o użyciu procesora CPU. Jest to szczególnie przydatne w przypadku kroczących danych wyjściowych, gdzie wzorce stają się łatwe do zapunktowania.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` drukuje podobne informacje o procesorze CPU, ale podzielone według wątku procesora CPU. Jednoczesne wyświetlanie wszystkich rdzeni może być przydatne do wykrywania wysoce niezrównoważonego użycia procesora CPU, na przykład gdy aplikacja jednowątkowa używa jednego rdzenia na 100%. Ten problem może być trudniejszy do zapamiętania, gdy jest zagregowany cały procesor CPU w systemie.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` zapewnia podobne informacje `mpstat` i `top` wylicza liczbę procesów oczekujących na procesor CPU (r Column), statystyk pamięci i procent czasu procesora CPU spędzony w każdym stanie pracy.

## <a name="memory"></a>Pamięć

Pamięć to bardzo ważne i Thankfully, które umożliwiają śledzenie. Niektóre narzędzia mogą raportować zarówno procesor, jak i pamięć, na przykład `vmstat` . Jednak narzędzia takie jak `free` nadal mogą być przydatne do szybkiego debugowania.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` przedstawia podstawowe informacje o całkowitej ilości pamięci oraz używanej i wolnej pamięci. `vmstat` może być bardziej użyteczna nawet w przypadku podstawowej analizy pamięci z powodu jej zdolności do dostarczania danych wyjściowych.

## <a name="disk"></a>Dysk

Narzędzia te mierzą operacje we/wy na dysku, kolejki oczekiwania i łączną przepływność. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` zapewnia szczegółowe informacje o wykorzystaniu dysku. To wywołanie przekazuje `-x` szczegółowe dane statystyczne, `-y` Aby pominąć średnią orednią systemu drukowania wyjściowego od rozruchu i `1 1` określić, że chcemy 1 sekundowy interwał, kończąc po jednym bloku danych wyjściowych. 

`iostat` uwidacznia wiele przydatnych statystyk:

- `r/s` i `w/s` są odczytami na sekundę i zapisu na sekundę. Suma tych wartości to IOPS.
- `rkB/s` i `wkB/s` są kilobajtami do odczytu/zapisu na sekundę. Suma tych wartości jest przepływność.
- `await` to średni czas iowait w milisekundach dla żądań umieszczonych w kolejce.
- `avgqu-sz` jest średnim rozmiarem kolejki w podanym interwale.

Na maszynie wirtualnej platformy Azure:

- suma `r/s` i `w/s` dla poszczególnych urządzeń blokujących nie może przekroczyć limitów jednostek SKU tego dysku.
- suma `rkB/s` i `wkB/s`  dla poszczególnych urządzeń blokujących nie może przekroczyć limitów jednostek SKU tego dysku
- suma `r/s` i `w/s` dla wszystkich urządzeń blokujących nie może przekroczyć limitów dla jednostki SKU maszyny wirtualnej.
- suma  `rkB/s` i "wkB/s" dla wszystkich zablokowanych urządzeń nie może przekraczać limitów dla jednostki SKU maszyny wirtualnej.

Należy zauważyć, że dysk systemu operacyjnego jest liczony jako dysk zarządzany o najmniejszej jednostce SKU odpowiadającej jej pojemności. Na przykład dysk systemu operacyjnego 1024GB odnosi się do dysku P30. Stałe dyski systemu operacyjnego i dyski tymczasowe nie mają indywidualnych limitów dysku; są one ograniczone tylko przez pełne limity maszyn wirtualnych.

Niezerowe wartości await lub avgqu-sz są również dobrymi wskaźnikami rywalizacji we/wy.

## <a name="network"></a>Sieć

Narzędzia te mierzą dane statystyczne sieci, takie jak przepływność, awarie transmisji i wykorzystanie. Dokładniejsza analiza może uwidaczniać szczegółowe dane statystyczne protokołu TCP dotyczące przeciążenia i pakietów porzuconych.

### <a name="sar"></a>SAR

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` jest zaawansowanym narzędziem do szerokiego zakresu analizy. Chociaż w tym przykładzie wykorzystuje ona możliwości mierzenia statystyk sieci, jest to równie wydajne dla mierzenia zużycia procesora i pamięci. Ten przykład wywołuje `sar` przy użyciu `-n` flagi, aby określić `DEV` słowo kluczowe (urządzenie sieciowe), wyświetlając przepływność sieci według urządzenia.

- Suma `rxKb/s` i `txKb/s` jest całkowitą przepływność dla danego urządzenia. Gdy ta wartość przekroczy limit aprowizacji kart sieciowych platformy Azure, obciążenia na maszynie będą zwiększone do opóźnienia sieci.
- `%ifutil` mierzy wykorzystanie dla danego urządzenia. Ponieważ ta wartość zbliża się do 100%, obciążenia będą powodować zwiększone opóźnienia sieci.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

To wywołanie `sar` używa `TCP,ETCP` słów kluczowych do sprawdzenia połączeń TCP. Trzecia kolumna ostatniego wiersza "retransd" to liczba retransmisji TCP na sekundę. Wysokie wartości dla tego pola wskazują niezawodne połączenie sieciowe. W pierwszym i trzecim wierszu "aktywny" oznacza połączenie pochodzące z urządzenia lokalnego, podczas gdy "zdalny" wskazuje połączenie przychodzące.  Typowym problemem związanym z platformą Azure jest wyczerpanie portów, które `sar` może pomóc wykryć. Wyczerpanie portów protokołu przesyłania adresów sieciowych będzie manifestować jako wysokie wartości "aktywne", ponieważ przyczyną tego problemu jest wysoka liczba wychodzących, inicjowanych lokalnie połączeń TCP.

Jak `sar` przyjmuje interwał, drukuje dane wyjściowe, a następnie drukuje końcowe wiersze danych wyjściowych zawierające średnie wyniki wywołania.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` może Introspect szeroką gamę statystyk sieci w tym miejscu z podsumowaniem danych wyjściowych. W zależności od tego problemu istnieje wiele przydatnych pól. Jednym z przydatnych pól w sekcji TCP jest "nieudane próby połączenia". Może to wskazywać na wyczerpanie portów lub inne problemy związane z ruchem wychodzącym. Wysoka szybkość przesyłania segmentów (również w sekcji TCP) może wskazywać na problemy z dostarczaniem pakietów. 
