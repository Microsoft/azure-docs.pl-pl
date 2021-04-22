---
title: Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure za pomocą środowiska ISE
description: Tworzenie środowiska usługi integracji (ISE), które może uzyskać dostęp do sieci wirtualnych platformy Azure z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: bfef9f2b5420ac9377cc369d7bf9a9bdac76743b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874229"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Łączenie się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps przy użyciu środowiska usługi integracji

W przypadku scenariuszy, w których aplikacje logiki i konta integracji muszą mieć dostęp do sieci wirtualnej platformy [Azure,](../virtual-network/virtual-networks-overview.md)utwórz środowisko usługi integracji [  (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Środowisko usługi integracji (ISE) to dedykowane środowisko, korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od „globalnej” wielodostępnej usługi Logic Apps. Takie rozdzielenie zmniejsza również wpływ innych dzierżaw platformy Azure na wydajność aplikacji. Środowisko ISE zapewnia Ci także własne statyczne adresy IP. Te adresy IP są oddzielone od statycznych adresów IP udostępnianych przez aplikacje logiki w publicznej usłudze wielodostępnej.

Podczas tworzenia środowiska ISE platforma *Azure* wprowadza to ise do sieci wirtualnej platformy Azure, a następnie wdraża usługę Logic Apps w sieci wirtualnej. Podczas tworzenia aplikacji logiki lub konta integracji wybierz platformę ISE jako lokalizację. Aplikacja logiki lub konto integracji może wtedy bezpośrednio uzyskać dostęp do zasobów, takich jak maszyny wirtualne, serwery, systemy i usługi, w sieci wirtualnej.

![Wybieranie środowiska usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Aby aplikacje logiki i konta integracji działały razem w programie ISE, obie muszą używać tego samego środowiska *ISE* co ich lokalizacja.

W przypadku rozwiązania ISE zwiększono limity czasu trwania działania, przechowywania magazynu, przepływności, limitów czasu żądań HTTP i odpowiedzi, rozmiarów komunikatów i niestandardowych żądań łącznika. Aby uzyskać więcej informacji, zobacz [Limits and configuration for Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Aby dowiedzieć się więcej na temat platform ISE, zobacz Access to Azure Virtual Network resources from Azure Logic Apps (Dostęp do zasobów usługi [Azure Virtual Network z Azure Logic Apps).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

W tym artykule pokazano, jak wykonać te zadania przy użyciu Azure Portal:

* Włącz dostęp dla twojego programu ISE.
* Utwórz swoje urządzenie ISE.
* Dodaj dodatkową pojemność do swojego ise.

Możesz również utworzyć program ISE, korzystając z przykładowego szablonu Azure Resource Manager [Szybki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) start lub przy użyciu interfejsu API REST usługi Logic Apps, w tym konfigurowania kluczy zarządzanych przez klienta:

* [Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST usługi Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania danych w spoczynku dla platform ISE](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki uruchamiane w programie ISE używają planu cenowego innego niż plan cenowy oparty na użyciu. Aby dowiedzieć się, jak działają ceny i rozliczenia dla platform ISE, zobacz [Logic Apps cennika](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, [zobacz Logic Apps cennika.](../logic-apps/logic-apps-pricing.md)

* Sieć  [wirtualna platformy Azure,](../virtual-network/virtual-networks-overview.md) która ma cztery puste podsieci, które są wymagane do tworzenia i wdrażania zasobów w środowisku ISE i są używane przez te składniki wewnętrzne i ukryte:

  * Logic Apps Compute
  * Wewnętrzne App Service Environment (łączniki)
  * Wewnętrzne API Management (łączniki)
  * Wewnętrzna pamięć redis do buforowania i wydajności
  
  Podsieci można utworzyć z wyprzedzeniem lub podczas tworzenia wirtualnego komputera(ISE), aby można było utworzyć podsieci w tym samym czasie. Jednak przed utworzeniem podsieci upewnij się, że wymagania dotyczące podsieci są [spełnione.](#create-subnet)

  * Upewnij się, że twoja sieć wirtualna umożliwia dostęp do środowiska [ISE,](#enable-access) dzięki czemu twoje środowiska ISE mogą działać prawidłowo i pozostać dostępne.

  * Jeśli używasz lub chcesz używać usługi [ExpressRoute](../expressroute/expressroute-introduction.md) wraz [](../virtual-network/manage-route-table.md) z wymuszonym tunelowaniem, [](../firewall/forced-tunneling.md)musisz utworzyć tabelę tras z następującą konkretną trasą i połączyć tabelę tras z każdą podsiecią używaną przez platformę ISE:

    **Nazwa**: <*route-name*><br>
    **Prefiks adresu:** 0.0.0.0/0<br>
    **Następny przeskok:** Internet
    
    Ta tabela tras jest wymagana, aby składniki Logic Apps mogą komunikować się z innymi zależnmi usługami platformy Azure, takimi jak Azure Storage i Azure SQL DB. Aby uzyskać więcej informacji na temat tej trasy, zobacz prefiks adresu [0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Jeśli nie używasz wymuszonego tunelowania z użyciem funkcji ExpressRoute, ta tabela tras nie jest potrzebna.
    
    Usługa ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę firmy Microsoft i łączenie się z usługami w chmurze firmy Microsoft za pośrednictwem połączenia prywatnego, które jest zapewniane przez dostawcę połączenia. W szczególności expressRoute to wirtualna sieć prywatna, która kieruje ruchem przez sieć prywatną, a nie za pośrednictwem publicznego Internetu. Aplikacje logiki mogą łączyć się z zasobami lokalnymi, które znajdują się w tej samej sieci wirtualnej, gdy łączą się za pośrednictwem usługi ExpressRoute lub wirtualnej sieci prywatnej.
   
  * W przypadku korzystania z wirtualnego urządzenia sieciowego [(WUS)](../virtual-network/virtual-networks-udr-overview.md#user-defined)upewnij się, że nie włączyć zakończenia protokołu TLS/SSL ani nie zmienić ruchu wychodzącego protokołu TLS/SSL. Upewnij się również, że nie włączyć inspekcji ruchu pochodzącego z podsieci ise. Aby uzyskać więcej informacji, zobacz [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

  * Jeśli chcesz użyć niestandardowych serwerów DNS dla sieci wirtualnej platformy [Azure,](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) skonfiguruj te serwery, korzystając z poniższych kroków przed wdrożeniem środowiska ISE w sieci wirtualnej. Aby uzyskać więcej informacji na temat zarządzania ustawieniami serwera DNS, zobacz [Create, change, or delete a virtual network (Tworzenie, zmienianie lub usuwanie sieci wirtualnej).](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Jeśli zmienisz ustawienia serwera DNS lub serwera DNS, musisz ponownie uruchomić program ISE, aby program ISE może je pobrać. Aby uzyskać więcej informacji, zobacz [Restart your ISE (Ponowne uruchamianie programu ISE).](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Włączanie dostępu dla środowiska ISE

W przypadku korzystania ze środowiska ISE z siecią wirtualną platformy Azure częstym problemem z konfiguracją jest co najmniej jeden zablokowany port. Łączniki, których używasz do tworzenia połączeń między platformą ISE a systemami docelowymi, mogą również mieć własne wymagania dotyczące portów. Jeśli na przykład komunikuje sięsz z systemem FTP za pomocą łącznika FTP, port, który jest w systemie FTP, musi być dostępny, na przykład przez port 21, aby można było wysyłać polecenia.

Aby upewnić się, że twoje środowiska ISE są dostępne i że aplikacje logiki w tym środowisku mogą komunikować się przez każdą podsieć w sieci wirtualnej, otwórz porty opisane w tej tabeli dla każdej [podsieci](#network-ports-for-ise). Jeśli jakiekolwiek wymagane porty są niedostępne, twoja usługa ISE nie będzie działać prawidłowo.

* Jeśli masz wiele wystąpień środowiska ISE, które wymagają dostępu do innych punktów końcowych [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) z ograniczeniami adresów IP, wd wdrażaj urządzenie [wirtualne Azure Firewall](../firewall/overview.md) lub sieciowe w sieci wirtualnej i przekieruj ruch wychodzący przez zaporę lub sieciowe urządzenie wirtualne. Następnie można skonfigurować [jeden, wychodzący, publiczny,](connect-virtual-network-vnet-set-up-single-ip-address.md) statyczny i przewidywalny adres IP, którego wszystkie wystąpienia środowiska ISE w sieci wirtualnej mogą używać do komunikowania się z systemami docelowymi. Dzięki temu nie trzeba skonfigurować dodatkowych otworów zapory w tych systemach docelowych dla każdego programu ISE.

   > [!NOTE]
   > Tej metody można użyć w przypadku pojedynczego rozwiązania ISE, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Zastanów się, czy dodatkowe koszty zapory lub wirtualnego urządzenia sieciowego mają sens dla Twojego scenariusza. Dowiedz się więcej o [Azure Firewall usługi .](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Jeśli utworzono nową sieć wirtualną i podsieci platformy Azure bez żadnych ograniczeń, nie musisz tworzyć sieciowych grup zabezpieczeń [w](../virtual-network/network-security-groups-overview.md#network-security-groups) sieci wirtualnej, aby kontrolować ruch między podsieciami.

* W przypadku istniejącej sieci  wirtualnej można opcjonalnie skonfigurować sieciowe grupy zabezpieczeń [w](../virtual-network/network-security-groups-overview.md#network-security-groups) celu filtrowania ruchu sieciowego [w podsieciach.](../virtual-network/tutorial-filter-network-traffic.md) Jeśli chcesz przejść tą trasą lub jeśli już używasz sieciowych sieci, upewnij się, że otwarto porty opisane w tej tabeli dla tych sieciowych sieciowych sieciowych sieci. [](#network-ports-for-ise)

  Po skonfigurowaniu [](../virtual-network/network-security-groups-overview.md#security-rules)reguł zabezpieczeń sieciowej grupy  zabezpieczeń należy użyć protokołów **TCP** i  **UDP.** Można też wybrać opcję Dowolne, aby nie trzeba było tworzyć oddzielnych reguł dla każdego protokołu. Reguły zabezpieczeń sieciowej organizacji zabezpieczeń opisują porty, które należy otworzyć dla adresów IP, które wymagają dostępu do tych portów. Upewnij się, że wszelkie zapory, routery lub inne elementy istniejące między tymi punktami końcowymi również zachowają dostęp do tych portów dla tych adresów IP.

* W przypadku skonfigurowania wymuszonego tunelowania przez zaporę w celu przekierowywania ruchu internetowego zapoznaj się z wymaganiami w zakresie [wymuszonego tunelowania.](#forced-tunneling)

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porty sieciowe używane przez środowisko ISE

W tej tabeli opisano porty, których dostępność wymaga twoja ise, oraz przeznaczenie tych portów. Aby zmniejszyć złożoność podczas konfigurowanie reguł zabezpieczeń, tabela używa tagów usługi, które reprezentują grupy prefiksów adresów IP dla określonej usługi platformy Azure. [](../virtual-network/service-tags-overview.md) Tam, gdzie wspomniano, *wewnętrzne* i zewnętrzne *ise odnoszą* się do punktu końcowego dostępu [wybranego podczas tworzenia ise.](connect-virtual-network-vnet-isolated-environment.md#create-environment) Aby uzyskać więcej informacji, zobacz [Dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> W przypadku wszystkich reguł upewnij się, że ustawiono porty źródłowe na , ponieważ porty źródłowe `*` są efemeralne.

#### <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| Przeznaczenie | Tag usługi źródłowej lub adresy IP | Porty źródłowe | Tag usługi docelowej lub adresy IP | Porty docelowe | Uwagi |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikacja międzysubnetowa w sieci wirtualnej | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Wymagany do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne:** Aby ruch przepływał między *składnikami* w każdej podsieci, upewnij się, że otwarto wszystkie porty w każdej podsieci. |
| Oba: <p>Komunikacja z aplikacją logiki <p><p>Historia przebiegów dla aplikacji logiki| Wewnętrzne ise: <br>**VirtualNetwork** <p><p>Zewnętrzne ise: **Internet lub** zobacz **uwagi** | * | **VirtualNetwork** | 443 | Zamiast używać **tagu usługi** internetowej, możesz określić źródłowy adres IP dla tych elementów: <p><p>— Komputer lub usługa, która wywołuje wyzwalacze żądań lub webhook w aplikacji logiki <p>— Historia przebiegów komputera lub usługi, z której chcesz uzyskać dostęp do aplikacji logiki <p><p>**Ważne:** Zamknięcie lub zablokowanie tego portu uniemożliwia wywoływanie aplikacji logiki, które mają wyzwalacze żądań lub element webhook. Nie można również uzyskać dostępu do danych wejściowych i wyjściowych dla każdego kroku w historii przebiegów. Nie można jednak uniemożliwić dostępu do historii przebiegów aplikacji logiki.|
| Logic Apps — właściwości dynamiczne | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Żądania pochodzą z adresów IP Logic Apps punktu końcowego dostępu przychodzącego [dla](../logic-apps/logic-apps-limits-and-config.md#inbound) tego regionu. <p><p>**Ważne:** Jeśli pracujesz z chmurą Azure Government, tag **usługi LogicAppsManagement** nie będzie działać. Zamiast tego należy podać Logic Apps [ip](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) dla Azure Government. |
| Sprawdzanie kondycji sieci | **LogicApps** | * | **VirtualNetwork** | 454 | Żądania pochodzą z adresów IP Logic Apps [](../logic-apps/logic-apps-limits-and-config.md#inbound) punktu końcowego dostępu do ruchu przychodzącego i [wychodzących adresów IP dla](../logic-apps/logic-apps-limits-and-config.md#outbound) tego regionu. <p><p>**Ważne:** Jeśli pracujesz z chmurą Azure Government, tag **usługi LogicApps** nie będzie działać. Zamiast tego należy podać zarówno adresy [IP](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) ruchu Logic Apps ruchu przychodzącego, jak i adresy IP ruchu [wychodzącego](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) dla Azure Government. |
| Wdrażanie łącznika | **AzureConnectors (Połączenia platformy Azure)** | * | **VirtualNetwork** | 454 | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje niepowodzenie wdrożeń programu ISE oraz uniemożliwia aktualizacje i poprawki łącznika. <p><p>**Ważne:** Jeśli pracujesz z chmurą Azure Government, tag usługi **AzureConnectors** nie będzie działać. Zamiast tego należy podać adresy IP ruchu [wychodzącego](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) łącznika zarządzanego dla Azure Government. |
| App Service zależności zarządzania | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Komunikacja z Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Wewnętrzne ise: 454 <p><p>Zewnętrzne ise: 443 ||
| Oba: <p>Wdrażanie zasad łącznika <p>API Management — punkt końcowy zarządzania | **APIManagement** | * | **VirtualNetwork** | 3443 | W przypadku wdrażania zasad łącznika wymagany jest dostęp do portu w celu wdrożenia i zaktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje niepowodzenie wdrożeń ise oraz uniemożliwia aktualizacje i poprawki łącznika. |
| Uzyskiwanie Azure Cache for Redis wystąpień między wystąpieniami roli | **VirtualNetwork** | * | **VirtualNetwork** | 6379–6383 oraz zobacz **Uwagi**| Aby program ISE działał z Azure Cache for Redis, musisz otworzyć te porty wychodzące i przychodzące opisane w te Azure Cache for Redis FAQ ( Często zadawane [pytania).](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
|||||||

#### <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| Przeznaczenie | Tag usługi źródłowej lub adresy IP | Porty źródłowe | Tag usługi docelowej lub adresy IP | Porty docelowe | Uwagi |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikacja międzysubnetowa w sieci wirtualnej | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | Wymagany do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne:** aby ruch przepływał między składnikami w każdej podsieci, upewnij się, że otwarto wszystkie porty w każdej podsieci.  |
| Komunikacja z aplikacji logiki | **VirtualNetwork** | * | Różni się w zależności od miejsca docelowego | Różni się w zależności od miejsca docelowego | Porty docelowe różnią się w zależności od punktów końcowych dla usług zewnętrznych, z którymi aplikacja logiki musi się komunikować. <p><p>Na przykład port docelowy to 443 dla usługi sieci Web, port 25 dla usługi SMTP, port 22 dla usługi SFTP i tak dalej. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Zależność usługi Azure Storage | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Zarządzanie połączeniami | **VirtualNetwork** | * | **AppService** | 443 ||
| Publikowanie dzienników diagnostycznych & metryk | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL zależności | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Wymagane do publikowania stanu kondycji Resource Health. |
| Zależność od zasad dziennika do centrum zdarzeń i agenta monitorowania | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Uzyskiwanie Azure Cache for Redis wystąpień między wystąpieniami roli | **VirtualNetwork** | * | **VirtualNetwork** | 6379–6383, a także zobacz **Uwagi**| Aby program ISE działał z Azure Cache for Redis, należy otworzyć te porty wychodzące i przychodzące opisane w te Azure Cache for Redis FAQ ( Często [zadawane pytania).](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
| Rozpoznawanie nazw DNS | **VirtualNetwork** | * | Adresy IP dowolnych niestandardowych serwerów systemu nazw domen (DNS) w sieci wirtualnej | 53 | Wymagane tylko w przypadku używania niestandardowych serwerów DNS w sieci wirtualnej |
|||||||

Ponadto należy dodać reguły ruchu wychodzącego dla App Service Environment [(ASE):](../app-service/environment/intro.md)

* Jeśli używasz usługi Azure Firewall, musisz skonfigurować zaporę przy użyciu tagu w pełni kwalifikowanej nazwy domeny [(FQDN)](../firewall/fqdn-tags.md#current-fqdn-tags)App Service Environment (ASE), który zezwala na dostęp wychodzący do ruchu platformy ASE.

* Jeśli używasz urządzenia zapory innego niż Azure Firewall, musisz skonfigurować zaporę przy użyciu [](../app-service/environment/firewall-integration.md#dependencies) wszystkich reguł wymienionych w zależnościach integracji zapory, które są wymagane dla App Service Environment. 

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Wymagania dotyczące wymuszonego tunelowania

W przypadku skonfigurowania lub użycia [wymuszonego tunelowania](../firewall/forced-tunneling.md) przez zaporę musisz zezwolić na dodatkowe zależności zewnętrzne dla twojego programu ISE. Wymuszone tunelowanie umożliwia przekierowywanie ruchu związanego z Internetem do wyznaczonego następnego przeskoku, takiego jak wirtualna sieć prywatna (VPN) lub urządzenie wirtualne, a nie do Internetu, aby można było sprawdzać i przeprowadzać inspekcję wychodzącego ruchu sieciowego.

Jeśli nie zezwolisz na dostęp do tych zależności, wdrożenie środowiska ISE zakończy się niepowodzeniem i wdrożone środowiska ISE przestanie działać.

* Trasy zdefiniowane przez użytkownika

  Aby zapobiec routingowi asymetrycznemu, należy zdefiniować trasę dla każdego adresu IP wymienionego poniżej z **Internetem** jako następnym przeskoku.
  
  * [App Service Environment adresów zarządzania](../app-service/environment/management-addresses.md)
  * [Adresy IP platformy Azure dla łączników w regionie ISE, dostępne w tym pliku pobierania](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager adresów zarządzania](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps adresów przychodzących i wychodzących dla regionu ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Adresy IP platformy Azure dla łączników w regionie ISE, które znajdują się w tym pliku pobierania](https://www.microsoft.com/download/details.aspx?id=56519)

* Punkty końcowe usługi

  Należy włączyć punkty końcowe usługi dla usług Azure SQL, Storage, Service Bus, KeyVault i Event Hubs, ponieważ nie można wysyłać ruchu przez zaporę do tych usług.

*  Inne zależności dla ruchu przychodzącego i wychodzącego

   Zapora *musi zezwalać* na następujące zależności ruchu przychodzącego i wychodzącego:
   
   * [Azure App Service zależności](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Zależności usługi Azure Cache Service](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Zależności API Management Azure](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Tworzenie własnego środowiska ISE

1. W polu [Azure Portal](https://portal.azure.com)azure search wprowadź jako filtr, a następnie `integration service environments` wybierz pozycję Środowiska usługi **integracji**.

   ![Znajdź i wybierz pozycję "Środowiska usługi integracji"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W **okienku Środowiska usługi integracji** wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj", aby utworzyć środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Podaj te szczegóły dla swojego środowiska, a następnie wybierz pozycję **Przejrzyj i utwórz,** na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Subskrypcja platformy Azure do użycia w twoim środowisku |
   | **Grupa zasobów** | Tak | <*Nazwa grupy zasobów platformy Azure*> | Nowa lub istniejąca grupa zasobów platformy Azure, w której chcesz utworzyć środowisko |
   | **Nazwa środowiska usługi integracji** | Tak | <*nazwa środowiska*> | Nazwa ise, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ) i kropki ( `.` ). |
   | **Lokalizacja** | Tak | <*Azure-datacenter-region*> | Region centrum danych platformy Azure, w którym ma zostać wdrożone środowisko |
   | **SKU** | Tak | **Premium** lub **Developer (bez umowy SLA)** | SKU ise do tworzenia i używania. Aby uzyskać informacje o różnicach między tymi jednostkami SKU, [zobacz Jednostki SKU ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) <p><p>**Ważne:** ta opcja jest dostępna tylko podczas tworzenia ise i nie można jej zmienić później. |
   | **Dodatkowa pojemność** | Premium: <br>Tak <p><p>Deweloper: <br>Nie dotyczy | Premium: <br>Od 0 do 10 <p><p>Deweloper: <br>Nie dotyczy | Liczba dodatkowych jednostek przetwarzania do użycia dla tego zasobu ISE. Aby dodać pojemność po utworzeniu, zobacz [Add ISE capacity (Dodawanie pojemności ise).](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) |
   | **Punkt końcowy dostępu** | Tak | **Wewnętrzne** lub **zewnętrzne** | Typ punktów końcowych dostępu do użycia dla twojego programu ISE. Te punkty końcowe określają, czy wyzwalacze żądania lub webhook w aplikacjach logiki w środowisku ISE mogą odbierać wywołania spoza sieci wirtualnej. <p><p>Jeśli na przykład chcesz użyć następujących wyzwalaczy opartych na elementy webhook, upewnij się, że wybierasz pozycję **External (Zewnętrzne):** <p><p>— Azure DevOps <br>— Azure Event Grid <br>— Common Data Service <br>— Office 365 <br>— SAP (wersja ISE) <p><p>Wybór ma również wpływ na sposób wyświetlania i uzyskiwania dostępu do danych wejściowych i wyjściowych w historii uruchomień aplikacji logiki. Aby uzyskać więcej informacji, zobacz [IsE endpoint access (Dostęp do punktu końcowego ise).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Ważne:** punkt końcowy dostępu można wybrać tylko podczas tworzenia programu ISE i nie można później zmienić tej opcji. |
   | **Sieć wirtualna** | Tak | <*Nazwa sieci wirtualnej platformy Azure*> | Sieć wirtualna platformy Azure, w której chcesz wstrzyknąć środowisko, aby aplikacje logiki w tym środowisku mogą uzyskać dostęp do sieci wirtualnej. Jeśli nie masz sieci, najpierw [utwórz sieć wirtualną platformy Azure.](../virtual-network/quick-create-portal.md) <p><p>**Ważne:** to *wstrzyknięcie* można wykonać tylko podczas tworzenia urządzenia ISE. |
   | **Podsieci** | Tak | <*subnet-resource-list*> | Program ISE  wymaga czterech pustych podsieci, które są wymagane do tworzenia i wdrażania zasobów w programie ISE i są używane przez wewnętrzne składniki Logic Apps, takie jak łączniki i buforowanie w celu zachowania wydajności. <p>**Ważne:** przed kontynuowaniem tych kroków należy przejrzeć wymagania dotyczące [podsieci, aby utworzyć podsieci.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   Program ISE  wymaga czterech pustych podsieci, które są potrzebne do tworzenia i wdrażania zasobów w tym programie i są używane przez wewnętrzne składniki Logic Apps, takie jak łączniki i buforowanie w celu wydajności. Po *utworzeniu* środowiska nie można zmienić tych adresów podsieci. Jeśli tworzysz i wdrażasz platformę ISE za pośrednictwem Azure Portal, upewnij się, że te podsieci nie są delegowane do żadnych usług platformy Azure. Jednak w przypadku tworzenia i wdrażania platformy ISE za pomocą interfejsu API REST, [](../virtual-network/manage-subnet-delegation.md) interfejsu AZURE POWERSHELL lub szablonu Azure Resource Manager należy delegować jedną pustą podsieć do usługi `Microsoft.integrationServiceEnvironment` . Aby uzyskać więcej informacji, zobacz [Dodawanie delegowania podsieci.](../virtual-network/manage-subnet-delegation.md)

   Każda podsieć musi spełniać następujące wymagania:

   * Używa nazwy, która rozpoczyna się od znaku alfabetycznego lub podkreślenia (bez cyfr) i nie używa tych znaków: `<` , , , , , , `>` `%` `&` `\\` `?` `/` .

   * Używa formatu [routingu Inter-Domain classless (CIDR).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
   
     > [!IMPORTANT]
     >
     > Nie używaj następujących przestrzeni adresowych IP dla sieci wirtualnej lub podsieci, ponieważ nie są one rozpoznawalne przez Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Używa w `/27` przestrzeni adresowej , ponieważ każda podsieć wymaga 32 adresów. Na przykład `10.0.0.0/27` ma 32 adresy, ponieważ 2<sup>(32–27)</sup> to 2<sup>5</sup> lub 32. Więcej adresów nie zapewni dodatkowych korzyści. Aby dowiedzieć się więcej na temat obliczania adresów, zobacz [Bloki CIDR protokołu IPv4.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)

   * Jeśli używasz usługi [ExpressRoute,](../expressroute/expressroute-introduction.md)musisz utworzyć tabelę tras, która ma następującą trasę i połączyć tabelę z każdą podsiecią używaną przez platformę ISE: [](../virtual-network/manage-route-table.md)

     **Nazwa**: <*route-name*><br>
     **Prefiks adresu:** 0.0.0.0/0<br>
     **Następny przeskok:** Internet

   1. Na liście **Podsieci wybierz** pozycję **Zarządzaj konfiguracją podsieci.**

      ![Zarządzanie konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. W **okienku Podsieci** wybierz pozycję **Podsieć**.

      ![Dodawanie czterech pustych podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. W **okienku Dodaj podsieć** podaj te informacje.

      * **Nazwa:** nazwa podsieci
      * **Zakres adresów (blok CIDR):** zakres podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodawanie szczegółów podsieci](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Gdy skończysz, wybierz opcję **OK**.

   1. Powtórz te kroki dla trzech kolejnych podsieci.

      > [!NOTE]
      > Jeśli podsieci, które próbujesz utworzyć, są nieprawidłowe, w Azure Portal zostanie wyświetlony komunikat, ale nie zablokuje postępu.

   Aby uzyskać więcej informacji na temat tworzenia podsieci, zobacz [Dodawanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

1. Po pomyślnym zweryfikowaniu informacji o platformie ISE na platformie Azure wybierz **pozycję Utwórz,** na przykład:

   ![Po pomyślnej weryfikacji wybierz pozycję "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Platforma Azure rozpoczyna wdrażanie środowiska, co zwykle trwa w ciągu dwóch godzin. Czasami wdrożenie może potrwać do czterech godzin. Aby sprawdzić stan wdrożenia, na pasku narzędzi platformy Azure wybierz ikonę powiadomień, co spowoduje otwarcie okienka powiadomień.

   ![Sprawdzanie stanu wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Jeśli wdrożenie zakończy się pomyślnie, platforma Azure pokaże to powiadomienie:

   ![Wdrażanie zakończyło się pomyślnie](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   W przeciwnym razie postępuj zgodnie z Azure Portal rozwiązywania problemów z wdrażaniem.

   > [!NOTE]
   > Jeśli wdrożenie nie powiedzie się lub usuniesz platformę ISE, udostępnienie podsieci na platformie Azure może potrwać do godziny lub być może dłużej w rzadkich przypadkach. Dlatego może być konieczne zaczekaj, zanim będzie można ponownie użyć tych podsieci w innym programie ISE.
   >
   > W przypadku usunięcia sieci wirtualnej platforma Azure zazwyczaj przed zwolnieniem podsieci zajmuje do dwóch godzin, ale ta operacja może trwać dłużej. 
   > Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
   > Zobacz [Usuwanie sieci wirtualnej.](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)

1. Aby wyświetlić środowisko, wybierz pozycję **Przejdź do zasobu,** jeśli platforma Azure nie zostanie automatycznie przekierowyna do Twojego środowiska po zakończeniu wdrażania.

1. W przypadku programu  ISE, który ma dostęp do zewnętrznego punktu końcowego, musisz utworzyć sieciową grupę zabezpieczeń, jeśli jeszcze jej nie masz, i dodać regułę zabezpieczeń dla ruchu przychodzącego, aby zezwolić na ruch z wychodzących adresów IP łącznika zarządzanego. Aby skonfigurować tę regułę, wykonaj następujące kroki:

   1. W menu programu ISE w obszarze **Ustawienia** wybierz pozycję **Właściwości**.

   1. W **obszarze Wychodzące adresy IP łącznika** skopiuj zakresy publicznych adresów IP, które również są widoczne w tym artykule Limity i konfiguracja [— Wychodzące adresy IP.](../logic-apps/logic-apps-limits-and-config.md#outbound)

   1. Utwórz sieciową grupę zabezpieczeń, jeśli jeszcze jej nie masz.
   
   1. Na podstawie poniższych informacji dodaj regułę zabezpieczeń dla ruchu przychodzącego dla skopiowanych publicznych wychodzących adresów IP. Aby uzyskać więcej informacji, zobacz [Samouczek: filtrowanie ruchu sieciowego za](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group)pomocą sieciowej grupy zabezpieczeń przy użyciu Azure Portal .

      | Przeznaczenie | Tag usługi źródłowej lub adresy IP | Porty źródłowe | Tag usługi docelowej lub adresy IP | Porty docelowe | Uwagi |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Zezwalanie na ruch z wychodzących adresów IP łącznika | <*connector-public-outbound-IP-addresses*> | * | Przestrzeń adresowa dla sieci wirtualnej z podsieciami ISE | * | |
      |||||||

1. Aby sprawdzić kondycję sieci dla środowiska ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Jeśli sieć ise stanie się w złej kondycji, wewnętrzna App Service Environment (ASE), która jest używana przez to urządzenie, może również stać się w złej kondycji. Jeśli stan ase jest w złej kondycji przez więcej niż siedem dni, zostanie wstrzymany. Aby rozwiązać ten problem, sprawdź konfigurację sieci wirtualnej. Rozwiąż wszelkie problemy, które znajdziesz, a następnie uruchom ponownie program ISE. W przeciwnym razie po upływie 90 dni wstrzymane ase zostanie usunięte, a twoje ise stanie się bezużytelne. Dlatego upewnij się, że twoje środowiska ISE są w dobrej kondycji, aby zezwolić na niezbędny ruch.
   > 
   > Więcej informacji można znaleźć w następujących tematach:
   >
   > * [Azure App Service diagnostyki](../app-service/overview-diagnostics.md)
   > * [Rejestrowanie komunikatów dla Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

1. Aby rozpocząć tworzenie aplikacji logiki i innych artefaktów w środowisku ISE, zobacz Dodawanie zasobów [do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Po utworzeniu własnego programu ISE zarządzane łączniki ISE staną się dostępne do użycia, ale nie będą automatycznie wyświetlane w s wyboru łącznika w Projektancie aplikacji logiki. Przed użyciem tych łączników ISE należy ręcznie dodać i wdrożyć te łączniki w programie [ISE,](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) aby były one wyświetlane w Projektancie aplikacji logiki.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej [o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
