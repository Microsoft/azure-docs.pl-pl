---
title: Dostrajanie wydajności TCP/IP dla maszyn wirtualnych platformy Azure | Microsoft Docs
description: Poznaj różne metody dostrajania wydajności protokołu TCP/IP oraz ich relacje z maszynami wirtualnymi platformy Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 67b635f09cb9407279e89b5f7b8526dab3c08946
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017614"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure

W tym artykule omówiono typowe techniki dostrajania wydajności protokołu TCP/IP i niektóre zagadnienia, które należy wziąć pod uwagę podczas korzystania z nich w przypadku maszyn wirtualnych działających na platformie Azure. Udostępnimy podstawowe Omówienie technik i dowiesz się, jak można je dostrajać.

## <a name="common-tcpip-tuning-techniques"></a>Typowe techniki strojenia protokołu TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>Jednostki MTU, fragmentacja i duże odciążanie wysyłania

#### <a name="mtu"></a>ROZMIAR

Maksymalna jednostka transmisji (MTU) to największy rozmiar ramki (pakiet) określony w bajtach, który można wysłać za pośrednictwem interfejsu sieciowego. Jednostka MTU jest ustawieniem konfigurowalnym. Domyślna Jednostka MTU używana na maszynach wirtualnych platformy Azure oraz domyślna wartość ustawienia na większości urządzeń sieciowych wynosi 1 500 bajtów.

#### <a name="fragmentation"></a>Fragmentacji

Fragmentacja występuje, gdy zostanie wysłany pakiet, który przekracza rozmiar jednostki MTU interfejsu sieciowego. Stos TCP/IP spowoduje przerwanie pakietu na mniejsze fragmenty (fragmenty), które są zgodne z rozmiarem MTU interfejsu. Fragmentacja występuje w warstwie IP i jest niezależna od bazowego protokołu (takiego jak TCP). Gdy pakiet 2 000-bajtowy jest przesyłany za pośrednictwem interfejsu sieciowego o identyfikatorze MTU 1 500, pakiet zostanie podzielony na pakiet 1 1 500-Byte i pakiet 1 500-Byte.

Urządzenia sieciowe w ścieżce między źródłem a miejscem docelowym mogą porzucić pakiety, które przekraczają rozmiar jednostki MTU, lub podzielić pakiet na mniejsze części.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Bit nie fragmentacji w pakiecie IP

Bit nie fragmentuj (DF) jest flagą w nagłówku protokołu IP. Bit DF wskazuje, że urządzenia sieciowe na ścieżce między nadawcą i odbiornikiem nie mogą fragmentować pakietu. Ten bit może być ustawiony z wielu powodów. (Zobacz sekcję "odnajdywanie MTU ścieżki" tego artykułu na jeden przykład). Gdy urządzenie sieciowe odbiera pakiet z ustawionym bitem braku fragmentacji, a ten pakiet przekracza jednostkę MTU interfejsu urządzenia, standardowe zachowanie dotyczy urządzenia do porzucenia pakietu. Urządzenie wysyła komunikat o fragmentacji protokołu ICMP z powrotem do oryginalnego źródła pakietu.

#### <a name="performance-implications-of-fragmentation"></a>Wpływ na wydajność fragmentacji

Fragmentacja może mieć negatywny wpływ na wydajność. Jednym z głównych przyczyn wpływu na wydajność jest wykorzystanie procesora CPU lub pamięci na potrzeby fragmentacji i ponownego montażu pakietów. Gdy urządzenie sieciowe musi fragmentować pakiet, konieczne będzie przydzielenie zasobów procesora/pamięci w celu przeprowadzenia fragmentacji.

Ta sama czynność ma miejsce, gdy pakiet jest ponownie składany. Urządzenie sieciowe musi przechowywać wszystkie fragmenty, dopóki nie zostaną odebrane, aby można je było ponownie połączyć z oryginalnym pakietem. Ten proces fragmentacji i ponownego montażu może również spowodować opóźnienia.

Innym możliwym nieujemnym wpływem na wydajność fragmentacji jest to, że pofragmentowane pakiety mogą dotrzeć do nieuporządkowanej kolejności. Gdy pakiety są odbierane poza kolejnością, niektóre typy urządzeń sieciowych mogą je usunąć. W takim przypadku cały pakiet musi zostać ponownie przesłany.

Fragmenty są zwykle opuszczane przez urządzenia zabezpieczeń, takie jak zapory sieciowe lub Bufory odbioru urządzenia sieciowego. Gdy Bufory odbioru urządzenia sieciowego są wyczerpane, urządzenie sieciowe próbuje ponownie połączyć pofragmentowany pakiet, ale nie ma zasobów do przechowywania i ponownego założenia pakietu.

Fragmentacja może być traktowana jako operacja ujemna, ale Obsługa fragmentacji jest konieczna w przypadku łączenia różnorodnych sieci za pośrednictwem Internetu.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Zalety i konsekwencje modyfikacji jednostki MTU

Ogólnie mówiąc, można utworzyć wydajniejszą sieć przez zwiększenie jednostki MTU. Każdy przesłany pakiet zawiera informacje nagłówka, które są dodawane do oryginalnego pakietu. Gdy fragmentacja tworzy więcej pakietów, istnieje więcej obciążeń związanych z nagłówkami, co sprawia, że sieć jest mniej wydajna.

Oto przykład. Rozmiar nagłówka Ethernet to 14 bajtów i sekwencja kontroli klatek 4-bajtowych, aby zapewnić spójność ramek. Jeśli zostanie wysłany pakiet 1 2 000-bajtowy, w sieci zostanie dodany 18-bajtowe obciążenie sieci Ethernet. Jeśli pakiet jest pofragmentowany do 1 500-bajtowego pakietu i 500 bajtów, każdy pakiet będzie miał 18 bajtów nagłówka Ethernet, łącznie z 36 bajtów.

Należy pamiętać, że zwiększenie jednostki MTU nie będzie koniecznie tworzyć bardziej wydajną sieć. Jeśli aplikacja wysyła tylko 500-bajtowe pakiety, to samo obciążenie nagłówka będzie istnieć, czy rozmiar jednostki MTU to 1 500 bajtów czy 9 000 bajtów. Sieć stanie się bardziej wydajna tylko wtedy, gdy używa większych rozmiarów pakietów, na które ma wpływ Jednostka MTU.

#### <a name="azure-and-vm-mtu"></a>Platforma Azure i Jednostka MTU maszyny wirtualnej

Domyślna Jednostka MTU dla maszyn wirtualnych platformy Azure to 1 500 bajtów. Stos Virtual Network platformy Azure podejmie próbę fragmentacji pakietu o 1 400 bajtów.

Należy zauważyć, że stos Virtual Network nie jest nieefektywny, ponieważ zawiera fragmenty pakietów o 1 400 bajtów, nawet jeśli maszyny wirtualne mają rozmiar MTU równy 1 500. Duża część pakietów sieciowych jest znacznie mniejsza niż 1 400 lub 1 500 bajtów.

#### <a name="azure-and-fragmentation"></a>Platforma Azure i fragmentacja

Stos Virtual Network został skonfigurowany tak, aby porzucić fragmenty "poza kolejnością", czyli pofragmentowanych pakietów, które nie dotarły do ich oryginalnej uporządkowanej kolejności. Te pakiety są usuwane głównie z powodu luki w zabezpieczeniach sieci ogłoszonej w listopadzie 2018 o nazwie FragmentSmack.

FragmentSmack to wada w sposobie, w jaki jądro systemu Linux obsłużyć remontaż pofragmentowanych pakietów IPv4 i IPv6. Zdalna osoba atakująca może wykorzystać tę usterkę do wyzwolenia kosztownych operacji ponownego montażu fragmentów, co może prowadzić do zwiększonego procesora i odmowy usługi w systemie docelowym.

#### <a name="tune-the-mtu"></a>Dostosuj jednostkę MTU

Jednostkę MTU maszyny wirtualnej platformy Azure można skonfigurować w taki sam sposób, jak w przypadku każdego innego systemu operacyjnego. Należy jednak wziąć pod uwagę fragmentację występującą na platformie Azure, która została opisana powyżej podczas konfigurowania jednostki MTU.

Nie Zachęcamy klientów do zwiększenia MTUs maszyny wirtualnej. W tej dyskusji przedstawiono szczegółowe informacje na temat sposobu implementowania jednostki MTU przez platformę Azure i przeprowadzania fragmentacji.

> [!IMPORTANT]
>Zwiększenie wydajności nie jest znane, aby zwiększyć wydajność i mieć negatywny wpływ na wydajność aplikacji.
>
>

#### <a name="large-send-offload"></a>Odciążanie dużego wysyłania

Odciążanie wysyłania dużej liczby (LSO) może zwiększyć wydajność sieci, przenosząc segmentację pakietów na kartę Ethernet. Gdy jest włączona funkcja LSO, stos TCP/IP tworzy duży pakiet TCP i wysyła go do karty Ethernet w celu segmentacji przed przekazaniem go. Korzyść odciążania polega na tym, że może zwolnić procesor z segmentacji pakietów na rozmiary, które są zgodne z rozmiarem MTU i odciążać, które przetwarza w interfejsie Ethernet, w którym jest wykonywane na sprzęcie. Aby dowiedzieć się więcej o zaletach korzystania z dużej ilości pamięci, zobacz [Obsługa dużego wysyłania](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Po włączeniu obsługi dużej ilości danych klienci platformy Azure mogą widzieć duże rozmiary ramek podczas przeprowadzania przechwytywania pakietów. Duże rozmiary ramek mogą prowadzić do tego, że niektórzy klienci zapewnią fragmentację lub że jest używana duża Jednostka MTU, gdy nie jest. Dzięki użyciu dużej ilości odciążeń karta Ethernet może anonsować większy maksymalny rozmiar segmentu (w szczycie) do stosu TCP/IP, aby utworzyć większy pakiet TCP. Ta cała niesegmentacja ramka jest następnie przekazywana do karty Ethernet i będzie widoczna w przechwyceniu pakietu wykonywanym na maszynie wirtualnej. Ale pakiet zostanie podzielony na wiele mniejszych ramek przez kartę Ethernet, zgodnie z rozmiarem jednostki MTU karty Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Skalowanie okna przez TCP/PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maksymalny rozmiar segmentu TCP

Maksymalny rozmiar segmentu TCP (Maximum) to ustawienie ograniczające rozmiar segmentów TCP, co pozwala uniknąć fragmentacji pakietów TCP. Systemy operacyjne zwykle używają tej formuły do ustawiania wartości:

`MSS = MTU - (IP header size + TCP header size)`

Nagłówek IP i nagłówek TCP to 20 bajtów każdego lub 40 bajtów. Dzięki temu interfejs o identyfikatorze MTU 1 500 będzie miał rozmiar o wartości = 1 460. Jednak rozmiar tego samego poziomu można skonfigurować.

To ustawienie jest uzgadniane z trzema uzgadnianiem TCP w przypadku skonfigurowania sesji protokołu TCP między źródłem a miejscem docelowym. Obie strony wysyłają wartość o przeciążeniu, a dolna część dwóch jest używana do połączenia TCP.

Należy pamiętać, że MTUs źródła i miejsca docelowego nie są jedynymi czynnikami, które określają wartość parametru. Pośredniczące urządzenia sieciowe, takie jak bramy sieci VPN, w tym Azure VPN Gateway, umożliwiają dostosowanie jednostki MTU niezależnie od źródła i miejsca docelowego w celu zapewnienia optymalnej wydajności sieci.

#### <a name="path-mtu-discovery"></a>Odnajdywanie MTU ścieżki

Rozmiar jest negocjowany, ale może nie wskazywać rzeczywistego, który może być używany. Wynika to z faktu, że inne urządzenia sieciowe w ścieżce między źródłem i lokalizacją docelową mogą mieć niższą wartość MTU niż źródłowa i docelowa. W takim przypadku urządzenie, którego Jednostka MTU jest mniejsza niż pakiet, spowoduje porzucenie pakietu. Urządzenie wyśle komunikat o fragmentacji protokołu ICMP (typ 3, kod 4), który zawiera jego jednostkę MTU. Ten komunikat ICMP umożliwia hostowi źródłowej zredukowanie odpowiedniej jednostki MTU ścieżki. Ten proces jest nazywany odnajdywaniem ścieżki MTU (PMTUD).

Proces PMTUD jest nieefektywny i wpływa na wydajność sieci. Po wysłaniu pakietów, które przekraczają jednostkę MTU ścieżki sieciowej, pakiety muszą zostać ponownie przesłane przy użyciu mniejszego poziomu. Jeśli nadawca nie odbierze komunikatu o fragmentacji protokołu ICMP, może być spowodowany przez zaporę sieciową w ścieżce (nazywaną zwykle jako *PMTUD Blackhole*), nadawca nie wie, że musi obniżyć rozmiar pasma i ciągle ponownie przesyła pakiet. Dlatego nie zaleca się zwiększania jednostki MTU maszyny wirtualnej platformy Azure.

#### <a name="vpn-and-mtu"></a>Sieci VPN i MTU

W przypadku używania maszyn wirtualnych, które wykonują hermetyzację (na przykład sieci VPN z protokołem IPsec), istnieją pewne dodatkowe zagadnienia dotyczące rozmiaru pakietów i jednostki MTU. Sieci VPN dodają więcej nagłówków do pakietów, co zwiększa rozmiar pakietu i wymaga mniejszego rozmiaru.

W przypadku platformy Azure zaleca się ustawienie opcji Ogranicz ruch TCP do 1 350 bajtów i jednostki MTU interfejsu tunelu do 1 400. Aby uzyskać więcej informacji, zobacz [stronę sieci VPN i parametry protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Skalowanie opóźnienia, czasu rundy i okna TCP

#### <a name="latency-and-round-trip-time"></a>Opóźnienie i czas błądzenia

Opóźnienie sieci zależy od szybkości sieci światłowodowej. Przepustowość sieci TCP jest również efektywnie określana przez czas błądzenia (RTT) między dwoma urządzeniami sieciowymi.

| Trasa | Odległość | Czas jednorazowy | RTT |
| ----- | -------- | ------------ | --- |
|Nowy Jork do sieci San Francisco|4 148 km|21 MS|42 MS|
|Nowy Jork do Londyn|5 585 km|28 MS|56 MS|
|Nowy Jork do Sydney|15 993 km|80 MS|160 MS|

W tej tabeli przedstawiono prostą odległość między dwiema lokalizacjami. W sieciach odległość jest zazwyczaj większa od odległości liniowej. Oto prosta formuła służąca do obliczania minimalnego RTT, zgodnie z szybkością światła:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Aby uzyskać szybkość propagacji, można użyć 200. Jest to odległość (w kilometrach), która świeci się w 1 milisekundach.

Na przykład przyjrzyjmy się nowemu Jorku do sieci San Francisco. Odległość liniowa to 4 148 km. Dołączenie tej wartości do równania zapewnia następujące korzyści:

`Minimum RTT = 2 * (4,148 / 200)`

Dane wyjściowe równania są w milisekundach.

Jeśli chcesz uzyskać najlepszą wydajność sieci, opcja logiczna polega na wybraniu lokalizacji docelowych z najkrótszą odległością między nimi. Należy również zaprojektować sieć wirtualną w celu optymalizacji ścieżki ruchu i zmniejszania opóźnień. Aby uzyskać więcej informacji, zobacz sekcję "zagadnienia dotyczące projektowania sieci" w tym artykule.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efekty opóźnienia i czasu rundy w przypadku protokołu TCP

Czas błądzenia ma bezpośredni wpływ na maksymalną przepływność TCP. W polu protokół TCP *rozmiar okna* jest maksymalną ilością ruchu sieciowego, który można wysłać za pośrednictwem połączenia TCP, zanim nadawca musi odebrać potwierdzenie z odbiornika. Jeśli wartość właściwości TCP/rozmiaru jest ustawiona na 1 460, a rozmiar okna TCP jest ustawiony na 65 535, nadawca może wysłać 45 pakietów, zanim będzie on otrzymywać potwierdzenie od odbiornika. Jeśli nadawca nie uzyska potwierdzenia, nastąpi ponowne przesłanie danych. Oto formuła:

`TCP window size / TCP MSS = packets sent`

W tym przykładzie 65 535/1 460 jest zaokrąglana do 45.

W przypadku stanu "Oczekiwanie na potwierdzenie" mechanizm zapewnienia niezawodnego dostarczania danych jest taki sam, co powoduje, że RTT ma wpływ na przepływność TCP. Im dłużej nadawca czeka na potwierdzenie, tym dłużej musi czekać przed wysłaniem większej ilości danych.

Oto formuła służąca do obliczania maksymalnej przepływności pojedynczego połączenia TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

W tej tabeli przedstawiono maksymalną przepływność (w megabajtach/na sekundę) jednego połączenia TCP. (Na potrzeby odczytywania, megabajtów jest używany dla jednostki miary).

| Rozmiar okna TCP (w bajtach) | Opóźnienie RTT (MS) | Maksymalna przepływność (MB/s) | Maksymalna przepływność w megabitach/sekundę |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|1|65,54|524,29|
|65 535|30|2,18|17,48|
|65 535|60|1.09|8,74|
|65 535|90|.73|5,83|
|65 535|120|.55|4,37|

Jeśli pakiety zostaną utracone, maksymalna przepływność połączenia TCP zostanie zmniejszona, podczas gdy nadawca ponownie przesyła dane, które zostały już wysłane.

#### <a name="tcp-window-scaling"></a>Skalowanie okna TCP

Skalowanie okna TCP to technika, która dynamicznie zwiększa rozmiar okna TCP, aby umożliwić wysyłanie większej ilości danych, zanim będzie wymagane potwierdzenie. W poprzednim przykładzie pakiety 45 będą wysyłane przed potwierdzeniem. W przypadku zwiększenia liczby pakietów, które mogą być wysyłane przed potwierdzeniem, można skrócić, ile razy nadawca oczekuje na potwierdzenie, co zwiększa przepływność TCP.

W tej tabeli przedstawiono relacje:

| Rozmiar okna TCP (w bajtach) | Opóźnienie RTT (MS) | Maksymalna przepływność (MB/s) | Maksymalna przepływność w megabitach/sekundę |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|30|2,18|17,48|
|131 070|30|4,37|34,95|
|262 140|30|8,74|69,91|
|524 280|30|17,48|139,81|

Ale wartość nagłówka TCP dla rozmiaru okna TCP ma długość tylko 2 bajty, co oznacza, że maksymalna wartość okna odbierania to 65 535. Aby zwiększyć maksymalny rozmiar okna, wprowadzono współczynnik skalowania okna TCP.

Współczynnik skalowania jest również ustawieniem, które można skonfigurować w systemie operacyjnym. Oto formuła służąca do obliczania rozmiaru okna TCP przy użyciu czynników skalowania:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Oto Obliczanie współczynnika skalowania okna o wartości 3 i rozmiaru okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Współczynnik skali wynoszący 14 skutkuje rozmiarem okna TCP wynoszącym 14 (dozwolone jest maksymalne przesunięcie). Rozmiar okna TCP będzie wynosił 1 073 725 440 bajtów (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Obsługa skalowania okna TCP

System Windows może ustawić różne czynniki skalowania dla różnych typów połączeń. (Klasy połączeń obejmują centrum danych, Internet i tak dalej). `Get-NetTCPConnection` Aby wyświetlić typ połączenia skalowania okna, należy użyć polecenia programu PowerShell:

```powershell
Get-NetTCPConnection
```

Możesz użyć `Get-NetTCPSetting` polecenia programu PowerShell, aby wyświetlić wartości każdej klasy:

```powershell
Get-NetTCPSetting
```

Możesz ustawić początkowy rozmiar okna TCP i współczynnik skalowania TCP w systemie Windows za pomocą `Set-NetTCPSetting` polecenia programu PowerShell. Aby uzyskać więcej informacji, zobacz  [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Są to obowiązujące ustawienia protokołu TCP dla `AutoTuningLevel` :

| AutoTuningLevel | Współczynnik skalowania | Mnożnik skalowania | Formuła do<br/>Oblicz maksymalny rozmiar okna |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Disabled|Brak|Brak|Rozmiar okna|
|Z ograniczeniami|4|2 ^ 4|Rozmiar okna * (2 ^ 4)|
|Wysoce ograniczone|2|2 ^ 2|Rozmiar okna * (2 ^ 2)|
|Normalne|8|2 ^ 8|Rozmiar okna * (2 ^ 8)|
|Eksperymentalne|14|2 ^ 14|Rozmiar okna * (2 ^ 14)|

Te ustawienia najprawdopodobniej mają wpływ na wydajność protokołu TCP, ale należy pamiętać, że wiele innych czynników w Internecie, poza kontrolą platformy Azure, może również wpływać na wydajność protokołu TCP.

#### <a name="increase-mtu-size"></a>Zwiększ rozmiar jednostki MTU

Ze względu na to, że większa Jednostka MTU oznacza większy rozmiar, może się zdarzyć, że zwiększenie wydajności może zwiększyć wydajność protokołu TCP. Prawdopodobnie nie. Nie tylko ruch TCP ma pewne wady i zalety rozmiaru pakietu. Jak wspomniano wcześniej, najważniejsze czynniki wpływające na wydajność przepływności TCP to rozmiar okna TCP, utrata pakietów i czas RTT.

> [!IMPORTANT]
> Nie zalecamy, aby klienci platformy Azure mogli zmienić domyślną wartość MTU na maszynach wirtualnych.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Przyspieszanie sieci i skalowanie po stronie odbierającej

#### <a name="accelerated-networking"></a>Wydajniejsze sieci

Funkcja sieci maszyn wirtualnych znacznie intensywnie korzysta z procesora dla maszyny wirtualnej gościa i funkcji hypervisor/hosta. Każdy pakiet, który jest przesyłany przez hosta, jest przetwarzany w oprogramowaniu przez procesor hosta, w tym wszystkie hermetyzacje i decapsulation sieci wirtualnej. Tym większym ruchem przechodzącym przez hosta jest wyższy poziom obciążenia procesora. A jeśli procesor CPU hosta jest zajęty innymi operacjami, to wpływa również na przepływność i opóźnienie sieci. Platforma Azure rozwiązuje ten problem przy użyciu przyspieszonej sieci.

Przyspieszona sieć zapewnia spójne ultralow sieci w ramach programowalnego sprzętu platformy Azure i technologii, takich jak SR-IOV. Przyspieszona sieć przenosi wiele procesorów sieci zdefiniowanych przez oprogramowanie platformy Azure na platformę i na FPGA SmartNICs. Ta zmiana umożliwia aplikacjom użytkowników końcowych odzyskiwanie cykli obliczeniowych, co zmniejsza obciążenie maszyny wirtualnej, zmniejszając wahania i niespójności w czasie oczekiwania. Innymi słowy, wydajność może być bardziej deterministyczna.

Przyspieszona sieć zwiększa wydajność, umożliwiając maszynie wirtualnej gościa obejście hosta i ustanowienie ścieżki danych bezpośrednio z SmartNIC hosta. Oto niektóre zalety przyspieszonej sieci:

- **Mniejsze opóźnienia/wyższe pakiety na sekundę (PPS)**: usunięcie przełącznika wirtualnego ze ścieżki datapath eliminuje pakiety czasu spędzane przez hosta w celu przetworzenia zasad i zwiększa liczbę pakietów, które można przetworzyć w maszynie wirtualnej.

- **Zmniejszona wahanie**: przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować, oraz obciążenia procesora, które przetwarza. Odciążanie wymuszania zasad sprzętowo eliminuje tę zmienność przez dostarczanie pakietów bezpośrednio do maszyny wirtualnej, eliminując komunikację między hostami i MASZYNami wirtualnymi oraz wszystkie przerwy w oprogramowaniu i przełączenia kontekstu.

- **Zmniejszone użycie procesora**: obejście przełącznika wirtualnego na hoście prowadzi do mniejszego użycia procesora CPU do przetwarzania ruchu sieciowego.

Aby korzystać z przyspieszonej sieci, należy jawnie włączyć ją na każdej odpowiedniej maszynie wirtualnej. Aby uzyskać instrukcje, zobacz temat [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

#### <a name="receive-side-scaling"></a>Skalowanie po stronie odbierającej

Skalowanie po stronie odbierającej (RSS) to technologia sterownika sieci, która dystrybuuje odbierający ruch sieciowy bardziej wydajnie przez dystrybucję przetwarzania odbierania między wieloma procesorami w systemie wieloprocesorowym. W prostych warunkach funkcja RSS umożliwia systemowi przetwarzanie większej liczby odbieranych danych, ponieważ używa ona wszystkich dostępnych procesorów, a nie tylko jednego. Aby zapoznać się z bardziej technicznym omówieniem funkcji RSS, zobacz [wprowadzenie do skalowania po stronie odbierającej](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Aby uzyskać najlepszą wydajność, gdy na maszynie wirtualnej jest włączona przyspieszona sieć, należy włączyć funkcję RSS. Funkcja RSS może również zapewniać korzyści dotyczące maszyn wirtualnych, które nie używają przyspieszonej sieci. Aby dowiedzieć się, jak ustalić, czy funkcja RSS jest włączona i jak ją włączyć, zobacz [Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT i TIME_WAIT Assassination

TCP TIME_WAIT jest innym wspólnym ustawieniem, które ma wpływ na wydajność sieci i aplikacji. W przypadku zajętych maszyn wirtualnych otwierających i zamykających wiele gniazd jako klientów lub jako serwery (źródłowy adres IP: Port źródłowy + docelowy adres IP: Port docelowy) podczas normalnego działania protokołu TCP, dany gniazdo może kończyć się w stanie TIME_WAIT przez długi czas. Stan TIME_WAIT ma pozwolić na dostarczenie wszelkich dodatkowych danych w gnieździe przed jego zamknięciem. Dlatego stosy TCP/IP zwykle uniemożliwiają ponowne użycie gniazda przez dyskretne porzucenie pakietu TCP SYN klienta.

Czas, w którym gniazdo jest w TIME_WAIT można skonfigurować. Może to być z zakresu od 30 sekund do 240 sekund. Gniazda są ograniczonym zasobem, a liczba gniazd, które mogą być używane w danym momencie, można skonfigurować. (Liczba dostępnych gniazd jest zwykle około 30 000). Jeśli dostępne gniazda są używane lub w przypadku, gdy klienci i serwery mają niezgodne ustawienia TIME_WAIT, a maszyna wirtualna próbuje ponownie użyć gniazda w stanie TIME_WAIT, nowe połączenia będą kończyć się niepowodzeniem, ponieważ pakiety protokołu TCP SYN są dyskretnie porzucane.

Wartość zakresu portów dla gniazd wychodzących jest zazwyczaj konfigurowana w ramach stosu TCP/IP systemu operacyjnego. Ta sama wartość dotyczy ustawień TIME_WAIT TCP i ponownego użycia gniazd. Zmiana tych liczb może potencjalnie zwiększyć skalowalność. Jednak w zależności od sytuacji te zmiany mogą powodować problemy ze współdziałaniem. Należy zachować ostrożność w przypadku zmiany tych wartości.

Do rozwiązania tego ograniczenia skalowania można użyć TIME_WAIT Assassination. TIME_WAIT Assassination umożliwia ponowne użycie gniazda w pewnych sytuacjach, na przykład wtedy, gdy numer sekwencyjny w pakiecie IP nowego połączenia przekracza numer sekwencyjny ostatniego pakietu z poprzedniego połączenia. W takim przypadku system operacyjny zezwoli na nawiązanie nowego połączenia (zostanie zaakceptowana Nowa SYN/ACK) i Wymuś zamknięcie poprzedniego połączenia, które było w stanie TIME_WAIT. Ta funkcja jest obsługiwana na maszynach wirtualnych z systemem Windows na platformie Azure. Aby dowiedzieć się więcej o pomocy technicznej na innych maszynach wirtualnych, należy skontaktować się z dostawcą systemu operacyjnego.

Aby dowiedzieć się więcej na temat konfigurowania ustawień TIME_WAIT TCP i zakresu portów źródłowych, zobacz [Ustawienia, które można zmodyfikować w celu zwiększenia wydajności sieci](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Czynniki sieci wirtualnej, które mogą mieć wpływ na wydajność

### <a name="vm-maximum-outbound-throughput"></a>Maksymalna przepływność wychodząca maszyny wirtualnej

Platforma Azure udostępnia różne rozmiary i typy maszyn wirtualnych, z których każdy ma różne możliwości wydajności. Jedną z tych możliwości jest przepustowość sieci (lub przepustowość), która jest mierzona w megabitach na sekundę (MB/s). Ponieważ maszyny wirtualne są hostowane na udostępnionym sprzęcie, pojemność sieci musi być współużytkowana przez maszyny wirtualne korzystające z tego samego sprzętu. Większe maszyny wirtualne są przydzielono więcej przepustowości niż mniejsze maszyny wirtualne.

Przepustowość sieci przypisana do każdej maszyny wirtualnej jest naliczana na ruch wychodzący z maszyny wirtualnej. Cały ruch sieciowy opuszczający maszynę wirtualną jest liczony do przydzielonych limitów, niezależnie od miejsca docelowego. Na przykład jeśli maszyna wirtualna ma limit 1 000 MB/s, ten limit dotyczy tego, czy ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej, czy też poza platformą Azure.

Ruch przychodzący nie jest naliczany ani ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i magazynu, które mogą mieć wpływ na możliwość przetwarzania przychodzących danych przez maszynę wirtualną.

Przyspieszona sieć została zaprojektowana w celu poprawienia wydajności sieci, w tym opóźnienia, przepływności i użycia procesora CPU. Przyspieszona sieć może zwiększyć przepływność maszyny wirtualnej, ale może to zrobić tylko do przydzieloną przepustowość maszyny wirtualnej.

Do maszyn wirtualnych platformy Azure jest dołączony co najmniej jeden interfejs sieciowy. Mogą one mieć kilka. Przepustowość przypisana do maszyny wirtualnej to suma całego ruchu wychodzącego między wszystkimi interfejsami sieciowymi podłączonymi do maszyny. Innymi słowy, przepustowość jest alokowana na maszynę wirtualną, niezależnie od tego, ile interfejsów sieciowych jest dołączonych do maszyny.

Oczekiwana przepływność wychodząca i liczba interfejsów sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych są szczegółowo opisane w obszarze [rozmiary dla maszyny wirtualnej z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby zobaczyć maksymalną przepływność, wybierz typ, taki jak **ogólnego przeznaczenia**, a następnie znajdź sekcję dotyczącą serii rozmiarów na stronie wyników (na przykład "Dv2 Series"). Dla każdej serii istnieje tabela, która zawiera specyfikacje sieci w ostatniej kolumnie zatytułowane "Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s)".

Limit przepływności ma zastosowanie do maszyny wirtualnej. Te czynniki nie mają wpływ na przepływność:

- **Liczba interfejsów sieciowych**: limit przepustowości dotyczy sumy całego ruchu wychodzącego z maszyny wirtualnej.

- **Przyspieszona sieć**: Ta funkcja może być przydatna w osiągnięciu opublikowanego limitu, ale nie powoduje zmiany limitu.

- **Miejsce docelowe ruchu**: liczba wszystkich miejsc docelowych w kierunku limitu wychodzącego.

- **Protokół**: cały ruch wychodzący przez wszystkie protokoły jest liczony do limitu.

Aby uzyskać więcej informacji, zobacz [przepustowość sieci maszyny wirtualnej](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Zagadnienia dotyczące wydajności Internetu

Jak opisano w tym artykule, czynniki w Internecie i poza kontrolą platformy Azure mogą mieć wpływ na wydajność sieci. Poniżej przedstawiono niektóre z tych czynników:

- **Opóźnienie**: w przypadku sieci pośrednich można mieć wpływ na czas błądzenia między dwoma lokalizacjami docelowymi, przez co ruch nie przyjmuje ścieżki do odległości "najkrótszej" i przez nieoptymalne ścieżki komunikacji równorzędnej.

- **Utrata pakietów**: utrata pakietów może być spowodowana przez Przeciążenie sieci, problemy ze ścieżką fizyczną i przeprowadzeniem urządzeń sieciowych.

- **Rozmiar jednostki MTU/fragmentacja**: fragmentacja na ścieżce może prowadzić do opóźnień w przybyciu danych lub w pakietach, które przechodzą z kolejności, co może mieć wpływ na dostarczanie pakietów.

Traceroute to dobre narzędzie do mierzenia charakterystyki wydajności sieci (na przykład utraty pakietów i opóźnień) w każdej ścieżce sieciowej między urządzeniem źródłowym a urządzeniem docelowym.

### <a name="network-design-considerations"></a>Zagadnienia dotyczące projektowania sieci

Wraz z zagadnieniami opisanymi wcześniej w tym artykule topologia sieci wirtualnej może mieć wpływ na wydajność sieci. Na przykład konstrukcja typu Hub i szprych polega na tym, że ruch wychodzący globalnie do jednowęzłowej sieci wirtualnej spowoduje opóźnienie sieci, co wpłynie na ogólną wydajność sieci.

Liczba urządzeń sieciowych, przez które przechodzi ruch sieciowy, może również wpływać na ogólne opóźnienia. Na przykład w przypadku projektu gwiazdy, jeśli ruch przechodzi przez wirtualne urządzenie sieciowe i urządzenie wirtualne koncentratora przed przekazaniem do Internetu, wirtualne urządzenia sieciowe mogą wprowadzać opóźnienia.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiony platformy Azure, sieci wirtualne i czas oczekiwania

Regiony platformy Azure składają się z wielu centrów danych, które znajdują się w obszarze ogólnego obszaru geograficznego. Te centra danych mogą nie być fizycznie obok siebie. W niektórych przypadkach są one oddzielone o maksymalnie 10 kilometrów. Sieć wirtualna jest nałożeniem logicznym na podstawie fizycznej sieci centrów danych platformy Azure. Sieć wirtualna nie implikuje żadnej konkretnej topologii sieci w centrum danych.

Na przykład dwie maszyny wirtualne, które znajdują się w tej samej sieci wirtualnej i podsieci, mogą znajdować się w różnych stojakach, wierszach lub nawet centrach danych. Mogą one być oddzielone za pomocą kabla światłowodowego lub przez kilometry kabla światłowodowego. Ta odmiana może wprowadzać zmienne opóźnienie (kilka milisekund) między różnymi maszynami wirtualnymi.

Geograficzne rozmieszczenie maszyn wirtualnych oraz potencjalne opóźnienia między dwiema maszynami wirtualnymi mogą mieć wpływ na konfigurację zestawów dostępności i Strefy dostępności. Jednak odległość między centrami danych w regionie jest specyficzna dla regionu i przede wszystkim ma wpływ na topologię centrum danych w regionie.

### <a name="source-nat-port-exhaustion"></a>Wyczerpanie portów źródłowego NAT

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej sieci Internet i/lub w publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje połączenie wychodzące, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania przez platformę Azure ruch powrotny dla wychodzącego przepływu można także uzyskać dostęp do prywatnego adresu IP, z którego pochodzi przepływ.

Dla każdego połączenia wychodzącego Azure Load Balancer musi zachować to mapowanie przez pewien czas. Z wielodostępnym charakterem platformy Azure utrzymywanie tego mapowania dla każdego przepływu wychodzącego dla każdej maszyny wirtualnej może być czasochłonne. W związku z tym obowiązują limity, które są ustawione na podstawie konfiguracji Virtual Network platformy Azure. Aby na przykład dokładniej określić, maszyna wirtualna platformy Azure może w danym momencie utworzyć tylko określoną liczbę połączeń wychodzących. Po osiągnięciu tych limitów maszyna wirtualna nie będzie mogła nawiązać większej liczby połączeń wychodzących.

Jednak to zachowanie można skonfigurować. Aby uzyskać więcej informacji o wykorzystaniu i wyczerpaniu portów przez przytranslatora adresów sieciowych, zobacz [ten artykuł](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mierzenie wydajności sieci na platformie Azure

Liczba maksymalnych wydajności w tym artykule dotyczy opóźnienia sieci/czasu rundy (RTT) między dwiema maszynami wirtualnymi. Ta sekcja zawiera kilka sugestii dotyczących sposobu testowania opóźnień/czasu RTT i testowania wydajności sieci maszyn wirtualnych TCP. Można dostroić i testować wartości TCP/IP i sieci omówione wcześniej przy użyciu technik opisanych w tej sekcji. Wartości Opóźnij, MTU,/rozmiaru okna można podłączyć do określonych wcześniej obliczeń i porównać wartości teoretyczne z wartościami rzeczywistymi, które obserwujesz podczas testowania.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mierzenie czasu rundy i utraty pakietów

Wydajność TCP jest intensywnie oparta na RTT i utracie pakietów. Narzędzie PING dostępne w systemach Windows i Linux zapewnia najprostszy sposób mierzenia ubytku RTT i utraty pakietów. W danych wyjściowych polecenia PING będzie wyświetlane minimalne/maksymalne/Średnie opóźnienie między źródłem a miejscem docelowym. Zostanie również wyświetlona utrata pakietów. Polecenie PING domyślnie używa protokołu ICMP. Można użyć PsPing do testowania RTT protokołu TCP. Aby uzyskać więcej informacji, zobacz [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mierzenie rzeczywistej przepływności połączenia TCP

NTttcp to narzędzie do testowania wydajności TCP maszyny wirtualnej z systemem Linux lub Windows. Można zmienić różne ustawienia protokołu TCP, a następnie przetestować korzyści przy użyciu NTttcp. Aby uzyskać więcej informacji, zobacz następujące zasoby:

- [Testowanie przepustowości/przepływności (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Narzędzie NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mierzenie rzeczywistej przepustowości maszyny wirtualnej

Za pomocą narzędzia o nazwie iPerf można testować wydajność różnych typów maszyn wirtualnych, przyspieszonej sieci i tak dalej. iPerf jest również dostępna w systemach Linux i Windows. iPerf może używać protokołu TCP lub UDP do testowania ogólnej przepływności sieci. testy przepływności TCP iPerf mają wpływ na czynniki omówione w tym artykule (takie jak opóźnienia i czas RTT). Dzięki temu protokół UDP może dać lepsze wyniki, jeśli chcesz przetestować maksymalną przepływność.

Więcej informacji można znaleźć w następujących artykułach:

- [Rozwiązywanie problemów z wydajnością sieci ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Jak zweryfikować przepływność sieci VPN do sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Wykrywanie nieefektywnych zachowań TCP

W przypadku przechwytywania pakietów klienci platformy Azure mogą zobaczyć pakiety TCP z flagami TCP (Work, DUP ACK, retransmisja i szybkie ponowne przesyłanie), które mogą wskazywać na problemy z wydajnością sieci. Te pakiety wskazują na nieefektywność sieci, które wynikają z utraty pakietów. Jednak utrata pakietów nie musi być spowodowana przez problemy z wydajnością platformy Azure. Problemy z wydajnością mogą być spowodowane problemami z aplikacjami, problemami z systemem operacyjnym lub innymi problemami, które mogą nie być bezpośrednio związane z platformą Azure.

Należy również pamiętać, że niektóre retransmisje i duplikaty potwierdzeń są normalne w sieci. Protokoły TCP zostały skompilowane jako niezawodne. Dowody tych pakietów TCP w przechwyceniu pakietu nie zawsze wskazują na problem z siecią systemową, o ile nie są zbyt duże.

Mimo że te typy pakietów są wskazywane, że przepływność TCP nie osiąga maksymalnej wydajności, z powodów opisanych w innych sekcjach tego artykułu.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak dostrajasz wydajność protokołu TCP/IP dla maszyn wirtualnych platformy Azure, możesz zapoznać się z innymi kwestiami dotyczącymi [planowania sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) lub [Dowiedz się więcej na temat łączenia i konfigurowania sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/).
