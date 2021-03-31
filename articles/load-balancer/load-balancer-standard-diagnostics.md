---
title: Diagnostyka przy użyciu metryk, alertów i kondycji zasobów — usługa Load Balancer w warstwie Standardowa platformy Azure
description: Korzystając z dostępnych metryk, alertów i informacji o kondycji zasobów, można zdiagnozować usługa Load Balancer w warstwie Standardowa platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: 29584a9453fa052745f417cba0bbe940766c30e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699083"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostyka usługi Load Balancer w warstwie Standardowa przy użyciu metryk, alertów i kondycji zasobów

Usługa Azure usługa Load Balancer w warstwie Standardowa udostępnia następujące możliwości diagnostyczne:

* **Wielowymiarowe metryki i alerty**: zapewniają wielowymiarowe funkcje diagnostyczne, [Azure monitor](../azure-monitor/overview.md) w przypadku konfiguracji usługi równoważenia obciążenia w warstwie Standardowa. Możesz monitorować zasoby standardowego modułu równoważenia obciążenia, zarządzać nimi i rozwiązywać problemy.

* **Kondycja zasobów**: Resource Health stan Load Balancer jest dostępny na stronie Resource Health w obszarze monitorowanie. To automatyczne sprawdzenie informuje o bieżącej dostępności zasobu Load Balancer.
Ten artykuł zawiera krótki przewodnik po tych możliwościach i oferuje sposoby ich używania do usługa Load Balancer w warstwie Standardowa. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Metryki wielowymiarowe

Azure Load Balancer udostępniają wielowymiarowe metryki za pośrednictwem metryk platformy Azure w Azure Portal i ułatwiają uzyskiwanie informacji diagnostycznych w czasie rzeczywistym do zasobów modułu równoważenia obciążenia. 

Różne konfiguracje usługa Load Balancer w warstwie Standardowa zapewniają następujące metryki:

| Metric | Typ zasobu | Opis | Zalecana agregacja |
| --- | --- | --- | --- |
| Dostępność ścieżki danych | Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa stale wykonuje ścieżkę danych z regionu do frontonu modułu równoważenia obciążenia, aż do stosu SDN, który obsługuje maszynę wirtualną. Tak długo, jak wystąpienia w dobrej kondycji, pomiar jest zgodny z tą samą ścieżką, co ruch o zrównoważonym obciążeniu aplikacji. Ścieżka danych używana przez klientów również jest sprawdzana. Pomiar jest niewidoczny dla aplikacji i nie zakłóca innych operacji.| Średnia |
| Stan sondy kondycji | Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa używa rozproszonej usługi badania kondycji, która monitoruje kondycję punktu końcowego aplikacji zgodnie z ustawieniami konfiguracji. Ta metryka zawiera zagregowany widok lub widok filtrowany dla każdego punktu końcowego wystąpienia w puli modułu równoważenia obciążenia. Możesz zobaczyć, jak moduł równoważenia obciążenia przegląda kondycję aplikacji, zgodnie z konfiguracją sondy kondycji. |  Średnia |
| Liczba synch (synchronizacja) | Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa nie przerywa połączeń protokołu TCP (Transmission Control Protocol) ani nie wchodzi w interakcje z przepływami pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze realizowane między wystąpieniem źródłowym a wystąpieniem maszyny wirtualnej. Aby lepiej rozwiązać problemy ze scenariuszami protokołu TCP, można użyć liczników pakietów SYN w celu sprawdzenia, ile wykonano prób połączenia TCP. Metryka zgłasza liczbę odebranych pakietów TCP SYN.| Sum |
| Liczba połączeń z translatorem adresów sieciowych | Publiczny moduł równoważenia obciążenia |Usługa Load Balancer w warstwie Standardowa zgłasza liczbę zamaskowanych przepływów wychodzących do frontonu publicznego adresu IP. Porty źródłowego translatora adresów sieciowych (SNAT) to zasób ulegający wyczerpaniu. Ta metryka może wskazywać na to, jak bardzo aplikacja jest zależna od translatora SNAT dla przepływów przychodzących. Zgłaszane są liczniki dla zakończonych powodzeniem i zakończonych niepowodzeniem przepływów wychodzących SNAT. Mogą one służyć do rozwiązywania problemów i poznawania kondycji przepływów wychodzących.| Sum |
| Przydzielono porty przydziałów adresów sieciowych | Publiczny moduł równoważenia obciążenia | usługa Load Balancer w warstwie Standardowa zgłasza liczbę portów przyznanych przez wystąpienie wewnętrznej bazy danych | Obliczon. |
| Używane porty | Publiczny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa raportuje liczbę portów, które są używane dla wystąpienia zaplecza. | Średnia | 
| Liczba bajtów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa zgłasza przetworzone dane na fronton. Można zauważyć, że bajty nie są równomiernie rozłożone między wystąpieniami zaplecza. Jest to oczekiwane, ponieważ algorytm Load Balancer platformy Azure jest oparty na przepływach | Sum |
| Liczba pakietów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa zgłasza przetworzone pakiety na fronton.| Sum |

  >[!NOTE]
  >W przypadku korzystania z dystrybucji ruchu z wewnętrznego modułu równoważenia obciążenia za pośrednictwem pakietu urządzenie WUS lub syn, liczba bajtów i metryki liczby pakietów nie są dostępne i będą wyświetlane jako zero. 
  
  >[!NOTE]
  >Agregacje Max i min nie są dostępne dla licznika SYN, liczby pakietów, liczby połączeń z przyłączaniem do adresów sieciowych i metryk liczby bajtów 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Wyświetl metryki modułu równoważenia obciążenia w Azure Portal

Azure Portal uwidacznia metryki modułu równoważenia obciążenia za pośrednictwem strony metryk, która jest dostępna na stronie zasobów modułu równoważenia obciążenia dla określonego zasobu i strony Azure Monitor. 

Aby wyświetlić metryki dla zasobów usługa Load Balancer w warstwie Standardowa:
1. Przejdź do strony metryki i wykonaj jedną z następujących czynności:
   * Na stronie zasób usługi równoważenia obciążenia wybierz typ metryki z listy rozwijanej.
   * Na stronie Azure Monitor wybierz zasób usługi równoważenia obciążenia.
2. Ustaw odpowiedni typ agregacji metryki.
3. Opcjonalnie skonfiguruj wymagane filtrowanie i grupowanie.
4. Opcjonalnie można skonfigurować zakres czasu i agregację. Domyślnie czas jest wyświetlany w formacie UTC.

  >[!NOTE] 
  >Agregacja czasu jest ważna podczas interpretowania pewnych metryk jako dane są próbkowane raz na minutę. Jeśli agregacja czasu jest ustawiona na pięć minut, a suma typu agregacji metryki jest używana w przypadku metryk takich jak alokacja, na wykresie zostanie wyświetlonych pięć razy łączne przydzielone porty podrzędnego protokołu adresów sieciowych. 

![Metryki dla usługa Load Balancer w warstwie Standardowa](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Ilustracja: Metryka dostępności ścieżki danych dla usługa Load Balancer w warstwie Standardowa*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Programowe pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API

Aby uzyskać wskazówki dotyczące interfejsu API na potrzeby pobierania wielowymiarowych definicji i wartości metryk, zobacz [Przewodnik po interfejsie API REST monitorowania platformy Azure](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Te metryki mogą być zapisywane na koncie magazynu przez dodanie [ustawień diagnostycznych](../azure-monitor/essentials/diagnostic-settings.md) dla kategorii "wszystkie metryki". 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostyczne i zalecane widoki

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Czy ścieżka danych jest dostępna dla frontonu Load Balancer?
<details><summary>Rozwiń</summary>

Metryka dostępności ścieżki danych opisuje prawidłowość ścieżki danych w regionie względem hosta obliczeniowego, na którym znajdują się maszyny wirtualne. Metryka to odbicie kondycji infrastruktury platformy Azure. Możesz użyć metryki, aby:
- Monitorowanie zewnętrznej dostępności usługi
- Dig się bardziej szczegółowo i Dowiedz się, czy platforma, w której jest wdrażana usługa, jest w dobrej kondycji, czy jej wystąpienie jest w dobrej kondycji.
- Izoluj, czy zdarzenie jest powiązane z usługą lub podstawową płaszczyzną danych. Nie należy mylić tej metryki ze stanem sondy kondycji ("dostępność wystąpienia zaplecza").

Aby uzyskać dostępność ścieżki danych dla usługa Load Balancer w warstwie Standardowa zasobów:
1. Upewnij się, że wybrano prawidłowy zasób modułu równoważenia obciążenia. 
2. Z listy rozwijanej **Metryka** wybierz opcję **dostępność ścieżki danych**. 
3. Z listy rozwijanej **agregacja** wybierz pozycję **średnia**. 
4. Dodatkowo należy dodać filtr dla adresu IP frontonu lub portu frontonu jako wymiar z wymaganym adresem IP frontonu lub portem frontonu, a następnie zgrupować je według wybranego wymiaru.

![Sondowanie VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ilustracja: szczegóły dotyczące sondowania frontonu Load Balancer*

Metryka jest generowana przez aktywną miarę w paśmie. Usługa sondowania w regionie odniesie ruch do miary. Usługa zostanie aktywowana zaraz po utworzeniu wdrożenia z publicznym frontonem i będzie kontynuowane do momentu usunięcia frontonu. 

Pakiet zgodny z frontonem i regułą wdrożenia jest generowany okresowo. Przechodzą region z lokalizacji źródłowej na host, na którym znajduje się maszyna wirtualna w puli zaplecza. Infrastruktura modułu równoważenia obciążenia wykonuje te same operacje związane z równoważeniem obciążenia i translacji, co dla całego ruchu. To sondowanie jest w paśmie w punkcie końcowym ze zrównoważonym obciążeniem. Po nadejściu sondy na hoście obliczeniowym, w którym znajduje się dobra maszyna wirtualna w puli zaplecza, Host obliczeniowy generuje odpowiedź do usługi sondowania. Ten ruch nie jest widoczny dla maszyny wirtualnej.

Dostępność ścieżki danych nie powiodła się z następujących powodów:
- Wdrożenie nie obejmuje żadnych prawidłowych maszyn wirtualnych w puli zaplecza. 
- Wystąpiła awaria infrastruktury.

W celach diagnostycznych można użyć [metryki dostępności ścieżki danych wraz ze stanem sondy kondycji](#vipavailabilityandhealthprobes).

Użyj **średniej** jako agregacji dla większości scenariuszy.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Czy wystąpienia zaplecza dla mojej Load Balancer odpowiadają na sondy?
<details>
  <summary>Rozwiń</summary>
Metryka stanu sondy kondycji opisuje kondycję wdrożenia aplikacji zgodnie z konfiguracją podczas konfigurowania sondy kondycji modułu równoważenia obciążenia. Moduł równoważenia obciążenia używa stanu sondy kondycji, aby określić, gdzie mają być wysyłane Nowe przepływy. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w systemie operacyjnym gościa maszyny wirtualnej.

Aby uzyskać stan sondy kondycji dla zasobów usługa Load Balancer w warstwie Standardowa:
1. Wybierz metrykę **stanu sondy kondycji** z **średnim** typem agregacji. 
2. Zastosuj filtr na wymaganym adresie IP frontonu (lub w obu portach).

Sondy kondycji nie powiodły się z następujących powodów:
- Można skonfigurować sondę kondycji do portu, który nie nasłuchuje lub nie odpowiada lub używa niewłaściwego protokołu. Jeśli usługa korzysta z bezpośrednich reguł powrotu serwera (DSR lub zmiennoprzecinkowych adresów IP), upewnij się, że usługa nasłuchuje na adresie IP konfiguracji protokołu IP karty sieciowej, a nie tylko w sprzężeniu zwrotnym, które skonfigurowano przy użyciu adresu IP frontonu.
- Sonda nie jest dozwolona przez grupę zabezpieczeń sieci, zaporę systemu operacyjnego gościa maszyny wirtualnej ani filtry warstwy aplikacji.

Użyj **średniej** jako agregacji dla większości scenariuszy.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak mogę sprawdzić statystykę połączenia wychodzącego? 
<details>
  <summary>Rozwiń</summary>
Metryka połączenia z przyłączaniem do translatora adresów sieciowych opisuje ilość pomyślnych i nieudanych połączeń dla [przepływów wychodzących](./load-balancer-outbound-connections.md).

Liczba nieudanych połączeń w liczbie większej niż zero oznacza wyczerpanie portów przez translatora adresów sieciowych. Aby określić, co może powodować te błędy, należy zbadać więcej. Manifesty wyczerpania portów strumienia adresów sieciowych jako niepowodzenie do ustanowienia [przepływu wychodzącego](./load-balancer-outbound-connections.md). Zapoznaj się z artykułem dotyczącym połączeń wychodzących, aby poznać scenariusze i mechanizmy w pracy oraz dowiedzieć się, jak ograniczyć i zaprojektować, aby uniknąć wyczerpania portów. 

Aby uzyskać statystyki połączeń z podłączaniem adresów sieciowych:
1. Wybierz typ metryki połączenia z podłączaniem **adresów sieciowych** i **sumę** jako agregację. 
2. Grupuj według **stanu połączenia** dla zakończonych powodzeniem i niepowodzeniem liczby połączeń z przydziałem adresów sieciowych do reprezentowania przez różne wiersze. 

![Połączenie z przywiązaniem](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek: Load Balancer liczba połączeń z przyłączaniem do adresów sieciowych*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Jak mogę sprawdzić użycie i alokację portu współdziałania?
<details>
  <summary>Rozwiń</summary>
Metryki używanych portów przychodzących adresów sieciowych śledzą liczbę portów, które są używane do obsługi przepływów wychodzących. Wskazuje to liczbę unikatowych przepływów między źródłem internetowym i maszyną wirtualną zaplecza lub zestawem skalowania maszyn wirtualnych, która znajduje się za modułem równoważenia obciążenia i nie ma publicznego adresu IP. Porównując liczbę portów ze strumieniami adresów sieciowych, które są używane z przydzieloną liczbą portów przydzielonej, możesz określić, czy dana usługa występuje, czy też grozi wyczerpaniem przydziałów adresów sieciowych i wychodzącym błędem przepływu. 

Jeśli metryki wskazują na ryzyko awarii [przepływu wychodzącego](./load-balancer-outbound-connections.md) , należy odwołać się do artykułu i podjąć kroki w celu ograniczenia tego problemu w celu zapewnienia kondycji usługi.

Aby wyświetlić użycie i alokację portów przydziałów adresów sieciowych:
1. Ustaw agregację czasu wykresu na 1 minutę, aby zapewnić wyświetlanie żądanych danych.
1. Wybierz **używane porty** i/lub **przydzielony porty** dla przydziałów adresów sieciowych jako typ metryki i **średnią** jako agregację
    * Domyślnie te metryki to średnia liczba portów przydzielone lub używanych przez każdą maszynę wirtualną zaplecza lub VMSS, odpowiadającą wszystkim publicznym adresom IP frontonu zamapowanym na Load Balancer, zagregowanym za pośrednictwem protokołów TCP i UDP.
    * Aby wyświetlić łączną liczbę portów lub przydzielonej do modułu równoważenia obciążenia, użyj **sumy** agregacji metryk
1. Filtrowanie na określony **Typ protokołu**, zestaw **adresów IP zaplecza** i/lub **adresy IP frontonu**.
1. Aby monitorować kondycję według zaplecza lub wystąpienia frontonu, Zastosuj podział. 
    * Dzielenie uwagi pozwala tylko na wyświetlanie pojedynczej metryki. 
1. Na przykład aby monitorować użycie protokołu reportowego dla przepływów TCP na maszynę, należy agregować według **średniej**, podzielić według **adresów IP zaplecza** i filtrować według **typu protokołu**. 

![Alokacja i użycie przydziałów adresów sieciowych](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Ilustracja: Średnia alokacja portów i użycie portu protokołu TCP-adresów sieciowych dla zestawu maszyn wirtualnych zaplecza*

![Użycie źródłowego obiektu podprogramu przez wystąpienie zaplecza](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Rysunek: użycie portów protokołu TCP/adresów sieciowych na wystąpienie zaplecza*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak mogę sprawdzić połączenia przychodzące/wychodzące dla mojej usługi?
<details>
  <summary>Rozwiń</summary>
Metryka pakietów SYN opisuje ilość pakietów TCP SYN, które zostały odebrane lub wysłane (dla [przepływów wychodzących](./load-balancer-outbound-connections.md)), które są skojarzone z określonym frontonem. Ta Metryka służy do zrozumienia prób połączenia TCP z usługą.

Użyj **sum** jako agregacji dla większości scenariuszy.

![Połączenie SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ilustracja: liczba SYN Load Balancer*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak mogę sprawdzić użycie przepustowości sieci? 
<details>
  <summary>Rozwiń</summary>
Metryki bajtów i pakietów opisują ilość bajtów i pakietów, które są wysyłane lub odbierane przez usługę, na podstawie frontonu.

Użyj **sum** jako agregacji dla większości scenariuszy.

Aby uzyskać statystykę liczby bajtów lub pakietów:
1. Wybierz **liczbę bajtów** i/lub typ metryki **Liczba pakietów** , z **sumą** jako agregację. 
2. Wykonaj jedną z następujących czynności:
   * Zastosuj filtr dla określonego adresu IP frontonu, portu frontonu, adresu IP zaplecza lub portu zaplecza.
   * Uzyskaj ogólne statystyki dla zasobu modułu równoważenia obciążenia bez filtrowania.

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ilustracja: liczba bajtów Load Balancer*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Jak mogę zdiagnozować wdrożenie modułu równoważenia obciążenia?
<details>
  <summary>Rozwiń</summary>
Korzystając z kombinacji metryk dostępności ścieżki danych i stanu sondy kondycji na jednym wykresie, można określić, gdzie szukać problemu i rozwiązać problem. Możesz uzyskać gwarancję, że platforma Azure działa prawidłowo i korzystaj z tej wiedzy, aby w sposób niejednoznaczny określić, że konfiguracja lub aplikacja jest główną przyczyną.

Metryki sondy kondycji umożliwiają zrozumienie sposobu, w jaki platforma Azure przegląda kondycję wdrożenia zgodnie z podaną konfiguracją. Spojrzenie na sondy kondycji jest zawsze doskonałym pierwszym krokiem w zakresie monitorowania lub określania przyczyny.

Możesz to zrobić w dalszej części i użyć metryki dostępności ścieżki danych, aby uzyskać wgląd w to, jak platforma Azure przegląda kondycję podstawowej płaszczyzny danych, która jest odpowiedzialna za określone wdrożenie. Podczas łączenia obu metryk można wyizolować miejsce, w którym może się pojawić błąd, jak pokazano w tym przykładzie:

![Łączenie metryk stanu badania dostępności i kondycji ścieżki danych](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Ilustracja: łączenie metryk dostępności ścieżki danych i stanu sondy kondycji*

Na wykresie są wyświetlane następujące informacje:
- Infrastruktura obsługująca maszyny wirtualne była niedostępna, a na początku wykresu wynosi 0 procent. Później infrastruktura była w dobrej kondycji, a maszyny wirtualne były dostępne i więcej niż jedna maszyna wirtualna została umieszczona w zapleczu. Te informacje są wskazywane przez niebieskie śledzenie dostępności ścieżki danych, która była późniejsza o 100 procent. 
- Stan sondy kondycji, wskazywany przez ślad purpurowy, wynosi 0 procent na początku wykresu. Obszar koła w zielonym świetleniu, w którym stan sondy kondycji został w dobrej kondycji i w którym momencie wdrożenie klienta mogło przyjąć Nowe przepływy.

Wykres umożliwia klientom Samodzielne rozwiązywanie problemów ze wdrożeniem bez konieczności odgadnięcia lub poproszenia o ewentualne problemy. Usługa była niedostępna, ponieważ sondy kondycji kończyły się niepowodzeniem z powodu błędnej konfiguracji lub niepowodzenia aplikacji.
</details>

## <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurowanie alertów dla wielowymiarowych metryk ###

Usługa Azure usługa Load Balancer w warstwie Standardowa obsługuje łatwo konfigurowalne alerty dla metryk wielowymiarowych. Skonfiguruj niestandardowe progi dla określonych metryk, aby wyzwalać alerty o zróżnicowanych poziomach ważności w celu zapewnienia środowiska monitorowania zasobów bez dotknięcia.

Aby skonfigurować alerty:
1. Przejdź do bloku podrzędnego alertu dotyczącego usługi równoważenia obciążenia
1. Tworzenie nowej reguły alertu
    1.  Skonfiguruj warunek alertu
    1.  Obowiązkowe Dodaj grupę akcji dla automatycznej naprawy
    1.  Przypisywanie ważności, nazwy i opisu alertu, który umożliwia intuicyjną reagowanie

### <a name="inbound-availability-alerting"></a>Alerty dostępności ruchu przychodzącego
Aby ostrzec o dostępności przychodzącej, można utworzyć dwa osobne alerty przy użyciu metryki dostępności ścieżki danych i stanu sondy kondycji. Klienci mogą mieć różne scenariusze, które wymagają określonej logiki alertów, ale poniższe przykłady będą przydatne w przypadku większości konfiguracji.

Za pomocą funkcji dostępności ścieżki danych można uruchamiać alerty za każdym razem, gdy określona reguła równoważenia obciążenia stanie się niedostępna. Ten alert można skonfigurować przez ustawienie warunku alertu dotyczącego dostępności i dzielenia ścieżki danych przez wszystkie bieżące wartości i przyszłe wartości dla portu frontonu i adresu IP frontonu. Ustawienie logiki alertu na wartość mniejszą lub równą 0 spowoduje, że ten alert zostanie wywołany za każdym razem, gdy jakakolwiek reguła równoważenia obciążenia nie będzie odpowiadać. Ustaw stopień szczegółowości agregacji i częstotliwość oceny zgodnie z wymaganą oceną. 

Stan sondy kondycji można zgłaszać w przypadku niepowodzenia reakcji danego wystąpienia zaplecza na sondę kondycji przez znaczną ilość czasu. Skonfiguruj warunek alertu, aby użyć metryki stanu sondy kondycji i podzielić według adresu IP zaplecza i portu zaplecza. Dzięki temu można generować alerty osobno dla każdego wystąpienia zaplecza, który będzie obsługiwał ruch na określonym porcie. Użyj **średniego** typu agregacji, a następnie ustaw wartość progową zgodnie z częstotliwość sondowania wystąpienia zaplecza i to, co należy wziąć pod uwagę jako próg w dobrej kondycji. 

Możesz również otrzymywać alerty na poziomie puli zaplecza, nie dzieląc przez żadne wymiary i używając **średniego** typu agregacji. Umożliwi to skonfigurowanie reguł alertów, takich jak alert, gdy 50% członków puli zaplecza jest w złej kondycji.

### <a name="outbound-availability-alerting"></a>Alerty dostępności wychodzącej
Aby skonfigurować dostęp do ruchu wychodzącego, można skonfigurować dwa osobne alerty przy użyciu licznika połączeń z przyłączaniem i używanych metryk portów.

Aby wykryć błędy połączenia wychodzącego, skonfiguruj alert przy użyciu liczby połączeń z przydziałem adresów sieciowych i przefiltruj stan połączenia = niepowodzenie. Użyj agregacji **całkowitej** . Następnie można podzielić tę wartość przez adres IP zaplecza na wszystkie bieżące i przyszłe wartości, aby otrzymywać alerty osobno dla każdego wystąpienia zaplecza, dla którego występują nieudane połączenia. Ustaw próg na wartość większą niż zero lub wyższą liczbę, Jeśli spodziewasz się, że niektóre błędy połączenia wychodzącego są widoczne.

Za pomocą portów protokołu wiązania adresów sieciowych można otrzymywać alerty na wyższym poziomie ryzyka wyczerpania i błędów połączenia wychodzącego. Upewnij się, że podczas korzystania z tego alertu są dzielone adresy IP i protokoły zaplecza, i Użyj **średniej** agregacji. Ustaw wartość progową na wartość większą niż wyrażona w procentach liczba portów przyznanych na wystąpienie uznawane za niebezpieczne. Można na przykład skonfigurować alert o niskiej ważności, gdy wystąpienie zaplecza użyje 75% jego przyznanych portów i o wysokiej ważności, gdy korzysta z 90% lub 100% przyznanych portów.  
## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów usługa Load Balancer w warstwie Standardowa jest udostępniany za pośrednictwem istniejącej **kondycji zasobów** w obszarze **monitorowanie > Service Health**. Jest ona oceniana co **dwie minuty** przez pomiar dostępności ścieżki danych, która określa, czy punkty końcowe równoważenia obciążenia frontonu są dostępne.

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępne | Zasób standardowego modułu równoważenia obciążenia jest w dobrej kondycji i jest dostępny. |
| Obniżona wydajność | Moduł równoważenia obciążenia w warstwie Standardowa ma zdarzenia zainicjowane przez platformę lub użytkownika, które mają wpływ na wydajność. Metryka Dostępność ścieżki danych od co najmniej dwóch minut zgłasza kondycję niższą niż 90%, ale wyższą niż 25%. Zostanie napotkany umiarkowany wpływ na wydajność. [Postępuj zgodnie z przewodnikiem rozwiązywania problemów z systemie RHC występuje,](./troubleshoot-rhc.md) aby ustalić, czy istnieją zdarzenia zainicjowane przez użytkownika, które powodują wpływ na dostępność.
| Niedostępny | Zasób standardowego modułu równoważenia obciążenia nie jest w dobrej kondycji. Metryka dostępności ścieżki datapath zgłosiła mniej niż 25% kondycji przez co najmniej dwie minuty. Wystąpi znaczny wpływ na wydajność lub brak dostępności dla łączności przychodzącej. Mogą istnieć zdarzenia użytkownika lub platformy powodujące niedostępność. [Postępuj zgodnie z przewodnikiem rozwiązywania problemów z systemie RHC występuje,](./troubleshoot-rhc.md) aby ustalić, czy istnieją zdarzenia zainicjowane przez użytkownika, które mają wpływ na dostępność. |
| Nieznane | Stan kondycji zasobu dla zasobu standardowego modułu równoważenia obciążenia nie został jeszcze zaktualizowany lub nie otrzymał informacji o dostępności ścieżki danych dla ostatnich 10 minut. Ten stan powinien występować przejściowo i zmienić się na prawidłowy stan po otrzymaniu danych. |

Aby wyświetlić kondycję publicznych zasobów usługa Load Balancer w warstwie Standardowa:
1. Wybierz pozycję **Monitoruj**  >  **Service Health**.

   ![Strona monitorowanie](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ilustracja: łącze Service Health na Azure Monitor*

2. Wybierz pozycję **Resource Health**, a następnie upewnij się, że wybrano opcję **Identyfikator subskrypcji** i **Typ zasobu = Load Balancer** .

   ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ilustracja: Wybieranie zasobów dla widoku kondycji*

3. Z listy wybierz zasób Load Balancer, aby wyświetlić jego historyczny stan kondycji.

    ![Load Balancer stan kondycji](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ilustracja: widok kondycji zasobów Load Balancer*
 
Opis ogólnego stanu kondycji zasobu jest dostępny w [dokumentacji systemie RHC występuje](../service-health/resource-health-overview.md). Dla określonych stanów Azure Load Balancer są wymienione w poniższej tabeli: 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z usługi [Insights](./load-balancer-insights.md) , aby wyświetlić te metryki wstępnie skonfigurowane dla Load Balancer
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](./load-balancer-overview.md).
- Dowiedz się więcej o [łączności wychodzącej modułu równoważenia obciążenia](./load-balancer-outbound-connections.md).
- Dowiedz się więcej na temat [Azure monitor](../azure-monitor/overview.md).
- Dowiedz się więcej o [interfejsie API rest Azure monitor](/rest/api/monitor/) i [sposobach pobierania metryk za pośrednictwem interfejsu API REST](/rest/api/monitor/metrics/list).