---
title: Network Performance Monitor rozwiązanie na platformie Azure | Microsoft Docs
description: Network Performance Monitor na platformie Azure ułatwia monitorowanie wydajności sieci w czasie niemal rzeczywistym w celu wykrywania i lokalizowania wąskich gardeł wydajności sieci.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 1bb2b7ca22896ed279c8aac215109b7f7bc7854c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394275"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor rozwiązanie na platformie Azure

![Symbol Network Performance Monitor](./media/network-performance-monitor/npm-symbol.png)


Network Performance Monitor to rozwiązanie hybrydowego monitorowania sieci opartego na chmurze, które ułatwia monitorowanie wydajności sieci między różnymi punktami infrastruktury sieciowej. Ułatwia ono również monitorowanie łączności sieciowej z punktami końcowymi usług i aplikacji oraz monitorowanie wydajności usługi Azure ExpressRoute. 

Network Performance Monitor wykrywa problemy z siecią, takie jak ruch blackholing, błędy routingu i problemy, które nie są w stanie wykryć konwencjonalnych metod monitorowania sieci. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Network Performance Monitor oferuje trzy szerokie możliwości: 

* [Monitor wydajności](network-performance-monitor-performance-monitor.md): można monitorować łączność sieciową między wdrożeniami w chmurze i lokalizacjami lokalnymi, wieloma centrami danych i biurami oddziałów oraz aplikacjami wielowarstwowymi i mikrousługami o kluczowym znaczeniu. Korzystając z monitora wydajności, można wykrywać problemy z siecią przed zaskargą użytkowników.

* [Monitor łączności usługi](network-performance-monitor-service-connectivity.md): możesz monitorować łączność od użytkowników do usług, których potrzebujesz, określić, jaka infrastruktura znajduje się w ścieżce, i określić, gdzie występują wąskie gardła sieci. Możesz dowiedzieć się więcej o awarii przed użytkownikami i zobaczyć dokładną lokalizację problemów w ścieżce sieci. 

    Ta funkcja ułatwia wykonywanie testów opartych na protokole HTTP, HTTPS, TCP i ICMP, aby monitorować niemal w czasie rzeczywistym lub historycznie zapewnić dostępność i czas odpowiedzi usługi. Możesz również monitorować udział sieci w utracie i opóźnieniu pakietu. Za pomocą mapy topologii sieci można izolować spowolnienia sieci. W przypadku każdego przeskoku można zidentyfikować problemy występujące wzdłuż ścieżki sieciowej z węzła do usługi. Za pomocą wbudowanych testów można monitorować łączność sieciową z Microsoft 365 i Dynamics CRM bez żadnej wstępnej konfiguracji. Dzięki tej możliwości można monitorować łączność sieciową z dowolnym punktem końcowym z obsługą protokołu TCP, takim jak websites, SaaS Applications, PaaS Applications i SQL Database.

* [Monitor ExpressRoute](network-performance-monitor-expressroute.md): monitorowanie kompleksowej łączności i wydajności między oddziałami i platformą Azure przy użyciu usługi Azure ExpressRoute.  

Więcej informacji na temat różnych możliwości obsługiwanych przez [Network Performance Monitor](../../networking/network-monitoring-overview.md) jest dostępnych w trybie online.
 
## <a name="supported-regions"></a>Obsługiwane regiony
NPM może monitorować łączność między sieciami i aplikacjami w dowolnej części świata, z obszaru roboczego, który jest hostowany w jednym z następujących regionów:
* Europa Północna
* Europa Zachodnia
* Szwajcaria Północna
* Francja Środkowa
* Północna Republika Południowej Afryki
* Kanada Środkowa
* Zachodnie stany USA
* Zachodnio-środkowe stany USA
* Północno-środkowe stany USA
* South Central US
* Central US
* East US
* Wschodnie stany USA 2
* Zachodnie stany USA 2
* Japonia Wschodnia
* Azja Południowo-Wschodnia
* Australia Południowo-Wschodnia
* Australia Środkowa
* Australia Wschodnia
* Południowe Zjednoczone Królestwo
* Azja Wschodnia
* Korea Środkowa
* Indie Środkowe
* Wirginia instytucji rządowych USA
* Arizona instytucji rządowych USA
* Chiny Wschodnie 2


Lista obsługiwanych regionów dla monitora ExpressRoute jest dostępna w [dokumentacji](../../expressroute/how-to-npm.md?utm_swu=8117).


## <a name="set-up-and-configure"></a>Skonfiguruj i skonfiguruj

> [!NOTE]
> Może również zostać wyświetlony Agent Log Analytics nazywany Microsoft Monitoring Agent (MMA) lub agentem usługi OMS Linux.

### <a name="install-and-configure-agents"></a>Instalacja i konfiguracja agentów 

Użyj podstawowych procesów, aby zainstalować agentów na [komputerach z systemem Windows w celu Azure monitor](../platform/agent-windows.md), [podłączyć komputery z systemem Linux do Azure monitor (wersja zapoznawcza)](../../virtual-machines/extensions/oms-linux.md) i [nawiązać Operations Manager z Azure monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Gdzie zainstalować agentów 

* **Monitor wydajności** : Zainstaluj log Analytics agentów na co najmniej jednym węźle połączonym z każdą podsiecią, z której chcesz monitorować łączność sieciową z innymi podsieciami.

    Aby monitorować łącze sieciowe, zainstaluj agentów na obu punktach końcowych tego łącza. Jeśli nie masz pewności co do topologii sieci, zainstaluj agentów na serwerach z krytycznymi obciążeniami, między którymi chcesz monitorować wydajność sieci. Na przykład jeśli chcesz monitorować połączenie sieciowe między serwerem sieci Web a serwerem z uruchomionym programem SQL, Zainstaluj agenta na obu serwerach. Agenci monitorują łączność sieciową (linki) między hostami, a nie samymi hostami. 

* **Monitor łączności usługi** : zainstaluj agenta log Analytics w każdym węźle, z którego chcesz monitorować łączność sieciową do punktu końcowego usługi. Przykładem jest to, że chcesz monitorować łączność sieciową w celu Microsoft 365 z witryn biur oznaczonych jako O1, O2 i O3. Zainstaluj agenta Log Analytics na co najmniej jednym węźle, każdy w O1, O2 i O3. 

* **Monitor ExpressRoute** : Zainstaluj co najmniej jednego agenta log Analytics w sieci wirtualnej platformy Azure. Zainstaluj także co najmniej jednego agenta w podsieci lokalnej, który jest połączony za pomocą prywatnej komunikacji równorzędnej ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Skonfiguruj Log Analytics agentów do monitorowania 

Network Performance Monitor używa transakcji syntetycznych do monitorowania wydajności sieci między agentami źródłowymi i docelowymi. Można wybrać między portami TCP i ICMP jako protokół do monitorowania w ramach funkcji monitorowania wydajności i monitora łączności usług. Tylko protokół TCP jest dostępny jako protokół monitorowania dla monitora ExpressRoute. Upewnij się, że zapora zezwala na komunikację między agentami Log Analytics używanymi do monitorowania w wybranym protokole. 

* **Protokół TCP** : w przypadku wybrania na potrzeby monitorowania protokołu TCP jako protokołu należy otworzyć port zapory na agentach używanych do Network Performance Monitor i monitor ExpressRoute, aby upewnić się, że agenci mogą się ze sobą łączyć. Aby otworzyć port dla maszyn z systemem Windows, uruchom skrypt programu PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript) bez żadnych parametrów w oknie programu PowerShell z uprawnieniami administracyjnymi.
W przypadku maszyn z systemem Linux należy ręcznie zmienić portNumbers. 
* Przejdź do ścieżki:/var/opt/Microsoft/omsagent/npm_state. 
* Otwórz plik: npmdregistry
* Zmień wartość numeru portu ```“PortNumber:<port of your choice>”```

 Należy pamiętać, że używane numery portów powinny być takie same dla wszystkich agentów używanych w obszarze roboczym. 

Skrypt tworzy klucze rejestru wymagane przez rozwiązanie. Tworzy także reguły zapory systemu Windows, aby umożliwić agentom tworzenie połączeń TCP ze sobą. Klucze rejestru utworzone przez skrypt określają, czy Dzienniki debugowania i ścieżka pliku dzienników mają być rejestrowane. Skrypt definiuje również port TCP agenta używany do komunikacji. Wartości tych kluczy są automatycznie ustawiane przez skrypt. Nie zmieniaj ręcznie tych kluczy. Port otwarty domyślnie to 8084. Możesz użyć portu niestandardowego, dostarczając parametr numer_portu do skryptu. Użyj tego samego portu na wszystkich komputerach, na których jest uruchomiony skrypt. 

   >[!NOTE]
   > Skrypt konfiguruje tylko zaporę systemu Windows lokalnie. Jeśli masz zaporę sieciową, upewnij się, że zezwala ona na ruch przeznaczony dla portu TCP używanego przez Network Performance Monitor.

   >[!NOTE]
   > Nie musisz uruchamiać skryptu [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell dla monitora łączności usług.

    

* **Protokół ICMP** : w przypadku wybrania na potrzeby monitorowania protokołu ICMP należy włączyć następujące reguły zapory, aby niezawodne korzystanie z protokołu ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Skonfiguruj rozwiązanie 

1. Dodaj rozwiązanie Network Performance Monitor do obszaru roboczego z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Można również użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](./solutions.md). 
2. Otwórz obszar roboczy Log Analytics i wybierz kafelek **Przegląd** . 
3. Wybierz kafelek **Network Performance Monitor** z *rozwiązaniem wiadomości wymaga dodatkowej konfiguracji*.

   ![Kafelek Network Performance Monitor](media/network-performance-monitor/npm-config.png)

4. Na stronie **Instalator** zobaczysz opcję zainstalowania agentów log Analytics i skonfigurowania agentów do monitorowania w widoku **wspólnych ustawień** . Zgodnie z opisem wcześniej, jeśli zainstalowano i skonfigurowano Log Analytics agentów, wybierz widok **instalacji** , aby skonfigurować możliwości, których chcesz użyć. 

   **Monitor wydajności** : Wybierz protokół, który ma być używany dla transakcji syntetycznych w **domyślnej** regule monitora wydajności, a następnie wybierz pozycję **Zapisz & Kontynuuj**. Ten wybór protokołu jest przechowywany tylko dla reguły domyślnej wygenerowanej przez system. Należy wybrać protokół za każdym razem, gdy tworzysz regułę monitora wydajności jawnie. Zawsze możesz przejść do **domyślnych** ustawień reguły na karcie **Monitor wydajności** (pojawia się po zakończeniu konfiguracji dnia 0) i zmienić protokół później. Jeśli nie potrzebujesz możliwości monitora wydajności, możesz wyłączyć regułę domyślną z ustawień reguły **domyślnej** na karcie **Monitor wydajności** .

   ![Widok monitora wydajności](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor łączności usługi** : funkcja udostępnia wbudowane wstępnie skonfigurowane testy do monitorowania łączności sieciowej z Microsoft 365 i Dynamics 365 od agentów. Wybierz usługi Microsoft 365 i Dynamics 365, które chcesz monitorować, zaznaczając obok siebie pola wyboru. Aby wybrać agentów, z których chcesz monitorować, wybierz pozycję **Dodaj agentów**. Jeśli nie chcesz używać tej funkcji lub chcesz ją skonfigurować później, nie wybieraj niczego i wybierz pozycję **zapisz & Kontynuuj**.

   ![Widok monitora łączności usług](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor ExpressRoute** : wybierz pozycję **Odkryj teraz** , aby odnaleźć wszystkie prywatne połączenia równorzędne ExpressRoute połączone z sieciami wirtualnymi w ramach subskrypcji platformy Azure połączonej z tym obszarem roboczym log Analytics. 

   ![Widok monitora ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Po zakończeniu odnajdywania odnalezione obwody i sieci równorzędne są wyświetlane w tabeli. 

   ![Strona konfiguracji Network Performance Monitor](media/network-performance-monitor/npm-private-peerings.png)
    
Monitorowanie tych obwodów i komunikacji równorzędnej jest początkowo w stanie wyłączenia. Wybierz każdy zasób, który chcesz monitorować, i skonfiguruj jego monitorowanie w widoku szczegółów po prawej stronie. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. Aby dowiedzieć się więcej, zobacz artykuł "Konfigurowanie monitorowania ExpressRoute". 

Po zakończeniu instalacji trwa od 30 minut do godziny w celu wypełnienia danych. Chociaż rozwiązanie agreguje dane z sieci, to *rozwiązanie komunikatu wymaga dodatkowej konfiguracji* na kafelku **Przegląd** Network Performance Monitor. Po zebraniu i indeksowaniu danych kafelek **Przegląd** zmienia się i informuje o kondycji sieci w podsumowaniu. Następnie można edytować monitorowanie węzłów, na których zainstalowano Log Analytics agenci, a także podsieci wykryte ze środowiska.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edytowanie ustawień monitorowania dla podsieci i węzłów 

Wszystkie podsieci z zainstalowanym co najmniej jednym agentem są wyświetlane na karcie **podsieci** na stronie Konfiguracja. 


Aby włączyć lub wyłączyć monitorowanie określonych podsieci:

1. Zaznacz lub wyczyść pole wyboru obok **identyfikatora podsieci**. Następnie upewnij się, że **do monitorowania** jest wybrane lub wyczyszczone, zgodnie z potrzebami. Możesz zaznaczyć lub wyczyścić wiele podsieci. Po wyłączeniu podsieci nie są monitorowane, a agenci są zaktualizowani, aby zrezygnować z wysyłania poleceń ping do innych agentów. 
2. Wybierz węzły, które mają być monitorowane w określonej podsieci. Z listy wybierz podsieć i Przenieś wymagane węzły między listami zawierającymi niemonitorowane i monitorowane węzły. Do podsieci można dodać niestandardowy opis.
3. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. 

#### <a name="choose-nodes-to-monitor"></a>Wybierz węzły do monitorowania

Wszystkie węzły, na których zainstalowano agenta, są wyświetlane na karcie **węzły** . 

1. Zaznacz lub wyczyść węzły, które chcesz monitorować lub zatrzymać monitorowanie. 
2. Wybierz pozycję **Użyj do monitorowania** lub wyczyść ją odpowiednio do potrzeb. 
3. Wybierz pozycję **Zapisz**. 


Skonfiguruj odpowiednie możliwości:

- [Monitorowanie wydajności](network-performance-monitor-performance-monitor.md#configuration)
- [Monitorowanie łączności usług](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Szczegóły zbierania danych
Aby zbierać informacje o utracie i opóźnieniu, Network Performance Monitor używa pakietów uzgadniania TCP SYN-SYNACK-ACK, gdy wybierzesz opcję TCP jako protokół. Network Performance Monitor korzysta z protokołu ICMP ECHO odpowiedzi echa ICMP w przypadku wybrania jako protokołu protokołu ICMP. Marszruta śledzenia jest również używana do uzyskiwania informacji o topologii.

W poniższej tabeli przedstawiono metody zbierania danych oraz inne szczegóły dotyczące sposobu zbierania danych dla Network Performance Monitor.

| Platforma | Agent bezpośredni | Agent System Center Operations Manager | Azure Storage | Operations Manager jest wymagane? | Dane agenta Operations Manager wysyłane przez grupę zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Uzgadnianie TCP/ICMP ECHO komunikatów co 5 sekund, dane wysyłane co 3 minuty |
 

 
Rozwiązanie używa transakcji syntetycznych do oceny kondycji sieci. Log Analytics agenci zainstalowani w różnych punktach protokołu TCP wymiany sieciowej lub echa ICMP ze sobą. Czy agenci używają pakietów TCP, czy echo ICMP zależy od protokołu wybranego do monitorowania. W procesie agenci poznaniy czasu rundy i utraty pakietów (jeśli istnieją). Okresowo każdy agent wykonuje także trasę śledzenia do innych agentów, aby znaleźć wszystkie różne trasy w sieci, które muszą zostać przetestowane. Korzystając z tych danych, agenci mogą wywnioskować opóźnienia sieci i dane dotyczące utraty pakietów. Testy są powtarzane co pięć sekund. Dane są agregowane przez agentów przed przekazaniem ich do obszaru roboczego Log Analytics w Azure Monitor.



>[!NOTE]
> Chociaż agenci komunikują się ze sobą często, nie generują znaczącego ruchu sieciowego podczas przeprowadzania testów. Agenci mogą korzystać tylko z pakietów TCP SYN-SYNACK-ACK, aby określić utratę i opóźnienie. Żadne pakiety danych nie są wymieniane. W trakcie tego procesu agenci komunikują się ze sobą tylko w razie konieczności. Topologia komunikacji agentów jest zoptymalizowana pod kątem zmniejszenia ruchu sieciowego.

## <a name="use-the-solution"></a>Korzystanie z rozwiązania 

### <a name="network-performance-monitor-overview-tile"></a>Kafelek przeglądu Network Performance Monitor 

Po włączeniu rozwiązania Network Performance Monitor kafelek rozwiązania na stronie **Przegląd** zawiera krótkie omówienie kondycji sieci. 

 ![Kafelek przeglądu Network Performance Monitor](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Pulpit nawigacyjny Network Performance Monitor 

* **Najważniejsze zdarzenia dotyczące kondycji sieci** : Ta strona zawiera listę najnowszych zdarzeń i alertów dotyczących kondycji w systemie oraz czas aktywności zdarzeń. Zdarzenie kondycji lub alert jest generowany za każdym razem, gdy wartość wybranej metryki (utrata, opóźnienie, czas odpowiedzi lub wykorzystanie przepustowości) dla reguły monitorowania przekroczy wartość progową. 

* **Monitor ExpressRoute** : Ta strona zawiera podsumowania kondycji różnych połączeń komunikacji równorzędnej ExpressRoute monitorowanych przez rozwiązanie. Kafelek **topologia** przedstawia liczbę ścieżek sieciowych za pomocą obwodów usługi ExpressRoute monitorowanych w sieci. Wybierz ten kafelek, aby przejść do widoku **topologii** .

* **Monitor łączności usługi** : Ta strona zawiera podsumowania kondycji dla różnych utworzonych testów. Kafelek **topologia** pokazuje liczbę monitorowanych punktów końcowych. Wybierz ten kafelek, aby przejść do widoku **topologii** .

* **Monitor wydajności** : Ta strona zawiera podsumowania kondycji dla linków **sieciowych** i linków **podsieci** monitorowanych przez rozwiązanie. Kafelek **topologia** przedstawia liczbę ścieżek sieciowych monitorowanych w sieci. Wybierz ten kafelek, aby przejść do widoku **topologii** . 

* **Typowe zapytania** : Ta strona zawiera zestaw zapytań wyszukiwania, które bezpośrednio pobierają dane monitorowania sieci RAW. Tych zapytań można użyć jako punktu wyjścia do tworzenia własnych zapytań na potrzeby niestandardowego raportowania. 

   ![Pulpit nawigacyjny Network Performance Monitor](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Przechodzenie do szczegółów w celu uzyskania głębokości 

Możesz wybrać różne linki na pulpicie nawigacyjnym rozwiązania, aby przelepszyć szczegółowe informacje w dowolnym interesującym obszarze. Na przykład jeśli na pulpicie nawigacyjnym pojawi się alert lub link sieciowy o złej kondycji, wybierz go, aby dokładniej zbadać. Na stronie znajduje się lista wszystkich łączy podsieci dla danego łącza sieciowego. Możesz zobaczyć utratę, opóźnienie i stan kondycji poszczególnych linków podsieci. Możesz szybko sprawdzić, które łącze podsieci powoduje problemy. Wybierz pozycję **Wyświetl linki węzłów** , aby wyświetlić wszystkie linki węzłów dla łącza podsieci w złej kondycji. Następnie można zobaczyć poszczególne linki węzeł-węzeł i znaleźć linki węzłów w złej kondycji. 

Wybierz pozycję **Wyświetl topologię** , aby wyświetlić topologię przeskoków między węzłami źródłowymi a docelowymi. Trasy w złej kondycji są wyświetlane na czerwono. Możesz wyświetlić czas opóźnienia przytworzony przez każdy z przeskoków, aby szybko zidentyfikować problem do konkretnej części sieci.

 

### <a name="network-state-recorder-control"></a>Kontrolka rejestratora stanu sieci

Każdy widok przedstawia migawkę kondycji sieci w konkretnym momencie. Domyślnie jest wyświetlany ostatni stan. Pasek w górnej części strony pokazuje punkt w czasie, dla którego jest wyświetlany stan. Aby wyświetlić migawkę kondycji sieci w poprzednim momencie, wybierz pozycję **Akcje**. Podczas wyświetlania najnowszego stanu można także włączyć lub wyłączyć funkcję autoodświeżania dla każdej strony. 

 ![Rejestrator stanu sieci](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Wykresy trendów 

Na każdym poziomie przechodzenia do szczegółów można zobaczyć trend odpowiedniej metryki. Może to być utrata, opóźnienie, czas odpowiedzi lub wykorzystanie przepustowości. Aby zmienić przedział czasu dla trendu, użyj kontrolki Time w górnej części wykresu. 

Wykresy trendów przedstawiają historyczną perspektywę wydajności metryki wydajności. Niektóre problemy sieciowe mają charakter przejściowy i są trudne do przechwycenia, sprawdzając tylko bieżący stan sieci. Problemy mogą szybko pojawić się i zniknąć przed powiadomieniami, tylko w późniejszym czasie. Takie problemy przejściowe mogą być trudne dla administratorów aplikacji. Te problemy często są wyświetlane jako niewyjaśnione wzrosty czasu odpowiedzi aplikacji, nawet jeśli wszystkie składniki aplikacji wyglądają bezproblemowo. 

Możesz łatwo wykrywać te rodzaje problemów, patrząc na wykres trendu. Problem pojawia się jako nagły wzrost opóźnienia sieci lub utraty pakietów. Aby zbadać ten problem, użyj kontrolki rejestratora stanu sieci, aby wyświetlić migawkę i topologię sieci dla tego punktu w czasie, kiedy wystąpił problem.

 
![Wykresy trendów](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa topologii 

Network Performance Monitor pokazuje topologię przeskoków przez przeskok tras między źródłowym i docelowym punktem końcowym na interaktywnej mapie topologii. Aby wyświetlić mapę topologii, wybierz kafelek **topologia** na pulpicie nawigacyjnym rozwiązania. Możesz również wybrać łącze **Wyświetl topologię** na stronach do przechodzenia do szczegółów. 

Mapa topologii zawiera informacje o liczbie tras między lokalizacją źródłową i docelową a ścieżkami pobieranymi przez pakiety danych. Widoczne jest również opóźnienie spowodowane przez każdy przeskok sieci. Wszystkie ścieżki, dla których łączne opóźnienie ścieżki przekracza wartość progową (ustawioną w odpowiedniej regule monitorowania), są wyświetlane na czerwono. 

Po wybraniu węzła lub umieszczeniu na nim wskaźnika na mapie topologii są wyświetlane właściwości węzła, takie jak nazwa FQDN i adres IP. Wybierz przeskok, aby wyświetlić jego adres IP. Przeskok sieci problematycznych można zidentyfikować, obserwowanie czas opóźnienia, jaki przyczynia. Aby filtrować określone trasy, użyj filtrów w okienku akcji zwijanej. Aby uprościć topologie sieci, Ukryj przeskoki pośrednie przy użyciu suwaka w okienku Akcja. Możesz powiększyć lub pomniejszyć mapę topologii, używając kółka myszy. 

Topologia wyświetlana na mapie jest topologią warstwy 3 i nie zawiera urządzeń i połączeń warstwy 2. 

 
![Mapa topologii](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Rejestruj zapytania w Azure Monitor

Wszystkie dane, które są udostępniane graficznie za pomocą pulpitu nawigacyjnego Network Performance Monitor i stron przechodzenia do szczegółów, są również dostępne natywnie w [kwerendach dzienników](../log-query/log-query-overview.md). Można przeprowadzić interaktywną analizę danych w repozytorium i skorelować dane z różnych źródeł. Można również tworzyć niestandardowe alerty i widoki oraz eksportować dane do programu Excel, Power BI lub link możliwego do udostępniania. Obszar **typowe zapytania** na pulpicie nawigacyjnym zawiera kilka przydatnych zapytań, których można użyć jako punktu wyjścia do tworzenia własnych zapytań i raportów. 

## <a name="alerts"></a>Alerty

Network Performance Monitor używa funkcji alertów [Azure monitor](../platform/alerts-overview.md).

Oznacza to, że wszystkie powiadomienia są zarządzane przy użyciu [grup akcji](../platform/action-groups.md).  

Jeśli jesteś użytkownikiem NPM, tworzysz alert za pośrednictwem Log Analytics: 
1. Zobaczysz link, który przekieruje Cię do Azure Portal. Kliknij go, aby uzyskać dostęp do portalu.
2. Kliknij kafelek Network Performance Monitor rozwiązanie. 
3. Przejdź do konfiguracji.  
4. Wybierz test, dla którego chcesz utworzyć alert, i postępuj zgodnie z poniższymi wymienionymi krokami.

Jeśli jesteś użytkownikiem NPM, tworzysz alert za pośrednictwem Azure Portal:  
1. Możesz wybrać opcję bezpośredniego wprowadzania wiadomości e-mail lub można utworzyć alerty za pomocą grup akcji.
2. W przypadku wybrania bezpośredniej wiadomości e-mail zostanie utworzona grupa akcji z grupą **akcji poczty E-mail npm** , a identyfikator e-mail zostanie dodany do tej grupy akcji.
3. Jeśli zdecydujesz się użyć grup akcji, musisz wybrać wcześniej utworzoną grupę akcji. W [tym miejscu](../platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) możesz dowiedzieć się, jak utworzyć grupę akcji. 
4. Po pomyślnym utworzeniu alertu możesz użyć linku Zarządzanie alertami w celu zarządzania alertami. 

Za każdym razem, gdy tworzysz alert, NPM tworzy regułę alertu dziennika na podstawie zapytania w Azure Monitor. To zapytanie jest domyślnie wyzwalane co 5 minut. Usługa Azure monitor nie jest naliczana w przypadku pierwszych utworzonych reguł alertów dziennika 250, a wszystkie reguły alertów przekraczające limit alertów dziennika 250 są rozliczane zgodnie z [cennikiem na stronie Azure monitor cenowej](https://azure.microsoft.com/pricing/details/monitor/).
Opłaty za powiadomienia są naliczone osobno zgodnie z [cennikiem za powiadomienia na stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Cennik

Informacje o cenach są dostępne w [trybie online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Przekazywanie opinii 

* **UserVoice:** Możesz publikować swoje pomysły dotyczące Network Performance Monitor funkcji, nad którymi chcesz, nad którymi pracujemy. Odwiedź [stronę usługi UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Dołącz do naszego kohorta:** Zawsze interesuje Cię, aby nowi klienci mogli dołączyć do naszego kohortau. W ramach tego artykułu otrzymujesz wczesny dostęp do nowych funkcji i możliwość poprawy Network Performance Monitor. Jeśli interesuje Cię dołączenie, Wypełnij tę [szybką ankietę](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Następne kroki 
Dowiedz się więcej na temat [monitora wydajności](network-performance-monitor-performance-monitor.md), [monitora łączności usług](network-performance-monitor-performance-monitor.md)i [monitora ExpressRoute](network-performance-monitor-expressroute.md). 

