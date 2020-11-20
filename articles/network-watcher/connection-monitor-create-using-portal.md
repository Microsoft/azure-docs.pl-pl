---
title: Utwórz monitor połączeń (wersja zapoznawcza) — Azure Portal
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób tworzenia monitora w monitorze połączeń przy użyciu Azure Portal.
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
ms.openlocfilehash: f1ba23d53d79587c9d75c1d840c1d3857725d8dd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984383"
---
# <a name="create-a-monitor-in-connection-monitor-preview-by-using-the-azure-portal"></a>Tworzenie monitora w monitorze połączeń (wersja zapoznawcza) przy użyciu Azure Portal

Dowiedz się, jak używać monitora połączeń do monitorowania komunikacji między zasobami. W tym artykule opisano sposób tworzenia monitora przy użyciu Azure Portal. Monitor połączeń obsługuje wdrożenia hybrydowe i w chmurze platformy Azure.

> [!IMPORTANT]
> Monitor połączeń jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

W monitorach połączeń utworzonych przy użyciu monitora połączeń można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub w innym adresie URL lub IP.

Oto kilka definicji, które ułatwią rozpoczęcie pracy:

* **Zasób monitora połączeń**. Zasób platformy Azure specyficzny dla regionu. Wszystkie następujące jednostki są właściwościami zasobu monitora połączeń.
* **Punkt końcowy**. Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykładowe punkty końcowe to:
    * Maszyny wirtualne platformy Azure.
    * Sieci wirtualne platformy Azure.
    * Podsieci platformy Azure.
    * Agenci lokalni.
    * Podsieci lokalne.
    * Lokalne sieci niestandardowe z wieloma podsieciami.
    * Adresy URL i IP.
* **Konfiguracja testu**. Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa**. Grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test**. Kombinacja źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagram przedstawiający monitor połączeń i definiuje relację między grupami testów i testami.":::


## <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń

Aby utworzyć monitor w monitorze połączeń przy użyciu Azure Portal:

1. Na stronie głównej Azure Portal przejdź do **Network Watcher**.
1. W okienku po lewej stronie w sekcji **monitorowanie** wybierz pozycję **monitor połączeń**.

   Zobaczysz wszystkie monitory połączeń, które zostały utworzone w monitorze połączenia. Aby wyświetlić monitory połączeń, które zostały utworzone w klasycznym monitorze połączeń, przejdź do karty **monitor połączeń** .

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Zrzut ekranu przedstawiający monitory połączeń utworzone w monitorze połączeń.":::
   
    
1. Na pulpicie nawigacyjnym **monitora połączeń** w lewym górnym rogu wybierz pozycję **Utwórz**.

   

1. Na karcie **podstawowe** wprowadź informacje dotyczące monitora połączeń: 
   * **Nazwa monitora połączeń**: Wprowadź nazwę monitora połączeń. Użyj standardowych reguł nazewnictwa dla zasobów platformy Azure.
   * **Subskrypcja**: wybierz subskrypcję dla monitora połączeń.
   * **Region**: Wybierz region monitora połączeń. Można wybrać tylko źródłowe maszyny wirtualne, które są tworzone w tym regionie.
   * **Konfiguracja obszaru** roboczego: Wybierz niestandardowy obszar roboczy lub domyślny obszar roboczy. Obszar roboczy zawiera dane monitorowania.
       * Aby użyć domyślnego obszaru roboczego, zaznacz to pole wyboru. 
       * Aby wybrać niestandardowy obszar roboczy, usuń zaznaczenie pola wyboru. Następnie wybierz subskrypcję i region dla niestandardowego obszaru roboczego. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Zrzut ekranu przedstawiający kartę podstawowe w monitorze połączenia.":::
   
1. W dolnej części karty wybierz pozycję **Dalej: grupy testowe**.

1. Dodawanie źródeł, miejsc docelowych i konfiguracji testów w grupach testowych. Aby dowiedzieć się więcej o konfigurowaniu grup testowych, zobacz [Tworzenie grup testowych w monitorze połączeń](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Zrzut ekranu przedstawiający kartę grupy testów w monitorze połączenia.":::

1. W dolnej części karty wybierz kolejno pozycje **Dalej: tworzenie alertów**. Aby dowiedzieć się więcej o tworzeniu alertów, zobacz [tworzenie alertów w monitorze połączeń](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Zrzut ekranu przedstawiający kartę tworzenie alertu.":::

1. W dolnej części karty wybierz kolejno pozycje **Dalej: recenzja + Utwórz**.

1. Przed utworzeniem monitora połączeń na karcie **Przegląd + tworzenie** Przejrzyj podstawowe informacje i grupy testowe. Jeśli musisz edytować monitor połączeń, możesz to zrobić, wracając do odpowiednich kart. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Zrzut ekranu pokazujący kartę Przegląd i tworzenie w monitorze połączeń.":::
   > [!NOTE] 
   > Karta **Przegląd + tworzenie** pokazuje koszt miesięcznie na etapie monitora połączenia. Obecnie w kolumnie **bieżący koszt/miesiąc** nie jest naliczana opłata. Gdy monitor połączeń będzie ogólnie dostępny, w tej kolumnie będzie wyświetlana opłata miesięczna. 
   > 
   > Nawet w trakcie etapu monitora połączeń obowiązują Log Analytics opłaty za pozyskiwanie.

1. Gdy wszystko będzie gotowe do utworzenia monitora połączeń, w dolnej części karty **Recenzja + tworzenie** wybierz pozycję **Utwórz**.

Monitor połączeń tworzy zasób monitora połączeń w tle.

## <a name="create-test-groups-in-a-connection-monitor"></a>Tworzenie grup testowych w monitorze połączeń

Każda grupa testowa w monitorze połączeń zawiera źródła i miejsca docelowe, które są przetestowane w oparciu o parametry sieci. Są one testowane pod kątem procentu kontroli zakończonych niepowodzeniem i RTT przez konfiguracje testów.

W Azure Portal, aby utworzyć grupę testową w monitorze połączeń, należy określić wartości dla następujących pól:

* **Wyłącz grupę testową**: można zaznaczyć to pole wyboru, aby wyłączyć monitorowanie dla wszystkich źródeł i miejsc docelowych, które określa Grupa testowa. Ten wybór jest domyślnie wyczyszczony.
* **Nazwa**: nadaj nazwę grupie testowej.
* **Źródła**: można określić maszyny wirtualne platformy Azure i maszyny lokalne jako źródła, jeśli agenci są na nich zainstalowani. Aby dowiedzieć się więcej o instalowaniu agenta dla źródła, zobacz [Instalowanie agentów monitorowania](./connection-monitor-overview.md#install-monitoring-agents).
   * Aby wybrać agentów platformy Azure, wybierz kartę **punkty końcowe platformy Azure** . W tym miejscu są wyświetlane tylko maszyny wirtualne, które są powiązane z regionem określonym podczas tworzenia monitora połączeń. Domyślnie maszyny wirtualne są pogrupowane w subskrypcję, do której należą. Te grupy są zwinięte. 
   
       Możesz przejść do szczegółów z poziomu **subskrypcji** na inne poziomy w hierarchii:

      **Subskrypcja**  >  **Grupa zasobów**  >  **Sieć wirtualna**  >  **Podsieć**  >  **Maszyny wirtualne z agentami**

      Możesz również zmienić **grupę według** selektora, aby uruchomić drzewo z dowolnego innego poziomu. Na przykład Jeśli grupujesz według sieci wirtualnej, zobaczysz maszyny wirtualne, które mają agentów w sieci **wirtualnej**  >  **podsieci**  >  **wirtualne z agentami**.

       W przypadku wybrania sieci wirtualnej, podsieci lub pojedynczej, odpowiedni identyfikator zasobu jest ustawiany jako punkt końcowy. Domyślnie wszystkie maszyny wirtualne w wybranej sieci wirtualnej lub podsieci, które mają rozszerzenie Azure Network Watcher, uczestniczą w monitorowaniu. Aby zmniejszyć zakres, zaznacz opcję określone podsieci lub agenci lub zmień wartość właściwości Scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Zrzut ekranu przedstawiający okienko Dodawanie źródeł i kartę punkty końcowe platformy Azure w monitorze połączenia.":::

   * Aby wybrać agentów lokalnych, wybierz kartę **punkty końcowe inne niż Azure** . Domyślnie agenci są pogrupowani w obszary robocze według regionów. Wszystkie te obszary robocze mają skonfigurowaną Network Performance Monitor. 
   
       Jeśli musisz dodać Network Performance Monitor do obszaru roboczego, Pobierz go z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Aby uzyskać informacje o sposobach dodawania Network Performance Monitor, zobacz [monitorowanie rozwiązań w Azure monitor](../azure-monitor/insights/solutions.md). 
   
       W obszarze **Utwórz monitor połączeń** na karcie **podstawowe** jest wybierany region domyślny. Jeśli zmienisz region, możesz wybrać agentów z obszarów roboczych w nowym regionie. Można wybrać co najmniej jednego agenta lub podsieci. W widoku **podsieci** można wybrać określone adresy IP do monitorowania. W przypadku dodania wielu podsieci zostanie utworzona niestandardowa sieć lokalna o nazwie **OnPremises_Network_1** . Możesz również zmienić selektor **Grupuj według** , aby grupować według agentów.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Zrzut ekranu przedstawiający okienko Dodawanie źródeł oraz kartę punkty końcowe spoza platformy Azure w monitorze połączenia.":::

   * Aby wybrać ostatnio używane punkty końcowe, można użyć karty **Ostatnia wersja punktu końcowego** 
   
   * Po zakończeniu konfigurowania źródeł wybierz pozycję **gotowe** w dolnej części karty. Można nadal edytować podstawowe właściwości, takie jak nazwa punktu końcowego, wybierając punkt końcowy w widoku **Tworzenie grupy testowej** . 

* **Miejsca docelowe**: możesz monitorować łączność z maszyną wirtualną platformy Azure, maszyną lokalną lub dowolnym punktem końcowym (publicznym adresem IP, URL lub nazwą FQDN) przez określenie go jako miejsca docelowego. W ramach pojedynczej grupy testowej można dodać maszyny wirtualne platformy Azure, usługi lokalne, adresy URL pakietu Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.

    * Aby wybrać maszyny wirtualne platformy Azure jako miejsca docelowe, wybierz kartę **punkty końcowe platformy Azure** . Domyślnie maszyny wirtualne platformy Azure są pogrupowane w hierarchię subskrypcji, która znajduje się w regionie wybranym w obszarze **Utwórz monitor połączeń** na karcie **podstawy** . Możesz zmienić region i wybrać maszyny wirtualne platformy Azure z nowego regionu. Następnie możesz przejść do szczegółów z poziomu **subskrypcji** na inne poziomy w hierarchii, tak jak podczas ustawiania źródłowych punktów końcowych platformy Azure.

      Można wybrać sieci wirtualnych, podsieci lub pojedyncze maszyny wirtualne, jak można ustawić źródłowe punkty końcowe platformy Azure. W przypadku wybrania sieci wirtualnej, podsieci lub pojedynczej, odpowiedni identyfikator zasobu jest ustawiany jako punkt końcowy. Domyślnie wszystkie maszyny wirtualne w wybranej sieci wirtualnej lub podsieci, które mają rozszerzenie Network Watcher, uczestniczą w monitorowaniu. Aby zmniejszyć zakres, zaznacz opcję określone podsieci lub agenci lub zmień wartość właściwości Scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<zrzut ekranu, który pokazuje okienko Dodawanie miejsc docelowych oraz kartę punkty końcowe platformy Azure. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych na poziomie subskrypcji. >":::
       
    
    * Aby wybrać agentów nienależących do platformy Azure jako miejsca docelowe, wybierz kartę **punkty końcowe spoza platformy Azure** . Domyślnie agenci są pogrupowani w obszary robocze według regionów. Wszystkie te obszary robocze mają skonfigurowane Network Performance Monitor. 
    
      Jeśli musisz dodać Network Performance Monitor do obszaru roboczego, Pobierz go z witryny Azure Marketplace. Aby uzyskać informacje o sposobach dodawania Network Performance Monitor, zobacz [monitorowanie rozwiązań w Azure monitor](../azure-monitor/insights/solutions.md). 

      W obszarze **Utwórz monitor połączeń** na karcie **podstawowe**   jest wybierany region domyślny. Jeśli zmienisz region, możesz wybrać agentów z obszarów roboczych w nowym regionie. Można wybrać co najmniej jednego agenta lub podsieci. W widoku **podsieci** można wybrać określone adresy IP do monitorowania. W przypadku dodania wielu podsieci zostanie utworzona niestandardowa sieć lokalna o nazwie **OnPremises_Network_1** .  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych oraz kartę punkty końcowe spoza platformy Azure.":::
    
    * Aby wybrać publiczne punkty końcowe jako miejsca docelowe, wybierz kartę **adresy zewnętrzne** . Lista punktów końcowych zawiera adresy URL testów pakietu Office 365 i adresy URL testów Dynamics 365, pogrupowane według nazwy. Możesz również wybrać punkty końcowe, które zostały utworzone w innych grupach testowych w tym samym monitorze połączeń. 
    
        Aby dodać punkt końcowy, w prawym górnym rogu wybierz pozycję **Dodaj punkt końcowy**. Podaj nazwę punktu końcowego i adres URL lub nazwę FQDN.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Zrzut ekranu pokazujący, gdzie dodać publiczne punkty końcowe jako miejsca docelowe w monitorze połączeń.":::

    * Aby wybrać ostatnio używane punkty końcowe, przejdź do karty **Ostatnia wersja punktu końcowego**   .
    * Po zakończeniu wybierania miejsc docelowych wybierz pozycję **gotowe**. Można nadal edytować podstawowe właściwości, takie jak nazwa punktu końcowego, wybierając punkt końcowy w widoku **Tworzenie grupy testowej** . 

* **Konfiguracje testów**: można dodać co najmniej jedną konfigurację testową do grupy testowej. Utwórz nową konfigurację testu za pomocą nowej karty **Konfiguracja** . Lub Dodaj konfigurację testową z innej grupy testowej w tym samym monitorze połączeń z karty **Wybierz istniejące** .

    * **Nazwa konfiguracji testu**: Nazwij konfigurację testu.
    * **Protokół**: wybierz opcję **TCP**, **ICMP** lub **http**. Aby zmienić protokół HTTP na HTTPS, wybierz pozycję **http** jako protokół, a następnie wybierz pozycję **443** jako port.
        * **Utwórz konfigurację testu TCP**: to pole wyboru jest wyświetlane tylko w przypadku wybrania z listy **protokołów** protokołu **http** . Zaznacz to pole wyboru, aby utworzyć kolejną konfigurację testu, która używa tych samych źródeł i miejsc docelowych, które zostały określone w innym miejscu konfiguracji. Nowa konfiguracja testu ma nazwę **\<name of test configuration> _networkTestConfig**.
        * **Wyłącz traceroute**: to pole wyboru ma zastosowanie, gdy protokół to TCP lub ICMP. Zaznacz to pole, aby zatrzymać odnajdywanie topologii i RTT przeskoków przez przeskok.
    * **Port docelowy**: można podać wybrany port docelowy.
        * **Nasłuchiwanie na porcie**: to pole wyboru jest stosowane, gdy protokół jest TCP. Zaznacz to pole wyboru, aby otworzyć wybrany port TCP, jeśli nie jest jeszcze otwarty. 
    * **Częstotliwość testów**: na tej liście Określ, jak często źródła będą wysyłać polecenia ping do miejsc docelowych w określonym protokole i porcie. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Wybierz opcję **niestandardowy** , aby wprowadzić kolejną częstotliwość, która należy do zakresu od 30 sekund do 30 minut. Źródła przetestują łączność do miejsc docelowych na podstawie wybranej wartości. Jeśli na przykład wybierzesz 30 sekund, źródła będą sprawdzać łączność z miejscem docelowym co najmniej raz w każdym okresie 30 sekund.
    * **Próg sukcesu**: można ustawić progi dla następujących parametrów sieci:
       * **Sprawdzenie nie powiodło się**: Ustaw procent kontroli, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z lokalizacjami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP wartość procentowa nieudanych testów może być równa wartości procentowej utraty pakietów. W przypadku protokołu HTTP Ta wartość reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
       * **Czas błądzenia**: Ustaw wartość RTT (w milisekundach), o jaką można wykonać długie źródła, aby połączyć się z miejscem docelowym za pośrednictwem konfiguracji testu.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Zrzut ekranu pokazujący, gdzie skonfigurować konfigurację testu w monitorze połączeń.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Tworzenie alertów w monitorze połączeń

Można skonfigurować alerty dotyczące testów, które kończą się niepowodzeniem na podstawie progów ustawionych w konfiguracjach testów.

W Azure Portal, aby utworzyć alerty dla monitora połączeń, należy określić wartości dla tych pól: 

- **Utwórz alert**: możesz zaznaczyć to pole wyboru, aby utworzyć alert dotyczący metryki w Azure monitor. Po zaznaczeniu tego pola wyboru pozostałe pola zostaną włączone do edycji. Opłaty zostaną naliczone na podstawie [cen alertów](https://azure.microsoft.com/pricing/details/monitor/). 

- **Zakres**  >  **Zasób**  >  **Hierarchia**: te wartości są wypełniane automatycznie na podstawie wartości określonych na karcie **podstawy** .

- **Nazwa warunku**: alert jest tworzony w ramach `Test Result(preview)` metryki. Gdy wynik testu monitora połączeń ma wynik niepowodzeniem, reguła alertu zostanie wyzwolona. 

- **Nazwa grupy akcji**: możesz wprowadzić swój adres e-mail bezpośrednio lub można utworzyć alerty za pomocą grup akcji. Jeśli adres e-mail zostanie wprowadzony bezpośrednio, zostanie utworzona grupa akcji z grupą **akcji poczty E-mail npm** . Identyfikator e-mail zostanie dodany do tej grupy akcji. W przypadku wybrania opcji używania grup akcji należy wybrać wcześniej utworzoną grupę akcji. Aby dowiedzieć się, jak utworzyć grupę akcji, zobacz [Tworzenie grup akcji w Azure Portal](../azure-monitor/platform/action-groups.md). Po utworzeniu alertu możesz [zarządzać alertami](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Nazwa reguły alertu**: Nazwa monitora połączeń.

- **Włącz regułę przy tworzeniu**: zaznacz to pole wyboru, aby włączyć regułę alertu na podstawie warunku. Wyłącz to pole wyboru, jeśli chcesz utworzyć regułę bez włączania jej. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Zrzut ekranu przedstawiający kartę tworzenie alertu w monitorze połączeń.":::

## <a name="scale-limits"></a>Limity skalowania

Te limity skali są monitorowane przez monitory połączeń:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączenia: 2 za pośrednictwem Azure Portal

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak analizować dane monitorowania i ustawiać alerty](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Dowiedz się [, jak zdiagnozować problemy w sieci](./connection-monitor-overview.md#diagnose-issues-in-your-network).