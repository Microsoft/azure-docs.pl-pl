---
title: Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure za pomocą ISE
description: Utwórz środowisko usługi integracji (ISE), które może uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fa63380a8e27dcc8f4de414c483f8d8ed2323e7b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234117"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps przy użyciu środowiska usługi integracji (ISE)

W przypadku scenariuszy, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE to środowisko izolowane korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od "globalnej" Logic Apps z wieloma dzierżawcami. Ta separacja również zmniejsza wpływ innych dzierżawców platformy Azure na wydajność aplikacji. ISE udostępnia także własne statyczne adresy IP. Te adresy IP są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, wielodostępnej usłudze.

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

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](../logic-apps/logic-apps-pricing.md).

* [Sieć wirtualna platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli nie masz sieci wirtualnej, Dowiedz się, jak [utworzyć sieć wirtualną platformy Azure](../virtual-network/quick-create-portal.md).

  * Sieć wirtualna musi mieć cztery *puste* podsieci do tworzenia i wdrażania zasobów w ISE. Każda podsieć obsługuje inny składnik Logic Apps używany w ISE. Można utworzyć te podsieci z wyprzedzeniem lub poczekać, aż utworzysz ISE, w którym można tworzyć podsieci w tym samym czasie. Dowiedz się więcej o [wymaganiach podsieci](#create-subnet).

  * Nazwy podsieci muszą zaczynać się znakiem alfabetycznym lub podkreśleniem i nie mogą używać następujących znaków `<`: `>`, `%`, `&` `\\`,, `?`, `/`,. 
  
  * Jeśli chcesz wdrożyć ISE za pomocą szablonu Azure Resource Manager, najpierw upewnij się, że delegowano jedną pustą podsieć do Microsoft. Logic/integrationServiceEnvironment. Nie musisz wykonywać tego delegowania podczas wdrażania za pomocą Azure Portal.

  * Upewnij się, że sieć wirtualna [umożliwia dostęp do usługi ISE](#enable-access) , dzięki czemu ISE może działać prawidłowo i pozostać dostępne.

  * Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md), który zapewnia prywatne połączenie z usługami w chmurze firmy Microsoft, które są obsługiwane przez dostawcę połączeń, musisz [utworzyć tabelę tras](../virtual-network/manage-route-table.md) , która ma następującą trasę i połączyć tę tabelę z każdą podsiecią używaną przez ISE:

    **Nazwa**: <*nazwę trasy*><br>
    **Prefiks adresu**: 0.0.0.0/0<br>
    **Następny przeskok**: Internet

* Jeśli chcesz użyć niestandardowych serwerów DNS dla sieci wirtualnej platformy Azure, [Skonfiguruj te serwery, wykonując następujące czynności](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) przed wdrożeniem ISE w sieci wirtualnej. Aby uzyskać więcej informacji na temat zarządzania ustawieniami serwera DNS, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > W przypadku zmiany ustawień serwera DNS lub serwera DNS należy ponownie uruchomić ISE, aby ISE mógł pobrać te zmiany. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Włączanie dostępu dla środowiska ISE

Gdy używasz ISE z siecią wirtualną platformy Azure, typowy problem z instalacją ma co najmniej jeden blokowany port. Łączniki używane do tworzenia połączeń między systemami ISE i docelowymi mogą również mieć własne wymagania dotyczące portów. Na przykład w przypadku komunikowania się z systemem FTP przy użyciu łącznika FTP port używany w systemie FTP musi być dostępny, na przykład port 21 dla poleceń wysyłania.

Aby upewnić się, że ISE jest dostępny i że aplikacje logiki w tym ISE mogą komunikować się między każdą podsiecią w sieci wirtualnej, [Otwórz porty opisane w tej tabeli dla każdej podsieci](#network-ports-for-ise). Jeśli którykolwiek z wymaganych portów jest niedostępny, ISE nie będzie działał poprawnie.

* Jeśli masz wiele wystąpień ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, wdróż [zaporę platformy Azure](../firewall/overview.md) lub [sieciowe urządzenie wirtualne](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i Roześlij ruch wychodzący za pomocą tej zapory lub wirtualnego urządzenia sieciowego. Następnie można [skonfigurować jeden, wychodzący, publiczny, statyczny i przewidywalny adres IP](connect-virtual-network-vnet-set-up-single-ip-address.md) , który może być używany przez wszystkie wystąpienia ISE w sieci wirtualnej do komunikowania się z systemami docelowymi. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w tych systemach docelowych dla każdego ISEu.

   > [!NOTE]
   > Tego podejścia można użyć dla jednej ISE, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Należy rozważyć, czy dodatkowe koszty zapory lub urządzenia sieci wirtualnej mają sens dla danego scenariusza. Dowiedz się więcej o [cenach zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* W przypadku utworzenia nowej sieci wirtualnej platformy Azure i podsieci bez żadnych ograniczeń nie trzeba konfigurować [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/security-overview.md#network-security-groups) w sieci wirtualnej, aby kontrolować ruch w różnych podsieciach.

* W istniejącej sieci wirtualnej można *Opcjonalnie* skonfigurować sieciowych grup zabezpieczeń przez [Filtrowanie ruchu sieciowego między podsieciami](../virtual-network/tutorial-filter-network-traffic.md). Jeśli chcesz przejść do tej trasy lub jeśli korzystasz już z sieciowych grup zabezpieczeń, upewnij się, że masz [otwarte porty w tej tabeli](#network-ports-for-ise) w sieci wirtualnej, w której masz sieciowych grup zabezpieczeń, lub chcesz skonfigurować sieciowych grup zabezpieczeń.

  > [!NOTE]
  > W przypadku używania [reguł zabezpieczeń sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules)należy używać *zarówno* protokołów TCP, jak i UDP. SIECIOWEJ grupy zabezpieczeń reguły zabezpieczeń zawierają informacje o portach, które muszą być otwarte dla adresów IP, które wymagają dostępu do tych portów. Upewnij się, że wszystkie zapory, routery lub inne elementy, które istnieją między tymi punktami końcowymi, zachowują również dostęp do tych adresów IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porty sieciowe używane przez ISE

W tej tabeli opisano porty w sieci wirtualnej platformy Azure używane przez ISE oraz miejsce, w którym te porty są używane. Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, [Tagi usług](../virtual-network/service-tags-overview.md) w tabeli reprezentują grupy prefiksów adresów IP dla określonej usługi platformy Azure.

> [!IMPORTANT]
> Porty źródłowe są tymczasowe, dlatego upewnij się, że zostały ustawione `*` na dla wszystkich reguł. Gdzie zanotowano, wewnętrzne ISE i zewnętrzna ISE odnoszą się do [punktu końcowego, który został wybrany podczas tworzenia ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Przeznaczenie | Kierunek | Porty docelowe | Tag usługi źródłowej | Docelowy tag usługi | Uwagi |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Komunikacja między podsieciami w ramach sieci wirtualnej | Przychodzące & wychodzące | * | Przestrzeń adresowa sieci wirtualnej, która ma podsieci ISE | Przestrzeń adresowa sieci wirtualnej, która ma podsieci ISE | Wymagany do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne**: w przypadku ruchu między *składnikami* w poszczególnych podsieciach upewnij się, że otwarto wszystkie porty w każdej podsieci. |
| Komunikacja z aplikacją logiki | Przychodzący | 443 | ISE wewnętrzny: <br>VirtualNetwork <p><p>ISE zewnętrzne: <br>Internet <br>(zobacz **uwagi** — kolumna) | VirtualNetwork | Zamiast korzystać z tagu usługi **internetowej** , można określić źródłowy adres IP dla komputera lub usługi, który wywołuje wszystkie wyzwalacze żądań lub elementy webhook w aplikacji logiki. <p><p>**Ważne**: zamknięcie lub zablokowanie tego portu zapobiega WYWOŁYWANiu http przez aplikacje logiki, które mają wyzwalacze żądań. |
| Historia uruchamiania aplikacji logiki | Przychodzący | 443 | ISE wewnętrzny: <br>VirtualNetwork <p><p>ISE zewnętrzne: <br>Internet <br>(zobacz **uwagi** — kolumna) | VirtualNetwork | Zamiast korzystać z tagu usługi **internetowej** , można określić źródłowy adres IP komputera lub usługi, z którego ma zostać wyświetlona historia uruchamiania aplikacji logiki. <p><p>**Ważne**: Chociaż zamknięcie lub blokowanie tego portu nie uniemożliwia wyświetlenia historii uruchamiania, nie można wyświetlić danych wejściowych i wyjściowych dla każdego kroku w tej historii uruchamiania. |
| Logic Apps Designer — właściwości dynamiczne | Przychodzący | 454 | LogicAppsManagement | VirtualNetwork | Żądania pochodzą z Logic Apps adresów IP [ruchu przychodzącego](../logic-apps/logic-apps-limits-and-config.md#inbound) punktu końcowego dostępu dla danego regionu. |
| Wdrożenie łącznika | Przychodzący | 454 | AzureConnectors | VirtualNetwork | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje lub poprawki łącznika. |
| Sprawdzenie kondycji sieci | Przychodzący | 454 | LogicApps | VirtualNetwork | Żądania pochodzą z punktu końcowego dostępu Logic Apps dla adresów IP zarówno dla [ruchu przychodzącego](../logic-apps/logic-apps-limits-and-config.md#inbound) , jak i [wychodzącego](../logic-apps/logic-apps-limits-and-config.md#outbound) . |
| Zależność zarządzania App Service | Przychodzący | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Komunikacja z Traffic Manager platformy Azure | Przychodzący | ISE wewnętrzny: 454 <p><p>ISE zewnętrzne: 443 | AzureTrafficManager | VirtualNetwork | |
| Punkt końcowy zarządzania API Management | Przychodzący | 3443 | APIManagement | VirtualNetwork | |
| Wdrożenie zasad łącznika | Przychodzący | 3443 | APIManagement | VirtualNetwork | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje lub poprawki łącznika. |
| Komunikacja z aplikacji logiki | Wychodzący | 80, 443 | VirtualNetwork | Różni się w zależności od miejsca docelowego | Punkty końcowe usługi zewnętrznej, z którą aplikacja logiki musi się komunikować. |
| Azure Active Directory | Wychodzący | 80, 443 | VirtualNetwork | Usługi azureactivedirectory | |
| Zarządzanie połączeniami | Wychodzący | 443 | VirtualNetwork  | AppService | |
| Publikowanie dzienników diagnostycznych & metryki | Wychodzący | 443 | VirtualNetwork  | AzureMonitor | |
| Zależność usługi Azure Storage | Wychodzący | 80, 443, 445 | VirtualNetwork | Magazyn | |
| Zależność SQL platformy Azure | Wychodzący | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Wychodzący | 1886 | VirtualNetwork | AzureMonitor | Wymagane do publikowania stanu kondycji w Resource Health |
| Zależność od dziennika do zasad usługi Event Hub i agenta monitorowania | Wychodzący | 5672 | VirtualNetwork | EventHub | |
| Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między wystąpieniami roli | Przychodzący <br>Wychodzący | 6379 – 6383 | VirtualNetwork | VirtualNetwork | Ponadto, aby ISE współpracował z usługą Azure cache for Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w pamięci podręcznej platformy Azure dla Redis często zadawane pytania](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Tworzenie własnego środowiska ISE

1. W [Azure Portal](https://portal.azure.com)w głównym polu wyszukiwania platformy Azure wprowadź `integration service environments` jako filtr, a następnie wybierz pozycję **środowiska usługi integracji**.

   ![Znajdowanie i wybieranie "środowisk usługi integracji"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W okienku **środowiska usługi integracji** wybierz pozycję **Dodaj**.

   ![Znajdowanie i wybieranie "środowisk usługi integracji"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Podaj te szczegóły dla danego środowiska, a następnie wybierz pozycję **Przegląd + Utwórz**, na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Subskrypcja platformy Azure do użycia w danym środowisku |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-Name*> | Nowa lub istniejąca Grupa zasobów platformy Azure, w której chcesz utworzyć środowisko |
   | **Nazwa środowiska usługi integracji** | Tak | <*Nazwa środowiska*> | Nazwa ISE, która może zawierać tylko litery, cyfry, łączniki (`-`), podkreślenia (`_`) i kropki (`.`). |
   | **Lokalizacja** | Tak | <*Azure — centrum danych — region*> | Region centrum danych platformy Azure, w którym ma zostać wdrożone środowisko |
   | **Magazyn** | Tak | **Premium** lub **Developer (bez umowy SLA)** | Jednostka SKU ISE do utworzenia i użycia. Aby uzyskać różnice między tymi jednostkami SKU, zobacz [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Ważne**: Ta opcja jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić. |
   | **Dodatkowa pojemność** | Premium: <br>Tak <p><p>Pisał <br>Nie dotyczy | Premium: <br>od 0 do 10 <p><p>Pisał <br>Nie dotyczy | Liczba dodatkowych jednostek przetwarzania, które mają być używane dla tego zasobu ISE. Aby dodać pojemność po utworzeniu, zobacz [Dodawanie pojemności ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Punkt końcowy dostępu** | Tak | **Wewnętrzne** lub **zewnętrzne** | Typ punktów końcowych dostępu, które mają być używane dla ISE. Te punkty końcowe określają, czy wyzwalacze żądań lub elementów webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej. <p><p>Wybór ma także wpływ na sposób wyświetlania i uzyskiwania dostępu do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Ważne**: Ta opcja jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić. |
   | **Sieć wirtualna** | Tak | <*Azure-Virtual-Network-Name*> | Sieć wirtualna platformy Azure, w której chcesz wstrzyknąć środowisko, aby aplikacje logiki w tym środowisku mogły uzyskiwać dostęp do sieci wirtualnej. Jeśli nie masz sieci, [najpierw Utwórz sieć wirtualną platformy Azure](../virtual-network/quick-create-portal.md). <p><p>**Ważne**: to iniekcja można wykonać *tylko* po utworzeniu ISE. |
   | **Podsieci** | Tak | <*podsieć-Lista zasobów*> | ISE wymaga czterech *pustych* podsieci do tworzenia i wdrażania zasobów w środowisku. Aby utworzyć każdą podsieć, [wykonaj kroki opisane w tej tabeli](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   Aby tworzyć i wdrażać zasoby w danym środowisku, ISE muszą mieć cztery *puste* podsieci, które nie są delegowane do żadnej usługi. Każda podsieć obsługuje inny składnik Logic Apps używany w ISE. *Nie* można zmienić tych adresów podsieci po utworzeniu środowiska. Każda podsieć musi spełniać następujące wymagania:

   * Ma nazwę zaczynającą się od litery lub znaku podkreślenia (bez cyfr) i nie używa następujących znaków `<`:, `>`, `%` `&` `\\`,,, `?`,. `/`

   * Używa [formatu routingu bezklasowego (cidr)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) i przestrzeni adresowej klasy B.

   * Używa `/27` w przestrzeni adresowej, ponieważ każda podsieć wymaga 32 adresów. Na przykład ma `10.0.0.0/27` 32 adresów, ponieważ 2<sup>(32-27)</sup> jest 2<sup>5</sup> lub 32. Więcej adresów nie zapewnia dodatkowych korzyści.  Aby dowiedzieć się więcej o obliczaniu adresów, zobacz [bloki protokołu IPv4 w protokole CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

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
   > Jeśli wdrożenie nie powiedzie się lub usuniesz ISE, platforma Azure może upłynąć do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być konieczne odczekanie przed ponownym użyciem tych podsieci w innym ISE.
   >
   > Po usunięciu sieci wirtualnej platforma Azure zazwyczaj zajmie maksymalnie dwie godziny przed zwolnieniem podsieci, ale ta operacja może trwać dłużej. 
   > Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
   > Zobacz [usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Aby wyświetlić swoje środowisko, wybierz pozycję **Przejdź do zasobu** , jeśli platforma Azure nie przejdzie automatycznie do środowiska po zakończeniu wdrażania.

1. Aby sprawdzić kondycję sieci ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Aby rozpocząć tworzenie aplikacji logiki i innych artefaktów w ISE, zobacz [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Zarządzane łączniki ISE, które staną się dostępne po utworzeniu ISE, nie są automatycznie wyświetlane w selektorze łącznika w Projektancie aplikacji logiki. Aby można było używać tych łączników ISE, należy ręcznie [dodać te łączniki do ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) , aby były wyświetlane w Projektancie aplikacji logiki.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
