---
title: Utwórz Podgląd monitora połączeń — Azure Portal
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak utworzyć monitor połączeń (wersja zapoznawcza) przy użyciu Azure Portal.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362918"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Tworzenie monitora połączeń (wersja zapoznawcza) przy użyciu Azure Portal

Dowiedz się, jak utworzyć monitor połączeń (wersja zapoznawcza) w celu monitorowania komunikacji między zasobami przy użyciu Azure Portal. Obsługuje wdrożenia w chmurze hybrydowej i platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem 

W monitorach połączeń utworzonych w monitorze połączeń (wersja zapoznawcza) można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub dowolnym innym adresem URL lub adresie IP.

Monitor połączeń (wersja zapoznawcza) zawiera następujące jednostki:


* **Zasób monitora połączeń** — zasób platformy Azure specyficzny dla regionu. Wszystkie poniższe jednostki są właściwościami zasobu monitora połączeń.
* **Endpoint** — Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykłady punktów końcowych: 
    * Maszyny wirtualne platformy Azure
    * Azure sieci wirtualnych
    * Podsieci platformy Azure
    * Agenci lokalni
    * Podsieci lokalne
    * Lokalna sieć niestandardowa składająca się z wielu podsieci
    * Adresy URL i IP 
* **Konfiguracja testu** — Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa** — grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test** — połączenie źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

    ![Diagram przedstawiający monitor połączeń, który definiuje relację między grupami testów i testami](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Kroki do utworzenia

Aby utworzyć monitor połączeń (wersja zapoznawcza) przy użyciu Azure Portal, wykonaj następujące czynności:

1. Na stronie głównej Azure Portal przejdź do **Network Watcher**.
1. Po lewej stronie w sekcji **monitorowanie** wybierz pozycję **monitor połączeń (wersja zapoznawcza)**.
1. Zobaczysz wszystkie monitory połączeń, które zostały utworzone w monitorze połączeń (wersja zapoznawcza). Aby wyświetlić monitory połączeń, które zostały utworzone w klasycznym środowisku monitora połączeń, przejdź do karty **monitor połączeń** .

    ![Zrzut ekranu przedstawiający monitory połączeń, które zostały utworzone w monitorze połączeń (wersja zapoznawcza)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. Na pulpicie nawigacyjnym **monitor połączeń (wersja zapoznawcza)** w lewym górnym rogu wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający kartę podstawowe w monitorze połączeń](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na karcie **podstawowe** wprowadź informacje dotyczące monitora połączeń:
   * **Nazwa monitora połączeń** — Dodaj nazwę monitora połączeń. Użyj standardowych reguł nazewnictwa dla zasobów platformy Azure.
   * **Subskrypcja** — wybierz subskrypcję monitora połączeń.
   * **Region** — wybierz region monitora połączeń. Można wybrać tylko źródłowe maszyny wirtualne, które są tworzone w tym regionie.
   * **Konfiguracja obszaru roboczego** — obszar roboczy zawiera dane monitorowania. Możesz użyć niestandardowego obszaru roboczego lub domyślnego obszaru roboczego. 
       * Aby użyć domyślnego obszaru roboczego, zaznacz to pole wyboru. 
       * Aby wybrać niestandardowy obszar roboczy, usuń zaznaczenie pola wyboru. Następnie wybierz subskrypcję i region dla niestandardowego obszaru roboczego. 
1. W dolnej części karty wybierz pozycję **Dalej: grupy testowe**.

   ![Zrzut ekranu przedstawiający kartę Tworzenie grupy testów w monitorze połączeń](./media/connection-monitor-2-preview/create-tg.png)

1. Dodawanie źródeł, miejsc docelowych i konfiguracji testów w grupach testowych. Aby skonfigurować grupy testów, zobacz [Tworzenie grup testowych w monitorze połączeń](#create-test-groups-in-a-connection-monitor). 
1. W dolnej części karty wybierz kolejno pozycje **Dalej: tworzenie alertów**.

   ![Zrzut ekranu przedstawiający okienko, w którym tworzysz alerty](./media/connection-monitor-2-preview/create-alert.png)

1. Aby utworzyć alerty, zobacz [tworzenie alertów w monitorze połączeń](#create-alerts-in-connection-monitor)
1. W dolnej części karty wybierz kolejno pozycje **Dalej: recenzja + Utwórz**.

  ![Zrzut ekranu przedstawiający monitor połączeń z kartą przegląd + tworzenie](./media/connection-monitor-2-preview/review-create-cm.png)

1. Przed utworzeniem monitora połączeń na karcie **Przegląd + tworzenie** Przejrzyj podstawowe informacje i grupy testowe. Jeśli musisz edytować monitor połączeń:
   * Aby edytować podstawowe szczegóły, wybierz ikonę ołówka.
   * Aby edytować grupę testową, wybierz ją.

   > [!NOTE] 
   > Karta **Przegląd + tworzenie** pokazuje koszt miesięcznie na etapie wersji zapoznawczej monitora połączenia. Obecnie w kolumnie **bieżący koszt** nie jest naliczana opłata. Gdy monitor połączeń będzie ogólnie dostępny, w tej kolumnie będzie wyświetlana opłata miesięczna. 
   > 
   > Nawet w przypadku wersji zapoznawczej monitora połączeń obowiązują Log Analytics opłaty za pozyskiwanie.

1. Gdy wszystko będzie gotowe do utworzenia monitora połączeń, w dolnej części karty **Recenzja + tworzenie** wybierz pozycję **Utwórz**.

Monitor połączeń (wersja zapoznawcza) tworzy zasób monitora połączeń w tle.

## <a name="create-test-groups-in-a-connection-monitor"></a>Tworzenie grup testowych w monitorze połączeń

Każda grupa testowa w monitorze połączeń zawiera źródła i miejsca docelowe, które są przetestowane w oparciu o parametry sieci. Są one testowane pod kątem procentu kontroli zakończonych niepowodzeniem i RTT przez konfiguracje testów.

W Azure Portal, aby utworzyć grupę testową w monitorze połączeń, należy określić wartości dla następujących pól:

* **Wyłącz grupę testową** — można zaznaczyć to pole, aby wyłączyć monitorowanie dla wszystkich źródeł i miejsc docelowych, które określa Grupa testowa. Ten wybór jest domyślnie wyczyszczony.
* **Nazwa** — Nadaj nazwę grupie testowej.
* **Źródła** — Jeśli agenci są na nich zainstalowani, można określić zarówno maszyny wirtualne platformy Azure, jak i lokalne. Aby zainstalować agenta dla źródła, zobacz [Instalowanie agentów monitorowania](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Aby wybrać agentów platformy Azure, wybierz kartę **punkty końcowe platformy Azure** . W tym miejscu są wyświetlane tylko maszyny wirtualne, które są powiązane z regionem określonym podczas tworzenia monitora połączeń. Domyślnie maszyny wirtualne są pogrupowane w subskrypcję, do której należą. Te grupy są zwinięte. 
   
       Możesz przejść do szczegółów z poziomu subskrypcji na inne poziomy w hierarchii:

      **Subskrypcja**  >  **Grupy zasobów**  >  **Sieci wirtualnych**  >  **Podsieci**  >  **Maszyny wirtualne z agentami**

      Możesz również zmienić wartość pola **Grupuj według** , aby uruchomić drzewo z dowolnego innego poziomu. Na przykład Jeśli grupujesz według sieci wirtualnej, zobaczysz maszyny wirtualne, które mają agentów w hierarchii **sieci wirtualnych**  >  **podsieci**  >  **maszyn wirtualnych z agentami**.
       Wybranie sieci wirtualnej, podsieć lub pojedynczej maszyn wirtualnych ustawia odpowiedni identyfikator zasobu jako punkt końcowy. Domyślnie wszystkie maszyny wirtualne w wybranej sieci wirtualnej lub podsieci z rozszerzeniem Network Watcher uczestniczą w monitorowaniu. Aby zmniejszyć zakres, wybierz opcję określone podsieci/agenci lub zmień wartość właściwości Scope. 

      ![Zrzut ekranu przedstawiający monitor połączeń z panelem Dodawanie źródeł i kartą Azure Agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Aby wybrać agentów lokalnych, wybierz kartę **inne niż agenci platformy Azure** . Domyślnie agenci są pogrupowani w obszary robocze według regionów. Wszystkie te obszary robocze mają skonfigurowane rozwiązanie Network Performance Monitor. 
   
       Jeśli musisz dodać Network Performance Monitor do obszaru roboczego, Pobierz go z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Aby uzyskać informacje o sposobach dodawania Network Performance Monitor, zobacz [monitorowanie rozwiązań w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       W widoku **Tworzenie monitora połączeń** na karcie **podstawowe** jest wybierany region domyślny. Jeśli zmienisz region, możesz wybrać agentów z obszarów roboczych w nowym regionie.  Można wybrać co najmniej jednego agenta lub podsieci. W widoku podsieci można wybrać określone adresy IP do monitorowania. Dodanie wielu podsieci spowoduje utworzenie niestandardowej sieci lokalnej, a mianowicie "OnPremises_Network_1". Możesz również zmienić wartość pola **Grupuj według** , aby grupować według podsieci.

      ![Zrzut ekranu przedstawiający monitor połączeń z panelem Dodawanie źródeł i kartami nienależącymi do platformy Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * Możesz również wybrać ostatnio używane punkty końcowe przy użyciu karty **Ostatnia punkt końcowy** 
   
   * Po zakończeniu konfigurowania źródeł wybierz pozycję **gotowe**. Można nadal edytować podstawowe właściwości, takie jak nazwa punktu końcowego, klikając punkt końcowy w widoku Utwórz grupę testową. 

* **Miejsca docelowe** — możesz monitorować łączność z maszynami wirtualnymi platformy Azure, maszynami lokalnymi lub dowolnym punktem końcowym (publicznym adresem IP, adresem URL lub nazwą FQDN) przez określenie ich jako miejsc docelowych. W ramach jednej grupy testowej można dodać maszyny wirtualne platformy Azure, maszynę lokalną, adresy URL pakietu Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.

    * Aby wybrać maszyny wirtualne platformy Azure jako miejsca docelowe, wybierz kartę **punkty końcowe platformy Azure** . Domyślnie maszyny wirtualne platformy Azure są pogrupowane w hierarchię subskrypcji znajdującą się w tym samym regionie, który został wybrany w widoku **Tworzenie monitora połączeń** , na karcie **podstawy** . Możesz zmienić region i wybrać maszyny wirtualne platformy Azure w nowo wybranym regionie. Następnie możesz przejść do szczegółów z poziomu subskrypcji na inne poziomy w hierarchii, podobnie jak źródłowe punkty końcowe platformy Azure.
     Można wybrać sieci wirtualnych, podsieci lub pojedyncze maszyny wirtualne, podobnie jak źródłowe punkty końcowe platformy Azure. Wybranie sieci wirtualnej, podsieć lub pojedynczej maszyn wirtualnych ustawia odpowiedni identyfikator zasobu jako punkt końcowy. Domyślnie wszystkie maszyny wirtualne w wybranej sieci wirtualnej lub podsieci z rozszerzeniem Network Watcher uczestniczą w monitorowaniu. Aby zmniejszyć zakres, wybierz opcję określone podsieci/agenci lub zmień wartość właściwości Scope. 

       ![Zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych z kartami maszyny wirtualne platformy Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych z poziomu subskrypcji](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Aby wybrać agentów platformy Azure jako miejsca docelowe, wybierz kartę punkty końcowe spoza platformy Azure. Domyślnie agenci są pogrupowani w obszary robocze według regionów. Wszystkie te obszary robocze mają skonfigurowane rozwiązanie Network Performance Monitor. Jeśli musisz dodać Network Performance Monitor do obszaru roboczego, Pobierz go z witryny Azure Marketplace. Aby uzyskać informacje o sposobach dodawania Network Performance Monitor, zobacz Monitorowanie rozwiązań w Azure Monitor. W widoku tworzenie monitora połączeń na karcie podstawowe jest wybierany region domyślny. Jeśli zmienisz region, możesz wybrać agentów z obszarów roboczych w nowym regionie. Można wybrać co najmniej jednego agenta lub podsieci. W widoku podsieci można wybrać określone adresy IP do monitorowania. Dodanie wielu podsieci spowoduje utworzenie niestandardowej sieci lokalnej, a mianowicie "OnPremises_Network_1".  
    
     ![Zrzut ekranu przedstawiający okienko Dodawanie innych miejsc docelowych platformy Azure](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Aby wybrać publiczne punkty końcowe jako miejsca docelowe, wybierz kartę **adresy zewnętrzne** . Lista punktów końcowych zawiera adresy URL testów pakietu Office 365 i adresy URL testów Dynamics 365, pogrupowane według nazwy. Oprócz tych punktów końcowych można wybrać punkt końcowy, który został utworzony w innych grupach testowych w tym samym monitorze połączeń. 
    
        Aby dodać nowy punkt końcowy, w prawym górnym rogu wybierz pozycję **+ punkty końcowe**. Podaj nazwę punktu końcowego i adres URL lub nazwę FQDN.

       ![Zrzut ekranu przedstawiający, gdzie dodać publiczne punkty końcowe jako miejsca docelowe w monitorze połączeń](./media/connection-monitor-2-preview/add-endpoints.png)

    * Aby wybrać ostatnio używane punkty końcowe, przejdź do karty **ostatnie punkty końcowe**   .
    * Po zakończeniu wybierania miejsc docelowych wybierz pozycję **gotowe**. Można nadal edytować podstawowe właściwości, takie jak nazwa punktu końcowego, klikając punkt końcowy w widoku Utwórz grupę testową. 

* **Konfiguracje testów** — można skojarzyć co najmniej jedną konfigurację testową w grupie testowej. Utwórz nową konfigurację testu przy użyciu karty "Nowa konfiguracja" lub użyj konfiguracji testowej używanej w innych grupach testowych w tym samym monitorze połączeń za pośrednictwem karty "Wybierz istniejący".

    * **Nazwa** — Nazwij konfigurację testu.
    * **Protokół** — wybierz TCP, ICMP lub http. Aby zmienić protokół HTTP na HTTPS, wybierz pozycję **http** jako protokół i wybierz pozycję **443** jako port.
        * **Utwórz konfigurację testu sieci** — to pole wyboru jest wyświetlane tylko w przypadku wybrania opcji **http** w polu **Protokół** . Zaznacz to pole, aby utworzyć inną konfigurację testu, która używa tych samych źródeł i miejsc docelowych, które zostały określone w innym miejscu konfiguracji. Nowo utworzona konfiguracja testu ma nazwę `<the name of your test configuration>_networkTestConfig` .
        * **Disable traceroute** — to pole jest stosowane, gdy protokół to TCP lub ICMP. Zaznacz to pole, aby zatrzymać odnajdywanie topologii i RTT przeskoków przez przeskok.
    * **Port docelowy** — możesz dostosować to pole przy użyciu dowolnie wybranego portu docelowego.
        * Nasłuchiwanie na porcie — to pole jest stosowane, gdy protokół to TCP. Zaznacz to pole, aby otworzyć wybrany port TCP, jeśli nie jest jeszcze otwarty. 
    * **Częstotliwość testów** — to pole służy do wybierania, jak często źródła będą wysyłać polecenia ping do miejsc docelowych w określonym protokole i porcie. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Wybierz pozycję niestandardowa, aby wprowadzić częstotliwość wyboru z zakresu od 30 sekund do 30 minut. Źródła przetestują łączność do miejsc docelowych na podstawie wybranej wartości.  Jeśli na przykład wybierzesz 30 sekund, źródła będą sprawdzać łączność z miejscem docelowym co najmniej raz w okresie 30 sekund.
    * **Próg sukcesu** — można ustawić progi dla następujących parametrów sieci:
       * **Sprawdzenie nie powiodło się** — Ustaw procent testów, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z lokalizacjami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP wartość procentowa nieudanych testów może być równa wartości procentowej utraty pakietów. W przypadku protokołu HTTP to pole reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
       * **Czas rundy** — ustawienie czasu RTT w milisekundach, przez jaki mogą być podejmowane długie źródła w celu nawiązania połączenia z miejscem docelowym w konfiguracji testu.
    
       ![Zrzut ekranu pokazujący, gdzie skonfigurować konfigurację testu w monitorze połączeń](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Tworzenie alertów w monitorze połączeń

Można skonfigurować alerty dotyczące testów, które kończą się niepowodzeniem na podstawie progów ustawionych w konfiguracjach testów.   

W Azure Portal, aby utworzyć alerty w monitorze połączeń, należy określić wartości dla następujących pól: 

- Utwórz alert — możesz zaznaczyć to pole, aby utworzyć alert dotyczący metryki w Azure Monitor. Wybranie tej opcji spowoduje włączenie innych pól do edycji. Dodatkowe opłaty dla alertu będą stosowane na podstawie [cen alertów](https://azure.microsoft.com/pricing/details/monitor/) 

- Zakres — > zasobów i zakresu — hierarchia > — jest to wstępnie wypełnione na podstawie wartości określonych na karcie podstawowe 

- Warunek — > alert jest tworzony w metryki "wynik testu (wersja zapoznawcza)". Gdy wynik testu monitora połączeń zakończy się niepowodzeniem, reguła alertu zostanie wyzwolona. 

- Grupa akcji — możesz wybrać opcję bezpośredniego wprowadzania wiadomości e-mail lub można utworzyć alerty za pomocą grup akcji. W przypadku wybrania bezpośredniej wiadomości e-mail zostanie utworzona grupa akcji z grupą akcji poczty E-mail NPM, a identyfikator e-mail zostanie dodany do tej grupy akcji. Jeśli zdecydujesz się użyć grup akcji, musisz wybrać wcześniej utworzoną grupę akcji. W tym miejscu możesz dowiedzieć się, jak utworzyć grupę akcji. Po pomyślnym utworzeniu alertu możesz użyć linku Zarządzanie alertami w celu zarządzania alertami. 

- Nazwa reguły alertu — nazwa monitora połączeń 

- Włącz regułę podczas tworzenia — spowoduje to włączenie reguły alertu na podstawie warunku. Wyłącz tę opcję, jeśli chcesz utworzyć regułę, ale jej nie włączą. 

    ![Zrzut ekranu przedstawiający tworzenie alertów w okienku monitora połączeń](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Limity skalowania

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączenia: 2 za pośrednictwem Azure Portal

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak analizować dane monitorowania i ustawiać alerty](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Dowiedz się [, jak zdiagnozować problemy w sieci](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
