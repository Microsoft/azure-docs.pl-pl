---
title: Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure za pomocą ISE
description: Utwórz środowisko usługi integracji (ISE), które może uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 49248575cb10f3df746b9ba484244e4702fb5d72
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369012"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps przy użyciu środowiska usługi integracji (ISE)

W przypadku scenariuszy, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Środowisko usługi integracji (ISE) to dedykowane środowisko, korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od „globalnej” wielodostępnej usługi Logic Apps. Ta separacja również zmniejsza wpływ innych dzierżawców platformy Azure na wydajność aplikacji. Środowisko ISE zapewnia Ci także własne statyczne adresy IP. Te adresy IP są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, wielodostępnej usłudze.

Po utworzeniu ISE platforma Azure wprowadza tę ISE do sieci wirtualnej platformy Azure *, która następnie* wdraża usługę Logic Apps w sieci wirtualnej. Podczas tworzenia aplikacji logiki lub konta integracji wybierz swój ISE jako lokalizację. Aplikacja logiki lub konto integracji może następnie bezpośrednio uzyskać dostęp do zasobów, takich jak maszyny wirtualne, serwery, systemy i usługi, w sieci wirtualnej.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> W przypadku aplikacji logiki i kont integracji, które współpracują ze sobą w ISE, oba muszą używać tego *samego ISE* jako lokalizacji.

ISE zwiększono limity czasu trwania przebiegu, przechowywanie magazynu, przepływność, żądania HTTP i limity czasu odpowiedzi, rozmiary komunikatów i żądania łączników niestandardowych. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Aby dowiedzieć się więcej na temat ISEs, zobacz [dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

W tym artykule pokazano, jak wykonać te zadania przy użyciu Azure Portal:

* Włącz dostęp do ISE.
* Utwórz ISE.
* Dodaj dodatkową pojemność do ISE.

Możesz również utworzyć ISE za pomocą [Azure Resource Manager przykładowego szablonu przewodnika Szybki Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) lub interfejsu API REST usługi Logic Apps, w tym konfigurowania kluczy zarządzanych przez klienta:

* [Tworzenie środowiska usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w usłudze ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](../logic-apps/logic-apps-pricing.md).

* [Sieć wirtualna platformy Azure](../virtual-network/virtual-networks-overview.md). Sieć wirtualna musi mieć cztery *puste* podsieci, które są wymagane do tworzenia i wdrażania zasobów w ISE i są używane przez te składniki wewnętrzne i ukryte:

  * Logic Apps obliczeń
  * Wewnętrzny App Service Environment (łączniki)
  * Wewnętrzny API Management (łączniki)
  * Wewnętrzny Redis na potrzeby buforowania i wydajności
  
  Podsieci można utworzyć z wyprzedzeniem. Możesz też poczekać, aż utworzysz ISE, tak aby można było utworzyć podsieci w tym samym czasie. Jednak przed utworzeniem podsieci zapoznaj się z [wymaganiami dotyczącymi podsieci](#create-subnet).

  > [!IMPORTANT]
  >
  > Nie używaj następujących przestrzeni adresów IP dla sieci wirtualnej lub podsieci, ponieważ nie są one rozpoznawane przez Azure Logic Apps:<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * Upewnij się, że sieć wirtualna [umożliwia dostęp do usługi ISE](#enable-access) , dzięki czemu ISE może działać prawidłowo i pozostać dostępne.

  * Jeśli używasz programu lub chcesz korzystać z [ExpressRoute](../expressroute/expressroute-introduction.md) wraz z [wymuszonym tunelowaniem](../firewall/forced-tunneling.md), musisz [utworzyć tabelę tras](../virtual-network/manage-route-table.md) z następującą określoną trasą i połączyć tabelę tras z każdą podsiecią używaną przez ISE:

    **Nazwa**: <*nazwę trasy*><br>
    **Prefiks adresu**: 0.0.0.0/0<br>
    **Następny przeskok**: Internet
    
    Ta określona tabela tras jest wymagana, aby składniki Logic Apps mogły komunikować się z innymi zależnymi usługami platformy Azure, takimi jak Azure Storage i Azure SQL DB. Aby uzyskać więcej informacji na temat tej trasy, zobacz [0.0.0.0/0 prefiks adresu](../virtual-network/virtual-networks-udr-overview.md#default-route). Jeśli nie korzystasz z wymuszonego tunelowania z ExpressRoute, nie potrzebujesz tej konkretnej tabeli tras.
    
    Usługa ExpressRoute umożliwia poszerzanie sieci lokalnych w chmurze firmy Microsoft i nawiązywanie połączenia z usługami w chmurze firmy Microsoft za pośrednictwem połączenia prywatnego, które jest obsługiwane przez dostawcę połączenia. W odniesieniu do ExpressRoute jest wirtualną siecią prywatną, która kieruje ruchem w sieci prywatnej, a nie za pośrednictwem publicznego Internetu. Aplikacje logiki mogą łączyć się z zasobami lokalnymi, które znajdują się w tej samej sieci wirtualnej, gdy łączą się za pomocą ExpressRoute lub wirtualnej sieci prywatnej.
   
  * Jeśli używasz [wirtualnego urządzenia sieciowego (urządzenie WUS)](../virtual-network/virtual-networks-udr-overview.md#user-defined), upewnij się, że nie jest włączone zakończenie protokołu TLS/SSL lub Zmień ruch wychodzący TLS/SSL. Upewnij się również, że nie włączono inspekcji dla ruchu pochodzącego z podsieci ISE. Aby uzyskać więcej informacji, zobacz [routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

  * Jeśli chcesz użyć niestandardowych serwerów DNS dla sieci wirtualnej platformy Azure, [Skonfiguruj te serwery, wykonując następujące czynności](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) przed wdrożeniem ISE w sieci wirtualnej. Aby uzyskać więcej informacji na temat zarządzania ustawieniami serwera DNS, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > W przypadku zmiany ustawień serwera DNS lub serwera DNS należy ponownie uruchomić usługę ISE, aby ISE mogły pobrać te zmiany. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Włączanie dostępu dla środowiska ISE

Gdy używasz ISE z siecią wirtualną platformy Azure, typowy problem z instalacją ma co najmniej jeden blokowany port. Łączniki używane do tworzenia połączeń między systemami ISE i docelowymi mogą również mieć własne wymagania dotyczące portów. Na przykład w przypadku komunikowania się z systemem FTP przy użyciu łącznika FTP port używany w systemie FTP musi być dostępny, na przykład port 21 dla poleceń wysyłania.

Aby upewnić się, że ISE jest dostępny i że aplikacje logiki w tym ISE mogą komunikować się między każdą podsiecią w sieci wirtualnej, [Otwórz porty opisane w tej tabeli dla każdej podsieci](#network-ports-for-ise). Jeśli którykolwiek z wymaganych portów jest niedostępny, ISE nie będzie działał poprawnie.

* Jeśli masz wiele wystąpień ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, wdróż [zaporę platformy Azure](../firewall/overview.md) lub [sieciowe urządzenie wirtualne](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i Roześlij ruch wychodzący za pomocą tej zapory lub wirtualnego urządzenia sieciowego. Następnie można [skonfigurować jeden, wychodzący, publiczny, statyczny i przewidywalny adres IP](connect-virtual-network-vnet-set-up-single-ip-address.md) , który może być używany przez wszystkie wystąpienia ISE w sieci wirtualnej do komunikowania się z systemami docelowymi. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w tych systemach docelowych dla każdego ISEu.

   > [!NOTE]
   > Tego podejścia można użyć dla jednej ISE, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Należy rozważyć, czy dodatkowe koszty zapory lub urządzenia sieci wirtualnej mają sens dla danego scenariusza. Dowiedz się więcej o [cenach zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* W przypadku utworzenia nowej sieci wirtualnej platformy Azure i podsieci bez żadnych ograniczeń nie trzeba konfigurować [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/security-overview.md#network-security-groups) w sieci wirtualnej, aby kontrolować ruch w różnych podsieciach.

* W przypadku istniejącej sieci wirtualnej można *Opcjonalnie* skonfigurować [sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/security-overview.md#network-security-groups) w celu [filtrowania ruchu sieciowego między podsieciami](../virtual-network/tutorial-filter-network-traffic.md). Jeśli chcesz przejść do tej trasy lub jeśli korzystasz już z sieciowych grup zabezpieczeń, upewnij się, że zostały [otwarte porty opisane w tej tabeli](#network-ports-for-ise) dla tych sieciowych grup zabezpieczeń.

  Po skonfigurowaniu [reguł zabezpieczeń sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules)należy używać *zarówno* protokołów **TCP** , jak i **UDP** . można też wybrać **dowolną** z nich, aby nie trzeba było tworzyć oddzielnych reguł dla każdego protokołu. SIECIOWEJ grupy zabezpieczeń reguły zabezpieczeń zawierają informacje o portach, które muszą być otwarte dla adresów IP, które wymagają dostępu do tych portów. Upewnij się, że wszystkie zapory, routery lub inne elementy, które istnieją między tymi punktami końcowymi, zachowują również dostęp do tych adresów IP.

* W przypadku skonfigurowania wymuszonego tunelowania przez zaporę w celu przekierowania ruchu związanego z Internetem zapoznaj się z [dodatkowymi wymaganiami wymuszonego tunelowania](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porty sieciowe używane przez środowisko ISE

W tej tabeli opisano porty, które ISE muszą być dostępne, i przeznaczenie dla tych portów. Aby zmniejszyć złożoność podczas konfigurowania reguł zabezpieczeń, w tabeli są używane [Tagi usług](../virtual-network/service-tags-overview.md) reprezentujące grupy prefiksów adresów IP dla określonej usługi platformy Azure. Gdzie zanotowano, *wewnętrzne ISE* i *zewnętrzna ISE* odnoszą się do [punktu końcowego dostępu wybranego podczas tworzenia ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Upewnij się, że dla wszystkich reguł określono porty źródłowe, `*` ponieważ porty źródłowe są tymczasowe.

#### <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| Przeznaczenie | Tag lub adresy IP usługi źródłowej | Porty źródłowe | Tag lub adresy IP usługi docelowej | Porty docelowe | Uwagi |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikacja między podsieciami w ramach sieci wirtualnej | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Wymagany do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne**: w przypadku ruchu między *składnikami* w poszczególnych podsieciach upewnij się, że otwarto wszystkie porty w każdej podsieci. |
| Oba: <p>Komunikacja z aplikacją logiki <p><p>Historia uruchamiania aplikacji logiki| ISE wewnętrzny: <br>**VirtualNetwork** <p><p>Zewnętrzna ISE: **Internet** lub zobacz **uwagi** | * | **VirtualNetwork** | 443 | Zamiast korzystać z tagu usługi **internetowej** , można określić źródłowy adres IP dla następujących elementów: <p><p>-Komputer lub usługa, która wywołuje wszystkie wyzwalacze żądań lub elementy webhook w aplikacji logiki <p>-Komputer lub usługa, z której chcesz uzyskać dostęp do historii uruchomień aplikacji logiki <p><p>**Ważne**: zamknięcie lub zablokowanie tego portu uniemożliwia wywołania do aplikacji logiki, które mają wyzwalacze żądań lub elementy webhook. Można również uniemożliwić dostęp do danych wejściowych i wyjściowych dla każdego kroku w historii uruchamiania. Jednak nie masz dostępu do historii uruchomień aplikacji logiki.|
| Logic Apps Designer — właściwości dynamiczne | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Żądania pochodzą z [przychodzących adresów IP](../logic-apps/logic-apps-limits-and-config.md#inbound) punktu końcowego dostępu Logic Apps dla tego regionu. |
| Wdrożenie łącznika | **AzureConnectors** | * | **VirtualNetwork** | 454 | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje i poprawki łącznika. |
| Sprawdzenie kondycji sieci | **LogicApps** | * | **VirtualNetwork** | 454 | Żądania pochodzą z przychodzących [adresów IP](../logic-apps/logic-apps-limits-and-config.md#inbound) punktu końcowego dostępu Logic Apps i [wychodzących adresów IP](../logic-apps/logic-apps-limits-and-config.md#outbound) dla tego regionu. |
| Zależność zarządzania App Service | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Komunikacja z Traffic Manager platformy Azure | **AzureTrafficManager** | * | **VirtualNetwork** | ISE wewnętrzny: 454 <p><p>ISE zewnętrzne: 443 ||
| Oba: <p>Wdrożenie zasad łącznika <p>Punkt końcowy zarządzania API Management | **APIManagement** | * | **VirtualNetwork** | 3443 | W przypadku wdrożenia zasad łącznika dostęp do portu jest wymagany do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje i poprawki łącznika. |
| Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między wystąpieniami roli | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plus zobacz **uwagi**| Aby ISE współpracowały z usługą Azure cache for Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w pamięci podręcznej platformy Azure w celu uzyskania często zadawanych pytań](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| Przeznaczenie | Tag lub adresy IP usługi źródłowej | Porty źródłowe | Tag lub adresy IP usługi docelowej | Porty docelowe | Uwagi |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikacja między podsieciami w ramach sieci wirtualnej | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Wymagany do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne**: w przypadku ruchu między *składnikami* w poszczególnych podsieciach upewnij się, że otwarto wszystkie porty w każdej podsieci. |
| Komunikacja z aplikacji logiki | **VirtualNetwork** | * | Różni się w zależności od miejsca docelowego | 80, 443 | Lokalizacja docelowa zależy od punktów końcowych usługi zewnętrznej, z którą aplikacja logiki musi się komunikować. |
| Azure Active Directory | **VirtualNetwork** | * | **Usługi azureactivedirectory** | 80, 443 ||
| Zależność usługi Azure Storage | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Zarządzanie połączeniami | **VirtualNetwork** | * | **AppService** | 443 ||
| Publikowanie dzienników diagnostycznych & metryki | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Zależność SQL platformy Azure | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Wymagane do opublikowania stanu kondycji w Resource Health. |
| Zależność od dziennika do zasad usługi Event Hub i agenta monitorowania | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między wystąpieniami roli | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plus zobacz **uwagi**| Aby ISE współpracowały z usługą Azure cache for Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w pamięci podręcznej platformy Azure w celu uzyskania często zadawanych pytań](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Rozpoznawanie nazw DNS | **VirtualNetwork** | * | Adresy IP dla wszelkich niestandardowych serwerów systemu nazw domen (DNS) w sieci wirtualnej | 53 | Wymagane tylko w przypadku korzystania z niestandardowych serwerów DNS w sieci wirtualnej |
|||||||

Ponadto należy dodać reguły ruchu wychodzącego dla [App Service Environment (ASE)](../app-service/environment/intro.md):

* Jeśli używasz zapory platformy Azure, musisz skonfigurować zaporę za pomocą [tagu w pełni kwalifikowanej nazwy domeny](../firewall/fqdn-tags.md#current-fqdn-tags)App Service Environment (ASE), co umożliwia wychodzący dostęp do ruchu platformy ASE.

* Jeśli używasz urządzenia zapory innego niż Zapora platformy Azure, musisz skonfigurować zaporę ze *wszystkimi* regułami wymienionymi w [zależnościach integracji zapory](../app-service/environment/firewall-integration.md#dependencies) , które są wymagane dla App Service Environment.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Wymagania wymuszonego tunelowania

W przypadku skonfigurowania lub użycia [wymuszonego tunelowania](../firewall/forced-tunneling.md) za pomocą zapory należy zezwolić na dodatkowe zależności zewnętrzne dla ISE. Wymuszone tunelowanie pozwala przekierowywać ruch związany z Internetem do określonego następnego przeskoku, takiego jak wirtualna sieć prywatna (VPN) lub do urządzenia wirtualnego, a nie Internetu, aby umożliwić inspekcję i inspekcję ruchu wychodzącego w sieci.

Zwykle cały ruch wychodzący zależności ISE przechodzi przez wirtualny adres IP (VIP), który jest inicjowany za pomocą ISE. Jednak w przypadku zmiany routingu ruchu do lub z ISE należy zezwolić na następujące zależności wychodzące w zaporze przez ustawienie następnego przeskoku na `Internet` . Jeśli używasz zapory platformy Azure, postępuj zgodnie z [instrukcjami w celu skonfigurowania zapory za pomocą App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).

Jeśli nie zezwolisz na dostęp do tych zależności, wdrożenie usługi ISE kończy się niepowodzeniem, a wdrożony ISE przestanie działać:

* [Adresy zarządzania App Service Environment](../app-service/environment/management-addresses.md)

* [Adresy API Management platformy Azure](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Adresy zarządzania Traffic Manager platformy Azure](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [Logic Apps adresy przychodzące i wychodzące dla regionu ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [Adresy IP platformy Azure dla łączników w regionie ISE, które znajdują się w tym pliku do pobrania](https://www.microsoft.com/download/details.aspx?id=56519)

* Należy włączyć punkty końcowe usługi dla usług Azure SQL, Storage, Service Bus i Event Hub, ponieważ nie można wysyłać ruchu przez zaporę do tych usług.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Tworzenie własnego środowiska ISE

1. W [Azure Portal](https://portal.azure.com)w głównym polu wyszukiwania platformy Azure wprowadź `integration service environments` jako filtr, a następnie wybierz pozycję **środowiska usługi integracji**.

   ![Znajdowanie i wybieranie "środowisk usługi integracji"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W okienku **środowiska usługi integracji** wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj", aby utworzyć środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Podaj te szczegóły dla danego środowiska, a następnie wybierz pozycję **Przegląd + Utwórz**, na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Subskrypcja platformy Azure do użycia w danym środowisku |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-Name*> | Nowa lub istniejąca Grupa zasobów platformy Azure, w której chcesz utworzyć środowisko |
   | **Nazwa środowiska usługi integracji** | Tak | <*Nazwa środowiska*> | Nazwa ISE, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ) i kropki ( `.` ). |
   | **Lokalizacja** | Tak | <*Azure — centrum danych — region*> | Region centrum danych platformy Azure, w którym ma zostać wdrożone środowisko |
   | **SKU** | Tak | **Premium** lub **Developer (bez umowy SLA)** | Jednostka SKU ISE do utworzenia i użycia. Aby uzyskać różnice między tymi jednostkami SKU, zobacz [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Ważne**: Ta opcja jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić. |
   | **Dodatkowa pojemność** | Premium: <br>Tak <p><p>Pisał <br>Nie dotyczy | Premium: <br>od 0 do 10 <p><p>Pisał <br>Nie dotyczy | Liczba dodatkowych jednostek przetwarzania, które mają być używane dla tego zasobu ISE. Aby dodać pojemność po utworzeniu, zobacz [Dodawanie pojemności ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Punkt końcowy dostępu** | Tak | **Wewnętrzne** lub **zewnętrzne** | Typ punktów końcowych dostępu, które mają być używane dla ISE. Te punkty końcowe określają, czy wyzwalacze żądań lub elementów webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej. <p><p>Wybór ma także wpływ na sposób wyświetlania i uzyskiwania dostępu do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Ważne**: można wybrać punkt końcowy dostępu tylko podczas tworzenia ISE i nie można zmienić tej opcji później. |
   | **Sieć wirtualna** | Tak | <*Azure-Virtual-Network-Name*> | Sieć wirtualna platformy Azure, w której chcesz wstrzyknąć środowisko, aby aplikacje logiki w tym środowisku mogły uzyskiwać dostęp do sieci wirtualnej. Jeśli nie masz sieci, [najpierw Utwórz sieć wirtualną platformy Azure](../virtual-network/quick-create-portal.md). <p><p>**Ważne**: to iniekcja można wykonać *tylko* po utworzeniu ISE. |
   | **Podsieci** | Tak | <*podsieć-Lista zasobów*> | ISE wymaga czterech *pustych* podsieci, które są wymagane do tworzenia i wdrażania zasobów w ISE i są używane przez wewnętrzne składniki Logic Apps, takie jak łączniki i buforowanie na potrzeby wydajności. <p>**Ważne**: [przed wykonaniem tych kroków w celu utworzenia podsieci należy przejrzeć wymagania dotyczące podsieci](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   ISE potrzebuje czterech *pustych* podsieci, które są wymagane do tworzenia i wdrażania zasobów w ISE i są używane przez wewnętrzne składniki Logic Apps, takie jak łączniki i buforowanie na potrzeby wydajności. *Nie* można zmienić tych adresów podsieci po utworzeniu środowiska. Jeśli tworzysz i wdrażasz ISE za pomocą Azure Portal, upewnij się, że te podsieci nie zostały delegowane do żadnych usług platformy Azure. Jeśli jednak tworzysz i wdrażasz ISE za pomocą interfejsu API REST, Azure PowerShell lub szablonu Azure Resource Manager, musisz [delegować](../virtual-network/manage-subnet-delegation.md) jedną pustą podsieć do `Microsoft.integrationServiceEnvironment` . Aby uzyskać więcej informacji, zobacz [Dodawanie delegowania podsieci](../virtual-network/manage-subnet-delegation.md).

   Każda podsieć musi spełniać następujące wymagania:

   * Używa nazwy zaczynającej się od litery lub znaku podkreślenia (bez cyfr) i nie używa następujących znaków:,,,, `<` , `>` `%` `&` `\\` `?` , `/` .

   * Używa [formatu routingu bezklasowego (cidr)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) i przestrzeni adresowej klasy B.
   
     > [!IMPORTANT]
     >
     > Nie używaj następujących przestrzeni adresów IP dla sieci wirtualnej lub podsieci, ponieważ nie są one rozpoznawane przez Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Używa `/27` w przestrzeni adresowej, ponieważ każda podsieć wymaga 32 adresów. Na przykład `10.0.0.0/27` ma 32 adresów, ponieważ 2<sup>(32-27)</sup> jest 2<sup>5</sup> lub 32. Więcej adresów nie zapewnia dodatkowych korzyści. Aby dowiedzieć się więcej o obliczaniu adresów, zobacz [bloki protokołu IPv4 w protokole CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md), musisz [utworzyć tabelę tras](../virtual-network/manage-route-table.md) , która ma następującą trasę i połączyć tę tabelę z każdą podsiecią używaną przez ISE:

     **Nazwa**: <*nazwę trasy*><br>
     **Prefiks adresu**: 0.0.0.0/0<br>
     **Następny przeskok**: Internet

   1. Na liście **podsieci** wybierz pozycję **Zarządzaj konfiguracją podsieci**.

      ![Zarządzanie konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. W okienku **podsieci** wybierz pozycję **podsieć**.

      ![Dodaj cztery puste podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. W okienku **Dodaj podsieć** podaj te informacje.

      * **Name**: Nazwa podsieci
      * **Zakres adresów (blok CIDR)**: zakres podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodawanie szczegółów podsieci](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Gdy skończysz, wybierz opcję **OK**.

   1. Powtórz te kroki dla trzech więcej podsieci.

      > [!NOTE]
      > Jeśli podsieci, które próbujesz utworzyć, są nieprawidłowe, Azure Portal pokazuje komunikat, ale nie blokuje postępu.

   Aby uzyskać więcej informacji na temat tworzenia podsieci, zobacz [Dodawanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

1. Po pomyślnym sprawdzeniu przez platformę Azure informacji o ISE wybierz pozycję **Utwórz**, na przykład:

   ![Po pomyślnej weryfikacji wybierz pozycję "Utwórz".](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Platforma Azure rozpocznie wdrażanie środowiska, które zwykle trwa w ciągu dwóch godzin. Czasami wdrożenie może trwać do czterech godzin. Aby sprawdzić stan wdrożenia, na pasku narzędzi platformy Azure wybierz ikonę powiadomienia, która spowoduje otwarcie okienka powiadomienia.

   ![Sprawdź stan wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Jeśli wdrożenie zakończyło się pomyślnie, na platformie Azure zostanie wyświetlone następujące powiadomienie:

   ![Wdrożenie powiodło się](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   W przeciwnym razie postępuj zgodnie z Azure Portal instrukcjami dotyczącymi rozwiązywania problemów z wdrażaniem.

   > [!NOTE]
   > Jeśli wdrożenie nie powiedzie się lub usuniesz ISE, platforma Azure może zająć do godziny lub prawdopodobnie dłużej w rzadkich przypadkach przed zwolnieniem podsieci. W związku z tym może być konieczne poczekanie, zanim będzie można ponownie użyć tych podsieci w innym ISE.
   >
   > Po usunięciu sieci wirtualnej platforma Azure zazwyczaj zajmie maksymalnie dwie godziny przed zwolnieniem podsieci, ale ta operacja może trwać dłużej. 
   > Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
   > Zobacz [usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Aby wyświetlić swoje środowisko, wybierz pozycję **Przejdź do zasobu** , jeśli platforma Azure nie przejdzie automatycznie do środowiska po zakończeniu wdrażania.

1. Aby sprawdzić kondycję sieci ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Aby rozpocząć tworzenie aplikacji logiki i innych artefaktów w ISE, zobacz [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Zarządzane łączniki ISE, które staną się dostępne po utworzeniu ISE, nie są automatycznie wyświetlane w selektorze łącznika w Projektancie aplikacji logiki. Aby można było używać tych łączników ISE, należy ręcznie [dodać te łączniki do ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) , aby były wyświetlane w Projektancie aplikacji logiki.

   > [!IMPORTANT]
   > Zarządzane łączniki ISE obecnie nie obsługują [tagów](../azure-resource-manager/management/tag-support.md). W przypadku skonfigurowania zasad, które wymuszają znakowanie, próba dodania łączników ISE  
   > może zakończyć się niepowodzeniem z błędem podobnym do tego przykładu: 
   > 
   > ```json
   > {
   >    "error": { 
   >       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
   >       "message": "The tags are not supported in the managed API 'azureblob'."
   >    }
   > }
   > ```
   > Aby dodać łączniki ISE, należy wyłączyć lub usunąć zasady.
   > 

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
