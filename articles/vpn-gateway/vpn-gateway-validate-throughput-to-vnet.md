---
title: Weryfikowanie przepływności sieci VPN do Microsoft Azure Virtual Network
description: Ten artykuł pomaga sprawdzić przepływność sieci z zasobów lokalnych do maszyny wirtualnej platformy Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: d2347c0688ca58698831019a193d03fe2c6721e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398511"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Jak zweryfikować przepływność sieci VPN do sieci wirtualnej

Połączenie bramy sieci VPN umożliwia nawiązanie bezpiecznej łączności między Virtual Networkami w ramach platformy Azure i lokalnej infrastruktury INFORMATYCZNej.

W tym artykule pokazano, jak zweryfikować przepływność sieci z zasobów lokalnych do maszyny wirtualnej platformy Azure.

> [!NOTE]
> Ten artykuł ma na celu ułatwienie diagnozowania i rozwiązywania typowych problemów. Jeśli nie możesz rozwiązać problemu, korzystając z poniższych informacji, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Omówienie

Połączenie bramy sieci VPN obejmuje następujące składniki:

* Lokalne urządzenie sieci VPN (Wyświetlanie listy [zweryfikowanych urządzeń sieci VPN](vpn-gateway-about-vpn-devices.md#devicetable)).
* Publiczny Internet
* Brama sieci VPN platformy Azure
* Maszyna wirtualna platformy Azure

Na poniższym diagramie przedstawiono logiczne połączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem sieci VPN.

![Połączenie logiczne sieci klienta z siecią MSFT przy użyciu sieci VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Oblicz maksymalną oczekiwaną wartość ruchu przychodzącego/wychodzącego

1. Ustal wymagania dotyczące przepływności linii bazowej aplikacji.
1. Ustal limity przepływności bramy sieci VPN platformy Azure. Aby uzyskać pomoc, zobacz sekcję "jednostki SKU bramy" tematu [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Określ [wskazówki dotyczące przepływności maszyn wirtualnych platformy Azure](../virtual-machines/sizes.md) dla rozmiaru maszyny wirtualnej.
1. Określ przepustowość usługodawcy internetowego (ISP).
1. Oblicz oczekiwaną przepływność, przyjmując najmniejszą przepustowość maszyny wirtualnej, VPN Gateway lub usługodawcy internetowego; który jest mierzony w megabitach na sekundę (/) podzielony przez osiem (8).

Jeśli obliczona przepływność nie spełnia wymagań dotyczących przepływności linii bazowej aplikacji, należy zwiększyć przepustowość zasobu, która została zidentyfikowana jako wąskie gardło. Aby zmienić rozmiar VPN Gateway platformy Azure, zobacz [Zmiana jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby zmienić rozmiar maszyny wirtualnej, zobacz [zmiana rozmiaru maszyny](../virtual-machines/windows/resize-vm.md)wirtualnej. Jeśli nie masz oczekiwanej przepustowości Internetu, możesz również skontaktować się z USŁUGODAWCą internetowym.

> [!NOTE]
> VPN Gateway przepływność jest zagregowana z wszystkich połączeń Site-to-Site\VNET-to-VNET lub punkt-lokacja.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Sprawdzanie przepływności sieci przy użyciu narzędzi do oceny wydajności

Ta weryfikacja powinna być wykonywana w godzinach poza godzinami szczytu, podobnie jak nasycenie przepływności tunelu VPN podczas testowania nie daje dokładnych wyników.

Narzędzie, którego używamy do tego testu, to iPerf, który działa w systemach Windows i Linux oraz ma zarówno tryb klienta, jak i serwera. Jest ona ograniczona do 3Gbps dla maszyn wirtualnych z systemem Windows.

To narzędzie nie wykonuje żadnych operacji odczytu/zapisu na dysku. Generuje on wyłącznie wygenerowany przez siebie ruch TCP z jednego końca do drugiego. Generuje on statystyki na podstawie eksperymentów, które mierzą dostępną przepustowość między węzłami klienta i serwera. Podczas testowania między dwoma węzłami jeden węzeł pełni rolę serwera, a drugi węzeł pełni rolę klienta. Po zakończeniu tego testu zalecamy odwrócenie ról węzłów w celu przetestowania przepływności przekazywania i pobierania na obu węzłach.

### <a name="download-iperf"></a>Pobierz iPerf

Pobierz [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Aby uzyskać szczegółowe informacje, zobacz [dokumentację iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Produkty innych firm omówione w tym artykule są wytwarzane przez firmy niezależne od firmy Microsoft. Firma Microsoft nie udziela żadnych gwarancji, dorozumianych ani żadnego innego rodzaju, w odniesieniu do wydajności ani niezawodności tych produktów.

### <a name="run-iperf-iperf3exe"></a>Uruchom iPerf (iperf3.exe)

1. Włącz regułę sieciowej grupy zabezpieczeń/ACL zezwalającą na ruch (na potrzeby testowania publicznego adresu IP na maszynie wirtualnej platformy Azure).

1. Na obu węzłach Włącz wyjątek zapory dla portu 5001.

   **System Windows:** Uruchom następujące polecenie jako administrator:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Aby usunąć regułę po zakończeniu testowania, uruchom następujące polecenie:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   System **Linux systemu Azure:** Obrazy systemu Linux na platformie Azure mają przypisaną zaporę. Jeśli aplikacja nasłuchuje na porcie, ruch jest dozwolony w. Chronione obrazy niestandardowe mogą wymagać jawnego otwierania portów. Typowe zapory systemu operacyjnego Linux obejmują `iptables` , `ufw` , lub `firewalld` .

1. W węźle serwer przejdź do katalogu, w którym wyodrębniono iperf3.exe. Następnie uruchom program iPerf w trybie serwera i ustaw go, aby nasłuchiwać na porcie 5001 jako następujące polecenia:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 można dostosować do konta dla określonych ograniczeń zapory w środowisku.

1. W węźle klienta przejdź do katalogu, w którym wyodrębniono narzędzie Iperf, a następnie uruchom następujące polecenie:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klient przekieruje 30-sekundowy ruch na port 5001 do serwera. Flaga "-P" wskazuje, że firma Microsoft 32 udostępnia liczbę jednoczesnych połączeń z węzłem serwera.

   Na poniższym ekranie przedstawiono dane wyjściowe z tego przykładu:

   ![Dane wyjściowe](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. OBOWIĄZKOWE Aby zachować wyniki testów, uruchom następujące polecenie:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Po wykonaniu poprzednich kroków wykonaj te same czynności z cofniętymi rolami, aby węzeł serwera był teraz węzłem klienta i na odwrót.

> [!Note]
> Iperf nie jest jedynym narzędziem. [NTTTCP jest alternatywnym rozwiązaniem do testowania](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem Windows

### <a name="load-latteexe-onto-the-vms"></a>Załaduj Latte.exe na maszynach wirtualnych

Pobierz najnowszą wersję programu [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Rozważ umieszczenie Latte.exe w osobnym folderze, na przykład `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Zezwalaj na Latte.exe za pomocą zapory systemu Windows

Na odbiorniku Utwórz regułę zezwalania w zaporze systemu Windows, aby zezwolić na odbieranie Latte.exe ruchu. Najłatwiej jest zezwolić na cały program Latte.exe według nazwy, a nie zezwalać na ruch przychodzący określonych portów TCP.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Zezwalaj na Latte.exe za pomocą zapory systemu Windows, takiej jak ta

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Na przykład, jeśli skopiowano latte.exe do folderu "c:\Tools", będzie to polecenie

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Testy opóźnienia uruchamiania

Uruchom latte.exe w ODBIORNIKu (Uruchom polecenie z polecenia CMD, a nie z programu PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Wszystkie iteracje 65 000 dla są wystarczająco długie, aby zwracały reprezentatywne wyniki.

Wszystkie dostępne numery portów są dokładne.

Jeśli maszyna wirtualna ma adres IP 10.0.0.4, będzie wyglądać następująco

`latte -c -a 10.0.0.4:5005 -i 65100`

Rozpocznij latte.exe na NADAWCy (Uruchom z polecenia CMD, a nie z programu PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Wyniki polecenia są takie same jak w odniesieniu do odbiorcy z wyjątkiem dodania wartości "-c", aby wskazać, że jest to "klient" lub nadawca

`latte -c -a 10.0.0.4:5005 -i 65100`

Poczekaj na wyniki. W zależności od tego, jak daleko są maszyny wirtualne, może to potrwać kilka minut. Rozważ rozpoczęcie od mniejszej liczby iteracji do przetestowania pod kątem sukcesu przed uruchomieniem dłuższych testów.

## <a name="test-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem Linux

Użyj [SockPerf](https://github.com/mellanox/sockperf) do testowania maszyn wirtualnych.

### <a name="install-sockperf-on-the-vms"></a>Instalowanie SockPerf na maszynach wirtualnych

Na maszynach wirtualnych z systemem Linux (NADAWCy i odbiornik) Uruchom następujące polecenia, aby przygotować SockPerf na maszynach wirtualnych:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL — Instalowanie usługi GIT i innych przydatnych narzędzi

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu — Instalowanie usługi GIT i innych przydatnych narzędzi

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash — wszystko

Z wiersza polecenia bash (założono, że zainstalowano Git)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Działa wolniej, może potrwać kilka minut

`make`

Szybka instalacja

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Uruchom SockPerf na maszynach wirtualnych

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Przykładowe polecenia po instalacji. Serwer/odbiornik — adres IP serwera zakłada, że jest 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient — zakłada, że adres IP serwera to 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Upewnij się, że podczas testowania przepływności między maszyną wirtualną a bramą nie ma przeskoków pośrednich (np. urządzenia wirtualnego).
> Jeśli istnieją słabe wyniki (pod względem ogólnej przepływności) pochodzące z testów iPERF/NTTTCP powyżej, zapoznaj się z poniższym artykułem, aby zrozumieć kluczowe czynniki związane z potencjalnymi przyczynami problemu: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

W szczególności analiza śladów przechwytywania pakietów (Wireshark/Monitor sieci) zebranych równolegle z klienta i serwera podczas tych testów będzie pomocna w ocenie złej wydajności. Te ślady mogą obejmować utratę pakietów, duże opóźnienia i rozmiar jednostki MTU. Fragmentacja, okno TCP 0, fragmenty poza kolejnością i tak dalej.

## <a name="address-slow-file-copy-issues"></a>Problemy z kopiowaniem wolnych plików

Nawet jeśli ogólna przepływność oceniona przy poprzednich krokach (iPERF/NTTTCP/itp.) była dobra, może wystąpić powolne polecenie ping do pliku podczas korzystania z Eksploratora Windows lub przeciąganie i upuszczanie przez sesję RDP. Ten problem jest zwykle spowodowany przez jeden lub oba z następujących czynników:

* Aplikacje do kopiowania plików, takie jak Eksplorator Windows i RDP, nie używają wielu wątków podczas kopiowania plików. Aby uzyskać lepszą wydajność, należy użyć wielowątkowej aplikacji do kopiowania plików, takiej jak [RichCopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) , do kopiowania plików przy użyciu 16 lub 32 wątków. Aby zmienić numer wątku dla kopiowania plików w RichCopy, kliknij pozycję **Akcja**  >  **Kopiuj opcje**  >  **pliku Kopiuj**.

   ![Problemy związane z kopiowaniem plików](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nie wszystkie aplikacje działają tak samo, jak nie wszystkie wątki są wykorzystywane przez wszystkie aplikacje/procesy. Po uruchomieniu testu można zobaczyć, że niektóre wątki są puste i nie będą zapewniać dokładnych wyników przepływności.
   > Aby sprawdzić wydajność transferu plików aplikacji, należy użyć wielowątkowości przez zwiększenie liczby wątków w pomyślnym lub zmniejszeniu, aby znaleźć optymalną przepływność aplikacji lub transferu plików.

* Niewystarczająca szybkość odczytu/zapisu dysku maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Zewnętrzny interfejs połączony z urządzeniem lokalnym

Wymieniono podsieci zakresów lokalnych, które mają być dostępne na platformie Azure za pośrednictwem sieci VPN w bramie sieci lokalnej. Jednocześnie Zdefiniuj przestrzeń adresową sieci wirtualnej na platformie Azure z urządzeniem lokalnym.

* **Brama oparta na trasach**: zasady lub selektor ruchu dla sieci VPN opartych na trasach są skonfigurowane jako dowolne (lub symbole wieloznaczne).

* **Brama oparta na zasadach**: sieci VPN oparte na zasadach szyfrują pakiety i kierują je bezpośrednio przez tunele IPSec na podstawie kombinacji prefiksów adresów między siecią lokalną i sieci wirtualnej platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji VPN.

* Połączenia **UsePolicyBasedTrafficSelector** : ("UsePolicyBasedTrafficSelectors" do $true połączenia skonfiguruje bramę sieci VPN platformy Azure w celu połączenia z ZAPORĄ sieci VPN opartą na zasadach lokalnie. W przypadku włączenia usługi PolicyBasedTrafficSelectors należy upewnić się, że urządzenie sieci VPN ma pasujące selektory ruchu zdefiniowane ze wszystkimi kombinacjami prefiksów sieci lokalnej (bramy sieć lokalna) do i z prefiksów sieci wirtualnej platformy Azure, a nie z dowolnego miejsca.

Niewłaściwa konfiguracja może prowadzić do częstego rozłączenia w tunelu, porzucania pakietów, złej przepływności i opóźnień.

## <a name="check-latency"></a>Opóźnienie sprawdzania

Opóźnienie można sprawdzić przy użyciu następujących narzędzi:

* WinMTR
* TCPTraceroute
* `ping` i `psping` (te narzędzia mogą zapewnić dobre oszacowanie RTT, ale nie mogą być używane we wszystkich przypadkach).

![Opóźnienie sprawdzania](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Jeśli zauważysz duże opóźnienia w dowolnych przeskokach przed wprowadzeniem szkieletu sieci firmy Microsoft, możesz chcieć kontynuować dalsze badania u usługodawcy internetowego.

Jeśli duże, nietypowe opóźnienia są zauważalne z przeskoków w ciągu "msn.net", skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania dalszych badań.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i uzyskać pomoc, zapoznaj się z następującym łączem:

* [pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
