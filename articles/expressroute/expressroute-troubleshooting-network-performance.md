---
title: 'Rozwiązywanie problemów z wydajnością łączy sieciowych: Azure'
description: Ta strona zawiera ustandaryzowaną metodę testowania wydajności łączy sieciowych platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/20/2017
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: a021d658711e77c3e3be0df722223cefe506abba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204592"
---
# <a name="troubleshooting-network-performance"></a>Rozwiązywanie problemów z wydajnością sieci
## <a name="overview"></a>Omówienie
Platforma Azure oferuje stabilne i szybkie sposoby nawiązywania połączenia z sieci lokalnej do platformy Azure. Metody, takie jak połączenie typu lokacja-lokacja między siecią VPN i usługą ExpressRoute, są z sukcesem używane przez klientów, którzy w swoich dużych i małych firmach stosują platformę Azure. Ale co się stanie, gdy wydajność nie spełni oczekiwań lub poprzedniego środowiska? Ten dokument może pomóc w standaryzacji sposobu testowania i określania planu bazowego określonego środowiska.

Ten dokument przedstawia, w jaki sposób można łatwo i spójnie testować opóźnienia i przepustowość sieci między dwoma hostami. Ten dokument zawiera również kilka wskazówek na temat sposobów przeglądania sieci platformy Azure i pomocy w odizolowaniu punktów problemu. Omawiany skrypt i narzędzia programu PowerShell wymagają dwóch hostów w sieci (na każdym końcu testowanego łącza). Jeden host musi być serwerem lub systemem Windows, a drugi może być systemem Windows lub Linux. 

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
 - **Maszyna wirtualna** — serwer może mieć wiele kart sieciowych, należy upewnić się, że wszystkie trasy statyczne, trasy domyślne i ustawienia systemu operacyjnego są wysyłane i odbierają ruch w taki sam sposób. Ponadto każda jednostka SKU maszyny wirtualnej ma ograniczenie przepustowości. Jeśli używasz mniejszej jednostki SKU maszyny wirtualnej, ruch jest ograniczony przez przepustowość dostępną dla karty sieciowej. Zwykle używamy DS5v2 do testowania (a następnie usuwania po zakończeniu testowania, aby zaoszczędzić pieniądze), aby zapewnić odpowiednią przepustowość na maszynie wirtualnej.
 - **Karta sieciowa** — upewnij się, że znasz prywatny adres IP przypisany do danej karty sieciowej.
 - **Karta sieciowa sieciowej grupy zabezpieczeń** — na poziomie karty sieciowej mogą być stosowane określone sieciowych grup zabezpieczeń, upewnij się, że zestaw reguł sieciowej grupy zabezpieczeń jest odpowiedni dla ruchu, który próbujesz przekazać. Na przykład upewnij się, że porty 5201 dla iPerf, 3389 dla protokołu RDP lub 22 dla SSH są otwarte, aby umożliwić przekazywanie ruchu testowego.
 - **Podsieć VNET** — karta sieciowa jest przypisana do określonej podsieci. Upewnij się, że masz pewność, która z nich i reguły skojarzone z tą podsiecią.
 - **Sieciowej grupy zabezpieczeń podsieci** — podobnie jak karta sieciowa, sieciowych grup zabezpieczeń można również stosować w podsieci. Upewnij się, że zestaw reguł sieciowej grupy zabezpieczeń jest odpowiedni dla ruchu, który próbujesz przekazać. (w przypadku ruchu przychodzącego do karty sieciowej sieciowej grupy zabezpieczeń ma zastosowanie do sieci, a następnie karta sieciowa sieciowej grupy zabezpieczeń, podobnie jak w przypadku ruchu wychodzącego z maszyny wirtualnej karta sieciowa sieciowej grupy zabezpieczeń stosuje się najpierw do odtwarzania, sieciowej grupy zabezpieczeń podsieć).
 - **UDR podsieci** — trasy zdefiniowane przez użytkownika mogą kierować ruch do pośredniego skoku (na przykład zapory lub modułu równoważenia obciążenia). Upewnij się, że masz pewność, że dla ruchu wykorzystano UDR, a jeśli tak się stało, a co następnym przeskokiem zostanie nadany ruch. (na przykład Zapora może przekazać ruch i odmówić innego ruchu między tymi samymi dwoma hostami).
 - **Podsieć bramy/sieciowej grupy zabezpieczeń/UDR** — podobnie jak w przypadku podsieci maszyny wirtualnej, podsieć bramy może mieć sieciowych grup zabezpieczeń i UDR. Upewnij się, że wiesz tam, gdzie się znajdują, i jakie efekty mają wpływ na ruch.
 - **Brama sieci wirtualnej (ExpressRoute)** — po włączeniu komunikacji równorzędnej (ExpressRoute) lub VPN nie ma wielu ustawień, które mogą mieć wpływ na sposób lub trasy ruchu. Jeśli masz wiele obwodów usługi ExpressRoute lub tuneli VPN połączone z tą samą bramą sieci wirtualnej, zapoznaj się z ustawieniami wagi połączenia, ponieważ to ustawienie wpływa na preferencję połączenia i ma wpływ na ścieżkę pobieraną przez ruch.
 - **Filtr trasy** (niepokazywany) — filtr trasy dotyczy tylko komunikacji równorzędnej firmy Microsoft w witrynie ExpressRoute, ale ma krytyczne znaczenie, aby sprawdzić, czy nie widzisz oczekiwanych tras w komunikacji równorzędnej firmy Microsoft. 

W tym momencie jesteś częścią łącza sieci WAN. Ta domena routingu może być dostawcą usług, firmową siecią WAN lub Internetem. Wiele przeskoków, technologii i firm związanych z tymi łączami może utrudniać Rozwiązywanie problemów. Często przed przechodzeniem do tej kolekcji firm i przeskoków będziesz mieć możliwość wypróbowania na zewnątrz i sieci firmowej.

Na powyższym diagramie po lewej stronie znajduje się sieć firmowa. W zależności od wielkości firmy ta domena routingu może być kilkoma urządzeniami sieciowymi między nimi a siecią WAN lub wieloma warstwami urządzeń w sieci kampusowej/firmowej.

Ze względu na złożoność tych trzech różnych środowisk sieciowych wysokiego poziomu, często najlepiej jest zacząć od krawędzi i próbować pokazać, gdzie wydajność jest dobra i gdzie ulega obniżeniu. Takie podejście może pomóc w zidentyfikowaniu domeny routingu problemu trzech, a następnie skoncentrowaniu się na tym konkretnym środowisku.

## <a name="tools"></a>narzędzia
Większość problemów z siecią można analizować i izolować przy użyciu podstawowych narzędzi, takich jak ping i traceroute. Jest to rzadki przypadek, w którym należy przeanalizować pakiet, np. program Wireshark. Aby pomóc w rozwiązywaniu problemów, zestaw narzędzi Azure Connectivity Toolkit (AzureCT) został opracowany w celu umieszczenia niektórych z tych narzędzi w łatwym pakiecie. W przypadku testów wydajnościowych chcę używać iPerf i PSPing. iPerf to powszechnie używane narzędzie i działa w większości systemów operacyjnych. iPerf jest dobre dla podstawowych testów wydajności i jest dość łatwe w użyciu. PSPing to narzędzie ping opracowane przez program SysInternals. PSPing to prosty sposób na wykonywanie protokołu ICMP i poleceń ping protokołu TCP w jednym z nich. Oba te narzędzia są lekkie i są "zainstalowane" po prostu dodając polecenie ping do pliku do katalogu na hoście.

Wszystkie te narzędzia i metody zostały zawinięte do modułu programu PowerShell (AzureCT), który można zainstalować i użyć.

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

    Najpierw na hoście zdalnym należy zainstalować i uruchomić program iPerf w trybie serwera. Upewnij się również, że host zdalny nasłuchuje na 3389 (RDP dla systemu Windows) lub 22 (SSH dla Linux) i zezwalając na ruch na porcie 5201 dla iPerf. Jeśli host zdalny jest systemem Windows, zainstaluj AzureCT i uruchom polecenie Install-LinkPerformance, aby skonfigurować iPerf i reguły zapory, które będą musiały zostać pomyślnie uruchomione iPerf w trybie serwera. 
    
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
Jeśli test wydajności nie daje oczekiwanych wyników, ustalenie, dlaczego powinien być krokiem krok po kroku. Mając na względzie liczbę składników w ścieżce, systematyczne podejście zwykle zapewnia szybszą ścieżkę do rozdzielczości niż przeskoczanie i potencjalnie niepotrzebnie wykonywanie tych samych testów.

>[!NOTE]
>Oto scenariusz dotyczący wydajności, a nie problem z łącznością. Kroki będą różne, jeśli ruch nie przeprowadził się wcale.
>
>

Najpierw założenie założeń. Czy Twoje oczekiwania są uzasadnione? Na przykład jeśli masz obwód ExpressRoute o pojemności 1 GB/s i 100 ms opóźnień, to nieuzasadnione jest oczekiwanie na pełny 1 GB/s ruchu sieciowego, pod kątem cech wydajności TCP przez duże opóźnienia. Więcej informacji na temat założeń dotyczących wydajności zawiera [sekcja References](#references) .

Następnie zaleca się rozpoczęcie od krawędzi domen routingu i próba odizolowania problemu do jednej głównej domeny routingu; Sieć firmowa, sieci WAN lub sieć platformy Azure. Ludzie często polecenia Blame "czarny pudełko" w ścieżce, podczas gdy blaming czarne pole jest łatwe do wykonania, może znacząco opóźnić rozwiązanie, zwłaszcza jeśli problem faktycznie znajduje się w obszarze, w którym można wprowadzać zmiany. Przed przystąpieniem do dostawcy usług lub usługodawcy internetowego upewnij się, że masz należytą staranność.

Po zidentyfikowaniu głównej domeny routingu, która wydaje się zawierać problem, należy utworzyć diagram danego obszaru. W przypadku tablicy, Notatnika lub programu Visio jako diagramu znajduje się konkretna "Mapa sprawdzonej" umożliwiająca Metodyczne podejście do dalszej odizolowania problemu. Możesz planować punkty testowania i aktualizować mapę w miarę jasnego obszaru lub Dig się bardziej szczegółowo w miarę postępów testów.

Teraz, gdy masz diagram, zacznij podzielić sieć na segmenty i zawęzić problem. Dowiedz się, gdzie działa i gdzie nie. Kontynuuj przesuwanie punktów testowych, aby odizolować do składnika powodującego problemy.

Nie zapomnij też, aby przyjrzeć się innym warstwom modelu OSI. Łatwo jest skupić się na sieci i warstwach 1-3 (fizycznych, danych i sieciowych), ale te problemy mogą również znajdować się w warstwie 7 w warstwie aplikacji. Zachowaj otwarte zdanie i sprawdź założenia.

## <a name="advanced-expressroute-troubleshooting"></a>Zaawansowane rozwiązywanie problemów z ExpressRoute
Jeśli nie masz pewności, gdzie rzeczywiście jest krawędzią chmury, Izolowanie składników platformy Azure może być wyzwaniem. Gdy ExpressRoute jest używany, Edge jest składnik sieci o nazwie Microsoft Enterprise Edge (MSEE). W **przypadku korzystania z ExpressRoute**, MSEE to pierwszy punkt kontaktu z siecią firmy Microsoft i ostatni przeskok opuszcza sieć firmy Microsoft. Podczas tworzenia obiektu połączenia między bramą sieci wirtualnej i obwodem usługi ExpressRoute w rzeczywistości nastąpi połączenie z MSEE. Rozpoznanie MSEE jako pierwszego lub ostatniego przeskoku (w zależności od tego, jaki jest kierunek), jest decydujący dla izolowania problemów z siecią platformy Azure, aby udowodnić, że problem występuje na platformie Azure lub w sieci firmowej. 

![2][2]

>[!NOTE]
> Zwróć uwagę, że MSEE nie znajduje się w chmurze platformy Azure. ExpressRoute jest w rzeczywistości na granicy sieci firmy Microsoft, która nie jest faktycznie na platformie Azure. Po nawiązaniu połączenia z usługą ExpressRoute z MSEEem nastąpi połączenie z siecią firmy Microsoft, a następnie możesz przejść do dowolnej z usług w chmurze, takich jak Microsoft 365 (z usługą komunikacji równorzędnej firmy Microsoft) lub na platformę Azure (z usługą komunikacji równorzędnej i/lub firmy Microsoft).
>
>

Jeśli dwa sieci wirtualnych (sieci wirtualnych A i B w diagramie) są podłączone do tego **samego** obwodu usługi ExpressRoute, można wykonać serię testów w celu wyizolowania problemu na platformie Azure (lub udowodnić, że nie jest on na platformie Azure)
 
### <a name="test-plan"></a>Plan testu
1. Uruchom test Get-LinkPerformance między VM1 i VM2. Ten test zawiera szczegółowe informacje o tym, czy problem jest lokalny, czy nie. Jeśli ten test generuje akceptowalne wyniki opóźnienia i przepustowości, można oznaczyć lokalną sieć sieci wirtualnej jako dobra.
2. Przy założeniu, że lokalny ruch sieci wirtualnej jest dobry, Uruchom test Get-LinkPerformance między VM1 i VM3. Ten test wykonuje połączenie za pomocą sieci firmy Microsoft z MSEE i z powrotem na platformę Azure. Jeśli ten test generuje akceptowalne opóźnienia i przepustowość, można oznaczyć sieć platformy Azure jako dobra.
3. Jeśli usługa Azure została wykluczona, można wykonać podobną sekwencję testów w sieci firmowej. Jeśli również testuje dobrze, czas na skontaktowanie się z dostawcą usług lub usługodawcą internetowym w celu zdiagnozowania połączenia z siecią WAN. Przykład: Uruchom ten test między dwoma oddziałami lub między biurem a serwerem centrum danych. W zależności od używanego testowania Znajdź punkty końcowe (serwery, komputery itp.), które mogą korzystać z tej ścieżki.

>[!IMPORTANT]
> Jest to ważne, aby dla każdego testu oznaczyć godzinę uruchomienia testu i zarejestrować wyniki w wspólnej lokalizacji (podobnie jak w przypadku programu OneNote lub Excel). Każdy przebieg testowy powinien mieć identyczne dane wyjściowe, aby można było porównać wyniki wynikowe między przebiegami testów i nie mieć "dziur" w danych. Spójność wielu testów jest podstawową przyczyną rozwiązywania problemów przy użyciu AzureCT. Magic nie znajduje się w dokładnych scenariuszach obciążenia, które są uruchamiane, ale zamiast tego *magiczna* jest fakt, że otrzymujemy *spójny test i dane wyjściowe* z każdego testu. Rejestrowanie czasu i posiadanie spójnych danych każdego pojedynczego czasu jest szczególnie przydatne w przypadku późniejszego wyszukania problemu. Sumienni z początkową kolekcją danych, aby uniknąć godzin przetestowania tych samych scenariuszy (w tym trudnej liczbie lat temu).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problem jest izolowany, co teraz?
Im bardziej można wyizolować problem, który jest łatwiejszy do naprawienia, jednak często docierasz do punktu, w którym nie można uzyskać więcej informacji na temat rozwiązywania problemów. Przykład: zobaczysz link w ramach dostawcy usług, który podejmuje przeskoki do Europy, ale oczekiwana ścieżka jest wszystkie w Azji. Ten punkt ma miejsce, gdy należy skontaktować się z pomocą techniczną. Użytkownik, który pyta, zależy od domeny routingu, do której wyizolowano problem, lub nawet lepszy, jeśli można go zawęzić do określonego składnika.

W przypadku problemów z siecią firmową wewnętrzny dział IT lub dostawca usług obsługujący sieć (może to być producent sprzętu) może pomóc w zaoferowaniu konfiguracji urządzeń lub naprawy sprzętowej.

W przypadku sieci WAN udostępnianie wyników testów z dostawcą usług lub usługodawcą internetowym może pomóc w rozpoczęciu pracy i unikaniu powstawania niektórych już przetestowanych danych. Jednak nie są one używane, jeśli chcą zweryfikować swoje wyniki. "Zaufanie, ale weryfikacja" to dobre motto w przypadku rozwiązywania problemów w oparciu o wyniki raportowane przez inne osoby.

Po rozwiązaniu problemu z platformą Azure, gdy jest to możliwe, należy zapoznać się z [dokumentacją sieci platformy Azure][Network Docs] , a następnie w razie potrzeby [otworzyć bilet pomocy technicznej][Ticket Link].

## <a name="references"></a>Materiały źródłowe
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
 - Wszystkie testy dotyczyły AzureCT Get-LinkPerformance polecenie z 5-minutowym testem obciążenia dla każdego z sześciu przebiegów testowych. Na przykład:

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

| ExpressRoute<br/>Lokalizacja|Azure<br/>Region | Szacowany<br/>Odległość (km) | Opóźnienie|1 sesja<br/>Przepustowość | Maksimum<br/>Przepustowość |
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

\* Opóźnienie w Brazylii to dobry przykład, w którym odległość liniowa znacznie różni się od odległości od przebiegu. Oczekujemy, że opóźnienie będzie w okolicy 160 MS, ale rzeczywiście 189 MS. Różnica w odniesieniu do mojego oczekiwania może wskazywać na problem z siecią, ale najprawdopodobniej w przypadku, gdy uruchomienie włókna nie przechodzi do Brazylii w prostej linii i ma dodatkowe 1 000 km lub podróży w celu przejścia do Brazylii z Seattle.

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