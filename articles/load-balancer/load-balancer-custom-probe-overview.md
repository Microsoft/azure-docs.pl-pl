---
title: Sondy kondycji umożliwiające skalowanie i zapewnienie wysokiej dostępności usługi
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak używać sond kondycji do monitorowania wystąpień za Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780947"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji usługi Load Balancer

W przypadku używania reguł równoważenia obciążenia z Azure Load Balancer należy określić sondy kondycji, aby umożliwić Load Balancer wykryć stan punktu końcowego zaplecza.  Konfiguracja sondy kondycji i odpowiedzi sondy określają, które wystąpienia puli zaplecza będą otrzymywać Nowe przepływy. Sond kondycji można użyć do wykrywania awarii aplikacji w punkcie końcowym zaplecza. Możesz również wygenerować niestandardową odpowiedź na sondę kondycji i użyć sondy kondycji do sterowania przepływem, aby zarządzać obciążeniem lub planowanym przestojem. Gdy sonda kondycji zakończy się niepowodzeniem, Load Balancer przestanie wysyłać Nowe przepływy do odpowiedniego wystąpienia w złej kondycji. Nie ma to wpływu na łączność wychodzącą. dotyczy to tylko łączności przychodzącej.

Sondy kondycji obsługują wiele protokołów. Dostępność określonego protokołu sondy kondycji zależy od jednostki SKU Load Balancer.  Ponadto zachowanie usługi różni się w zależności od Load Balancer jednostki SKU, jak pokazano w poniższej tabeli:

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| **[Typy sond](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Zachowanie podczas sondowania](#probedown)** | Wszystkie sondy w dół, wszystkie przepływy TCP będą kontynuowane. | Wszystkie sondy w dół, wszystkie przepływy TCP wygasają. | 


>[!IMPORTANT]
>Zapoznaj się z tym dokumentem w całości, w tym ważne [wskazówki dotyczące projektowania](#design) poniżej, aby utworzyć niezawodną usługę.

>[!IMPORTANT]
>Sondy kondycji Load Balancer pochodzą z adresu IP 168.63.129.16 i nie mogą być blokowane w przypadku sondowania do oznaczania wystąpienia.  Przejrzyj [źródłowy adres IP sondy](#probesource) , aby uzyskać szczegółowe informacje.

>[!IMPORTANT]
>Niezależnie od skonfigurowanego progu limitu czasu HTTP (S) Load Balancer sondy kondycji automatycznie sonduje wystąpienie, jeśli serwer zwróci kod stanu, który nie jest HTTP 200 OK lub jeśli połączenie zostanie przerwane za pośrednictwem resetowania protokołu TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Konfiguracja sondy

Konfiguracja sondy kondycji składa się z następujących elementów:

- Czas trwania interwału między indywidualnymi sondami
- Liczba odpowiedzi sondy, które muszą zostać zaobserwowane przed przejściem sondy do innego stanu
- Protokół sondy
- Port sondy
- Ścieżka HTTP do użycia w przypadku protokołu HTTP GET podczas korzystania z sond HTTP (S)

>[!NOTE]
>Definicja sondy nie jest obowiązkowa lub sprawdzana w przypadku używania Azure PowerShell, interfejsu wiersza polecenia platformy Azure, szablonów lub interfejsu API. Testy weryfikacyjne sondy są wykonywane tylko w przypadku korzystania z witryny Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Informacje o sygnale aplikacji, wykrywaniu sygnału i reakcji platformy

Liczba odpowiedzi sondy odnosi się do obu

- Liczba pomyślnych sond, które zezwalają na oznaczenie wystąpienia jako up, i
- Liczba sond przekroczenia limitu czasu, które powodują oznaczenie wystąpienia jako wyłączonego.

Określone wartości limitu czasu i interwału określają, czy wystąpienie zostanie oznaczone jako w górę, czy w dół.  Czas trwania interwału pomnożony przez liczbę odpowiedzi sondy określa czas, w którym należy wykryć odpowiedzi sondy.  A usługa będzie reagować po uzyskaniu wymaganych sond.

Można również przedstawić zachowanie na przykład. Jeśli ustawiono liczbę odpowiedzi sondy do 2 i interwału do 5 sekund, oznacza to, że błędy czasu sondy są zaobserwowane w ciągu 10 sekund.  Ponieważ czas, w którym sonda jest wysyłana, nie jest zsynchronizowany, gdy aplikacja może zmienić stan, możemy powiązać się z czasem wykrywania przez dwa scenariusze:

1. Jeśli aplikacja zacznie wytwarzać odpowiedzi sondy czasu przed rozpoczęciem pierwszego sondy, wykrywanie tych zdarzeń będzie trwać 10 sekund (2 x 5 sekund) oraz czas, w którym aplikacja zaczyna sygnalizować czas do momentu odebrania pierwszej sondy.  Można założyć, że to wykrywanie zajmie nieco ponad 10 sekund.
2. Jeśli aplikacja zacznie tworzyć odpowiedzi sondy czasu po nadejściu pierwszej sondy, wykrywanie tych zdarzeń nie rozpocznie się do momentu odebrania kolejnej sondy (i przekroczenia limitu czasu) plus inne 10 sekund (2 x 5 sekund).  Można założyć, że wykrywanie ma trwać zaledwie 15 sekund.

Na potrzeby tego przykładu, gdy wystąpiło wykrycie, platforma zajmie trochę czasu na zareagowanie na tę zmianę.  Oznacza to, że w zależności od 

1. gdy aplikacja zacznie zmieniać stan i
2. Gdy ta zmiana zostanie wykryta i spełnione są wymagane kryteria (liczba sond wysyłanych w określonym interwale) i
3. gdy wykrycie zostało przekazane na platformie 

można założyć, że reakcja na czas odpowiedzi sondy trwa od minimum do 10 sekund, a maksymalnie przez 15 sekund będzie reagować na zmianę sygnału z aplikacji.  W tym przykładzie przedstawiono, jak to zrobić, ale nie jest możliwe prognozowanie dokładnego czasu trwania Poza powyższymi wskazówkami przedstawionymi w tym przykładzie.

>[!NOTE]
>Sonda kondycji będzie sondować wszystkie uruchomione wystąpienia w puli zaplecza. Jeśli wystąpienie zostanie zatrzymane, nie będzie ono badane, dopóki nie zostanie ponownie uruchomione.

## <a name="probe-types"></a><a name="types"></a>Typy sond

Protokół używany przez sondę kondycji można skonfigurować na jedną z następujących czynności:

- [Odbiorniki TCP](#tcpprobe)
- [Punkty końcowe HTTP](#httpprobe)
- [Punkty końcowe HTTPS](#httpsprobe)

Dostępne protokoły zależą od używanej jednostki SKU Load Balancer:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standardowy SKU** |    &#9989; |   &#9989; |   &#9989; |
| **Podstawowy SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> Sonda TCP

Sondy TCP inicjują połączenie, wykonując trzy-kierunkowe uzgadnianie protokołu TCP ze zdefiniowanym portem.  Sondy TCP przerywają połączenie z czterema sposobem zamykania protokołu TCP.

Minimalny interwał sondy wynosi 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Łączny czas trwania wszystkich interwałów nie może przekroczyć 120 sekund.

Sonda TCP kończy się niepowodzeniem, gdy:
* Odbiornik TCP w wystąpieniu nie odpowiada wcale w przedziale czasu.  Sonda jest oznaczona w dół na podstawie liczby żądań sondowania przekroczenia limitu czasu, które zostały skonfigurowane w taki sposób, aby nie odpowiadały przed oznaczeniem sondy.
* Sonda odbiera Resetowanie protokołu TCP z wystąpienia.

Poniżej pokazano, jak można wyrazić ten rodzaj konfiguracji sondy w szablonie Menedżer zasobów:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>Sonda protokołu HTTP/HTTPS

>[!NOTE]
>Sonda HTTPS jest dostępna tylko dla [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md).

Sondy protokołu HTTP i HTTPS kompilują sondę TCP i wystawią HTTP GET z określoną ścieżką. Obie te sondy obsługują ścieżki względne dla HTTP GET. Sondy HTTPS są takie same jak sondy HTTP z dodaniem Transport Layer Security (TLS, wcześniej znanego jako SSL) otoki. Sonda kondycji jest oznaczona, gdy wystąpienie reaguje na stan HTTP 200 w określonym limicie czasu.  Sonda kondycji próbuje domyślnie sprawdzić skonfigurowany port sondy kondycji co 15 sekund. Minimalny interwał sondy to 5 sekund. Łączny czas trwania wszystkich interwałów nie może przekroczyć 120 sekund.

Sondy protokołu HTTP/HTTPS mogą również być przydatne do implementowania własnej logiki do usuwania wystąpień z rotacji modułu równoważenia obciążenia, jeśli port sondy jest również odbiornikiem dla samej usługi. Na przykład możesz zdecydować się na usunięcie wystąpienia, jeśli jest ono powyżej 90% procesora CPU i zwróciło stan HTTP inny niż 200. 

> [!NOTE] 
> Sonda HTTPS wymaga użycia certyfikatów opartych na minimalnym skrócie podpisu SHA256 w całym łańcuchu.

Jeśli używasz Cloud Services i masz role sieci Web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwracają stan inny niż 200 do sondy usługi równoważenia obciążenia.

Sonda protokołu HTTP/HTTPS kończy się niepowodzeniem w przypadku:
* Punkt końcowy sondy zwraca kod odpowiedzi HTTP inny niż 200 (na przykład 403, 404 lub 500). Spowoduje to natychmiastowe oznaczenie sondy kondycji. 
* Punkt końcowy sondy nie odpowiada wcale w czasie minimalnym interwału sondowania i 30-sekundowym przedziale czasu. Jeśli sonda zostanie oznaczona jako Nieuruchomiona i zostanie osiągnięta suma wszystkich przedziałów czasu, nie ma odpowiedzi na wiele żądań sondowania.
* Punkt końcowy sondowania zamyka połączenie za pośrednictwem resetowania protokołu TCP.

Poniżej pokazano, jak można wyrazić ten rodzaj konfiguracji sondy w szablonie Menedżer zasobów:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sonda agenta gościa (tylko wersja klasyczna)

Role usługi w chmurze (role procesów roboczych i role sieci Web) domyślnie używają agenta gościa do monitorowania sondowania.  Sonda agenta gościa jest ostatnią konfiguracją.  Zawsze używaj sondy kondycji jawnie z sondą TCP lub HTTP. Sonda agenta gościa nie jest tak wydajna jak jawnie zdefiniowana sondy dla większości scenariuszy aplikacji.

Sonda agenta Gościa to sprawdzenie agenta gościa w ramach maszyny wirtualnej. Następnie nasłuchuje i reaguje na odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzane lub zatrzymywane).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sond kondycji](/previous-versions/azure/reference/ee758710(v=azure.100)) lub Rozpoczynanie [pracy przez utworzenie publicznego modułu równoważenia obciążenia dla usług Cloud Services](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Jeśli Agent gościa nie odpowie przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznaczy wystąpienie jako nieodpowiadające. Następnie przestaje wysyłać przepływy do tego wystąpienia. Moduł równoważenia obciążenia kontynuuje sprawdzanie wystąpienia. 

Jeśli Agent gościa odpowie przy użyciu protokołu HTTP 200, moduł równoważenia obciążenia ponownie wysyła Nowe przepływy do tego wystąpienia.

W przypadku korzystania z roli sieci Web kod witryny sieci Web jest zwykle uruchamiany w w3wp.exe, który nie jest monitorowany przez sieć szkieletową platformy Azure ani agenta gościa. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są raportowane agentowi gościa. W związku z tym moduł równoważenia obciążenia nie przeprowadzi tego wystąpienia z obrotu.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Zachowanie sondowania

Sondy kondycji protokołów TCP, HTTP i HTTPS są uznawane za zdrowe i oznaczają, że punkt końcowy wewnętrznej bazy danych jest w dobrej kondycji, gdy:

* Sonda kondycji kończy się pomyślnie po uruchomieniu maszyny wirtualnej.
* Określona liczba sond wymaganych do oznaczenia punktu końcowego zaplecza w miarę osiągnięcia w dobrej kondycji.

Każdy punkt końcowy zaplecza, który osiągnął prawidłowy stan, jest uprawniony do otrzymywania nowych przepływów.  

> [!NOTE]
> Jeśli sonda kondycji jest zmieniana, moduł równoważenia obciążenia czeka dłużej przed ponownym umieszczeniem punktu końcowego zaplecza w dobrej kondycji. Ten dodatkowy czas oczekiwania chroni użytkownika i infrastruktury oraz jest zamierzonymi zasadami.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Zachowanie podczas sondowania

### <a name="tcp-connections"></a>Połączenia TCP

Nowe połączenia TCP będą pomyślnie pozostawać w dalszym punkcie końcowym zaplecza.

Jeśli sonda kondycji punktu końcowego zaplecza nie powiedzie się, ustanowiono połączenia TCP z tym punktem końcowym zaplecza.

Jeśli wszystkie sondy dla wszystkich wystąpień w puli zaplecza zakończą się niepowodzeniem, nie będą wysyłane żadne nowe przepływy do puli zaplecza. Usługa Load Balancer w warstwie Standardowa umożliwi kontynuowanie ustanowionych przepływów TCP.  Podstawowa Load Balancer zakończy wszystkie istniejące przepływy TCP do puli zaplecza.
 
Load Balancer to usługa przekazująca (nie przerywa połączeń TCP), a przepływ jest zawsze między klientem i systemem operacyjnym gościa maszyny wirtualnej i aplikacją. Pula ze wszystkimi sondami w dół spowoduje, że fronton nie odpowie na otwarte próby połączenia TCP (SYN), ponieważ nie ma żadnego punktu końcowego wewnętrznej bazy danych do odebrania przepływu i zareaguje na potwierdzenie SYN.

### <a name="udp-datagrams"></a>Datagramy UDP

Datagramy UDP zostaną dostarczone do prawidłowych punktów końcowych zaplecza.

Protokół UDP jest bezpołączeni i nie ma stanu przepływu śledzonego dla protokołu UDP. Jeśli sonda kondycji dowolnego punktu końcowego zaplecza nie powiedzie się, istniejące przepływy UDP przechodzą na inne wystąpienie w dobrej kondycji w puli zaplecza.

Jeśli wszystkie sondy dla wszystkich wystąpień w puli zaplecza zakończą się niepowodzeniem, istniejące przepływy UDP zostaną przerwane dla usług podstawowa i Standardowa usługi równoważenia obciążenia.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Źródłowy adres IP sondy

Load Balancer używa rozproszonej usługi Bing dla swojego wewnętrznego modelu kondycji. Usługa Bing znajduje się na każdym hoście, na którym maszyny wirtualne i może być zaprogramowana na żądanie w celu wygenerowania sond kondycji zgodnie z konfiguracją klienta. Ruch sondy kondycji jest bezpośrednio między usługą sondowania, która generuje sondę kondycji i maszynę wirtualną klienta. Wszystkie sondy kondycji Load Balancer pochodzą z adresu IP 168.63.129.16 jako ich źródła.  Możesz użyć przestrzeni adresów IP wewnątrz sieci wirtualnej, która nie jest RFC1918.  Użycie zastrzeżonej globalnie adresu IP firmy Microsoft zmniejsza szansę, że adres IP jest w konflikcie z przestrzenią adresów IP używaną wewnątrz sieci wirtualnej.  Ten adres IP jest taki sam we wszystkich regionach i nie jest narażony na bezpieczeństwo, ponieważ tylko wewnętrzny składnik platformy platformy Azure może posłużyć do podzielenia pakietu z tego adresu IP. 

Tag usługi AzureLoadBalancer identyfikuje ten źródłowy adres IP w [sieciowych grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md) i domyślnie zezwala na ruch sondy kondycji.

Oprócz Load Balancer sond kondycji [następujące operacje używają tego adresu IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Umożliwia agentowi maszyny wirtualnej komunikowanie się z platformą w celu sygnalizowania, że jest w stanie "gotowe"
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia przefiltrowanego rozpoznawania nazw klientom, którzy nie definiują niestandardowych serwerów DNS.  Takie filtrowanie gwarantuje, że klienci będą mogli rozpoznać tylko nazwy hostów ich wdrożenia.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.

## <a name="design-guidance"></a><a name="design"></a> Wskazówki dotyczące projektowania

Sondy kondycji są używane do odporności usługi i umożliwienia jej skalowania. Niepoprawna konfiguracja lub nieodpowiedni Wzorzec projektowy może mieć wpływ na dostępność i skalowalność usługi. Zapoznaj się z tym całym dokumentem i zastanów się, co ma wpływ na Twój scenariusz, gdy ta odpowiedź sondy jest oznaczona jako wyłączona lub oznaczona jako taka, jak ma to wpływ na dostępność scenariusza aplikacji.

Podczas projektowania modelu kondycji aplikacji należy sondować port w punkcie końcowym zaplecza, który odzwierciedla kondycję tego wystąpienia __i__ usługi aplikacji, którą udostępniasz.  Port aplikacji i port sondy nie muszą być takie same.  W niektórych scenariuszach może być pożądane, aby port sondy był różny od portu, w którym usługa jest udostępniana przez aplikację.  

Czasami może być przydatne, aby aplikacja mogła wygenerować odpowiedź sondy kondycji, aby nie tylko wykrywać kondycję aplikacji, ale również sygnalizować bezpośrednio Load Balancer, czy Twoje wystąpienie powinno otrzymywać i nie odbierać nowych przepływów.  Można manipulować odpowiedzią sondy, aby umożliwić aplikacji Tworzenie i ograniczanie dostarczania nowych przepływów do wystąpienia przez niepowodzenie sondy kondycji lub przygotowanie do konserwacji aplikacji i Inicjowanie opróżniania scenariusza.  W przypadku korzystania z usługa Load Balancer w warstwie Standardowa sygnał [w dół sondowania](#probedown) będzie zawsze zezwalał na kontynuowanie PRZEPŁYWów TCP do momentu upłynięcia limitu czasu bezczynności lub zamknięcia połączenia. 

W przypadku równoważenia obciążenia UDP należy wygenerować niestandardowy sygnał sondy kondycji z punktu końcowego zaplecza i użyć sondy kondycji protokołu TCP, HTTP lub HTTPS dla odpowiedniego odbiornika, aby odzwierciedlić kondycję aplikacji UDP.

W przypadku używania [portów ha zasad równoważenia obciążenia](load-balancer-ha-ports-overview.md) z [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md)wszystkie porty są zrównoważone obciążenie, a pojedyncza odpowiedź sondy kondycji musi odzwierciedlać stan całego wystąpienia.

Nie należy tłumaczyć ani proxy sondy kondycji za pomocą wystąpienia, które odbiera sondę kondycji z innym wystąpieniem w sieci wirtualnej, ponieważ ta konfiguracja może prowadzić do błędów kaskadowych w Twoim scenariuszu.  Rozważmy następujący scenariusz: zestaw urządzeń innych firm jest wdrażany w puli zaplecza zasobu Load Balancer w celu zapewnienia skalowalności i nadmiarowości dla urządzeń, a sonda kondycji jest skonfigurowana do sondowania portu, który jest używany przez serwery proxy urządzenia innej firmy lub który tłumaczy na inne maszyny wirtualne znajdujące się za urządzeniem.  W przypadku sondowania tego samego portu, który jest używany do tłumaczenia lub żądania proxy na inne maszyny wirtualne za urządzeniem, Każda odpowiedź sondy z pojedynczej maszyny wirtualnej za urządzeniem spowoduje oznaczenie samego urządzenia jako nieaktywnego. Ta konfiguracja może prowadzić do niepowodzenia kaskadowego całego scenariusza aplikacji w wyniku pojedynczego punktu końcowego zaplecza za urządzeniem.  Wyzwalacz może być sporadycznym błędem sondy, co spowoduje, że Load Balancer oznaczyć pierwotne miejsce docelowe (wystąpienie urządzenia) i z kolei może wyłączyć cały scenariusz aplikacji. Należy sondować kondycję samego urządzenia. Wybór sondy do określenia sygnału kondycji jest ważnym zagadnieniem dotyczącym scenariuszy sieciowych urządzeń wirtualnych (urządzenie WUS) i należy skontaktować się z dostawcą aplikacji w celu uzyskania odpowiedniego sygnału kondycji dla takich scenariuszy.

Jeśli nie zezwolisz na [źródłowy adres IP](#probesource) sondy w zasadach zapory, sonda kondycji zakończy się niepowodzeniem, ponieważ nie można nawiązać połączenia z wystąpieniem.  Z kolei Load Balancer oznaczy wystąpienie z powodu błędu sondy kondycji.  Ta niepowodzna konfiguracja może spowodować niepowodzenie scenariusza aplikacji ze zrównoważonym obciążeniem.

Aby można było oznaczyć wystąpienie Load Balancer sondy kondycji, **należy** zezwolić na ten adres IP w dowolnych [grupach zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md) platformy Azure i lokalnych zasadach zapory.  Domyślnie każda sieciowa Grupa zabezpieczeń zawiera [tag usługi](../virtual-network/network-security-groups-overview.md#service-tags) AzureLoadBalancer, aby zezwolić na ruch sondy kondycji.

Jeśli chcesz przetestować błąd sondy kondycji lub oznaczyć pojedyncze wystąpienie, możesz użyć [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md) , aby jawnie zablokować sondę kondycji (port docelowy lub [źródłowy adres IP](#probesource)) i symulować awarię sondy.

Nie należy konfigurować sieci wirtualnej z zakresem adresów IP należącym do firmy Microsoft, który zawiera 168.63.129.16.  Takie konfiguracje kolidują z adresem IP sondy kondycji i mogą spowodować niepowodzenie scenariusza.

Jeśli masz wiele interfejsów na maszynie wirtualnej, musisz się upewnić, że masz odpowiedź na sondę w interfejsie, na którym został on odebrany.  Może być konieczne założenie, że adres sieciowy będzie przetłumaczyć ten adres na maszynę wirtualną dla poszczególnych interfejsów.

Nie włączaj [sygnatur czasowych protokołu TCP](https://tools.ietf.org/html/rfc1323).  Włączenie sygnatur czasowych protokołu TCP może spowodować niepowodzenie sondowania kondycji z powodu porzucenia pakietów TCP przez stos TCP systemu operacyjnego gościa maszyny wirtualnej, co powoduje Load Balancer oznaczania odpowiedniego punktu końcowego.  Sygnatury czasowe protokołu TCP są rutynowo włączane domyślnie w obrazach maszyny wirtualnej z zaostrzonymi zabezpieczeniami i muszą zostać wyłączone.

## <a name="monitoring"></a>Monitorowanie

Publiczna i wewnętrzna [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md) uwidaczniają na punkt końcowy, a stan sondy kondycji punktu końcowego zaplecza jako metryki wielowymiarowe za Azure monitor. Te metryki mogą być używane przez inne usługi platformy Azure lub Aplikacje partnerskie. 

Podstawowa Publiczna Load Balancer udostępnia stan sondy kondycji podsumowany dla puli zaplecza za pośrednictwem dzienników Azure Monitor.  Dzienniki Azure Monitor nie są dostępne dla wewnętrznych podstawowych modułów równoważenia obciążenia.  Możesz użyć [dzienników Azure monitor](load-balancer-monitor-log.md) , aby sprawdzić stan kondycji sondowania publicznego modułu równoważenia obciążenia i liczbę sond. Rejestrowanie może być używane z usługą Power BI lub Operational Insights platformy Azure w celu zapewnienia statystyk dotyczących stanu kondycji modułu równoważenia obciążenia.

## <a name="limitations"></a>Ograniczenia

- Sondy HTTPS nie obsługują wzajemnego uwierzytelniania przy użyciu certyfikatu klienta.
- W przypadku włączenia znaczników czasu TCP należy założyć, że sondy kondycji zakończą się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md)
- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w Menedżer zasobów przy użyciu programu PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [Interfejs API REST dla sond kondycji](/rest/api/load-balancer/loadbalancerprobes/)
- Zażądaj nowych możliwości sondowania kondycji za pomocą usługi [Uservoice Load Balancer](https://aka.ms/lbuservoice)
