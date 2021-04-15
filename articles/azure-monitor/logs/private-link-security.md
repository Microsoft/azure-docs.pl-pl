---
title: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
description: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 43707a99792ae3c4d817f47d770629287b8a774b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374339"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor

[Azure Private Link](../../private-link/private-link-overview.md) umożliwia bezpieczne łączenie usług PaaS platformy Azure z siecią wirtualną przy użyciu prywatnych punktów końcowych. W przypadku wielu usług wystarczy skonfigurować punkt końcowy dla każdego zasobu. Jednak Azure Monitor to połączenie różnych wzajemnie połączonych usług, które współpracują ze sobą w celu monitorowania obciążeń. W rezultacie sbudowaliśmy zasób o nazwie zakres Azure Monitor Private Link (AMPLS). Funkcja AMPLS umożliwia definiowanie granic sieci monitorowania i nawiązywanie połączenia z siecią wirtualną. W tym artykule opisano, kiedy używać i jak skonfigurować Azure Monitor Private Link zakres.

## <a name="advantages"></a>Zalety

Za Private Link można:

- Nawiązywanie prywatnego połączenia z Azure Monitor bez otwierania dostępu do sieci publicznej
- Upewnij się, że dostęp do danych monitorowania jest uzyskiwany tylko za pośrednictwem autoryzowanych sieci prywatnych
- Zapobieganie eksfiltracji danych z sieci prywatnych przez zdefiniowanie określonych Azure Monitor, które łączą się za pośrednictwem prywatnego punktu końcowego
- Bezpieczne łączenie prywatnej sieci lokalnej z usługą Azure Monitor expressRoute i Private Link
- Cały ruch w sieci szkieletowej Microsoft Azure sieci szkieletowej

Aby uzyskać więcej informacji,  [zobacz Najważniejsze zalety Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to działa

Azure Monitor Private Link Scope (AMPLS) łączy prywatne punkty końcowe (i sieci wirtualne, w których się znajdują) z co najmniej jednym zasobem usługi Azure Monitor — obszarami roboczymi usługi Log Analytics i Application Insights składników.

![Diagram podstawowej topologii zasobów](./media/private-link-security/private-link-basic-topology.png)

* Prywatny punkt końcowy w sieci wirtualnej umożliwia mu dostęp do punktów końcowych usługi Azure Monitor za pośrednictwem prywatnych ip z puli sieci, zamiast używania ich do publicznych ip tych punktów końcowych. Dzięki temu możesz nadal korzystać z zasobów Azure Monitor bez otwierania sieci wirtualnej w celu nieograniczonego ruchu wychodzącego. 
* Ruch z prywatnego punktu końcowego do zasobów Azure Monitor będzie odbywać się Microsoft Azure sieci szkieletowej, a nie do sieci publicznych. 
* Każdy z obszarów roboczych lub składników można skonfigurować tak, aby zezwalać na pozyskiwanie i zapytania z sieci publicznych lub odmawiać ich. Zapewnia to ochronę na poziomie zasobów, dzięki czemu można kontrolować ruch do określonych zasobów.

> [!NOTE]
> Pojedynczy zasób Azure Monitor może należeć do wielu trybów AMPLS, ale nie można połączyć jednej sieci wirtualnej z więcej niż jedną siecią AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planowanie konfiguracji Private Link konfiguracji

Przed skonfigurowaniem konfiguracji Azure Monitor Private Link należy wziąć pod uwagę topologię sieci, a w szczególności topologię routingu DNS. 

### <a name="the-issue-of-dns-overrides"></a>Problem z przesłonięciami DNS
Niektóre Azure Monitor używają globalnych punktów końcowych, co oznacza, że obsługują żądania przeznaczone dla dowolnego obszaru roboczego/składnika. Kilka przykładów to punkt końcowy Application Insights pozyskiwania danych oraz punkt końcowy zapytania usługi Application Insights i usługi Log Analytics.

Po skonfigurowaniu połączenia Private Link dns jest aktualizowany w celu mapowania punktów końcowych Azure Monitor na prywatne adresy IP z zakresu adresów IP sieci wirtualnej. Ta zmiana zastępuje każde poprzednie mapowanie tych punktów końcowych, co może mieć znaczący wpływ, które zostały przejmowane poniżej. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link dotyczy wszystkich Azure Monitor zasobów — jest to wszystko lub nic
Ponieważ niektóre Azure Monitor końcowe są globalne, nie można utworzyć połączenia Private Link dla określonego składnika lub obszaru roboczego. Zamiast tego po skonfigurowaniu konta usługi Private Link do pojedynczego składnika usługi Application Insights lub obszaru roboczego usługi Log Analytics rekordy DNS są aktualizowane dla wszystkich Application Insights roboczych.  Każda próba pozyskania lub odpytania składnika będzie przechodzić przez Private Link i prawdopodobnie nie powiedzie się. W przypadku usługi Log Analytics punkty końcowe pozyskiwania i konfiguracji są specyficzne dla obszaru roboczego, co oznacza, że konfiguracja łącza prywatnego będzie dotyczyć tylko określonych obszarów roboczych. Pozyskiwanie i konfigurowanie innych obszarów roboczych zostanie skierowane do domyślnych publicznych punktów końcowych usługi Log Analytics.

![Diagram przesłonięcia DNS w jednej sieci wirtualnej](./media/private-link-security/dns-overrides-single-vnet.png)

Dotyczy to nie tylko określonej sieci wirtualnej, ale również wszystkich sieci wirtualnych, które współużytkują ten sam serwer DNS (zobacz Problem przesłonięcia [DNS).](#the-issue-of-dns-overrides) Dlatego na przykład żądanie pozyskania dzienników do dowolnego Application Insights zawsze będzie wysyłane za pośrednictwem Private Link trasy. Składniki, które nie są połączone z usługą AMPLS, nie Private Link weryfikacji i nie zostaną przejdą.

> [!NOTE]
> Na koniec: po konfiguracji połączenie Private Link z pojedynczym zasobem ma zastosowanie do Azure Monitor zasobów w sieci. W Application Insights zasobów jest to "Wszystko lub Nic". W praktyce oznacza to, że należy Application Insights wszystkich zasobów sieciowych do systemu AMPLS lub do żadnego z nich.
> 
> Aby obsłużyć ryzyko eksfiltracji danych, zaleca się dodanie wszystkich zasobów usług Application Insights i Log Analytics do usługi AMPLS oraz jak najodpowiedniej blokowanie ruchu wychodzącego sieci.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link dotyczy całej sieci
Niektóre sieci składają się z wielu sieci wirtualnych. Jeśli sieci wirtualne używają tego samego serwera DNS, zastąpią one mapowania DNS siebie nawzajem i prawdopodobnie przerwią komunikację między sobą z usługą Azure Monitor (zobacz Problem z przesłonięciami [DNS).](#the-issue-of-dns-overrides) Ostatecznie tylko ostatnia sieć wirtualna będzie mogła komunikować się z usługą Azure Monitor, ponieważ system DNS będzie mapować punkty końcowe usługi Azure Monitor na prywatne ip z tego zakresu sieci wirtualnych (które mogą być nieoczywalne z innych sieci wirtualnych).

![Diagram przesłonięcia DNS w wielu sieciach wirtualnych](./media/private-link-security/dns-overrides-multiple-vnets.png)

Na powyższym diagramie sieć wirtualna 10.0.1.x najpierw łączy się z ampls1 i mapuje globalne punkty końcowe Azure Monitor na adresy IP z jej zakresu. Później sieć wirtualna 10.0.2.x łączy się z systemem AMPLS2 i zastępuje mapowanie DNS tych samych globalnych punktów końcowych przy użyciu adresów IP z zakresu.  Ponieważ te sieci wirtualne nie są równorzędne, pierwsza sieć wirtualna nie może teraz osiągnąć tych punktów końcowych.

> [!NOTE]
> Na koniec: konfiguracja usługi AMPLS ma wpływ na wszystkie sieci, które współużytkują te same strefy DNS. Aby uniknąć zastępowania mapowań punktów końcowych DNS, najlepiej skonfigurować pojedynczy prywatny punkt końcowy w sieci równorzędnej (takiej jak sieć wirtualna koncentratora) lub oddzielić sieci na poziomie dns (na przykład przy użyciu usług przesyłania dalej DNS lub oddzielnych serwerów DNS).

### <a name="hub-spoke-networks"></a>Sieci piasty i szprych
Topologie piasty i szprych mogą uniknąć problemu przesłonięcia DNS, ustawiając sieć Private Link w sieci wirtualnej piasty (głównej), zamiast samodzielnie Private Link dla każdej sieci wirtualnej. Ta konfiguracja ma sens zwłaszcza wtedy, Azure Monitor zasobów używanych przez sieci wirtualne szprychy są współdzielone. 

![Piasta i szprychy — pojedynczy pe](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Celowo możesz tworzyć osobne łącza prywatne dla sieci wirtualnych szprych, na przykład aby umożliwić każdej sieci wirtualnej dostęp do ograniczonego zestawu zasobów monitorowania. W takich przypadkach można utworzyć dedykowany prywatny punkt końcowy i ampls dla każdej sieci wirtualnej, ale należy również sprawdzić, czy nie współużytkują one tych samych stref DNS, aby uniknąć przesłonięcia DNS.


### <a name="consider-limits"></a>Rozważanie limitów

Jak [pokazano w sekcji Ograniczenia i ograniczenia,](#restrictions-and-limitations)obiekt AMPLS ma wiele limitów, jak pokazano w poniższej topologii:
* Każda sieć wirtualna łączy się tylko **z 1 obiektem** AMPLS.
* AmpLS B jest połączony z prywatnymi punktami końcowymi dwóch sieci wirtualnych (VNet2 i VNet3), używając 2 z 10 możliwych połączeń prywatnego punktu końcowego.
* AmpLS A łączy się z dwoma obszarami roboczymi i jednym składnikiem usługi Application Insights przy użyciu 3 z 50 możliwych połączeń Azure Monitor zasobów.
* Obszar roboczy Workspace2 łączy się z usługami AMPLS A i AMPLS B przy użyciu 2 z 5 możliwych połączeń AMPLS.

![Diagram limitów ampLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Przykładowe połączenie

Rozpocznij od utworzenia zasobu Azure Monitor Private Link Zakres.

1. Przejdź do **tematu Tworzenie zasobu** w Azure Portal i wyszukaj Azure Monitor Private Link **Zakres.**

   ![Znajdowanie Azure Monitor Private Link zakresów](./media/private-link-security/ampls-find-1c.png)

2. Wybierz pozycję **Utwórz**.
3. Wybierz subskrypcję i grupę zasobów.
4. Nadaj nazwę ujmowi nazwę w pliku AMPLS. Najlepiej użyć znaczącej i jasnej nazwy, takiej jak "AppServerProdTelem".
5. Wybierz pozycję **Przejrzyj i utwórz**. 

   ![Tworzenie Azure Monitor Private Link zakresów](./media/private-link-security/ampls-create-1d.png)

6. Pozwól, aby walidacja przebiegła, a następnie wybierz **pozycję Utwórz**.

### <a name="connect-azure-monitor-resources"></a>Łączenie Azure Monitor zasobów

Połącz Azure Monitor (obszary robocze usługi Log Analytics Application Insights składniki) z usługą AMPLS.

1. W swoim Azure Monitor Private Link wybierz pozycję **Azure Monitor Zasoby** w menu po lewej stronie. Wybierz przycisk **Add** (Dodaj).
2. Dodaj obszar roboczy lub składnik. Wybranie **przycisku** Dodaj powoduje wyświetlone okno dialogowe, w którym można wybrać Azure Monitor zasobów. Możesz przeglądać subskrypcje i grupy zasobów lub wpisać ich nazwy, aby je odfiltrować. Wybierz obszar roboczy lub składnik, a następnie wybierz **pozycję Zastosuj,** aby dodać je do zakresu.

    ![Zrzut ekranu przedstawiający wybieranie środowiska użytkownika zakresu](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Usunięcie Azure Monitor zasobów wymaga najpierw odłączenia ich od wszystkich obiektów AMPLS, z których są połączone. Nie można usunąć zasobów połączonych z ampls.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Teraz, gdy masz zasoby połączone z usługą AMPLS, utwórz prywatny punkt końcowy, aby połączyć naszą sieć. To zadanie można wykonać w centrum Azure Portal Private Link [lub](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)wewnątrz Azure Monitor Private Link zakres, jak w tym przykładzie.

1. W zasobie zakresu wybierz pozycję **Połączenia prywatnego punktu końcowego** w menu zasobów po lewej stronie. Wybierz **pozycję Prywatny punkt końcowy,** aby rozpocząć proces tworzenia punktu końcowego. Możesz również zatwierdzić połączenia, które zostały uruchomione w centrum Private Link, wybierając je i wybierając pozycję **Zatwierdź.**

    ![Zrzut ekranu środowiska użytkownika prywatnych połączeń punktu końcowego](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Wybierz subskrypcję, grupę zasobów i nazwę punktu końcowego oraz region, w których powinien się on żyć. Region musi być tym samym regionem, z który jest nawiązywana sieć wirtualna.

3. Wybierz **pozycję Dalej: Zasób.** 

4. Na ekranie Zasób

   a. Wybierz **subskrypcję,** która zawiera Azure Monitor zakres prywatny. 

   b. Jako **typ zasobu wybierz** **microsoft.insights/privateLinkScopes.** 

   c. Z **listy rozwijanej** zasobów wybierz utworzony wcześniej Private Link zakres zasobów. 

   d. Wybierz **pozycję Dalej: Konfiguracja >**.
      ![Zrzut ekranu przedstawiający wybieranie opcji Utwórz prywatny punkt końcowy](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. W okienku konfiguracji

   a.    Wybierz sieć **wirtualną** i **podsieć,** które chcesz połączyć z zasobami Azure Monitor wirtualnej. 
 
   b.    Wybierz **pozycję Tak** dla opcji **Integruj z prywatną strefą DNS** i pozwól jej automatycznie tworzyć nową strefę Prywatna strefa DNS DNS. Rzeczywiste strefy DNS mogą różnić się od pokazanych na poniższym zrzucie ekranu. 
   > [!NOTE]
   > Jeśli wybierzesz **opcję Nie** i wolisz ręcznie zarządzać rekordami DNS, najpierw wykonaj konfigurację usługi Private Link — w tym tego prywatnego punktu końcowego i konfiguracji ampls. Następnie skonfiguruj usługę DNS zgodnie z instrukcjami zawartymi w artykule [Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md). Pamiętaj, aby nie tworzyć pustych rekordów podczas przygotowywania się do konfiguracji łącza prywatnego. Tworzone rekordy DNS mogą przesłaniać istniejące ustawienia i mieć wpływ na łączność z usługą Azure Monitor.
 
   c.    Wybierz pozycję **Przejrzyj i utwórz**.
 
   d.    Niech walidacja przebiegnie. 
 
   e.    Wybierz przycisk **Utwórz**. 

    ![Zrzut ekranu przedstawiający wybieranie szczegółów prywatnego punktu końcowego.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Utworzono nowy prywatny punkt końcowy, który jest połączony z tym ampls.

## <a name="review-and-validate-your-private-link-setup"></a>Przeglądanie i weryfikowanie konfiguracji Private Link konfiguracji

### <a name="reviewing-your-endpoints-dns-settings"></a>Przeglądanie ustawień DNS punktu końcowego
Utworzony prywatny punkt końcowy powinien teraz mieć skonfigurowane cztery strefy DNS:

[![Zrzut ekranu przedstawiający strefy DNS prywatnego punktu końcowego.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> Każda z tych stref mapuje określone Azure Monitor końcowe na prywatne ip z puli ip sieci wirtualnej. Adresy IP pokazane na poniższych obrazach są tylko przykładami. Konfiguracja powinna zamiast tego pokazywać prywatne ip z własnej sieci.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Ta strefa obejmuje globalne punkty końcowe używane przez Azure Monitor, co oznacza, że te punkty końcowe obsługują żądania z uwzględnieniem wszystkich zasobów, a nie konkretnego. Ta strefa powinna mieć punkty końcowe zamapowane na:
* `in.ai` — (Application Insights pozyskiwania danych, zostanie wyświetlony wpis globalny i regionalny
* `api` — Application Insights punktu końcowego interfejsu API usługi Log Analytics
* `live` — Application Insights punktu końcowego metryk na żywo
* `profiler` — Application Insights punktu końcowego profilera
* `snapshot`— Application Insights migawki Zrzut [ ![ ekranu Prywatna strefa DNS monitor-azure-com strefy.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Ta strefa obejmuje mapowanie specyficzne dla obszaru roboczego na punkty końcowe usługi OMS. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z usługą AMPLS połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu Prywatna strefa DNS strefie oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Ta strefa obejmuje mapowanie specyficzne dla obszaru roboczego na punkty końcowe ODS — punkt końcowy pozyskiwania usługi Log Analytics. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z usługą AMPLS połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu Prywatna strefa DNS strefy ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Ta strefa obejmuje mapowanie specyficzne dla obszaru roboczego na punkty końcowe automatyzacji usługi agenta. Powinien zostać wyświetlony wpis dla każdego obszaru roboczego połączonego z usługą AMPLS połączoną z tym prywatnym punktem końcowym.
[![Zrzut ekranu Prywatna strefa DNS svc-azure-automation-net agenta strefy czasowej.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Sprawdzania poprawności komunikacji za pośrednictwem Private Link
* Aby zweryfikować, czy żądania są teraz wysyłane za pośrednictwem prywatnego punktu końcowego i do prywatnych punktów końcowych mapowanych na adresy IP, możesz przejrzeć je za pomocą śledzenia sieci w narzędziach, a nawet w przeglądarce. Na przykład podczas próby wysłania zapytania do obszaru roboczego lub aplikacji upewnij się, że żądanie jest wysyłane do prywatnego adresu IP zamapego na punkt końcowy interfejsu API. W tym przykładzie jest to *adres 172.17.0.9.*

    Uwaga: Niektóre przeglądarki mogą używać innych ustawień DNS (zobacz [Ustawienia DNS przeglądarki).](#browser-dns-settings) Upewnij się, że ustawienia DNS są stosowane.

* Aby upewnić się, że twój obszar roboczy lub składnik nie odbiera żądań z sieci publicznych (które nie są połączone za pośrednictwem usługi AMPLS), ustaw flagi publicznego pozyskiwania i zapytań zasobu na wartość *Nie,* jak wyjaśniono w części Zarządzanie dostępem spoza zakresów linków [prywatnych.](#manage-access-from-outside-of-private-links-scopes)

* Z klienta w sieci chronionej użyj nazwy dla dowolnego punktu końcowego `nslookup` wymienionego w strefach DNS. Powinien on zostać rozpoznany przez serwer DNS na zamapowane prywatne ip zamiast publicznych adresach IP używanych domyślnie.


## <a name="configure-log-analytics"></a>Konfigurowanie usługi Log Analytics

Przejdź do witryny Azure Portal. W menu zasobów obszaru roboczego usługi Log Analytics po lewej stronie znajduje się element o nazwie **Izolacja** sieci. Z tego menu można kontrolować dwa różne stany.

![Izolacja sieci LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Zakresy Azure Monitor Private Link połączonych
Na tym ekranie są wyświetlane wszystkie zakresy połączone z obszarem roboczym. Połączenie z zakresami (AMPLS) umożliwia ruch sieciowy z sieci wirtualnej podłączonej do poszczególnych ampls w celu dotarcia do tego obszaru roboczego. Utworzenie połączenia za pośrednictwem tego tematu ma taki sam efekt jak skonfigurowanie go w zakresie, jak w przypadku nawiązywania połączenia z [Azure Monitor zasobów.](#connect-azure-monitor-resources) Aby dodać nowe połączenie, wybierz pozycję **Dodaj** i wybierz Azure Monitor Private Link zakres. Wybierz **pozycję Zastosuj,** aby ją połączyć. Należy pamiętać, że obszar roboczy może łączyć się z 5 obiektami AMPLS, jak wspomniano w tece [Ograniczenia i ograniczenia.](#restrictions-and-limitations) 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Zarządzanie dostępem spoza zakresów linków prywatnych
Ustawienia w dolnej części tej strony kontrolują dostęp z sieci publicznych, co oznacza, że sieci nie są połączone za pośrednictwem zakresów wymienionych powyżej. Ustawienie **Zezwalaj na dostęp do sieci publicznej na potrzeby pozyskiwania danych** nie blokuje pozyskiwania dzienników z maszyn spoza połączonych zakresów.  Ustawienie **Zezwalaj na dostęp do sieci publicznej dla zapytań ma** ustawienie **Nie** blokuje zapytań pochodzących z maszyn spoza zakresów. Obejmuje to zapytania uruchamiane za pośrednictwem skoroszytów, pulpitów nawigacyjnych, interfejsów API, szczegółowych informacji w Azure Portal i innych. Środowisko pracy poza Azure Portal, które odpytują dane usługi Log Analytics, również muszą być uruchomione w ramach połączonej prywatnie sieci wirtualnej.

### <a name="exceptions"></a>Wyjątki
Ograniczanie dostępu, jak wyjaśniono powyżej, nie ma zastosowania do Azure Resource Manager i dlatego ma następujące ograniczenia:
* Dostęp do danych — chociaż blokowanie/zezwalanie na zapytania z sieci publicznych ma zastosowanie do większości funkcji usługi Log Analytics, niektóre doświadczenia wysyłają zapytania o dane za pośrednictwem usługi Azure Resource Manager i w związku z tym nie będą mogły wysyłać zapytań o dane, chyba że ustawienia usługi Private Link zostaną zastosowane również do usługi Resource Manager (funkcja zostanie wkrótce zastosowana). Przykłady Azure Monitor rozwiązań, skoroszytów i szczegółowych informacji oraz łącznika usługi LogicApp.
* Zarządzanie obszarem roboczym — zmiany ustawień i konfiguracji obszaru roboczego (w tym włączanie i wyłączanie tych ustawień dostępu) są zarządzane przez Azure Resource Manager. Ogranicz dostęp do zarządzania obszarem roboczym przy użyciu odpowiednich ról, uprawnień, kontrolek sieci i inspekcji. Aby uzyskać więcej informacji, [zobacz Azure Monitor role, uprawnienia i zabezpieczenia.](../roles-permissions-security.md)

> [!NOTE]
> Dzienniki i metryki przekazane [](../essentials/diagnostic-settings.md) do obszaru roboczego za pośrednictwem ustawień diagnostycznych są przesyłane za pośrednictwem bezpiecznego prywatnego kanału firmy Microsoft i nie są kontrolowane przez te ustawienia.

### <a name="log-analytics-solution-packs-download"></a>Pobieranie pakietów rozwiązań usługi Log Analytics

Aby umożliwić agentowi usługi Log Analytics pobieranie pakietów rozwiązań, dodaj odpowiednie w pełni kwalifikowane nazwy domen do listy zezwalania zapory. 


| Środowisko chmury | Zasób agenta | Porty | Kierunek |
|:--|:--|:--|:--|
|Azure — publiczna     | scadvisorcontent.blob.core.windows.net         | 443 | Wychodzący
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Wychodzący
|Azure w Chinach — 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Wychodzący


>[!NOTE]
> Od 19 kwietnia 2021 r. powyższe ustawienie nie będzie wymagane i będzie można uzyskać dostęp do konta magazynu pakietów rozwiązań za pośrednictwem linku prywatnego. Nowa funkcja wymaga ponownego utworzenia usługi AMPLS (19 kwietnia 2021 r. lub nowszej) i połączonego z nim prywatnego punktu końcowego. Nie będzie ona mieć zastosowania do istniejących systemów AMPLS i prywatnych punktów końcowych.

## <a name="configure-application-insights"></a>Konfigurowanie usługi Application Insights

Przejdź do witryny Azure Portal. W zasobie Azure Monitor Application Insights jest elementem menu **Izolacja sieci** po lewej stronie. Z tego menu można kontrolować dwa różne stany.

![Izolacja sieci AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Najpierw możesz połączyć ten zasób Application Insights z Azure Monitor Private Link zakresami, do których masz dostęp. Wybierz **pozycję** Dodaj i wybierz **Azure Monitor Private Link Zakres.** Wybierz pozycję Zastosuj, aby ją połączyć. Na tym ekranie są wyświetlane wszystkie połączone zakresy. Wykonanie tego połączenia umożliwia ruchowi sieciowemu w połączonych sieciach wirtualnych dostęp do tego składnika i ma taki sam efekt jak połączenie go z zakresu, co w przypadku nawiązywania połączenia z zasobami Azure Monitor [wirtualnej.](#connect-azure-monitor-resources) 

Następnie możesz kontrolować sposób, w jaki można uzyskać do tego zasobu spoza wymienionych wcześniej zakresów linków prywatnych (AMPLS). Jeśli ustawienie **Zezwalaj na dostęp do** sieci publicznej na potrzeby pozyskiwania ma wartość **Nie,** maszyny lub zestawy SDK spoza połączonych zakresów nie będą w stanie przekazać danych do tego składnika. Jeśli ustawisz ustawienie **Zezwalaj na dostęp** do sieci publicznej dla zapytań na wartość **Nie,** maszyny spoza zakresów nie będą mieć dostępu do danych w tym Application Insights zasobów. Te dane obejmują dostęp do dzienników, metryk i strumienia metryk na żywo programu APM, a także do opartych na nich funkcji, takich jak skoroszyty, pulpity nawigacyjne, środowisko klienta oparte na interfejsie API zapytań, szczegółowe informacje w Azure Portal i inne. 

> [!NOTE]
> Środowisko użycia poza portalem musi również działać w połączonej prywatnie sieci wirtualnej, która zawiera monitorowane obciążenia.

Musisz dodać zasoby hostowania monitorowanych obciążeń do linku prywatnego. Na przykład zobacz Using Private Endpoints for Azure Web App (Używanie [prywatnych punktów końcowych dla aplikacji internetowej platformy Azure).](../../app-service/networking/private-endpoint.md)

Ograniczanie dostępu w ten sposób dotyczy tylko danych w zasobie Application Insights zasobów. Jednak zmiany konfiguracji, w tym włączenie lub wyłączenie tych ustawień dostępu, są zarządzane przez Azure Resource Manager. Dlatego należy ograniczyć dostęp do Resource Manager przy użyciu odpowiednich ról, uprawnień, kontrolek sieci i inspekcji. Aby uzyskać więcej informacji, [zobacz Azure Monitor role, uprawnienia i zabezpieczenia.](../roles-permissions-security.md)

> [!NOTE]
> Aby w pełni zabezpieczyć zasoby oparte na Application Insights, należy zablokować zarówno dostęp do Application Insights zasobów, jak i do bazowego obszaru roboczego usługi Log Analytics.
>
> Diagnostyka na poziomie kodu (profiler/debuger) wymaga podania własnego konta [magazynu](../app/profiler-bring-your-own-storage.md) w celu obsługi linku prywatnego.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Obsługa typu "wszystkie lub nic" linków prywatnych
Jak wyjaśniono w te Private Link konfiguracji [usługi](#planning-your-private-link-setup), skonfigurowanie serwera Private Link nawet dla pojedynczego zasobu ma wpływ na wszystkie zasoby Azure Monitor w tych sieciach i w innych sieciach, które współużytkują ten sam system DNS. Takie zachowanie może utrudnić proces dołączania. Rozważ następujące opcje:

* Wszystko w programie — najprostszym i najbezpieczniejszym podejściem jest dodanie wszystkich składników Application Insights do ampLS. W przypadku składników, do których chcesz nadal uzyskać dostęp z innych sieci, pozostaw flagę "Zezwalaj na publiczny dostęp do Internetu w celu pozyskiwania/wykonywania zapytań" ustawioną na wartość Tak (ustawienie domyślne).
* Izolowanie sieci — jeśli używasz sieci wirtualnych szprych (lub możesz je dopasować), postępuj zgodnie ze wskazówkami w teście Topologia sieci piasty i szprych [na platformie Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Następnie skonfiguruj oddzielne ustawienia łącza prywatnego w odpowiednich sieciach wirtualnych szprych. Pamiętaj również o oddzieleniu stref DNS, ponieważ udostępnianie stref DNS innym sieciom szprych spowoduje przesłonięcia [DNS.](#the-issue-of-dns-overrides)
* Używanie niestandardowych stref DNS dla określonych aplikacji — to rozwiązanie umożliwia dostęp do wybranych składników Application Insights za pośrednictwem Private Link, przy jednoczesnym zachowaniu całego pozostałego ruchu na trasach publicznych.
    - Skonfiguruj niestandardową [prywatną strefę DNS](../../private-link/private-endpoint-dns.md)i nadaj jej unikatową nazwę, na przykład internal.monitor.azure.com
    - Utwórz usługę AMPLS i prywatny punkt końcowy, a **następnie** wybierz opcję, aby nie integrować się automatycznie z prywatnym systemem DNS
    - Przejdź do opcji Prywatny punkt końcowy > dns i przejrzyj sugerowane mapowanie nazw FQDN.
    - Wybierz pozycję Dodaj konfigurację i wybierz internal.monitor.azure.com właśnie utworzoną strefę
    - Dodaj rekordy dla powyższego Zrzut ![ ekranu przedstawiający skonfigurowaną strefę DNS](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Przejdź do składnika Application Insights i skopiuj [jego ciąg połączenia](../app/sdk-connection-string.md).
    - Aplikacje lub skrypty, które chcą wywołać ten składnik za pośrednictwem Private Link powinny używać parametrów połączenia z elementem EndpointSuffix=internal.monitor.azure.com
* Mapowanie punktów końcowych za pośrednictwem plików hostów zamiast systemu DNS — aby mieć dostęp Private Link tylko z określonej maszyny wirtualnej w sieci:
    - Skonfiguruj usługę AMPLS i prywatny  punkt końcowy, a następnie zamieć automatyczną integrację z prywatnym systemem DNS 
    - Skonfiguruj powyższe rekordy A na maszynie z uruchomianą aplikacją w pliku hosts


## <a name="use-apis-and-command-line"></a>Korzystanie z interfejsów API i wiersza polecenia

Opisany wcześniej proces można zautomatyzować przy użyciu Azure Resource Manager szablonów, interfejsów REST i interfejsów wiersza polecenia.

Aby utworzyć zakresy linków prywatnych i zarządzać nimi, użyj interfejsu [API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) lub interfejsu wiersza polecenia platformy [Azure (az monitor private-link-scope).](/cli/azure/monitor/private-link-scope)

Aby zarządzać dostępem sieciowym, użyj flag i w obszarach roboczych `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` usługi Log [Analytics](/cli/azure/monitor/log-analytics/workspace) lub [Application Insights usługi](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Zbieranie dzienników niestandardowych i dzienników usług IIS za pośrednictwem Private Link

Konta magazynu są używane w procesie pozyskiwania dzienników niestandardowych. Domyślnie są używane konta magazynu zarządzane przez usługę. Jednak w celu pozysowania dzienników niestandardowych w linkach prywatnych należy użyć własnych kont magazynu i skojarzyć je z obszarami roboczymi usługi Log Analytics. Zobacz więcej szczegółów na temat sposobu skonfigurowania takich kont przy użyciu [wiersza polecenia](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Aby uzyskać więcej informacji na temat wprowadzania własnego konta magazynu, zobacz Customer-owned storage accounts for log ingestion (Konta magazynu należące [do klienta na potrzeby pozyskiwania dzienników).](private-storage.md)

## <a name="restrictions-and-limitations"></a>Ograniczenia

### <a name="ampls"></a>AMPLS
Obiekt AMPLS ma kilka ograniczeń, które należy wziąć pod uwagę podczas planowania Private Link konfiguracji:

* Sieć wirtualna może łączyć się tylko z 1 obiektem AMPLS. Oznacza to, że obiekt AMPLS musi zapewniać dostęp do wszystkich Azure Monitor, do których sieć wirtualna powinna mieć dostęp.
* Zasób Azure Monitor (obszar roboczy lub składnik Application Insights) może łączyć się co najwyżej z 5 amplsami.
* Obiekt AMPLS może łączyć się z 50 Azure Monitor zasobów.
* Obiekt AMPLS może łączyć się najwyżej z 10 prywatnymi punktami końcowymi.

Zobacz [Rozważanie limitów,](#consider-limits) aby uzyskać bardziej szczegółowe informacje na temat tych limitów.

### <a name="agents"></a>Agenci

Najnowsze wersje agentów systemów Windows i Linux muszą być używane do obsługi bezpiecznego pozyskiwania do obszarów roboczych usługi Log Analytics. Starsze wersje nie mogą przekazywać danych monitorowania za pośrednictwem sieci prywatnej.

**Agent usługi Log Analytics dla systemu Windows**

Użyj agenta usługi Log Analytics w wersji 10.20.18038.0 lub nowszej.

**Agent usługi Log Analytics dla systemu Linux**

Użyj agenta w wersji 1.12.25 lub nowszej. Jeśli nie możesz tego zrobić, uruchom następujące polecenia na maszynie wirtualnej.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Aby używać Azure Monitor portali, takich jak usługi Application Insights i Log Analytics, należy zezwolić na dostęp rozszerzeń Azure Portal i Azure Monitor w sieciach prywatnych. Dodaj **tagi AzureActiveDirectory,** **AzureResourceManager,** **AzureFrontDoor.FirstParty** i **AzureFrontdoor.Frontend** [](../../firewall/service-tags.md) do sieciowej grupy zabezpieczeń.

### <a name="querying-data"></a>Wykonywanie zapytań na danych
Operator [ `externaldata` nie](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) jest obsługiwany przez Private Link, ponieważ odczytuje dane z kont magazynu, ale nie gwarantuje prywatnego dostępu do magazynu.

### <a name="programmatic-access"></a>Dostęp programowy

Aby używać interfejsu API REST, interfejsu [wiersza](/cli/azure/monitor) polecenia lub [](../../virtual-network/service-tags-overview.md)programu PowerShell Azure Monitor w sieciach prywatnych, dodaj tagi usługi **AzureActiveDirectory** i **AzureResourceManager** do zapory.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK do pobrania z sieci dostarczania zawartości

Pomiń kod JavaScript w skrypcie, aby przeglądarka nie próbowała pobrać kodu z sieci CDN. Przykład znajduje się w witrynie [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Ustawienia DNS przeglądarki

Jeśli łączysz się z zasobami Azure Monitor za pośrednictwem Private Link, ruch do tych zasobów musi przechodzić przez prywatny punkt końcowy skonfigurowany w sieci. Aby włączyć prywatny punkt końcowy, zaktualizuj ustawienia DNS, jak wyjaśniono w tece [Łączenie z prywatnym punktem końcowym](#connect-to-a-private-endpoint). Niektóre przeglądarki używają własnych ustawień DNS zamiast ustawień ustawionych przez Ciebie. Przeglądarka może próbować nawiązać połączenie z publicznymi Azure Monitor i całkowicie pominąć Private Link końcowe. Sprawdź, czy ustawienia przeglądarki nie zastępują ani nie buforują starych ustawień DNS. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [magazynie prywatnym](private-storage.md)