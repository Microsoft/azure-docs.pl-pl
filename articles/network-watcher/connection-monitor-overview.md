---
title: Monitor połączeń na platformie Azure | Microsoft Docs
description: Dowiedz się, jak używać monitora połączeń do monitorowania komunikacji sieciowej w środowisku rozproszonym.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 0fa5e09dbe7c0a8cd45557d535353ea4a0a00b16
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833103"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Monitorowanie łączności sieciowej z monitorem połączeń

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Nie będzie również można dodawać nowych monitorów połączeń w monitorze połączeń (klasyczny). Można nadal korzystać z testów i monitorów połączeń utworzonych przed 1 lipca 2021. Aby zminimalizować przerwy w działaniu usługi z bieżącymi obciążeniami, należy [migrować testy z Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) lub  [przeprowadzić migrację z monitora połączeń (klasycznego)](migrate-to-connection-monitor-from-connection-monitor-classic.md) do nowego monitora połączeń na platformie Azure Network Watcher przed 29 lutego 2024.

Monitor połączeń zapewnia ujednolicone kompleksowe monitorowanie połączeń w usłudze Azure Network Watcher. Funkcja monitor połączeń obsługuje wdrożenia hybrydowe i w chmurze platformy Azure. Network Watcher udostępnia narzędzia do monitorowania, diagnozowania i wyświetlania metryk związanych z łącznością dla wdrożeń platformy Azure.

Poniżej przedstawiono niektóre przypadki użycia monitora połączeń:

- Maszyna wirtualna serwera frontonu sieci Web komunikuje się z maszyną wirtualną serwera bazy danych w aplikacji wielowarstwowej. Chcesz sprawdzić łączność sieciową między dwiema maszynami wirtualnymi.
- Chcesz, aby maszyny wirtualne w regionie Wschodnie stany USA mogli wysyłać polecenia ping do maszyn wirtualnych w regionie Środkowe stany USA, a chcesz porównać opóźnienia sieci między regionami.
- Masz wiele lokalnych witryn biurowych w Seattle, Waszyngton i w Ashburn, Wirginia. Witryny pakietu Office nawiązują połączenie z adresami URL Microsoft 365. Dla użytkowników Microsoft 365 adresów URL Porównaj opóźnienia między Seattle i Ashburn.
- Aplikacja hybrydowa wymaga połączenia z punktem końcowym usługi Azure Storage. Lokacja lokalna i aplikacja platformy Azure nawiązują połączenie z tym samym punktem końcowym usługi Azure Storage. Chcesz porównać opóźnienia lokacji lokalnej z opóźnieniami aplikacji platformy Azure.
- Chcesz sprawdzić łączność między konfiguracjami lokalnymi i maszynami wirtualnymi platformy Azure, które obsługują aplikację w chmurze.

Monitor połączeń łączy najlepsze dwie funkcje: funkcja [monitor połączeń Network Watcher (klasyczna)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) i [monitor łączności usługi](../azure-monitor/insights/network-performance-monitor-service-connectivity.md)Network Performance Monitor (npm), [monitorowanie ExpressRoute](../expressroute/how-to-npm.md)i [monitorowanie wydajności](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) .

Poniżej przedstawiono niektóre zalety monitora połączeń:

* Ujednolicone, intuicyjne środowisko dla platform Azure i wymagania dotyczące monitorowania hybrydowego
* Międzyregionowe Monitorowanie łączności między obszarami roboczymi
* Wyższe częstotliwości sondowania i lepszy wgląd w wydajność sieci
* Szybsze generowanie alertów dla wdrożeń hybrydowych
* Obsługa sprawdzeń łączności opartych na protokole HTTP, TCP i ICMP 
* Metryki i Log Analytics obsługujące konfiguracje testów platformy Azure i innych niż platformy Azure

![Diagram przedstawiający sposób interakcji monitora połączeń z maszynami wirtualnymi platformy Azure, hostami spoza platformy Azure, punktami końcowymi i lokalizacjami przechowywania danych](./media/connection-monitor-2-preview/hero-graphic.png)

Aby rozpocząć korzystanie z monitora połączeń do monitorowania, wykonaj następujące kroki: 

1. Zainstaluj agentów monitorowania.
1. Włącz Network Watcher w ramach subskrypcji.
1. Utwórz monitor połączeń.
1. Konfigurowanie analizy danych i alertów.
1. Diagnozuj problemy w sieci.

Poniższe sekcje zawierają szczegółowe informacje dotyczące tych kroków.

## <a name="install-monitoring-agents"></a>Zainstaluj agentów monitorowania

Monitor połączeń opiera się na lekkich plikach wykonywalnych w celu uruchomienia kontroli łączności. Obsługuje ona sprawdzanie łączności ze środowiskami platformy Azure i środowiskami lokalnymi. Plik wykonywalny, którego używasz, zależy od tego, czy maszyna wirtualna jest hostowana na platformie Azure, czy lokalnie.

### <a name="agents-for-azure-virtual-machines"></a>Agenci usługi Azure Virtual Machines

Aby monitor połączeń rozpoznawał maszyny wirtualne platformy Azure jako źródła monitorowania, należy zainstalować na nich rozszerzenie maszyny wirtualnej agenta Network Watcher. To rozszerzenie jest również znane jako *rozszerzenie Network Watcher*. Usługa Azure Virtual Machines wymaga rozszerzenia, aby wyzwolić kompleksowe monitorowanie i inne zaawansowane funkcje. 

Rozszerzenie Network Watcher można zainstalować podczas [tworzenia maszyny wirtualnej](./connection-monitor.md#create-the-first-vm). Można również oddzielnie instalować, konfigurować i rozwiązywać problemy z rozszerzeniem Network Watcher dla systemów [Linux](../virtual-machines/extensions/network-watcher-linux.md) i [Windows](../virtual-machines/extensions/network-watcher-windows.md).

Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) lub zapory mogą blokować komunikację między lokalizacją źródłową i docelową. Monitor połączeń wykrywa ten problem i wyświetla go jako komunikat diagnostyczny w topologii. Aby włączyć monitorowanie połączeń, upewnij się, że reguły sieciowej grupy zabezpieczeń i zapory zezwalają na pakiety przez TCP lub ICMP między źródłem a miejscem docelowym.

### <a name="agents-for-on-premises-machines"></a>Agenci dla maszyn lokalnych

Aby monitor połączeń mógł rozpoznać maszyny lokalne jako źródła monitorowania, Zainstaluj agenta Log Analytics na komputerach. Następnie Włącz Network Performance Monitor rozwiązanie. Ci agenci są połączeni z obszarami roboczymi Log Analytics, więc musisz skonfigurować identyfikator obszaru roboczego i klucz podstawowy, aby umożliwić agentom rozpoczęcie monitorowania.

Aby zainstalować agenta Log Analytics dla maszyn z systemem Windows, zobacz [Azure monitor rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).

Jeśli ścieżka zawiera zapory lub wirtualne urządzenia sieciowe (urządzeń WUS), upewnij się, że lokalizacja docelowa jest osiągalna.

## <a name="enable-network-watcher-on-your-subscription"></a>Włącz Network Watcher w ramach subskrypcji

Wszystkie subskrypcje z siecią wirtualną są włączane przy użyciu Network Watcher. Podczas tworzenia sieci wirtualnej w ramach subskrypcji Network Watcher jest automatycznie włączana w regionie i subskrypcji sieci wirtualnej. To automatyczne włączenie nie wpływa na zasoby ani nie powoduje naliczania opłat. Upewnij się, że Network Watcher nie jest jawnie wyłączona w Twojej subskrypcji. 

Aby uzyskać więcej informacji, zobacz [włączanie Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń 

Monitor połączeń monitoruje komunikację w regularnych odstępach czasu. Informuje o zmianach w zakresie osiągalności i opóźnień. Możesz również sprawdzić bieżącą i historyczną topologię sieci między agentami źródłowymi i docelowymi punktami końcowymi.

Źródłami mogą być maszyny wirtualne platformy Azure lub maszyny lokalne z zainstalowanym agentem monitorowania. Docelowymi punktami końcowymi mogą być Microsoft 365 adresów URL, Dynamics 365 adresów URL, niestandardowych adresów URL, identyfikatorów zasobów maszyn wirtualnych platformy Azure, adresów IPv4, IPv6, nazw FQDN lub dowolnych nazw domen.

### <a name="access-connection-monitor"></a>Dostęp do monitora połączeń

1. Na stronie głównej Azure Portal przejdź do **Network Watcher**.
1. Po lewej stronie w sekcji **monitorowanie** wybierz pozycję **monitor połączeń**.
1. Zobaczysz wszystkie monitory połączeń, które zostały utworzone w monitorze połączenia. Aby wyświetlić monitory połączeń, które zostały utworzone w klasycznym środowisku monitora połączeń, przejdź do karty **monitor połączeń** .
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Zrzut ekranu przedstawiający monitory połączeń, które zostały utworzone w monitorze połączeń" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń

W monitorach połączeń utworzonych w monitorze połączeń można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub dowolnym innym adresem URL lub adresie IP.

Monitor połączeń obejmuje następujące jednostki:

* **Zasób monitora połączeń** — zasób platformy Azure specyficzny dla regionu. Wszystkie poniższe jednostki są właściwościami zasobu monitora połączeń.
* **Endpoint** — Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykładowe punkty końcowe obejmują maszyny wirtualne platformy Azure, agentów lokalnych, adresy URL i adresy IP.
* **Konfiguracja testu** — Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa** — grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test** — połączenie źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

 ![Diagram przedstawiający monitor połączeń, który definiuje relację między grupami testów i testami](./media/connection-monitor-2-preview/cm-tg-2.png)

Monitor połączeń można utworzyć przy użyciu [Azure Portal](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) lub [PowerShell](connection-monitor-create-using-powershell.md) .

Wszystkie źródła, miejsca docelowe i konfiguracje testów dodawane do grupy testowej są podzielone na poszczególne testy. Oto przykład sposobu, w jaki źródła i miejsca docelowe są podzielone:

* Grupa testowa: TG1
* Źródła: 3 (A, B, C)
* Miejsca docelowe: 2 (D, E)
* Konfiguracje testów: 2 (Konfiguracja 1, konfiguracja 2)
* Łączna liczba utworzonych testów: 12

| Numer testu | Element źródłowy | Element docelowy | Konfiguracja testu |
| --- | --- | --- | --- |
| 1 | A | D | Konfiguracja 1 |
| 2 | A | D | Konfiguracja 2 |
| 3 | A | E | Konfiguracja 1 |
| 4 | A | E | Konfiguracja 2 |
| 5 | B | D | Konfiguracja 1 |
| 6 | B | D | Konfiguracja 2 |
| 7 | B | E | Konfiguracja 1 |
| 8 | B | E | Konfiguracja 2 |
| 9 | C | D | Konfiguracja 1 |
| 10 | C | D | Konfiguracja 2 |
| 11 | C | E | Konfiguracja 1 |
| 12 | C | E | Konfiguracja 2 |

### <a name="scale-limits"></a>Limity skalowania

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączeń: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analizowanie danych monitorowania i Ustawianie alertów

Po utworzeniu monitora połączeń źródła sprawdzają łączność z miejscem docelowym na podstawie konfiguracji testu.

### <a name="checks-in-a-test"></a>Sprawdza w teście

W zależności od protokołu, który został wybrany w konfiguracji testu, monitor połączeń uruchamia serię kontroli pary Source-Destination. Kontrole są przeprowadzane zgodnie z wybraną częstotliwością testu.

W przypadku korzystania z protokołu HTTP usługa oblicza liczbę odpowiedzi HTTP, które zwróciły prawidłowy kod odpowiedzi. Prawidłowe kody odpowiedzi można ustawić przy użyciu programu PowerShell i interfejsu wiersza polecenia. Wynik określa procent testów zakończonych niepowodzeniem. Aby obliczyć RTT, usługa mierzy czas między wywołaniem HTTP a odpowiedzią.

W przypadku korzystania z protokołu TCP lub ICMP usługa oblicza procent utraty pakietów, aby określić procent niezakończonych testów. Aby obliczyć RTT, usługa mierzy czas potrzebny do otrzymania potwierdzenia (ACK) dla wysłanych pakietów. W przypadku włączenia danych traceroute dla testów sieci można zobaczyć utratę skoku i opóźnienie dla sieci lokalnej.

### <a name="states-of-a-test"></a>Stany testu

Na podstawie danych zwracanych przez testy testy mogą mieć następujące stany:

* Wartości **Pass** — rzeczywiste dla procentu nieudanych testów i RTT znajdują się w określonych progach.
* **Niepowodzenie** — wartości rzeczywiste dla procentu nieudanych testów lub RTT przekroczyły określone progi. Jeśli nie określono progu, test osiągnie stan niepowodzenia, gdy procent testów zakończonych niepowodzeniem wynosi 100.
* **Ostrzeżenie** — 
     * Jeśli jest określony próg, a monitor połączenia sprawdza, czy procent nie powiódł się więcej niż 80% wartości progowej, test jest oznaczany jako ostrzeżenie.
     * W przypadku braku określonych progów monitor połączeń automatycznie przypisuje próg. Po przekroczeniu tego progu stan testu zmieni się na ostrzeżenie.W przypadku czasu błądzenia w testach TCP lub ICMP próg jest 750msec. W przypadku czeków zakończonych niepowodzeniem wartość progowa wynosi 10%. 
* **Nieokreślone**   — Brak danych w obszarze roboczym Log Analytics.Sprawdź metryki. 
* **Nie uruchomiono**   — Wyłączone przez wyłączenie grupy testowej  

### <a name="data-collection-analysis-and-alerts"></a>Zbieranie danych, analiza i alerty

Dane zbierane przez Monitor połączeń są przechowywane w obszarze roboczym Log Analytics. Ten obszar roboczy jest skonfigurowany podczas tworzenia monitora połączeń. 

Dane monitorowania są również dostępne w metrykach Azure Monitor. Możesz użyć Log Analytics, aby zachować swoje dane monitorowania tak długo, jak chcesz. Azure Monitor przechowuje metryki tylko przez 30 dni. 

[Na podstawie danych można ustawiać alerty oparte na metrykach](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorowanie pulpitów nawigacyjnych

Na pulpitach nawigacyjnych monitorowania zostanie wyświetlona lista monitorów połączeń, do których można uzyskać dostęp do subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych.

Po przejściu do monitora połączeń z Network Watcher można wyświetlić dane według:

* **Monitor połączeń** — lista wszystkich monitorów połączeń utworzonych dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Ten widok jest domyślny.
* **Grupy testów** — lista wszystkich grup testowych utworzonych dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Te grupy testowe nie są filtrowane według monitorów połączeń.
* **Test** — lista wszystkich testów, które są uruchamiane dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Testy te nie są filtrowane według monitorów połączeń ani grup testowych.

Na poniższej ilustracji trzy widoki danych są wskazywane przez strzałkę 1.

Na pulpicie nawigacyjnym można rozwinąć każdy monitor połączeń, aby wyświetlić jego grupy testowe. Następnie można rozwinąć każdą grupę testową, aby zobaczyć testy, które w niej działają. 

Listę można filtrować na podstawie:

* **Filtry najwyższego poziomu** — lista wyszukiwania według tekstu, typ jednostki (Monitor połączeń, Grupa testów lub test) i zakres. Zakres obejmuje subskrypcje, regiony, źródła i typy docelowe. Zapoznaj się z polem 1 na poniższej ilustracji.
* **Filtry oparte na stanie** — Filtruj według stanu monitora połączenia, grupy testowej lub testu. Zobacz pole 2 na poniższej ilustracji.
* **Filtrowanie filtrów opartych na alertach** według alertów wyzwalanych w ramach zasobu monitora połączeń. Zobacz pole 3 na poniższej ilustracji.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Zrzut ekranu przedstawiający sposób filtrowania widoków monitorów połączeń, grup testowych i testów w monitorze połączeń " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Na przykład aby zobaczyć wszystkie testy w monitorze połączeń, gdzie źródłowy adres IP to 10.192.64.56:
1. Zmień widok na **test**.
1. W polu wyszukiwania wpisz *10.192.64.56*
1. W polu **zakres** w filtr najwyższego poziomu wybierz pozycję **źródła**.

Aby wyświetlić tylko testy zakończone niepowodzeniem w monitorze połączeń, gdzie źródłowy adres IP to 10.192.64.56:
1. Zmień widok na **test**.
1. W przypadku filtru opartego na stanie wybierz pozycję **Niepowodzenie**.
1. W polu wyszukiwania wpisz *10.192.64.56*
1. W polu **zakres** w filtr najwyższego poziomu wybierz pozycję **źródła**.

Aby wyświetlić tylko testy zakończone niepowodzeniem w monitorze połączeń, gdzie miejsce docelowe to outlook.office365.com:
1. Zmień widok na **test**.
1. W przypadku filtru opartego na stanie wybierz pozycję **Niepowodzenie**.
1. W polu wyszukiwania wprowadź *Office.Live.com*
1. W polu **zakres** w filtrze najwyższego poziomu wybierz pozycję **miejsca docelowe**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Zrzut ekranu przedstawiający widok, który jest filtrowany do wyświetlania tylko testów zakończonych niepowodzeniem dla miejsca docelowego Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Aby poznać przyczynę niepowodzenia monitora połączenia lub grupy testowej lub testu, kliknij kolumnę o nazwie przyczyna.  Oznacza to, który próg (sprawdzenia nie powiodło się% lub RTT) został naruszony i powiązane komunikaty diagnostyczne
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Zrzut ekranu przedstawiający przyczynę niepowodzenia dla monitora połączeń, testu lub grupy testów" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Aby wyświetlić trendy w RTT i procent nieudanych testów dla monitora połączeń:
1. Wybierz monitor połączeń, który chcesz zbadać.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Zrzut ekranu przedstawiający metryki monitora połączeń wyświetlane przez grupę testową" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Zostaną wyświetlone następujące sekcje:  
    1. Podstawowe informacje o właściwościach wybranego monitora połączeń 
    1. Podsumowanie 
        1. Zagregowane linie trendu dla RTT i procent testów zakończonych niepowodzeniem dla wszystkich testów w monitorze połączeń. Można ustawić określony czas, aby wyświetlić szczegóły.
        1. 5 najpopularniejszych grup testowych, źródeł i miejsc docelowych w oparciu o RTT lub procent testów zakończonych niepowodzeniem. 
    1. Karty grup testowych, źródeł, miejsc docelowych i konfiguracji testów — lista grup testowych, źródeł lub miejsc docelowych w monitorze połączeń. Sprawdzanie testów nie powiodło się, zagregowany czas RTT i sprawdzenia nie powiodły się% wartości%.  Możesz także wrócić do obszaru czasu, aby wyświetlić dane. 
    1. Problemy — problemy poziomu przeskoku dla każdego testu w monitorze połączeń. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Zrzut ekranu przedstawiający metryki monitora połączeń wyświetlane przez grupę testową (część 2)" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Można
    * Kliknij pozycję Wyświetl wszystkie testy — aby wyświetlić wszystkie testy w monitorze połączeń
    * Kliknij pozycję Wyświetl wszystkie grupy testowe, konfiguracje testów, źródła i miejsca docelowe — aby wyświetlić szczegółowe informacje specyficzne dla każdego z nich. 
    * Wybierz grupę testową, konfigurację testu, Źródło lub miejsce docelowe, aby wyświetlić wszystkie testy w jednostce.

1. W widoku Wyświetl wszystkie testy można:
    * Wybierz pozycję testy i kliknij przycisk Porównaj.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Zrzut ekranu przedstawiający porównanie 2 testów" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Używanie klastra do rozszerzania zasobów złożonych, takich jak sieć wirtualna, podsieci do zasobów podrzędnych
    * Wyświetl topologię dla dowolnych testów, klikając opcję topologia.

Aby wyświetlić trendy w RTT i procent nieudanych testów dla grupy testowej:
1. Wybierz grupę testową, którą chcesz zbadać. 
1. Zobaczysz podobny do monitora połączeń — Essentials, podsumowania, tabeli dla grup testów, źródeł, miejsc docelowych i konfiguracji testów. Nawiguj do nich tak jak w przypadku monitora połączeń

Aby wyświetlić trendy w RTT i procent testów zakończonych niepowodzeniem dla testu:
1. Wybierz test, który chcesz zbadać. Zostanie wyświetlona topologia sieci i kompleksowe wykresy trendów dla testów zakończonych niepowodzeniem% i czas błądzenia. Aby zobaczyć zidentyfikowane problemy, w topologii wybierz dowolny przeskok w ścieżce. (Te przeskoki to zasoby platformy Azure). Ta funkcja nie jest obecnie dostępna dla sieci lokalnych

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Zrzut ekranu przedstawiający widok topologii testu" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Rejestruj zapytania w Log Analytics

Użyj Log Analytics, aby utworzyć niestandardowe widoki danych monitorowania. Wszystkie dane wyświetlane przez interfejs użytkownika znajdują się w Log Analytics. Możesz interaktywnie analizować dane w repozytorium. Skorelowanie danych z Agent Health lub innych rozwiązań opartych na Log Analytics. Wyeksportuj dane do programu Excel lub Power BI lub Utwórz link możliwy do udostępnienia.

#### <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

W monitorach połączeń utworzonych przed rozpoczęciem korzystania z monitora połączeń są dostępne wszystkie cztery metryki:% sond nie powiodło się, AverageRoundtripMs, ChecksFailedPercent (wersja zapoznawcza) i RoundTripTimeMs (wersja zapoznawcza). W monitorach połączeń, które zostały utworzone w środowisku monitora połączeń, dane są dostępne tylko dla metryk, które są znakowane przy użyciu *(wersja zapoznawcza)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Zrzut ekranu przedstawiający metryki w monitorze połączeń" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Korzystając z metryk, ustaw typ zasobu jako Microsoft. Network/networkWatchers/connectionMonitors

| Metric | Nazwa wyświetlana | Jednostka | Typ agregacji | Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (klasyczny) | % Sond nie powiodło się (klasyczne) | Procent | Średnia | Procent sond monitorowania łączności nie powiódł się. | Brak wymiarów |
| AverageRoundtripMs (klasyczny) | Średni czas błądzenia (MS) (klasyczny) | ) | Średnia | Średni czas RTT sieci dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym. |             Brak wymiarów |
| ChecksFailedPercent | % Testów zakończonych niepowodzeniem | Procent | Średnia | Procent testów zakończonych niepowodzeniem dla testu. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Identyfikator sourceresourceid <br>SourceType <br>Protokół <br>DestinationAddress <br>Obiekt docelowy <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region (Region) |
| RoundTripTimeMs | Czas błądzenia (MS) | ) | Średnia | Czas RTT dla czeków wysyłanych między źródłem a miejscem docelowym. Ta wartość nie jest średnia. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Identyfikator sourceresourceid <br>SourceType <br>Protokół <br>DestinationAddress <br>Obiekt docelowy <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region (Region) |
| TestResult | Wynik testu | Liczba | Średnia | Wynik testu monitora połączeń | SourceAddress <br>SourceName <br>Identyfikator sourceresourceid <br>SourceType <br>Protokół <br>DestinationAddress <br>Obiekt docelowy <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Alerty na podstawie metryk dla monitora połączeń

Alerty metryki można tworzyć na monitorach połączeń przy użyciu poniższych metod 

1. Z monitora połączeń podczas tworzenia monitora połączeń [przy użyciu Azure Portal](connection-monitor-preview-create-using-portal.md#) 
1. Z poziomu monitora połączeń przy użyciu polecenia "Konfiguruj alerty" na pulpicie nawigacyjnym 
1. W programie Azure Monitor — aby utworzyć alert w Azure Monitor: 
    1. Wybierz zasób monitor połączeń, który został utworzony w monitorze połączenia.
    1. Upewnij się, że **Metryka** jest wyświetlana jako typ sygnału dla monitora połączenia.
    1. W polu **Dodaj warunek** dla **nazwy sygnału** wybierz pozycję **ChecksFailedPercent (wersja zapoznawcza)** lub **RoundTripTimeMs (wersja zapoznawcza)**.
    1. W obszarze **Typ sygnału** wybierz pozycję **metryki**. Na przykład wybierz pozycję **ChecksFailedPercent (wersja zapoznawcza)**.
    1. Zostaną wyświetlone wszystkie wymiary metryki. Wybierz nazwę wymiaru i wartość wymiaru. Na przykład wybierz pozycję **adres źródłowy** , a następnie wprowadź adres IP dowolnego źródła w monitorze połączenia.
    1. W obszarze **logika alertu** podaj następujące informacje:
        * **Typ warunku**: **statyczny**.
        * **Warunek** i **próg**.
        * **Stopień szczegółowości agregacji i częstotliwość oceny**: Monitor połączenia aktualizuje dane co minutę.
    1. W obszarze **Akcje** wybierz grupę akcji.
    1. Podaj szczegóły alertu.
    1. Utwórz regułę alertu.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Zrzut ekranu przedstawiający obszar Tworzenie reguły w Azure Monitor. Wyróżnia się adresy źródłowe i źródłowe nazwy punktów końcowych" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnozowanie problemów w sieci

Monitor połączeń ułatwia diagnozowanie problemów z monitorem połączeń i sieci. Problemy w sieci hybrydowej są wykrywane przez zainstalowane wcześniej agenci Log Analytics. Problemy na platformie Azure są wykrywane przez rozszerzenie Network Watcher. 

Problemy w sieci platformy Azure można wyświetlić w topologii sieci.

W przypadku sieci, których źródła są lokalnymi maszynami wirtualnymi, można wykryć następujące problemy:

* Upłynął limit czasu żądania.
* Punkt końcowy nie został rozpoznany przez system DNS — tymczasowy lub trwały. Nieprawidłowy adres URL.
* Nie znaleziono hostów.
* Źródło nie może połączyć się z miejscem docelowym. Element docelowy jest nieosiągalny za poorednictwem protokołu ICMP.
* Problemy związane z certyfikatem: 
    * Certyfikat klienta jest wymagany do uwierzytelnienia agenta. 
    * Lista relokacji certyfikatów jest niedostępna. 
    * Nazwa hosta punktu końcowego nie jest zgodna z podmiotem lub alternatywną nazwą podmiotu certyfikatu. 
    * Brak certyfikatu głównego w magazynie zaufanych urzędów certyfikacji komputera lokalnego. 
    * Certyfikat SSL wygasł, nieprawidłowy, odwołany lub niezgodny.

W przypadku sieci, których źródła są maszynami wirtualnymi platformy Azure, można wykryć następujące problemy:

* Problemy z agentem:
    * Agent został zatrzymany.
    * Rozpoznawanie nazw DNS nie powiodło się.
    * Brak aplikacji lub odbiornika nasłuchujących na porcie docelowym.
    * Nie można otworzyć gniazda.
* Problemy ze stanem maszyny wirtualnej: 
    * Uruchamianie
    * Zatrzymywanie
    * Zatrzymano
    * Cofanie przydziału
    * Cofnięto przydział
    * Ponowny rozruch
    * Nie przydzielono
* Brak wpisu tabeli ARP.
* Ruch został zablokowany z powodu lokalnych problemów z zaporą lub reguł sieciowej grupy zabezpieczeń.
* Problemy z bramą sieci wirtualnej: 
    * Brak tras.
    * Tunel między dwoma bramami jest odłączony lub nie istnieje.
    * Druga Brama nie została znaleziona przez tunel.
    * Nie znaleziono informacji o komunikacji równorzędnej.
* Brak trasy w przeglądarce Microsoft Edge.
* Ruch zatrzymany z powodu tras systemowych lub UDR.
* Protokół BGP nie jest włączony dla połączenia bramy.
* Sonda DIP jest wyłączona w module równoważenia obciążenia.

## <a name="next-steps"></a>Następne kroki
    
   * Dowiedz się [, jak utworzyć monitor połączeń przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)  
   * Dowiedz się [, jak utworzyć monitor połączeń przy użyciu ARMClient](./connection-monitor-create-using-template.md)
