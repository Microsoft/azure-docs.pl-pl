---
title: 'Rozwiązywanie problemów z wydajnością łączy sieciowych: Azure'
description: Ta strona zawiera ustandaryzowaną metodę testowania wydajności łączy sieciowych platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027472"
---
# <a name="troubleshooting-network-performance"></a>Rozwiązywanie problemów z wydajnością sieci
## <a name="overview"></a>Omówienie
Platforma Azure zapewnia stabilny i szybki sposób łączenia się z siecią lokalną z platformą Azure. Metody, takie jak połączenie typu lokacja-lokacja między siecią VPN i usługą ExpressRoute, są z sukcesem używane przez klientów, którzy w swoich dużych i małych firmach stosują platformę Azure. Ale co się stanie, gdy wydajność nie spełni oczekiwań lub poprzedniego środowiska? Ten artykuł może pomóc w standaryzacji sposobu testowania i określania planu bazowego określonego środowiska.

Dowiesz się, jak łatwo i spójnie Przetestuj opóźnienia sieci i przepustowość między dwoma hostami. Poznasz również kilka porad na temat sposobów przeglądania sieci platformy Azure w celu ułatwienia izolowania punktów problemu. Omawiany skrypt i narzędzia programu PowerShell wymagają dwóch hostów w sieci (na każdym końcu testowanego łącza). Jeden host musi być serwerem lub systemem Windows, a drugi może być systemem Windows lub Linux. 

>[!NOTE]
>Podejście do rozwiązywania problemów, narzędzia i używane metody są preferencjami osobistymi. W tym dokumencie opisano metody i często wykonywane narzędzia. Twoje podejście będzie prawdopodobnie inne, ale nie ma żadnych problemów z różnymi podejściami do rozwiązywania problemów. Jednakże jeśli nie masz ustalonego podejścia, ten dokument może rozpocząć pracę nad ścieżką do tworzenia własnych metod, narzędzi i preferencji do rozwiązywania problemów z siecią.
>
>

## <a name="network-components"></a>Składniki sieci
Zanim przeszukiwanie stosów się do rozwiązywania problemów, Omówmy niektóre typowe warunki i składniki. W tej dyskusji zawarto informacje na temat każdego składnika w łańcuchu kompleksowym, który umożliwia łączność na platformie Azure.
![1][1]

Na najwyższym poziomie opisano trzy główne domeny routingu sieciowego;

- Sieć platformy Azure (niebieska chmura po prawej)
- Internet lub WAN (zielona chmura w środku)
- Sieć firmowa (chmura brzoskwiń po lewej stronie)

Analizując diagram od prawej do lewej, Omówmy krótko każdy składnik:
 - **Maszyna wirtualna** — serwer może mieć wiele kart sieciowych. Upewnij się, że wszystkie trasy statyczne, trasy domyślne i ustawienia systemu operacyjnego są wysyłane i odbierają ruch w żądany sposób. Ponadto każda jednostka SKU maszyny wirtualnej ma ograniczenie przepustowości. Jeśli używasz mniejszej jednostki SKU maszyny wirtualnej, ruch jest ograniczony przez przepustowość dostępną dla karty sieciowej. Zwykle używamy DS5v2 do testowania (a następnie usuwania po zakończeniu testowania, aby zaoszczędzić pieniądze), aby zapewnić odpowiednią przepustowość na maszynie wirtualnej.
 - **Karta sieciowa** — upewnij się, że znasz prywatny adres IP przypisany do danej karty sieciowej.
 - **Karta sieciowa sieciowej grupy zabezpieczeń** — na poziomie karty sieciowej mogą być stosowane określone sieciowych grup zabezpieczeń, upewnij się, że zestaw reguł sieciowej grupy zabezpieczeń jest odpowiedni dla ruchu, który próbujesz przekazać. Na przykład upewnij się, że porty 5201 dla iPerf, 3389 dla protokołu RDP lub 22 dla SSH są otwarte, aby umożliwić przekazywanie ruchu testowego.
 - **Podsieć VNET** — karta sieciowa jest przypisana do określonej podsieci. Upewnij się, że masz pewność, która z nich i reguły skojarzone z tą podsiecią.
 - **Sieciowej grupy zabezpieczeń podsieci** — podobnie jak karta sieciowa, sieciowych grup zabezpieczeń można również stosować w podsieci. Upewnij się, że zestaw reguł sieciowej grupy zabezpieczeń jest odpowiedni dla ruchu, który próbujesz przekazać. (W przypadku ruchu przychodzącego do karty sieciowej sieciowej grupy zabezpieczeń jest stosowana najpierw karta sieciowa sieciowej grupy zabezpieczeń. Gdy ruch jest wychodzący z maszyny wirtualnej, karta sieciowa sieciowej grupy zabezpieczeń stosuje się najpierw do sieciowej grupy zabezpieczeń podsieci.
 - **UDR podsieci** — trasy User-Defined mogą kierować ruch do pośredniego skoku (na przykład zapory lub modułu równoważenia obciążenia). Upewnij się, że dla ruchu istnieje UDR. Jeśli tak, zapoznaj się z miejscem, w którym się znajduje, i przeniesieniu do ruchu w następnym przeskoku. Na przykład Zapora może przekazać ruch i odmówić innego ruchu między tymi samymi dwoma hostami.
 - **Podsieć bramy/sieciowej grupy zabezpieczeń/UDR** — podobnie jak w przypadku podsieci maszyny wirtualnej, podsieć bramy może mieć sieciowych grup zabezpieczeń i UDR. Upewnij się, że wiesz, czy są tam i jakie efekty mają w ruchu.
 - **Brama sieci wirtualnej (ExpressRoute)** — po włączeniu komunikacji równorzędnej (ExpressRoute) lub VPN nie ma wielu ustawień, które mogą mieć wpływ na sposób lub trasy ruchu. Jeśli Brama sieci wirtualnej jest podłączona do wielu obwodów usługi ExpressRoute lub tuneli VPN, należy pamiętać o ustawieniach wagi połączeń. Waga połączenia wpływa na preferencję połączenia i określa ścieżkę, jaką zajmuje ruch.
 - **Filtr trasy** (niepokazywany) — filtr trasy jest niezbędny w przypadku korzystania z komunikacji równorzędnej firmy Microsoft za pośrednictwem usługi ExpressRoute. Jeśli nie otrzymujesz żadnych tras, sprawdź, czy filtr trasy jest skonfigurowany i poprawnie stosowany do obwodu.

W tym momencie jesteś częścią łącza sieci WAN. Ta domena routingu może być dostawcą usług, firmową siecią WAN lub Internetem. Istnieje wiele przeskoków, urządzeń i firm związanych z tymi łączami, co może utrudnić rozwiązywanie problemów. Przed badaniem przeskoków między programem należy najpierw wykluczyć zarówno platformę Azure, jak i sieci firmowe.

Na powyższym diagramie po lewej stronie znajduje się sieć firmowa. W zależności od wielkości firmy ta domena routingu może być kilkoma urządzeniami sieciowymi między nimi a siecią WAN lub wieloma warstwami urządzeń w sieci kampusowej/firmowej.

Złożoność tych trzech różnych środowisk sieciowych wysokiego poziomu. Często można zacząć od krawędzi i próbować pokazać, gdzie wydajność jest dobra i gdzie ulega obniżeniu. Takie podejście może pomóc w zidentyfikowaniu domeny routingu problemu trzech. Następnie możesz skoncentrować się na rozwiązywaniu problemów w tym konkretnym środowisku.

## <a name="tools"></a>Narzędzia
Większość problemów z siecią można analizować i izolować przy użyciu podstawowych narzędzi, takich jak ping i traceroute. Jest to rzadki sposób, aby przeanalizować pakiet jako analizę pakietów przy użyciu narzędzi takich jak Wireshark. 

Aby pomóc w rozwiązywaniu problemów, zestaw narzędzi Azure Connectivity Toolkit (AzureCT) został opracowany w celu umieszczenia niektórych z tych narzędzi w łatwym pakiecie. W przypadku testowania wydajności narzędzia takie jak iPerf i PSPing mogą zapewnić informacje o sieci. iPerf to powszechnie używane narzędzie do podstawowych testów wydajności i jest dość łatwe w użyciu. PSPing to narzędzie ping opracowane przez program SysInternals. PSPing może wykonywać wywołania ping protokołu ICMP i TCP w celu uzyskania dostępu do hosta zdalnego. Oba te narzędzia są lekkie i są "zainstalowane" po prostu dodając polecenie ping do pliku do katalogu na hoście.

Te narzędzia i metody są opakowane do modułu programu PowerShell (AzureCT), który można zainstalować i użyć.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT — zestaw narzędzi Azure Connectivity Toolkit
Moduł AzureCT PowerShell ma dwie składniki [testowania dostępności][Availability Doc] i [testowania wydajności][Performance Doc]. Ten dokument jest objęty wyłącznie testami wydajnościowymi, więc pozwala skupić się na dwóch poleceniach wydajności łącza w tym module programu PowerShell.

Istnieją trzy podstawowe kroki umożliwiające korzystanie z tego zestawu narzędzi do testowania wydajności. 1) zainstaluj moduł programu PowerShell, 2) zainstalowanie aplikacji obsługujących iPerf i PSPing 3) uruchomienie testu wydajnościowego.

1. Instalowanie modułu programu PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    To polecenie umożliwia pobranie modułu PowerShell i zainstalowanie go lokalnie.

2. Instalowanie aplikacji pomocniczych
    ```powershell
    Install-LinkPerformance
    ```
    To polecenie AzureCT instaluje iPerf i PSPing w nowym katalogu "C:\ACTTools", a także otwiera porty zapory systemu Windows w celu zezwalania na ruch ICMP i port 5201 (iPerf).

3. Uruchom test wydajnościowy

    Najpierw na hoście zdalnym należy zainstalować i uruchomić program iPerf w trybie serwera. Upewnij się również, że host zdalny nasłuchuje na 3389 (RDP dla systemu Windows) lub 22 (SSH dla Linux) i zezwalając na ruch na porcie 5201 dla iPerf. Jeśli host zdalny jest systemem Windows, zainstaluj AzureCT i uruchom polecenie Install-LinkPerformance. Polecenie spowoduje skonfigurowanie iPerf oraz reguł zapory wymaganych do pomyślnego uruchomienia iPerf w trybie serwera. 
    
    Gdy maszyna zdalna jest gotowa, Otwórz program PowerShell na maszynie lokalnej i uruchom test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    To polecenie uruchamia serię współbieżnych testów obciążenia i opóźnienia, aby pomóc oszacować przepustowość i opóźnienie połączenia sieciowego.

4. Przejrzyj dane wyjściowe testów

    Format danych wyjściowych programu PowerShell wygląda podobnie do:

    ![4][4]

    Szczegółowe wyniki wszystkich testów iPerf i PSPing znajdują się w poszczególnych plikach tekstowych w katalogu narzędzi AzureCT w "C:\ACTTools."

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli test wydajności nie daje oczekiwanych wyników, ustalenie przyczyny powinno być krokiem krok po kroku. Mając na względzie liczbę składników w ścieżce, systematyczne podejście zapewni szybszą ścieżkę do rozdzielczości niż przeskoczyć. Poprzez systematyczne Rozwiązywanie problemów można zapobiec niepożądanym testom wiele razy.

>[!NOTE]
>Oto scenariusz dotyczący wydajności, a nie problem z łącznością. Kroki będą różne, jeśli ruch nie przeprowadził się wcale.
>
>

Najpierw założenie założeń. Czy Twoje oczekiwania są uzasadnione? Na przykład jeśli masz obwód ExpressRoute 1 GB/s i 100 ms opóźnienia. Nie jest uzasadnione, aby oczekiwać, że pełny 1 GB/s ruch ma cechy wydajności TCP przez duże opóźnienia. Więcej informacji na temat założeń dotyczących wydajności zawiera [sekcja References](#references) .

Następnie zaleca się rozpoczęcie od krawędzi domen routingu i próba odizolowania problemu do jednej głównej domeny routingu. Możesz zacząć od sieci firmowej, sieci WAN lub sieci platformy Azure. Ludzie często polecenia Blame "czarne pole" w ścieżce. Gdy blaming czarne pole jest łatwe, może znacznie opóźnić rozwiązanie. Szczególnie jeśli problem znajduje się w obszarze, w którym można wprowadzać zmiany w celu rozwiązania problemu. Przed przystąpieniem do dostawcy usług lub usługodawcy internetowego upewnij się, że masz należytą staranność.

Po zidentyfikowaniu głównej domeny routingu, która wydaje się zawierać problem, należy utworzyć diagram danego obszaru. Rysując diagram w tablicy, notatniku lub programie Visio, można w sposób metodyczny przeszukiwać i izolować problem. Możesz planować punkty testowania i aktualizować mapę w miarę jasnego obszaru lub Dig się bardziej szczegółowo w miarę postępów testów.

Teraz, gdy masz diagram, zacznij podzielić sieć na segmenty i zawęzić problem. Dowiedz się, gdzie działa i gdzie nie. Kontynuuj przesuwanie punktów testowych, aby odizolować do składnika powodującego problemy.

Nie zapomnij też, aby przyjrzeć się innym warstwom modelu OSI. Łatwo jest skupić się na sieci i warstwach 1-3 (fizycznych, danych i sieciowych), ale te problemy mogą również znajdować się w warstwie 7 w warstwie aplikacji. Zachowaj otwarte zdanie i sprawdź założenia.

## <a name="advanced-expressroute-troubleshooting"></a>Zaawansowane rozwiązywanie problemów z ExpressRoute
Jeśli nie masz pewności, gdzie rzeczywiście jest krawędzią chmury, Izolowanie składników platformy Azure może być wyzwaniem. Gdy ExpressRoute jest używany, Edge jest składnik sieci o nazwie Microsoft Enterprise Edge (MSEE). W **przypadku korzystania z ExpressRoute**, MSEE to pierwszy punkt kontaktu z siecią firmy Microsoft i ostatni przeskok podczas opuszczania sieci firmy Microsoft. Podczas tworzenia obiektu połączenia między bramą sieci wirtualnej i obwodem usługi ExpressRoute w rzeczywistości nastąpi połączenie z MSEE. Rozpoznawanie MSEE jako pierwszego lub ostatniego przeskoku w zależności od kierunku, w jaki ruch jest decydujący dla izolowania problemu z siecią platformy Azure. Wiedza o tym, jaki kierunek będzie się pojawić w przypadku, gdy problem występuje na platformie Azure lub w sieci firmowej. 

![2][2]

>[!NOTE]
> Zwróć uwagę, że MSEE nie znajduje się w chmurze platformy Azure. ExpressRoute jest w rzeczywistości na granicy sieci firmy Microsoft, która nie jest faktycznie na platformie Azure. Po nawiązaniu połączenia z usługą ExpressRoute z MSEEem nastąpi połączenie z siecią firmy Microsoft, a następnie możesz przejść do dowolnej z usług w chmurze, takich jak Microsoft 365 (z usługą komunikacji równorzędnej firmy Microsoft) lub na platformę Azure (z usługą komunikacji równorzędnej i/lub firmy Microsoft).
>
>

Jeśli dwa sieci wirtualnych są podłączone do tego **samego** obwodu usługi ExpressRoute, można wykonać serię testów w celu wyizolowania problemu na platformie Azure.
 
### <a name="test-plan"></a>Plan testu
1. Uruchom test Get-LinkPerformance między VM1 i VM2. Ten test zawiera szczegółowe informacje o tym, czy problem jest lokalny, czy nie. Jeśli ten test generuje akceptowalne wyniki opóźnienia i przepustowości, można oznaczyć lokalną sieć sieci wirtualnej jako dobra.
2. Przy założeniu, że lokalny ruch sieci wirtualnej jest dobry, Uruchom test Get-LinkPerformance między VM1 i VM3. Ten test wykonuje połączenie za pomocą sieci firmy Microsoft z MSEE i z powrotem na platformę Azure. Jeśli ten test generuje akceptowalne opóźnienia i przepustowość, można oznaczyć sieć platformy Azure jako dobra.
3. Jeśli usługa Azure została wykluczona, można wykonać podobną sekwencję testów w sieci firmowej. Jeśli również testuje dobrze, czas na skontaktowanie się z dostawcą usług lub usługodawcą internetowym w celu zdiagnozowania połączenia z siecią WAN. Przykład: Uruchom ten test między dwoma oddziałami lub między biurem a serwerem centrum danych. W zależności od używanego testowania Znajdź punkty końcowe, takie jak serwery i komputery klienckie, które mogą korzystać z tej ścieżki.

>[!IMPORTANT]
> Jest to ważne, aby dla każdego testu oznaczyć godzinę uruchomienia testu i zarejestrować wyniki w wspólnej lokalizacji (podobnie jak w przypadku programu OneNote lub Excel). Każdy przebieg testowy powinien mieć identyczne dane wyjściowe, aby można było porównać wyniki wynikowe między przebiegami testów i nie mieć "dziur" w danych. Spójność wielu testów jest podstawową przyczyną rozwiązywania problemów przy użyciu AzureCT. Magic nie znajduje się w dokładnych scenariuszach obciążenia, które są uruchamiane, ale zamiast tego *magiczna* jest fakt, że otrzymujemy *spójny test i dane wyjściowe* z każdego testu. Rejestrowanie czasu i posiadanie spójnych danych każdego pojedynczego czasu jest szczególnie przydatne w przypadku późniejszego wyszukania problemu. Sumienni z początkową kolekcją danych, aby uniknąć godzin przetestowania tych samych scenariuszy (w tym trudnej liczbie lat temu).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problem jest izolowany, co teraz?
Im bardziej izolowany problem, tym szybciej można znaleźć rozwiązanie. Kiedyś dojdziesz do punktu, w którym nie możesz kontynuować rozwiązywania problemów. Na przykład, zobaczysz link przez dostawcę usług, który przeniesie przeskoki do Europy, ale oczekujesz, że ścieżka pozostanie wszystkie w Azji. W tym momencie należy zaangażować kogoś o pomoc. Użytkownik, który pyta, zależy od domeny routingu, do której wyizolowano problem. Jeśli możesz zawęzić ją do określonego składnika, który będzie jeszcze lepszy.

W przypadku problemów z siecią firmową wewnętrzny dział IT lub dostawca usług może pomóc w konfiguracji urządzenia lub naprawie sprzętowej.

W przypadku sieci WAN udostępnianie wyników testów u dostawcy usług lub usługodawcy internetowego pomoże Ci rozpocząć pracę. Takie działanie pozwoli również uniknąć duplikowania tej samej już wykonanej pracy. Nie jest to możliwe, jeśli chcą zweryfikować swoje wyniki. "Zaufanie, ale weryfikacja" to dobre motto w przypadku rozwiązywania problemów w oparciu o wyniki raportowane przez inne osoby.

Po rozwiązaniu problemu z platformą Azure, gdy jest to możliwe, należy zapoznać się z [dokumentacją sieci platformy Azure][Network Docs] , a następnie w razie potrzeby [otworzyć bilet pomocy technicznej][Ticket Link].

## <a name="references"></a>Odwołania
### <a name="latencybandwidth-expectations"></a>Oczekiwania na opóźnienia/przepustowość
>[!TIP]
> Opóźnienie geograficzne (mile lub kilometry) między punktami końcowymi, które są testowane, to największy składnik opóźnienia. Chociaż występuje opóźnienie sprzętu (składniki fizyczne i wirtualne, liczba przeskoków itp.), Geografia została sprawdzona jako największy składnik ogólnego opóźnienia podczas pracy z połączeniami WAN. Należy również pamiętać, że odległość jest odległość między uruchomieniem włókna a odległością od osi liniowej lub mapy drogowej. Ta odległość jest trudno uzyskać z dokładnością niezwykle. W związku z tym ogólnie używamy kalkulatora odległości miast w Internecie i wiesz, że ta metoda jest miarowo niedokładnej miary, ale jest wystarczająca do ustawienia ogólnego oczekiwania.
>
>

Mam konfigurację ExpressRoute w Seattle, Waszyngton w USA. W poniższej tabeli przedstawiono czas oczekiwania i przetestowaną przepustowość do różnych lokalizacji platformy Azure. Oceniono odległość geograficzną między każdym końcem testu.

Konfiguracja testu:
 - Serwer fizyczny z systemem Windows Server 2016 z kartą sieciową 10 GB/s podłączony do obwodu ExpressRoute.
 - Obwód ExpressRoute 10Gbps Premium w lokalizacji zidentyfikowanej z włączonym prywatną usługą komunikacji równorzędnej.
 - Sieć wirtualna platformy Azure z bramą UltraPerformance w określonym regionie.
 - Maszyna wirtualna w DS5v2 z systemem Windows Server 2016 w sieci wirtualnej. Maszyna wirtualna nie została przyłączona do domeny, została utworzona na podstawie domyślnego obrazu platformy Azure (Brak optymalizacji lub dostosowania) z zainstalowanym programem AzureCT.
 - Wszystkie testy używają AzureCT Get-LinkPerformance polecenie z 5-minutowym testem obciążenia dla każdego z sześciu przebiegów testowych. Na przykład:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Przepływ danych dla każdego testu miał wpływ na ruch z lokalnego serwera fizycznego (iPerf Client w Seattle) do maszyny wirtualnej platformy Azure (iPerf serwer w wymienionym regionie platformy Azure).
 - Dane kolumny "opóźnienie" pochodzą z testu obciążenia (test opóźnienia TCP bez uruchomionej iPerf).
 - Dane kolumny "Max Bandwidth" pochodzą z 16 testów obciążenia przepływu TCP z rozmiarem okna 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Wyniki opóźnienia/przepustowości
>[!IMPORTANT]
> Te liczby są przeznaczone wyłącznie do celów ogólnych odniesienia. Wiele czynników wpływa na opóźnienia, a chociaż te wartości są zwykle spójne z upływem czasu, warunki na platformie Azure lub w sieci dostawców usług mogą wysyłać ruch przez różne ścieżki w dowolnym momencie, co może mieć wpływ na opóźnienia i przepustowość. Ogólnie rzecz biorąc, skutki tych zmian nie powodują znaczących różnic.
>
>

| ExpressRoute<br/>Lokalizacja|Azure<br/>Region (Region) | Szacowany<br/>Odległość (km) | Opóźnienie|1 sesja<br/>Szerokość pasma | Maksimum<br/>Szerokość pasma |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | Zachodnie stany USA 2        |    191 km |   5 ms | 262,0 MB/s |  3,74 Gbits/s |
| Seattle | Zachodnie stany USA          |  1 094 km |  18 MS |  82,3 MB/s |  3,70 Gbits/s |
| Seattle | Central US       |  2 357 km |  40 MS |  38,8 MB/s |  2,55 Gbits/s |
| Seattle | South Central US |  2 877 km |  51 MS |  30,6 MB/s |  2,49 Gbits/s |
| Seattle | Północno-środkowe stany USA |  2 792 km |  55 MS |  27,7 MB/s |  2,19 Gbits/s |
| Seattle | Wschodnie stany USA 2        |  3 769 km |  73 MS |  21,3 MB/s |  1,79 Gbits/s |
| Seattle | East US          |  3 699 km |  74 MS |  21,1 MB/s |  1,78 Gbits/s |
| Seattle | Japonia Wschodnia       |  7 705 km | 106 MS |  14,6 MB/s |  1,22 Gbits/s |
| Seattle | Południowe Zjednoczone Królestwo         |  7 708 km | 146 MS |  10,6 MB/s |   896 MB/s |
| Seattle | West Europe      |  7 834 km | 153 MS |  10,2 MB/s |   761 MB/s |
| Seattle | Australia Wschodnia   | 12 484 km | 165 MS |   9,4 MB/s |   794 MB/s |
| Seattle | Southeast Asia   | 12 989 km | 170 MS |   9,2 MB/s |   756 MB/s |
| Seattle | Brazylia Południowa *   | 10 930 km | 189 MS |   8,2 MB/s |   699 MB/s |
| Seattle | Indie Południowe      | 12 918 km | 202 MS |   7,7 MB/s |   634 MB/s |

\* Opóźnienie w Brazylii to dobry przykład, w którym odległość liniowa znacznie różni się od odległości od przebiegu. Oczekiwane opóźnienie będzie w okolicy 160 MS, ale rzeczywiście 189 MS. Różnica w opóźnieniu wydaje się wskazywać na problem z siecią. Jednak rzeczywistość to linia Fiber nie przechodzi do Brazylii w prostej linii. Należy więc oczekiwać, że zaistnieje dodatkowe 1 000 km lub do podróży do Brazylii z Seattle.

## <a name="next-steps"></a>Następne kroki
1. Pobierz zestaw narzędzi Azure Connectivity Toolkit z serwisu GitHub na stronie [https://aka.ms/AzCT][ACT]
2. Postępuj zgodnie z instrukcjami dotyczącymi [testowania wydajności łącza][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Składniki sieci platformy Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Rozwiązywanie problemów z ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Środowisko testowe wydajności"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Dane wyjściowe programu PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT