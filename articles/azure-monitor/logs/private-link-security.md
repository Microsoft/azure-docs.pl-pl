---
title: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
description: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 65af5810152034fd7b6014041edd07835eebd194
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101481"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia bezpieczne łączenie usług Azure PaaS z siecią wirtualną za pomocą prywatnych punktów końcowych. W przypadku wielu usług wystarczy skonfigurować punkt końcowy dla każdego zasobu. Azure Monitor jest jednak Constellation różnych połączonych usług, które współpracują ze sobą w celu monitorowania obciążeń. W związku z tym utworzyliśmy zasób o nazwie Azure Monitor prywatny zakres linków (AMPLS). AMPLS umożliwia zdefiniowanie granic sieci monitorowania i nawiązanie połączenia z siecią wirtualną. W tym artykule omówiono, kiedy należy używać programu i jak skonfigurować Azure Monitor zakres linków prywatnych.

## <a name="advantages"></a>Zalety

Za pomocą linku prywatnego można:

- Połącz się prywatnie, aby Azure Monitor bez otwierania dostępu do sieci publicznej
- Upewnij się, że dane monitorowania są dostępne tylko za poorednictwem autoryzowanych sieci prywatnych
- Zapobiegaj eksfiltracji danych z sieci prywatnych przez definiowanie określonych zasobów Azure Monitor, które łączą się za pośrednictwem prywatnego punktu końcowego
- Bezpieczne łączenie prywatnej sieci lokalnej w celu Azure Monitor przy użyciu linku ExpressRoute i prywatnego
- Zachowaj cały ruch w sieci szkieletowej Microsoft Azure

Aby uzyskać więcej informacji, zobacz  [najważniejsze zalety linku prywatnego](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to działa

Azure Monitor prywatny zakres linków (AMPLS) łączy prywatne punkty końcowe (oraz sieci wirtualnych, w których są zawarte) do co najmniej jednego Azure Monitor zasobów-Log Analytics obszarów roboczych i składników Application Insights.

![Diagram podstawowej topologii zasobów](./media/private-link-security/private-link-basic-topology.png)

* Prywatny punkt końcowy w sieci wirtualnej umożliwia działowi IT dostęp do Azure Monitor punktów końcowych za pośrednictwem prywatnych adresów IP z puli sieci, zamiast korzystać z publicznych adresów IP tych punktów końcowych. Dzięki temu można nadal korzystać z zasobów Azure Monitor bez konieczności otwierania sieci wirtualnej na niewymagany ruch wychodzący. 
* Ruch z prywatnego punktu końcowego do zasobów Azure Monitor przejdzie przez szkielet Microsoft Azure i nie jest kierowany do sieci publicznych. 
* Można skonfigurować poszczególne obszary robocze lub składniki, aby zezwalać na pozyskiwanie i wykonywanie zapytań z sieci publicznych. Zapewnia to ochronę na poziomie zasobów, dzięki czemu można kontrolować ruch do określonych zasobów.

> [!NOTE]
> Pojedynczy zasób Azure Monitor może należeć do wielu AMPLSs, ale nie można połączyć jednej sieci wirtualnej z więcej niż jedną AMPLSą. 

## <a name="planning-your-private-link-setup"></a>Planowanie konfiguracji linku prywatnego

Przed rozpoczęciem konfigurowania Azure Monitor konfiguracji łącza prywatnego należy wziąć pod uwagę topologię sieci, a w związku z tym topologię routingu DNS. 

### <a name="the-issue-of-dns-overrides"></a>Problem zastąpień DNS
Niektóre usługi Azure Monitor korzystają z globalnych punktów końcowych, co oznacza, że służą one do obsługi żądań dotyczących dowolnego obszaru roboczego/składnika. Kilka przykładów to punkt końcowy pozyskiwania Application Insights i punkt końcowy zapytania obu Application Insights i Log Analytics.

Po skonfigurowaniu połączenia z linkiem prywatnym usługa DNS jest aktualizowana w celu mapowania punktów końcowych Azure Monitor na prywatne adresy IP z zakresu adresów IP sieci wirtualnej. Ta zmiana zastępuje wszystkie poprzednie mapowania tych punktów końcowych, które mogą mieć zrozumiałe konsekwencje, poniżej. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor link prywatny dotyczy wszystkich zasobów Azure Monitor — to wszystko lub nic
Ponieważ niektóre Azure Monitor punkty końcowe są globalne, nie można utworzyć połączenia prywatnego dla określonego składnika lub obszaru roboczego. Zamiast tego po skonfigurowaniu prywatnego linku do jednego składnika Application Insights rekordy DNS są aktualizowane dla **wszystkich** składników Application Insights. Każda próba pozyskania lub wypróbowania składnika spowoduje przejście przez połączenie prywatne i prawdopodobnie nie powiedzie się. Podobnie skonfigurowanie prywatnego linku do jednego obszaru roboczego spowoduje, że wszystkie zapytania Log Analytics przechodzą przez punkt końcowy zapytania łącza prywatnego (ale nie żądania pozyskania, które mają punkty końcowe specyficzne dla obszaru roboczego).

![Diagram zastąpień DNS w pojedynczej sieci wirtualnej](./media/private-link-security/dns-overrides-single-vnet.png)

To prawda nie tylko dla określonej sieci wirtualnej, ale dla wszystkich sieci wirtualnych, które współużytkują ten sam serwer DNS (zobacz [problem zastąpień DNS](#the-issue-of-dns-overrides)). Na przykład żądanie pozyskania dzienników do dowolnego składnika Application Insights będzie zawsze wysyłane za pomocą prywatnej trasy linków. Składniki, które nie są połączone z AMPLS, nie będą mogły przeprowadzić walidacji prywatnego linku i nie przechodzą przez nie.

> [!NOTE]
> Aby dokończyć: po skonfigurowaniu połączenia prywatnego z pojedynczym zasobem zostanie ono zastosowane do wszystkich zasobów Azure Monitor w sieci — to wszystko lub nic nie rób. Dzięki temu należy dodać wszystkie zasoby Azure Monitor w sieci do AMPLS lub żadnego z nich.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor łącze prywatne ma zastosowanie do całej sieci
Niektóre sieci składają się z wielu sieci wirtualnych. Jeśli sieci wirtualnych używają tego samego serwera DNS, zastąpią wszystkie inne mapowania DNS i możliwe będzie przerwanie komunikacji z Azure Monitor (zobacz [problem z zastąpień DNS](#the-issue-of-dns-overrides)). Ostatecznie tylko Ostatnia Sieć wirtualna będzie mogła komunikować się z Azure Monitor, ponieważ usługa DNS mapuje Azure Monitor punktów końcowych na prywatne adresy IP z tego zakresu sieci wirtualnych (które mogą nie być dostępne z innych sieci wirtualnych).

![Diagram zastąpień DNS w wielu sieci wirtualnych](./media/private-link-security/dns-overrides-multiple-vnets.png)

Na powyższym diagramie Sieć wirtualna 10.0.1. x najpierw łączy się z AMPLS1 i mapuje globalne punkty końcowe Azure Monitor na adresy IP z zakresu. Później Sieć wirtualna 10.0.2. x nawiązuje połączenie z AMPLS2 i zastępuje mapowanie DNS tych *samych globalnych punktów końcowych* z adresami IP z zakresu. Ponieważ te sieci wirtualnych nie są połączone za pomocą komunikacji równorzędnej, pierwsza Sieć wirtualna nie może teraz połączyć się z tymi punktami końcowymi.

> [!NOTE]
> Aby dokończyć: Konfiguracja AMPLS ma wpływ na wszystkie sieci, które współużytkują te same strefy DNS. Aby uniknąć przesłaniania mapowań punktów końcowych DNS, najlepiej jest skonfigurować pojedynczy prywatny punkt końcowy w sieci równorzędnej (takiej jak koncentrator sieci wirtualnej) lub oddzielić sieci na poziomie systemu DNS (Foe przykład przy użyciu usług przesyłania dalej DNS lub całkowicie oddzielnych serwerów DNS).

### <a name="hub-spoke-networks"></a>Sieci gwiazdy
Topologie gwiazdy mogą uniknąć problemu zastąpień DNS przez ustawienie prywatnego linku w sieci wirtualnej Hub (głównej), zamiast konfigurowania prywatnego linku dla każdej sieci wirtualnej. Ta konfiguracja ma sens szczególnie w przypadku udostępnienia Azure Monitor zasobów używanych przez sieci wirtualnych szprych. 

![Koncentrator i szprycha — pojedynczy PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Celowe może być utworzenie oddzielnych linków prywatnych dla satelity sieci wirtualnych, na przykład w celu zezwolenia każdej sieci wirtualnej na dostęp do ograniczonego zestawu zasobów monitorowania. W takich przypadkach można utworzyć dedykowany prywatny punkt końcowy i AMPLS dla każdej sieci wirtualnej, ale również sprawdzić, czy nie współużytkują te same strefy DNS w celu uniknięcia zastąpień DNS.


### <a name="consider-limits"></a>Uwzględnij limity

Jak wymieniono w [ograniczeniach i ograniczeniach](#restrictions-and-limitations), obiekt AMPLS ma liczbę limitów pokazanych w poniższej topologii:
* Każda sieć wirtualna nawiązuje połączenie tylko z **1** AMPLS obiektem.
* AMPLS B jest połączony z prywatnymi punktami końcowymi dwóch sieci wirtualnych (VNet2 i sieci vnet3) przy użyciu 2 z 10 możliwych połączeń prywatnych punktów końcowych.
* AMPLS łączy się do dwóch obszarów roboczych i jednego składnika usługi Application Insights, używając 3 z 50 Azure Monitor możliwych połączeń zasobów.
* Workspace2 nawiązuje połączenie z AMPLS a i AMPLS B przy użyciu 2 z 5 możliwych połączeń AMPLS.

![Diagram limitów AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Przykładowe połączenie

Zacznij od utworzenia zasobu zakresu prywatnego linku Azure Monitor.

1. Przejdź do pozycji **Utwórz zasób** w Azure Portal i Wyszukaj **Azure monitor prywatny zakres linków**.

   ![Znajdź Azure Monitor zakres linków prywatnych](./media/private-link-security/ampls-find-1c.png)

2. Wybierz pozycję **Utwórz**.
3. Wybierz subskrypcję i grupę zasobów.
4. Nadaj nazwę AMPLS. Najlepiej używać zrozumiałej i jasnej nazwy, takiej jak "AppServerProdTelem".
5. Wybierz pozycję **Przejrzyj i utwórz**. 

   ![Utwórz Azure Monitor zakres linków prywatnych](./media/private-link-security/ampls-create-1d.png)

6. Pozwól na przekazanie walidacji, a następnie wybierz pozycję **Utwórz**.

### <a name="connect-azure-monitor-resources"></a>Łączenie Azure Monitor zasobów

Połącz zasoby Azure Monitor (Log Analytics obszary robocze i składniki Application Insights) z AMPLS.

1. W zakresie prywatnego łącza Azure Monitor wybierz pozycję **Azure monitor zasoby** w menu po lewej stronie. Wybierz przycisk **Add** (Dodaj).
2. Dodaj obszar roboczy lub składnik. Wybranie przycisku **Dodaj** powoduje wyświetlenie okna dialogowego, w którym można wybrać Azure monitor zasoby. Możesz przeglądać subskrypcje i grupy zasobów lub wpisywać ich nazwy, aby filtrować do nich. Wybierz obszar roboczy lub składnik, a następnie wybierz pozycję **Zastosuj** , aby dodać je do zakresu.

    ![Zrzut ekranu przedstawiający środowisko Select a Scope](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Usuwanie zasobów Azure Monitor wymaga, aby najpierw odłączyć je od wszelkich obiektów AMPLS, z którymi są połączone. Nie jest możliwe usunięcie zasobów połączonych z AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Teraz, gdy masz zasoby połączone z AMPLS, Utwórz prywatny punkt końcowy, aby połączyć naszą sieć. To zadanie można wykonać w ramach [prywatnego centrum linków Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)lub wewnątrz zakresu prywatnego linku Azure monitor, jak zostało to zrobione w tym przykładzie.

1. W obszarze zasób zakresu wybierz pozycję **połączenia prywatnych punktów końcowych** w menu zasobów po lewej stronie. Wybierz pozycję **prywatny punkt końcowy** , aby uruchomić proces tworzenia punktu końcowego. Możesz także zatwierdzać połączenia, które zostały uruchomione w centrum linku prywatnego, wybierając je i wybierając pozycję **Zatwierdź**.

    ![Zrzut ekranu środowiska użytkownika połączenia z prywatnymi punktami końcowymi](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Wybierz subskrypcję, grupę zasobów i nazwę punktu końcowego oraz region, w którym powinien się znajdować. Region musi być tym samym regionem, do którego jest podłączona Sieć wirtualna.

3. Wybierz pozycję **Dalej: zasób**. 

4. Na ekranie zasób

   a. Wybierz **subskrypcję** zawierającą zasób zakresu prywatnego Azure monitor. 

   b. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. Insights/privateLinkScopes**. 

   c. Z listy rozwijanej **zasób** wybierz swój prywatny zakres linków, który został utworzony wcześniej. 

   d. Wybierz kolejno pozycje **Dalej: Configuration >**.
      ![Zrzut ekranu przedstawiający pozycję Utwórz prywatny punkt końcowy](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. W okienku Konfiguracja,

   a.    Wybierz **sieć wirtualną** i **podsieć** , którą chcesz połączyć z zasobami Azure monitor. 
 
   b.    Wybierz opcję **tak** dla **integracji z prywatną strefą DNS** i zezwól na automatyczne tworzenie nowej strefy prywatna strefa DNS. Rzeczywiste strefy DNS mogą się różnić od tego, co pokazano na poniższym zrzucie ekranu. 
   > [!NOTE]
   > Jeśli wybierzesz opcję **nie** i wolisz ręcznie zarządzać rekordami DNS, najpierw Ukończ Konfigurowanie linku prywatnego — łącznie z tym prywatnym punktem końcowym i konfiguracją AMPLS. Następnie skonfiguruj usługę DNS zgodnie z instrukcjami zawartymi w artykule [Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md). Pamiętaj, aby nie tworzyć pustych rekordów podczas przygotowywania się do konfiguracji łącza prywatnego. Tworzone rekordy DNS mogą przesłaniać istniejące ustawienia i mieć wpływ na łączność z usługą Azure Monitor.
 
   c.    Wybierz pozycję **Przejrzyj i utwórz**.
 
   d.    Zezwalaj na weryfikację. 
 
   e.    Wybierz przycisk **Utwórz**. 

    ![Zrzut ekranu przedstawiający szczegóły wybierania prywatnego punktu końcowego.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Utworzono nowy prywatny punkt końcowy, który jest połączony z tym AMPLSem.

## <a name="review-and-validate-your-private-link-setup"></a>Przejrzyj i sprawdź poprawność konfiguracji linku prywatnego

### <a name="reviewing-your-endpoints-dns-settings"></a>Przeglądanie ustawień DNS punktu końcowego
Utworzony prywatny punkt końcowy powinien mieć teraz cztery skonfigurowane strefy DNS:

[![Zrzut ekranu strefy DNS prywatnego punktu końcowego.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-Azure-com
* privatelink-OMS-usługi OpInsights-Azure-com
* privatelink-ODS-usługi OpInsights-Azure-com
* privatelink-agentsvc-Azure-Automation-NET

> [!NOTE]
> Każda z tych stref mapuje określone punkty końcowe Azure Monitor na prywatne adresy IP z puli adresów IP sieci wirtualnej. Adresy IP wyświetlane w poniższych obrazach są tylko przykładowe. W takiej konfiguracji należy zamiast tego pokazać prywatne adresy IP z własnej sieci.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-Azure-com
Ta strefa obejmuje globalne punkty końcowe używane przez Azure Monitor, co oznacza, że te punkty końcowe obsłużą żądania rozważające wszystkie zasoby, a nie konkretną. Ta strefa powinna mieć punkty końcowe mapowane dla:
* `in.ai` -(Application Insights punkt końcowy pozyskiwania, zobaczysz globalny i regionalny wpis
* `api` -Application Insights i Log Analytics punkt końcowy interfejsu API
* `live` -Application Insights punkt końcowy metryk na żywo
* `profiler` -Application Insights punkt końcowy profilera
* `snapshot`-Application Insights zrzut ekranu punktu końcowego migawek [ ![ monitora strefy prywatna strefa DNS-Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-OMS-usługi OpInsights-Azure-com
Ta strefa obejmuje mapowanie specyficzne dla obszaru roboczego do punktów końcowych pakietu OMS. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z AMPLSą połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu Prywatna strefa DNS Zone OMS-usługi OpInsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ODS-usługi OpInsights-Azure-com
Ta strefa zawiera mapowanie specyficzne dla obszaru roboczego do punktów końcowych ODS — punkt końcowy pozyskiwania Log Analytics. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z AMPLSą połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu przedstawiający Prywatna strefa DNS Zone ODS-usługi OpInsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-Azure-Automation-NET
Ta strefa obejmuje mapowanie specyficzne dla obszaru roboczego do punktów końcowych automatyzacji usługi agenta. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z AMPLSą połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu Prywatna strefa DNS agenta strefy SVC — Azure-Automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Weryfikowanie komunikacji za pośrednictwem prywatnego linku
* Aby sprawdzić, czy żądania są teraz wysyłane przez prywatny punkt końcowy oraz do prywatnych punktów końcowych mapowanych przez adresy IP, możesz przejrzeć je za pomocą funkcji śledzenia sieci, a nawet przeglądarki. Na przykład podczas próby wysłania zapytania do obszaru roboczego lub aplikacji upewnij się, że żądanie jest wysyłane do prywatnego adresu IP zamapowanego na punkt końcowy interfejsu API, w tym przykładzie jest to *172.17.0.9*.

    Uwaga: Niektóre przeglądarki mogą używać innych ustawień DNS (zobacz [Ustawienia DNS przeglądarki](#browser-dns-settings)). Upewnij się, że ustawienia DNS są stosowane.

* Aby upewnić się, że obszar roboczy lub składnik nie odbiera żądań z sieci publicznych (niepołączone przez AMPLS), ustaw publiczne pozyskiwanie i flagi zapytania dla zasobu *, tak jak* wyjaśniono w temacie [Zarządzanie dostępem spoza zakresów linków prywatnych](#manage-access-from-outside-of-private-links-scopes).

* Z poziomu klienta w chronionej sieci, użyj `nslookup` do dowolnego punktu końcowego wymienionego w strefach DNS. Powinien on zostać rozwiązany przez serwer DNS z mapowanymi prywatnymi adresami IP zamiast publicznymi adresami IP używanymi domyślnie.


## <a name="configure-log-analytics"></a>Konfigurowanie usługi Log Analytics

Przejdź do witryny Azure Portal. W menu zasobów obszaru roboczego Log Analytics istnieje element o nazwie **izolacja sieci** po lewej stronie. W tym menu można kontrolować dwa różne stany.

![Izolacja sieci LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Połączone Azure Monitor zakresy linków prywatnych
Wszystkie zakresy połączone z obszarem roboczym są wyświetlane na tym ekranie. Łączenie z zakresami (AMPLSs) zezwala na ruch sieciowy z sieci wirtualnej połączonej z każdym AMPLS, aby dotrzeć do tego obszaru roboczego. Utworzenie połączenia w tym miejscu ma taki sam skutek jak ustawienie go w zakresie, tak jak w przypadku [łączenia Azure Monitor zasobów](#connect-azure-monitor-resources). Aby dodać nowe połączenie, wybierz opcję **Dodaj** i wybierz prywatny zakres linków Azure monitor. Wybierz pozycję **Zastosuj** , aby nawiązać połączenie. Należy pamiętać, że obszar roboczy może połączyć się z 5 AMPLS obiektów, jak wspomniano w [ograniczeniach i ograniczeniach](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Zarządzanie dostępem spoza zakresów linków prywatnych
Ustawienia w dolnej części tej strony kontrolują dostęp z sieci publicznych, czyli oznacza sieci, które nie są połączone przez wymienione powyżej zakresy. Ustawienie **Zezwalaj na dostęp do sieci publicznej na potrzeby** pozyskiwania **nie** ma bloków pozyskiwania dzienników z maszyn poza połączonymi zakresami. Ustawienie **Zezwalaj na dostęp do sieci publicznej dla zapytań w przypadku** **braku** bloków zapytań pochodzących z komputerów poza zakresem. Obejmuje to zapytania uruchamiane za pośrednictwem skoroszytów, pulpitów nawigacyjnych, środowiska klienta opartego na interfejsie API, szczegółowych informacji w Azure Portal i innych. Środowiska działające poza Azure Portal i że zapytanie Log Analytics dane muszą być uruchomione w prywatnej sieci wirtualnej.

### <a name="exceptions"></a>Wyjątki
Ograniczanie dostępu, jak wyjaśniono powyżej, nie ma zastosowania do Azure Resource Manager i dlatego ma następujące ograniczenia:
* Dostęp do danych — podczas blokowania/zezwalania na zapytania z sieci publicznych stosuje się do większości Log Analyticsych środowisk, ale niektóre z nich wykonują zapytania dotyczące danych za pośrednictwem Azure Resource Manager i w związku z tym nie będą mogły wysyłać zapytań o dane, chyba że prywatne ustawienia linku są stosowane również do Menedżer zasobów (funkcja jest dostępna wkrótce). Przykłady to Azure Monitor rozwiązania, skoroszyty i szczegółowe informacje oraz łącznik LogicApp.
* Zarządzanie obszarem roboczym — ustawienia obszaru roboczego i zmiany konfiguracji (w tym Włączanie lub wyłączanie tych ustawień dostępu) są zarządzane przez Azure Resource Manager. Ogranicz dostęp do zarządzania obszarami roboczymi przy użyciu odpowiednich ról, uprawnień, kontroli sieci i inspekcji. Aby uzyskać więcej informacji, zobacz [Azure monitor role, uprawnienia i zabezpieczenia](../roles-permissions-security.md).

> [!NOTE]
> Dzienniki i metryki przekazane do obszaru roboczego za pośrednictwem [ustawień diagnostycznych](../essentials/diagnostic-settings.md) korzystają z bezpiecznego prywatnego kanału firmy Microsoft i nie są kontrolowane przez te ustawienia.

### <a name="log-analytics-solution-packs-download"></a>Pobieranie pakietów rozwiązań Log Analytics

Aby zezwolić agentowi Log Analytics na pobieranie pakietów rozwiązań, Dodaj odpowiednie nazwy FQDN do listy dozwolonych zapór. 


| Środowisko chmury | Zasób agenta | Porty | Kierunek |
|:--|:--|:--|:--|
|Azure — publiczna     | scadvisorcontent.blob.core.windows.net         | 443 | Wychodzący
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Wychodzący
|Azure w Chinach — 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Wychodzący

## <a name="configure-application-insights"></a>Konfigurowanie usługi Application Insights

Przejdź do witryny Azure Portal. W Azure Monitor Application Insights zasobów składnika jest **izolacja sieci** elementu menu po lewej stronie. W tym menu można kontrolować dwa różne stany.

![Izolacja sieci AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Najpierw można podłączyć ten zasób Application Insights do Azure Monitor prywatnych zakresów łączy, do których masz dostęp. Wybierz pozycję **Dodaj** i wybierz **prywatny zakres linków Azure monitor**. Wybierz pozycję Zastosuj, aby nawiązać połączenie. Wszystkie połączone zakresy są wyświetlane na tym ekranie. Nawiązanie tego połączenia zezwala na ruch sieciowy podłączonych sieci wirtualnych do tego składnika i ma ten sam efekt, co połączenie z zakresem, tak jak w przypadku [łączenia Azure Monitor zasobów](#connect-azure-monitor-resources). 

Następnie można kontrolować sposób, w jaki można uzyskać dostęp do tego zasobu poza zakresem prywatnych linków (AMPLS) wymienionym wcześniej. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej na potrzeby** pozyskiwania **nie**, wówczas maszyny lub zestawy SDK poza połączonymi zakresami nie mogą przekazywać danych do tego składnika. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej dla zapytań** na wartość **nie**, wówczas maszyny spoza zakresów nie mogą uzyskać dostępu do danych w tym zasobie Application Insights. Te dane obejmują dostęp do dzienników APM, metryk i strumienia metryk na żywo, a także wbudowanych środowisk, takich jak skoroszyty, pulpity nawigacyjne, zapytania dotyczące środowiska klienta opartego na interfejsie API, szczegółowe informacje w Azure Portal i inne. 

> [!NOTE]
> Środowiska użycia poza portalem również muszą być uruchamiane w połączeniu z prywatną siecią wirtualną, która obejmuje monitorowane obciążenia.

Do prywatnego linku należy dodać zasoby obsługujące monitorowane obciążenia. Na przykład zobacz [Używanie prywatnych punktów końcowych dla aplikacji internetowej platformy Azure](../../app-service/networking/private-endpoint.md).

Ograniczanie dostępu w ten sposób dotyczy tylko danych w zasobie Application Insights. Jednak zmiany w konfiguracji, w tym włączenie lub wyłączenie tych ustawień dostępu, są zarządzane przez Azure Resource Manager. Dlatego należy ograniczyć dostęp do Menedżer zasobów przy użyciu odpowiednich ról, uprawnień, kontroli sieci i inspekcji. Aby uzyskać więcej informacji, zobacz [Azure monitor role, uprawnienia i zabezpieczenia](../roles-permissions-security.md).

> [!NOTE]
> Aby w pełni zabezpieczyć Application Insights oparte na obszarze roboczym, musisz zablokować zarówno dostęp do zasobu Application Insights, jak i bazowego obszaru roboczego Log Analytics.
>
> Diagnostyka na poziomie kodu (Profiler/debuger) wymaga [podania własnego konta magazynu](../app/profiler-bring-your-own-storage.md) do obsługi linku prywatnego.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Obsługa wszystkich rodzajów prywatnych linków lub nie
Zgodnie z opisem w temacie [Planowanie konfiguracji linku prywatnego](#planning-your-private-link-setup), skonfigurowanie prywatnego linku nawet dla pojedynczego zasobu ma wpływ na wszystkie zasoby Azure monitor w tych sieciach oraz w innych sieciach, które współużytkują ten sam serwer DNS. Takie zachowanie może sprawiać, że proces dołączania jest wyzwaniem. Należy wziąć pod uwagę następujące opcje:

* Wszystko to najprostsze i najbezpieczniejsze podejście polega na dodaniu wszystkich składników Application Insights do AMPLS. W przypadku składników, do których nadal uzyskuje się dostęp z innych sieci, pozostaw flagi "Zezwalaj na publiczny dostęp do Internetu na potrzeby pozyskiwania/wykonywania zapytań" ustawioną na wartość tak (domyślnie).
* Izoluj sieci — Jeśli masz (lub możesz ją wyrównać) przy użyciu sieci wirtualnych szprychy, postępuj zgodnie ze wskazówkami w [topologii sieci Hub i szprych na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Następnie skonfiguruj osobne ustawienia linku prywatnego w odpowiedniej sieci wirtualnych szprych. Upewnij się, że strefy DNS są również rozdzielone, ponieważ współużytkowanie stref DNS z innymi sieciami szprych spowoduje [przesłonięcie DNS](#the-issue-of-dns-overrides).
* Używanie niestandardowych stref DNS dla określonych aplikacji — to rozwiązanie umożliwia dostęp do wybranych składników Application Insights za pośrednictwem prywatnego linku, zachowując jednocześnie cały ruch w ramach tras publicznych.
    - Skonfiguruj [niestandardową prywatną strefę DNS](../../private-link/private-endpoint-dns.md)i nadaj jej unikatową nazwę, taką jak Internal.Monitor.Azure.com
    - Utwórz AMPLS i prywatny punkt końcowy, a następnie wybierz opcję " **nie** Integruj" z prywatną usługą DNS
    - Przejdź do pozycji prywatny punkt końcowy — > Konfiguracja DNS i przejrzyj sugerowane Mapowanie nazw FQDN.
    - Wybierz opcję dodania konfiguracji i wybrania właśnie utworzonej strefy internal.monitor.azure.com
    - Dodaj rekordy dla powyższego ![ zrzutu ekranu skonfigurowanej strefy DNS](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Przejdź do składnika Application Insights i skopiuj jego [Parametry połączenia](../app/sdk-connection-string.md).
    - Aplikacje lub skrypty, które chcą wywołać ten składnik za pośrednictwem prywatnego linku, powinny używać parametrów połączenia z EndpointSuffix = Internal. Monitor. Azure. com
* Mapuj punkty końcowe za pośrednictwem plików Hosts zamiast systemu DNS — Aby uzyskać dostęp do prywatnego linku tylko z określonej maszyny/maszyny wirtualnej w sieci:
    - Skonfiguruj AMPLS i prywatny punkt końcowy, a następnie wybierz pozycję **nie** należy przeprowadzać autointegracji z prywatnym systemem DNS 
    - Skonfiguruj powyższe rekordy A na komputerze, na którym jest uruchamiana aplikacja w pliku hosts


## <a name="use-apis-and-command-line"></a>Korzystanie z interfejsów API i wiersza polecenia

Można zautomatyzować proces opisany wcześniej przy użyciu Azure Resource Manager szablonów, REST i interfejsów wiersza polecenia.

Aby utworzyć prywatne zakresy łączy i zarządzać nimi, użyj [interfejsu API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) lub [wiersza polecenia platformy Azure (AZ monitor Private-Scope-zakres)](/cli/azure/monitor/private-link-scope).

Aby zarządzać dostępem do sieci, użyj flag `[--ingestion-access {Disabled, Enabled}]` i `[--query-access {Disabled, Enabled}]` na [log Analytics obszarach roboczych](/cli/azure/monitor/log-analytics/workspace) lub [składników Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Zbieranie niestandardowych dzienników i dzienników usług IIS przez łącze prywatne

Konta magazynu są używane w procesie pozyskiwania dzienników niestandardowych. Domyślnie są używane konta magazynu zarządzane przez usługę. Jednak w przypadku linków prywatnych można korzystać z własnych kont magazynu i kojarzyć je z obszarami roboczymi Log Analytics. Zobacz więcej szczegółowych informacji na temat konfigurowania takich kont przy użyciu [wiersza polecenia](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Aby uzyskać więcej informacji na temat przełączania własnych kont magazynu, zobacz [konta magazynu należące do klienta na potrzeby](private-storage.md) pozyskiwania dziennika

## <a name="restrictions-and-limitations"></a>Ograniczenia

### <a name="ampls"></a>AMPLS
Obiekt AMPLS ma liczbę limitów, które należy wziąć pod uwagę podczas planowania konfiguracji linku prywatnego:

* Sieć wirtualna może łączyć się tylko z 1 AMPLS obiektem. Oznacza to, że obiekt AMPLS musi zapewnić dostęp do wszystkich zasobów Azure Monitor, do których sieć wirtualna powinna mieć dostęp.
* Zasób Azure Monitor (składnik obszaru roboczego lub Application Insights) może łączyć się z 5 AMPLSs.
* Obiekt AMPLS może łączyć się z zasobami 50 Azure Monitor.
* Obiekt AMPLS może łączyć się z 10 prywatnymi punktami końcowymi.

Zobacz [limity](#consider-limits) dotyczące dokładniejszego przeglądu tych limitów.

### <a name="agents"></a>Agenci

Najnowsze wersje agentów systemów Windows i Linux muszą być używane do obsługi bezpiecznego pozyskiwania Log Analytics obszarów roboczych. Starsze wersje nie mogą przekazywać danych monitorowania za pośrednictwem sieci prywatnej.

**Agent usługi Log Analytics dla systemu Windows**

Użyj agenta Log Analytics w wersji 10.20.18038.0 lub nowszej.

**Agent usługi Log Analytics dla systemu Linux**

Użyj agenta w wersji 1.12.25 lub nowszej. Jeśli nie możesz, uruchom następujące polecenia na maszynie wirtualnej.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Aby korzystać z środowisk Azure Monitor Portal, takich jak Application Insights i Log Analytics, należy zezwolić na dostęp rozszerzeń Azure Portal i Azure Monitor w sieciach prywatnych. Dodaj [znaczniki usługi](../../firewall/service-tags.md) **usługi azureactivedirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** i **AzureFrontDoor. frontonu** do sieciowej grupy zabezpieczeń.

### <a name="querying-data"></a>Wykonywanie zapytań na danych
[ `externaldata` Operator](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) nie jest obsługiwany przez link prywatny, ponieważ odczytuje dane z kont magazynu, ale nie gwarantuje, że magazyn jest dostępny prywatnie.

### <a name="programmatic-access"></a>Dostęp programowy

Aby użyć interfejsu API REST, interfejsu [wiersza polecenia](/cli/azure/monitor) lub programu PowerShell z Azure monitor w sieciach prywatnych, należy dodać do zapory [Tagi usług](../../virtual-network/service-tags-overview.md)  **usługi azureactivedirectory** i **AzureResourceManager** .

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights pobierania zestawu SDK z usługi Content Delivery Network

Powiąż kod JavaScript w skrypcie, aby przeglądarka nie próbowała pobrać kodu z sieci CDN. Przykład jest dostępny w witrynie [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Ustawienia usługi DNS przeglądarki

W przypadku łączenia się z zasobami Azure Monitor za pośrednictwem prywatnego linku ruch do tych zasobów musi przechodzić przez prywatny punkt końcowy skonfigurowany w sieci. Aby włączyć prywatny punkt końcowy, zaktualizuj ustawienia DNS zgodnie z opisem w temacie [Connect to Private Endpoint](#connect-to-a-private-endpoint). Niektóre przeglądarki używają własnych ustawień DNS zamiast ustawionych przez użytkownika. Przeglądarka może próbować nawiązać połączenie z Azure Monitor publicznymi punktami końcowymi i całkowicie ominąć prywatny link. Sprawdź, czy ustawienia przeglądarki nie przesłaniają ani nie buforują starych ustawień DNS. 

## <a name="next-steps"></a>Następne kroki

- Informacje o [magazynie prywatnym](private-storage.md)