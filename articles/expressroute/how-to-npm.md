---
title: 'Azure ExpressRoute: Konfigurowanie NPM dla obwodów'
description: Skonfiguruj monitorowanie sieci oparte na chmurze (NPM) dla obwodów usługi Azure ExpressRoute. Obejmuje to monitorowanie ExpressRoute prywatnej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 907c03bd15463368def316e72f55ce214cb3e617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571041"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute

Ten artykuł ułatwia skonfigurowanie rozszerzenia Network Performance Monitor do monitorowania ExpressRoute. Network Performance Monitor (NPM) to program do monitorowania sieci w chmurze, który monitoruje łączność między wdrożeniami w chmurze na platformie Azure a lokalizacjami lokalnymi (oddziałami firmy itp.). Program NPM jest częścią dzienników usługi Azure Monitor. Program NPM oferuje rozszerzenie dla usługi ExpressRoute, które pozwala monitorować wydajność sieci przez obwody usługi ExpressRoute skonfigurowane do korzystania z prywatnej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu programu NPM dla usługi ExpressRoute można wykrywać problemy z siecią, określać ich przyczyny i je eliminować. Ta usługa jest również dostępna dla platformy Azure Government Cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Oto co możesz zrobić:

* Monitorowanie strat i opóźnień w różnych sieci wirtualnych i Ustawianie alertów

* Monitoruj wszystkie ścieżki (w tym nadmiarowe ścieżki) w sieci

* Rozwiązywanie problemów z siecią przejściową i punktem w czasie, które trudno się replikować

* Pomoc w ustaleniu określonego segmentu w sieci, który jest odpowiedzialny za obniżoną wydajność

* Uzyskaj przepływność na sieć wirtualną (Jeśli masz agentów zainstalowanych w każdej sieci wirtualnej)

* Zobacz stan systemu ExpressRoute z wcześniejszego punktu w czasie

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Agenci monitorowania są zainstalowani na wielu serwerach, zarówno lokalnie, jak i na platformie Azure. Agenci komunikują się ze sobą, ale nie wysyłają danych, wysyłają pakiety uzgadniania protokołu TCP. Komunikacja między agentami umożliwia platformie Azure zamapowanie topologii sieci i ścieżki, które może zająć ruch.

1. Utwórz obszar roboczy NPM. Jest to taka sama jak obszar roboczy Log Analytics.
2. Instalowanie i konfigurowanie agentów oprogramowania. (Jeśli chcesz monitorować tylko komunikację równorzędną firmy Microsoft, nie musisz instalować i konfigurować agentów oprogramowania): 
    * Zainstaluj agentów monitorowania na serwerach lokalnych i maszynach wirtualnych platformy Azure (dla prywatnej komunikacji równorzędnej).
    * Skonfiguruj ustawienia na serwerach agenta monitorowania, aby umożliwić komunikację z agentami monitorowania. (Otwarte porty zapory itp.)
3. Skonfiguruj reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby umożliwić agentowi monitorowania zainstalowanemu na maszynach wirtualnych platformy Azure komunikowanie się z lokalnymi agentami monitorowania.
4. Konfigurowanie monitorowania: wykrywaj i Zarządzaj sieciami, które są widoczne w NPM.

Jeśli używasz już Network Performance Monitor do monitorowania innych obiektów lub usług i masz już obszar roboczy w jednym z obsługiwanych regionów, możesz pominąć krok 1 i krok 2 i rozpocząć konfigurację z krok 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Krok 1. Tworzenie obszaru roboczego

Utwórz obszar roboczy w subskrypcji, który ma link sieci wirtualnych do obwodów usługi ExpressRoute.

1. W [Azure Portal](https://portal.azure.com)wybierz subskrypcję, która ma sieci wirtualnych komunikację równorzędną z obwodem usługi ExpressRoute. Następnie przeszukaj listę usług w **witrynie Marketplace** dla "Network Performance Monitor". W polu Wróć kliknij, aby otworzyć stronę **Network Performance Monitor** .

   >[!NOTE]
   >Można utworzyć nowy obszar roboczy lub użyć istniejącego obszaru roboczego. Jeśli chcesz użyć istniejącego obszaru roboczego, musisz upewnić się, że obszar roboczy został zmigrowany do nowego języka zapytań. [Więcej informacji...](../azure-monitor/logs/log-query-overview.md)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. W dolnej części strony głównej **Network Performance Monitor** kliknij pozycję **Utwórz** , aby otworzyć **Network Performance Monitor — Utwórz nową stronę rozwiązania** . Kliknij **log Analytics obszarze roboczym — wybierz obszar roboczy** , aby otworzyć stronę obszary robocze. Kliknij pozycję **+ Utwórz nowy obszar roboczy** , aby otworzyć stronę obszaru roboczego.
3. Na stronie **obszar roboczy log Analytics** wybierz pozycję **Utwórz nową**, a następnie skonfiguruj następujące ustawienia:

   * Log Analytics obszarze roboczym — wpisz nazwę obszaru roboczego.
   * Subskrypcja — Jeśli masz wiele subskrypcji, wybierz tę, którą chcesz skojarzyć z nowym obszarem roboczym.
   * Grupa zasobów — Utwórz grupę zasobów lub Użyj istniejącej.
   * Lokalizacja — ta lokalizacja służy do określania lokalizacji konta magazynu używanego w przypadku dzienników połączeń agentów.
   * Warstwa cenowa — wybierz warstwę cenową.
  
     >[!NOTE]
     >Obwód ExpressRoute może znajdować się w dowolnym miejscu na świecie. Nie musi znajdować się w tym samym regionie co obszar roboczy.
     >
  
     ![obszar roboczy](./media/how-to-npm/4.png)<br><br>
4. Kliknij przycisk **OK** , aby zapisać i wdrożyć szablon ustawień. Po sprawdzeniu poprawności szablonu kliknij pozycję **Utwórz** , aby wdrożyć obszar roboczy.
5. Po wdrożeniu obszaru roboczego przejdź do utworzonego zasobu **NetworkMonitoring (nazwa)** . Sprawdź poprawność ustawień, a następnie kliknij pozycję **rozwiązanie wymaga dodatkowej konfiguracji**.

   ![dodatkowa konfiguracja](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Krok 2. Instalowanie i konfigurowanie agentów

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: Pobierz plik instalacyjny agenta

1. Przejdź do karty **typowe ustawienia** na stronie **Konfiguracja Network Performance Monitor** dla danego zasobu. Kliknij agenta, który odpowiada procesorowi serwera, z sekcji **zainstaluj log Analytics agenci** i Pobierz plik instalacyjny.
2. Następnie skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
3. W sekcji **Konfigurowanie agentów log Analytics na potrzeby monitorowania przy użyciu protokołu TCP** Pobierz skrypt programu PowerShell. Skrypt programu PowerShell pomaga otworzyć odpowiedni port zapory dla transakcji TCP.

   ![Skrypt programu PowerShell](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: Zainstaluj agenta monitorowania na każdym serwerze monitorowania (w każdej sieci wirtualnej, która ma być monitorowana)

Zalecamy zainstalowanie co najmniej dwóch agentów po każdej stronie połączenia ExpressRoute (na przykład lokalnie, Azure sieci wirtualnych). Agent musi być zainstalowany w systemie Windows Server (2008 z dodatkiem SP1 lub nowszym). Monitorowanie obwodów usługi ExpressRoute przy użyciu systemu operacyjnego Windows Desktop i systemu operacyjnego Linux nie jest obsługiwane. Wykonaj następujące kroki, aby zainstalować agentów:
   
  >[!NOTE]
  >Agenci wypychani przez SCOM (w tym [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))) mogą nie być w stanie spójnie wykryć ich lokalizację, jeśli są hostowane na platformie Azure. Zalecamy, aby nie używać tych agentów w usłudze Azure sieci wirtualnych do monitorowania ExpressRoute.
  >

1. Uruchom **Instalatora** , aby zainstalować agenta na każdym serwerze, który ma być używany na potrzeby monitorowania ExpressRoute. Serwer używany do monitorowania może być maszyną wirtualną lub lokalną i musi mieć dostęp do Internetu. Należy zainstalować co najmniej jednego agenta lokalnego i jednego agenta w każdym segmencie sieci, który ma być monitorowany na platformie Azure.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **postanowienia licencyjne** zapoznaj się z licencją, a następnie kliknij przycisk **zgadzam** się.
4. Na stronie **folder docelowy** Zmień lub pozostaw domyślny folder instalacji, a następnie kliknij przycisk **dalej**.
5. Na stronie **Opcje instalacji agenta** można wybrać opcję połączenia agenta z dziennikami Azure Monitor lub Operations Manager. Można też pozostawić puste opcje, jeśli chcesz skonfigurować agenta później. Po dokonaniu wyboru kliknij przycisk **dalej**.

   * Jeśli wybrano opcję nawiązywania połączenia z **usługą Azure log Analytics**, wklej **Identyfikator obszaru roboczego** i **klucz obszaru roboczego** (klucz podstawowy) skopiowane do Notatnika w poprzedniej sekcji. Następnie kliknij przycisk **Dalej**.

     ![Identyfikator i klucz](./media/how-to-npm/8.png)
   * Jeśli wybrano opcję nawiązywania połączenia z **Operations Manager**, na stronie **Konfiguracja grupy zarządzania** wpisz **nazwę grupy zarządzania**, **serwer zarządzania** programu oraz **port serwera zarządzania**. Następnie kliknij przycisk **Dalej**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na stronie **konto działania agenta** wybierz konto **systemu lokalnego** lub  **konto domeny lub komputera lokalnego**. Następnie kliknij przycisk **Dalej**.

     ![Konto](./media/how-to-npm/10.png)
6. Na stronie **gotowy do instalacji** Przejrzyj wybrane opcje, a następnie kliknij przycisk **Instaluj**.
7. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.
8. Po zakończeniu Microsoft Monitoring Agent pojawia się w panelu sterowania. Możesz sprawdzić swoją konfigurację i sprawdzić, czy Agent jest połączony z dziennikami Azure Monitor. Po nawiązaniu połączenia agent wyświetla komunikat z informacją: **Microsoft Monitoring Agent pomyślnie nawiązał połączenie z usługą Microsoft Operations Management Suite**.

9. Powtórz tę procedurę dla każdej sieci wirtualnej, która ma być monitorowana.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: Skonfiguruj ustawienia serwera proxy (opcjonalnie)

Jeśli używasz serwera proxy sieci Web do uzyskiwania dostępu do Internetu, wykonaj następujące kroki, aby skonfigurować ustawienia serwera proxy dla Microsoft Monitoring Agent. Wykonaj te kroki dla każdego serwera. Jeśli masz wiele serwerów, które trzeba skonfigurować, łatwiejszym rozwiązaniem może być użycie skryptu automatyzującego ten proces. Jeśli tak, zobacz [Konfigurowanie ustawień serwera proxy dla Microsoft Monitoring Agent przy użyciu skryptu](../azure-monitor/agents/agent-windows.md).

Aby skonfigurować ustawienia serwera proxy dla Microsoft Monitoring Agent przy użyciu panelu sterowania:

1. Otwórz **Panel sterowania**.
2. Otwórz program **Microsoft Monitoring Agent**.
3. Kliknij kartę **Ustawienia serwera proxy**.
4. Wybierz opcję **Użyj serwera proxy** , a następnie wpisz adres URL i numer portu, jeśli jest wymagany. Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uzyskać dostęp do serwera proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: sprawdź łączność z agentem

Możesz łatwo sprawdzić, czy agenci komunikują się.

1. Na serwerze z agentem monitorowania Otwórz **Panel sterowania**.
2. Otwórz **Microsoft Monitoring Agent**.
3. Kliknij kartę **Azure log Analytics** .
4. W kolumnie **stan** należy sprawdzić, czy Agent połączył się pomyślnie z Azure monitor dzienników.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: Otwórz porty zapory na serwerach agentów monitorowania

Aby korzystać z protokołu TCP, należy otworzyć porty zapory, aby upewnić się, że agenci monitorowania mogą się komunikować.

Można uruchomić skrypt programu PowerShell, aby utworzyć klucze rejestru, które są wymagane przez Network Performance Monitor. Ten skrypt tworzy również reguły zapory systemu Windows zezwalające agentom monitorowania na tworzenie połączeń TCP ze sobą. Klucze rejestru utworzone przez skrypt określają, czy Dzienniki debugowania mają być rejestrowane, oraz ścieżkę do pliku dziennika. Definiuje również port TCP agenta używany do komunikacji. Wartości tych kluczy są automatycznie ustawiane przez skrypt. Nie należy ręcznie zmieniać tych kluczy.

Port 8084 jest domyślnie otwarty. Możesz użyć portu niestandardowego, podając parametr "numer_portu" dla skryptu. Jednak w takim przypadku należy określić ten sam port dla wszystkich serwerów, na których uruchomiono skrypt.

>[!NOTE]
>Skrypt programu PowerShell "skrypt enablerules" konfiguruje reguły zapory systemu Windows tylko na serwerze, na którym skrypt jest uruchamiany. Jeśli masz zaporę sieciową, upewnij się, że zezwala ona na ruch przeznaczony dla portu TCP używanego przez Network Performance Monitor.
>
>

Na serwerach agentów Otwórz okno programu PowerShell z uprawnieniami administracyjnymi. Uruchom skrypt programu PowerShell w programie [skrypt enablerules](https://aka.ms/npmpowershellscript) (pobrany wcześniej). Nie używaj żadnych parametrów.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Krok 3. Konfigurowanie reguł sieciowej grupy zabezpieczeń

Aby monitorować serwery agentów, które znajdują się na platformie Azure, należy skonfigurować reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zezwalania na ruch TCP na porcie używanym przez NPM dla transakcji syntetycznych. Domyślnym portem jest 8084. Dzięki temu Agent monitorowania zainstalowany na maszynie wirtualnej platformy Azure może komunikować się z lokalnym agentem monitorowania.

Aby uzyskać więcej informacji na temat sieciowej grupy zabezpieczeń, zobacz [Network Security Groups](../virtual-network/tutorial-filter-network-traffic.md).

>[!NOTE]
>Upewnij się, że zainstalowano agentów (zarówno agenta lokalnego serwera, jak i agenta serwera Azure), i uruchom skrypt programu PowerShell przed wykonaniem tego kroku.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Krok 4. odnajdywanie połączeń komunikacji równorzędnej

1. Przejdź do kafelka przegląd Network Performance Monitor, przechodząc do strony **wszystkie zasoby** , a następnie kliknij obszar roboczy allowlisted npm.

   ![obszar roboczy npm](./media/how-to-npm/npm.png)
2. Kliknij kafelek przegląd **Network Performance Monitor** , aby wyświetlić pulpit nawigacyjny. Pulpit nawigacyjny zawiera stronę ExpressRoute, która wskazuje, że ExpressRoute jest w stanie nieskonfigurowanym. Kliknij pozycję **Konfiguracja funkcji** , aby otworzyć stronę konfiguracja Network Performance Monitor.

   ![Konfiguracja funkcji](./media/how-to-npm/npm2.png)
3. Na stronie Konfiguracja przejdź do karty "Komunikacja równorzędna ExpressRoute" znajdującej się na panelu po lewej stronie. Następnie kliknij pozycję **Odkryj teraz**.

   ![Odnajdywanie](./media/how-to-npm/13.png)
4. Po zakończeniu odnajdywania zostanie wyświetlona lista zawierająca następujące elementy:
   * Wszystkie połączenia komunikacji równorzędnej firmy Microsoft w obwodach usługi ExpressRoute, które są skojarzone z tą subskrypcją.
   * Wszystkie połączenia prywatnej komunikacji równorzędnej, które łączą się z sieci wirtualnychą skojarzoną z tą subskrypcją.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Krok 5. Konfigurowanie monitorów

W tej sekcji można skonfigurować monitory. Postępuj zgodnie z instrukcjami dla typu komunikacji równorzędnej, którą chcesz monitorować: **prywatna Komunikacja równorzędna** lub **Komunikacja równorzędna firmy Microsoft**.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

Dla prywatnej komunikacji równorzędnej po zakończeniu odnajdywania są wyświetlane reguły dla unikatowej **nazwy obwodu** i **nazwy sieci wirtualnej**. Początkowo te reguły są wyłączone.

![rules](./media/how-to-npm/14.png)

1. Zaznacz pole wyboru **Monitoruj tę komunikację równorzędną** .
2. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
3. Wybierz warunki monitorowania. Można ustawić progi niestandardowe w celu generowania zdarzeń kondycji przez wpisanie wartości progowych. Zawsze, gdy wartość warunku spadnie powyżej progu wybranej pary sieć/podsieć, generowane jest zdarzenie kondycji.
4. Kliknij przycisk **Dodaj agentów** w AGENCIe Premium, aby dodać serwery lokalne, z których chcesz monitorować połączenie prywatnej komunikacji równorzędnej. Upewnij się, że wybrano tylko agentów, którzy mają łączność z punktem końcowym usługi firmy Microsoft określoną w sekcji Krok 2. Agenci lokalną muszą mieć możliwość uzyskania dostępu do punktu końcowego przy użyciu połączenia ExpressRoute.
5. Zapisz ustawienia.
6. Po włączeniu reguł i wybraniu wartości i agentów, które mają być monitorowane, istnieje poczekanie około 30-60 minut na wypełnienie wartości i udostępnienie kafelków **monitorowania ExpressRoute** .

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

W przypadku komunikacji równorzędnej firmy Microsoft kliknij połączenia komunikacji równorzędnej firmy Microsoft, które chcesz monitorować, a następnie skonfiguruj ustawienia.

1. Zaznacz pole wyboru **Monitoruj tę komunikację równorzędną** . 
2. Obowiązkowe Można zmienić docelowy punkt końcowy usługi firmy Microsoft. Domyślnie NPM wybiera punkt końcowy usługi firmy Microsoft jako element docelowy. NPM monitoruje łączność z serwerów lokalnych do tego docelowego punktu końcowego za pośrednictwem usługi ExpressRoute. 
    * Aby zmienić ten docelowy punkt końcowy, kliknij link **(Edytuj)** w obszarze **cel:**, a następnie wybierz inny docelowy punkt końcowy usługi firmy Microsoft z listy adresów URL.
      ![Edytuj miejsce docelowe](./media/how-to-npm/edit_target.png)<br>

    * Możesz użyć niestandardowego adresu URL lub adresu IP. Ta opcja jest szczególnie istotna, jeśli używasz komunikacji równorzędnej firmy Microsoft, aby nawiązać połączenie z usługami Azure PaaS, takimi jak Azure Storage, SQL Database i websites, które są oferowane na publicznych adresach IP. Aby to zrobić, kliknij link **(Użyj zamiast tego niestandardowego adresu URL lub adresu IP)** w dolnej części listy adresów URL, a następnie wprowadź publiczny punkt końcowy usługi Azure PaaS, która jest połączona za pomocą komunikacji równorzędnej Microsoft ExpressRoute.
    ![Niestandardowy adres URL](./media/how-to-npm/custom_url.png)<br>

    * Jeśli używasz tych ustawień opcjonalnych, upewnij się, że w tym miejscu wybrano tylko punkt końcowy usługi firmy Microsoft. Punkt końcowy musi być połączony z ExpressRoute i osiągalny przez agentów lokalnych.
3. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
4. Wybierz warunki monitorowania. Można ustawić progi niestandardowe w celu generowania zdarzeń kondycji przez wpisanie wartości progowych. Zawsze, gdy wartość warunku spadnie powyżej progu wybranej pary sieć/podsieć, generowane jest zdarzenie kondycji.
5. Kliknij przycisk **Dodaj agentów** w AGENCIe Premium, aby dodać serwery lokalne, z których chcesz monitorować połączenie komunikacji równorzędnej firmy Microsoft. Upewnij się, że wybrano tylko agentów, którzy mają łączność z punktami końcowymi usługi firmy Microsoft, które zostały określone w sekcji Krok 2. Agenci lokalną muszą mieć możliwość uzyskania dostępu do punktu końcowego przy użyciu połączenia ExpressRoute.
6. Zapisz ustawienia.
7. Po włączeniu reguł i wybraniu wartości i agentów, które mają być monitorowane, istnieje poczekanie około 30-60 minut na wypełnienie wartości i udostępnienie kafelków **monitorowania ExpressRoute** .

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Krok 6. Wyświetlanie kafelków monitorowania

Gdy zobaczysz kafelki monitorowania, obwody usługi ExpressRoute i zasoby połączeń są monitorowane przez program NPM. Możesz kliknąć kafelek komunikacji równorzędnej firmy Microsoft, aby przejść do szczegółów kondycji połączeń równorzędnych firmy Microsoft.

![monitorowanie kafelków](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Strona Network Performance Monitor

Strona NPM zawiera stronę ExpressRoute, która zawiera przegląd kondycji obwodów ExpressRoute i komunikacji równorzędnej.

![Zrzut ekranu przedstawia pulpit nawigacyjny z omówieniem kondycji obwodów usługi ExpressRoute i komunikacji równorzędnej.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista obwodów

Aby wyświetlić listę wszystkich monitorowanych obwodów usługi ExpressRoute, kliknij kafelek **obwodów usługi ExpressRoute** . Możesz wybrać obwód i wyświetlić jego stan kondycji, wykresy trendu dotyczące utraty pakietów, wykorzystania przepustowości i opóźnień. Wykresy są interaktywne. Można wybrać niestandardowe okno czasowe do kreślenia wykresów. Możesz przeciągnąć wskaźnik myszy nad obszar na wykresie, aby powiększyć i zobaczyć szczegółowe punkty danych.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend utraty, opóźnień i przepływności

Wykresy o przepustowości, opóźnieniu i utracie są interaktywne. Możesz powiększyć każdą sekcję tych wykresów, używając kontrolek myszy. Możesz również zobaczyć dane dotyczące przepustowości, opóźnienia i utraty dla innych interwałów, klikając pozycję **Data/godzina**, znajdującą się poniżej przycisku akcje w lewym górnym rogu.

![uległ](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Lista komunikacji równorzędnych

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, kliknij kafelek **prywatne elementy równorzędne** na pulpicie nawigacyjnym. W tym miejscu możesz wybrać połączenie sieci wirtualnej i wyświetlić jego stan kondycji, wykresy trendu dotyczące utraty pakietów, wykorzystania przepustowości i opóźnień.

![Lista obwodów](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Widok węzłów

Aby wyświetlić listę wszystkich linków między węzłami lokalnymi i maszynami wirtualnymi platformy Azure/punktami końcowymi usługi firmy Microsoft dla wybranego połączenia komunikacji równorzędnej ExpressRoute, kliknij przycisk **Wyświetl linki węzła**. Można wyświetlić stan kondycji każdego linku, a także Trend związany z nimi stratą i opóźnieniem.

![Widok węzłów](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologia obwodu

Aby wyświetlić topologię obwodu, kliknij kafelek **topologia** . Spowoduje to przejście do widoku topologia wybranego obwodu lub komunikacji równorzędnej. Diagram topologii zapewnia opóźnienie dla każdego segmentu w sieci. Każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Kliknięcie skoku powoduje wyświetlenie większej ilości szczegółów przeskoku.

Można zwiększyć poziom widoczności w celu uwzględnienia przeskoków lokalnych przez przeniesienie suwaka poniżej **filtrów**. Przesuwanie paska suwaka w lewo lub w prawo powoduje zwiększenie/zmniejszenie liczby przeskoków w grafie topologii. Opóźnienie w każdym segmencie jest widoczne, co umożliwia szybszą izolację segmentów dużych opóźnień w sieci.

![filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Szczegółowy widok topologii obwodu

Ten widok przedstawia połączenia sieci wirtualnej.
![Szczegółowa topologia](./media/how-to-npm/17.png)