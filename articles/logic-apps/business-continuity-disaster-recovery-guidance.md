---
title: Ciągłość działania i odzyskiwanie po awarii
description: Zaprojektuj strategię w celu ochrony danych, szybkiego odzyskiwania po zdarzeniach zakłócających, przywracania zasobów wymaganych przez krytyczne funkcje biznesowe oraz zachowania ciągłości działania Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790543"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Ciągłość działania i odzyskiwanie po awarii dla Azure Logic Apps

Aby zmniejszyć wpływ i wpływ nieprzewidywalnych zdarzeń na firmę i klientów, upewnij się, że masz rozwiązanie odzyskiwania po awarii [  ( DR)](https://en.wikipedia.org/wiki/Disaster_recovery) w celu ochrony danych, szybkiego przywracania zasobów, które obsługują krytyczne funkcje biznesowe, i utrzymywania działania operacji w celu zachowania ciągłości działania [  .](https://en.wikipedia.org/wiki/Business_continuity_planning) Na przykład zakłócenia mogą obejmować awarie, straty w podstawowej infrastrukturze lub składnikach, takich jak magazyn, sieć lub zasoby obliczeniowe, nieodwracalne awarie aplikacji, a nawet utrata pełnego centrum danych. Dzięki przygotowaniu rozwiązania zapewniającego ciągłość biznesową i odzyskiwanie po awarii (BCDR, business continuity and disaster recovery) przedsiębiorstwo lub organizacja może szybciej reagować na przerwy, planowane lub nieplanowane oraz ograniczać przestoje dla klientów.

Ten artykuł zawiera wskazówki i strategie BCDR, które można stosować podczas tworzenia zautomatyzowanych przepływów pracy przy użyciu [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Przepływy pracy aplikacji logiki ułatwiają integrowanie i organizowanie danych między aplikacjami, usługami w chmurze i systemami lokalnymi przez zmniejszenie ilości kodu, który trzeba napisać. Planjąc plan BCDR, upewnij się, że rozważasz nie tylko aplikacje logiki, ale również te zasoby platformy Azure, których używasz z aplikacjami logiki:

* [Połączenia,](../connectors/apis-list.md) które tworzysz z aplikacji logiki do innych aplikacji, usług i systemów. Aby uzyskać więcej informacji, zobacz [Połączenia z zasobami](#resource-connections) w dalszej części tego tematu.

* [Lokalne bramy danych, które](../logic-apps/logic-apps-gateway-connection.md) są zasobami platformy Azure, które tworzysz i używasz w aplikacjach logiki do uzyskiwania dostępu do danych w systemach lokalnych. Każdy zasób bramy reprezentuje oddzielną [instalację bramy danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym. Aby uzyskać więcej informacji, zobacz [Lokalne bramy danych](#on-premises-data-gateways) w dalszej części tego tematu.

* [Konta integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na których definiujesz i przechowujesz artefakty, których aplikacje logiki używają w scenariuszach integracji typu [firma-firma (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Na przykład można skonfigurować [odzyskiwanie po awarii między regionami dla kont integracji](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Środowiska usług integracji (ISE, Integration Service Environments),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) w których tworzysz aplikacje logiki uruchamiane w izolowanym Logic Apps uruchomieniowym w ramach sieci wirtualnej platformy Azure. Te aplikacje logiki mogą następnie uzyskać dostęp do zasobów chronionych za zaporą w tej sieci wirtualnej.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Lokalizacja podstawowa i pomocnicza

Każda aplikacja logiki musi określać lokalizację, której chcesz użyć do wdrożenia. Ta lokalizacja jest regionem publicznym na globalnej wielodostępnej platformie Azure, takim jak "Zachodnie stany USA", lub środowiskiem usługi integracji (ISE), które zostało wcześniej utworzone i wdrożone w sieci wirtualnej platformy Azure. Uruchamianie aplikacji logiki na platformie ISE jest podobne do uruchamiania aplikacji logiki w globalnym regionie świadczenia usługi Azure, co oznacza, że strategia odzyskiwania po awarii może mieć zastosowanie do obu scenariuszy. Istnieją jednak inne zagadnienia, takie jak konfigurowanie dostępu do zasobów, które są dostępne tylko dla tych ises.

> [!NOTE]
> Jeśli aplikacja logiki współpracuje również z artefaktami B2B, takimi jak partnerzy handlowi, umowy, schematy, mapy i certyfikaty, które są przechowywane na koncie integracji, zarówno konto integracji, jak i aplikacje logiki muszą określać tę samą lokalizację.

Ta strategia odzyskiwania po awarii koncentruje się na skonfigurowaniu podstawowej aplikacji logiki do pracy w [*trybie failover*](https://en.wikipedia.org/wiki/Failover) w aplikacji logiki w stanie wstrzymania lub tworzenia kopii zapasowej aplikacji logiki w alternatywnej lokalizacji, Azure Logic Apps jest również dostępna. W ten sposób, jeśli podstawowy poniesie straty, zakłócenia lub awarie, pomocniczy może podjąć pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne były już wdrożone i gotowe w lokalizacji alternatywnej.

Jeśli korzystasz z dobrych rozwiązań metodyki DevOps, już używasz szablonów [Azure Resource Manager](../azure-resource-manager/management/overview.md) do definiowania i wdrażania aplikacji logiki i ich zasobów zależnych. Resource Manager szablony zapewniają możliwość użycia pojedynczej definicji wdrożenia, a następnie użycia plików parametrów w celu zapewnienia wartości konfiguracji do użycia dla każdego miejsca docelowego wdrożenia. Ta możliwość oznacza, że możesz wdrożyć tę samą aplikację logiki w różnych środowiskach, na przykład w środowiskach programistycznych, testowych i produkcyjnych. Tę samą aplikację logiki można również wdrożyć w różnych regionach platformy Azure lub na różnych platformach ISE, które obsługują strategie odzyskiwania po awarii, które używają [sparowanych regionów.](../best-practices-availability-paired-regions.md)

W przypadku strategii trybu failover aplikacje logiki i lokalizacje muszą spełniać następujące wymagania:

* Wystąpienie pomocniczej aplikacji logiki ma dostęp do tych samych aplikacji, usług i systemów co podstawowe wystąpienie aplikacji logiki.

* Oba wystąpienia aplikacji logiki mają ten sam typ hosta. W związku z tym oba wystąpienia są wdrażane w regionach na globalnej wielodostępnych platformie Azure lub oba wystąpienia są wdrażane na środowiskach ISE, co umożliwia aplikacjom logiki bezpośredni dostęp do zasobów w sieci wirtualnej platformy Azure. Aby uzyskać najlepsze rozwiązania i więcej informacji na temat sparowanych regionów dla bcdr, zobacz [Business continuity and disaster recovery (BCDR): Azure paired regions](../best-practices-availability-paired-regions.md)( Ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR): regiony sparowane platformy Azure).

  Na przykład lokalizacja podstawowa i pomocnicza muszą być środowiskami ISE, gdy podstawowa aplikacja logiki działa w środowisku ISE i używa łączników w wersji [ISE,](../connectors/managed.md#ise-connectors)akcji HTTP do wywołania zasobów w sieci wirtualnej platformy Azure lub obu tych rozwiązań. W tym scenariuszu dodatkowa aplikacja logiki musi również mieć podobną konfigurację w lokalizacji pomocniczej, co podstawowa aplikacja logiki.

  > [!NOTE]
  > W przypadku bardziej zaawansowanych scenariuszy można mieszać zarówno wielodostępną platformę Azure, jak i platformę ISE jako lokalizacje. Pamiętaj jednak, aby wziąć pod uwagę różnice między uruchamianiem aplikacji logiki na platformie [ISE a wielodostępną platformą Azure.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Jeśli używasz isE, [upewnij się,](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) że są one skalowane w zewnątrz lub mają wystarczającą pojemność do obsługi obciążenia.

#### <a name="example-multi-tenant-azure"></a>Przykład: wielodostępna platforma Azure

W tym przykładzie przedstawiono wystąpienia podstawowej i pomocniczej aplikacji logiki, które w tym scenariuszu są wdrażane w oddzielnych regionach na globalnej wielodostępnym platformie Azure. Pojedynczy szablon [Resource Manager definiuje](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) zarówno wystąpienia aplikacji logiki, jak i zasoby zależne wymagane przez te aplikacje logiki. Oddzielne pliki parametrów określają wartości konfiguracji do użycia dla każdej lokalizacji wdrożenia:

![Podstawowe i pomocnicze wystąpienia aplikacji logiki w oddzielnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Przykład: Środowisko usługi integracji

W tym przykładzie przedstawiono poprzednie wystąpienia podstawowej i pomocniczej aplikacji logiki, ale wdrożone w oddzielnych rozwiązaniach ISE. Pojedynczy szablon Resource Manager definiuje zarówno wystąpienia aplikacji logiki, zasoby zależne wymagane przez te aplikacje logiki, jak i rozwiązania ISE jako lokalizacje wdrożenia. Oddzielne pliki parametrów definiują wartości konfiguracji do użycia podczas wdrażania w każdej lokalizacji:

![Podstawowe i pomocnicze aplikacje logiki w różnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Połączenia z zasobami

Azure Logic Apps udostępnia [](../connectors/apis-list.md) wbudowane wyzwalacze i akcje oraz setki zarządzanych łączników, których aplikacja logiki może używać do pracy z innymi aplikacjami, usługami, systemami i innymi zasobami, takimi jak konta usługi Azure Storage, bazy danych usługi SQL Server, służbowe konta e-mail itp. Jeśli aplikacja logiki wymaga dostępu do tych zasobów, tworzysz połączenia, które uwierzytelniają dostęp do tych zasobów. Każde połączenie jest oddzielnym zasobem platformy Azure, który istnieje w określonej lokalizacji i nie może być używany przez zasoby w innych lokalizacjach.

W przypadku strategii odzyskiwania po awarii należy wziąć pod uwagę lokalizacje, w których istnieją zasoby zależne względem wystąpień aplikacji logiki:

* Wystąpienie podstawowe i zasoby zależne istnieją w różnych lokalizacjach. W takim przypadku wystąpienie pomocnicze może połączyć się z tym samym zasobem zależnym lub punktami końcowymi. Należy jednak utworzyć połączenia przeznaczone specjalnie dla wystąpienia pomocniczego. W ten sposób, jeśli lokalizacja podstawowa stanie się niedostępna, nie będzie to mieć wpływu na połączenia pomocnicze.

  Załóżmy na przykład, że Podstawowa aplikacja logiki łączy się z usługą zewnętrzną, taką jak Salesforce. Zazwyczaj dostępność i lokalizacja usługi zewnętrznej są niezależne od dostępności aplikacji logiki. W takim przypadku wystąpienie pomocnicze może połączyć się z tą samą usługą, ale powinno mieć własne połączenie.

* Zarówno wystąpienie podstawowe, jak i zasoby zależne istnieją w tej samej lokalizacji. W takim przypadku zasoby zależne powinny mieć kopie zapasowe lub zreplikowane wersje w innej lokalizacji, aby wystąpienie pomocnicze nadal może uzyskać dostęp do tych zasobów.

  Załóżmy na przykład, że Podstawowa aplikacja logiki łączy się z usługą, która znajduje się w tej samej lokalizacji lub regionie, na przykład Azure SQL Database. Jeśli cały region stanie się niedostępny, usługa Azure SQL Database w tym regionie również będzie prawdopodobnie niedostępna. W takim przypadku wystąpienie pomocnicze ma używać replikowanej lub zapasowej bazy danych wraz z osobnym połączeniem z tą bazą danych.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokalne bramy danych

Jeśli twoja aplikacja logiki działa na wielodostępnych platformach Azure i wymaga dostępu do zasobów [](../logic-apps/logic-apps-gateway-install.md) lokalnych, takich jak bazy danych usługi SQL Server, musisz zainstalować lokalną bramę danych na komputerze lokalnym. Następnie możesz utworzyć zasób bramy danych w Azure Portal, aby aplikacja logiki może używać bramy podczas tworzenia połączenia z zasobem.

Zasób bramy danych jest skojarzony z lokalizacją lub regionem świadczenia usługi Azure, podobnie jak zasób aplikacji logiki. W strategii odzyskiwania po awarii upewnij się, że brama danych pozostaje dostępna do użycia przez aplikację logiki. Możesz [włączyć wysoką dostępność bramy, jeśli](../logic-apps/logic-apps-gateway-install.md#high-availability) masz wiele instalacji bramy.

> [!NOTE]
> Jeśli aplikacja logiki działa w środowisku usługi integracji (ISE) i używa tylko łączników w wersji ISE dla lokalnych źródeł danych, brama danych nie jest potrzebna, ponieważ łączniki środowiska ISE zapewniają bezpośredni dostęp do zasobu lokalnego.
>
> Jeśli dla zasobu lokalnego, którego chcesz użyć, nie jest dostępny żaden łącznik o wersji ISE, aplikacja logiki może nadal tworzyć połączenie przy użyciu łącznika bez platformy ISE, który działa na globalnej wielodostępnej platformie Azure, a nie w środowisku ISE. Jednak to połączenie wymaga lokalnej bramy danych.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Role aktywne-aktywne i aktywne/pasywne

Lokalizację podstawową i pomocniczą można skonfigurować tak, aby wystąpienia aplikacji logiki w tych lokalizacjach pełniły następujące role:

| Rola podstawowa i pomocnicza | Opis |
|------------------------|-------------|
| *Aktywny-aktywny* | Podstawowe i pomocnicze wystąpienia aplikacji logiki w obu lokalizacjach aktywnie obsługują żądania, korzystając z jednego z następujących wzorców: <p><p>- *Równoważenie obciążenia:* oba wystąpienia mogą nasłuchiwać punktu końcowego i w razie potrzeby równoważyć obciążenie ruchu do każdego wystąpienia. <p>- *Konkurujący odbiorcy:* Oba wystąpienia mogą działać jako konkurujący odbiorcy, aby wystąpienia konkurowały o komunikaty z kolejki. Jeśli jedno wystąpienie ulegnie awarii, inne wystąpienie przejmie obciążenie. |
| *Aktywne/pasywne* | Wystąpienie podstawowej aplikacji logiki aktywnie obsługuje całe obciążenie, a wystąpienie pomocnicze jest pasywne (wyłączone lub nieaktywne). Pomocniczy oczekuje na sygnał, że podstawowy jest niedostępny lub nie działa z powodu zakłóceń lub awarii, i przejmuje obciążenie jako aktywne wystąpienie. |
| Połączenie | Niektóre aplikacje logiki odgrywają rolę aktywne-aktywne, a inne aplikacje logiki pełnią rolę aktywne/pasywne. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Przykłady aktywne/aktywne

Te przykłady pokazują konfigurację aktywne-aktywne, w której oba wystąpienia aplikacji logiki aktywnie obsługują żądania lub komunikaty. Inny system lub usługa dystrybuuje żądania lub komunikaty między wystąpieniami, na przykład jedną z tych opcji:

* "Fizyczny" element równoważenia obciążenia, taki jak sprzęt, który kieruje ruchem

* "Soft" load balancer, taki [jak Azure Load Balancer](../load-balancer/load-balancer-overview.md) lub [Azure API Management.](../api-management/api-management-key-concepts.md) Za API Management można określić zasady, które określają sposób równoważenia obciążenia ruchu przychodzącego. Możesz też użyć usługi obsługującej śledzenie stanu, na przykład [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Chociaż w tym przykładzie Azure Load Balancer, można użyć opcji najlepiej dopasowanej do potrzeb scenariusza:

  ![Konfiguracja "aktywny-aktywny" z zastosowaniem usługi równoważenia obciążenia lub usługi stanowej](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Każde wystąpienie aplikacji logiki działa jako konsument i oba wystąpienia konkurują o komunikaty z kolejki:

  ![Konfiguracja "aktywny-aktywny" z zastosowaniem "konkurujących odbiorców"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Przykłady aktywne/pasywne

W tym przykładzie przedstawiono konfigurację aktywne/pasywne, w której wystąpienie podstawowej aplikacji logiki jest aktywne w jednej lokalizacji, podczas gdy wystąpienie pomocnicze pozostaje nieaktywne w innej lokalizacji. Jeśli w podstawowym przypadku wystąpi zakłócenie lub awaria, operator może uruchomić skrypt, który aktywuje pomocniczy element w celu podjęcia działania na obciążeniu.

![Konfiguracja "aktywne/pasywne" z zastosowaniem "konkurujących odbiorców"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Połączenie z trybem aktywny-aktywny i aktywny-pasywny

W tym przykładzie pokazano połączoną konfigurację, w której lokalizacja podstawowa ma zarówno aktywne wystąpienia aplikacji logiki, jak i lokalizację pomocniczą z wystąpieniami aplikacji logiki aktywne/pasywne. Jeśli w lokalizacji podstawowej wystąpi zakłócenie lub awaria, aktywna aplikacja logiki w lokalizacji pomocniczej, która już obsługiwa częściowe obciążenie, może przejąć całe obciążenie.

* W lokalizacji podstawowej aktywna aplikacja logiki nasłuchuje komunikatów w kolejce usługi Azure Service Bus, a inna aktywna aplikacja logiki sprawdza wiadomości e-mail przy użyciu wyzwalacza sondowania usługi Office 365 Outlook.

* W lokalizacji pomocniczej aktywna aplikacja logiki współpracuje z aplikacją logiki w lokalizacji podstawowej, nasłuchując komunikatów z tej samej kolejki Service Bus i konkurując o nie. W międzyczasie pasywna nieaktywna aplikacja logiki czeka w stanie wstrzymania,  aby sprawdzić wiadomości e-mail, gdy lokalizacja podstawowa stanie się niedostępna, ale jest wyłączona, aby uniknąć ponownego odczytania wiadomości e-mail.

![Kombinacja "Aktywne/pasywne" i "aktywne/pasywne" z wyzwalaczami cyklicznymi](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stan i historia aplikacji logiki

Gdy aplikacja logiki zostanie wyzwolona i zacznie działać, jej stan będzie przechowywany w tej samej lokalizacji, w której aplikacja została uruchomiona, i nie będzie można jej przenieść do innej lokalizacji. Jeśli wystąpi awaria lub zakłócenie, wszystkie w toku wystąpienia przepływu pracy zostaną porzucone. Po skonfigurowaniu lokalizacji podstawowej i dodatkowej nowe wystąpienia przepływu pracy zaczynają działać w lokalizacji pomocniczej.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Zmniejszanie porzuconych wystąpień w toku

Aby zminimalizować liczbę porzuconych wystąpień przepływu pracy w toku, można wybrać spośród różnych wzorców komunikatów, które można zaimplementować, na przykład:

* [Naprawiono wzorzec przecłania routingu](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ten wzorzec komunikatów przedsiębiorstwa, który dzieli proces biznesowy na mniejsze etapy. Dla każdego etapu należy skonfigurować aplikację logiki, która obsługuje obciążenie dla tego etapu. Aby komunikować się ze sobą, aplikacje logiki używają asynchronicznego protokołu obsługi komunikatów, takiego Azure Service Bus kolejki lub tematy. Podzielenie procesu na mniejsze etapy zmniejsza liczbę procesów biznesowych, które mogą zostać zablokowane w wystąpieniu aplikacji logiki, które zakończyło się niepowodzeniem. Aby uzyskać więcej ogólnych informacji na temat tego wzorca, zobacz [Wzorce integracji dla przedsiębiorstw — przesunięcie routingu](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  W tym przykładzie przedstawiono wzorzec przepływu routingu, w którym każda aplikacja logiki reprezentuje etap i używa kolejki Service Bus do komunikowania się z następną aplikacją logiki w procesie.

  ![Podzielenie procesu biznesowego na etapy reprezentowane przez aplikacje logiki, które komunikują się ze sobą przy użyciu Azure Service Bus kolejek](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Jeśli zarówno podstawowe, jak i pomocnicze wystąpienia aplikacji logiki w swoich [](/azure/architecture/patterns/competing-consumers) lokalizacjach mają ten sam wzorzec routingu, możesz zaimplementować wzorzec konkurujących odbiorców, ustawiając role [aktywne/aktywne](#roles) dla tych wystąpień.

* [Wzorzec menedżera procesów (brokera)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Wzorzec podglądu i blokady bez limitu czasu](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Dostęp do historii wyzwalaczy i przebiegów

Aby uzyskać więcej informacji na temat poprzednich wykonań przepływu pracy aplikacji logiki, możesz przejrzeć historię wyzwalaczy i uruchomień aplikacji. Historia wykonywania aplikacji logiki jest przechowywana w tej samej lokalizacji lub regionie, w którym uruchomiono tę aplikację logiki, co oznacza, że nie można migrować tej historii do innej lokalizacji. Jeśli wystąpienie podstawowe ulegnie awarii do wystąpienia pomocniczego, możesz uzyskać dostęp do wyzwalacza każdego wystąpienia i uruchamiać historię tylko w odpowiednich lokalizacjach, w których te wystąpienia są uruchamiane. Możesz jednak uzyskać niezależne od lokalizacji informacje o historii aplikacji logiki, ustawiając aplikacje logiki do wysyłania zdarzeń diagnostycznych do obszaru roboczego usługi Azure Log Analytics. Następnie możesz przejrzeć kondycję i historię aplikacji logiki uruchamianych w wielu lokalizacjach.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Wskazówki dotyczące typu wyzwalacza

Typ wyzwalacza, który jest dostępny w aplikacjach logiki, określa opcje konfiguracji aplikacji logiki w różnych lokalizacjach w strategii odzyskiwania po awarii. Poniżej znajdują się dostępne typy wyzwalaczy, których można używać w aplikacjach logiki:

* [Wyzwalacz cyklu](#recurrence-trigger)
* [Wyzwalacz sondowania](#polling-trigger)
* [Wyzwalacz żądania](#request-trigger)
* [Wyzwalacz webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Wyzwalacz cyklu

Wyzwalacz **Cykl** jest niezależny od dowolnej konkretnej usługi lub punktu końcowego i jest wyzwalany wyłącznie na podstawie określonego harmonogramu i bez innych kryteriów, na przykład:

* Stała częstotliwość i interwał, na przykład co 10 minut
* Bardziej zaawansowany harmonogram, na przykład ostatni poniedziałek każdego miesiąca o godzinie 17:00

Gdy aplikacja logiki rozpoczyna się od wyzwalacza Cyklicznie, należy skonfigurować wystąpienia podstawowej i pomocniczej aplikacji logiki z rolami [aktywne/pasywne.](#roles) Aby zmniejszyć cel czasu odzyskiwania (RTO, recovery *time objective),* który odnosi się do docelowego czasu trwania przywracania procesu biznesowego po przerwie lub awarii, możesz skonfigurować wystąpienia aplikacji logiki za pomocą kombinacji ról [aktywne/pasywne](#roles) i pasywne/aktywne. [](#roles) W tej konfiguracji harmonogram jest podzielony między lokalizacje.

Załóżmy na przykład, że masz aplikację logiki, która musi być uruchamiana co 10 minut. Aplikacje logiki i lokalizacje można skonfigurować tak, aby jeśli lokalizacja podstawowa stała się niedostępna, lokacja pomocnicza może przejąć pracę:

![Kombinacja "aktywny-pasywny" i "pasywny-aktywny" z zastosowaniem wyzwalaczy cyklicznych](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* W lokalizacji podstawowej skonfiguruj role [aktywne/pasywne dla](#roles) tych aplikacji logiki:

  * W  przypadku aktywnej włączonej aplikacji logiki ustaw wyzwalacz Cykl tak, aby rozpoczynał się w górnej części godziny i powtarzał się co 20 minut, na przykład 9:00, 9:20 i tak dalej.

  * W  przypadku pasywnej wyłączonej aplikacji logiki ustaw wyzwalacz Cykl na ten sam harmonogram, ale zacznij od 10 minut po godzinie i powtarzaj go co 20 minut, na przykład 9:10, 9:30 i tak dalej.

* W lokalizacji pomocniczej skonfiguruj ustawienie [passive-active dla](#roles) tych aplikacji logiki:

  * W  przypadku pasywnej wyłączonej aplikacji logiki ustaw wyzwalacz Cykl na taki sam harmonogram jak aktywna aplikacja logiki w lokalizacji podstawowej, która znajduje się na początku godziny, i powtarzaj to co 20 minut, na przykład 9:00, 9:10 i tak dalej.

  * W  przypadku aktywnej włączonej aplikacji logiki ustaw wyzwalacz Cykl na taki sam harmonogram jak pasywna aplikacja logiki w lokalizacji podstawowej, który ma rozpoczynać się o 10 minut po godzinie i powtarzać co 20 minut, na przykład 9:10, 9:20 i tak dalej.

Teraz, jeśli zdarzenie zakłócające występuje w lokalizacji podstawowej, aktywuj pasywną aplikację logiki w lokalizacji alternatywnej. W ten sposób, jeśli znalezienie błędu zajmuje trochę czasu, ta konfiguracja ogranicza liczbę pominiętych powtórzeń w czasie tego opóźnienia.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Wyzwalacz sondowania

Aby regularnie sprawdzać, czy nowe dane do przetworzenia są dostępne  z określonej usługi lub punktu końcowego, aplikacja logiki może używać wyzwalacza sondowania, który wielokrotnie wywołuje usługę lub punkt końcowy na podstawie stałego harmonogramu cyklu. Dane zapewniane przez usługę lub punkt końcowy mogą mieć jeden z następujących typów:

* Dane statyczne, które opisują dane, które są zawsze dostępne do odczytu
* Dane nietrwałe, które opisują dane, które nie są już dostępne po odczytaniu

Aby uniknąć wielokrotnego odczytywania tych samych danych, aplikacja logiki musi pamiętać, które dane były wcześniej odczytywane przez utrzymywanie stanu po stronie klienta lub po stronie serwera, usługi lub systemu.

* Aplikacje logiki, które pracują ze stanem po stronie klienta, używają wyzwalaczy, które mogą zachować stan.

  Na przykład wyzwalacz, który odczytuje nową wiadomość z skrzynki odbiorczej poczty e-mail, wymaga, aby wyzwalacz zapamiętał ostatnio odczytaną wiadomość. W ten sposób wyzwalacz uruchamia aplikację logiki tylko wtedy, gdy pojawi się następny nieprzeczytany komunikat.

* Aplikacje logiki, które działają z serwerem, usługą lub stanem po stronie systemu, używają wartości właściwości lub ustawień po stronie serwera, usługi lub systemu.

  Na przykład wyzwalacz oparty na zapytaniach, który odczytuje wiersz z bazy danych, wymaga, aby wiersz zawierał kolumnę ustawioną `isRead` na `FALSE` wartość . Za każdym razem, gdy wyzwalacz odczytuje wiersz, aplikacja logiki aktualizuje ten wiersz, zmieniając `isRead` kolumnę z `FALSE` na `TRUE` .

  To podejście po stronie serwera działa podobnie w Service Bus kolejkach lub tematach, które mają semantykę kolejkowania, gdzie wyzwalacz może odczytywać i blokować komunikat podczas przetwarzania komunikatu przez aplikację logiki. Po zakończeniu przetwarzania przez aplikację logiki wyzwalacz usuwa komunikat z kolejki lub tematu.

Z perspektywy odzyskiwania po awarii podczas konfiguracji wystąpienia podstawowego i pomocniczego aplikacji logiki upewnij się, że te zachowania są uwzględniane w zależności od tego, czy aplikacja logiki śledzi stan po stronie klienta, czy po stronie serwera:

* W przypadku aplikacji logiki, która działa ze stanem po stronie klienta, upewnij się, że aplikacja logiki nie odczytuje tego samego komunikatu więcej niż jeden raz. Tylko jedna lokalizacja może mieć aktywne wystąpienie aplikacji logiki w określonym czasie. Upewnij się, że wystąpienie aplikacji logiki w lokalizacji alternatywnej jest nieaktywne lub wyłączone, dopóki wystąpienie podstawowe nie zostanie przejecha do lokalizacji alternatywnej.

  Na przykład wyzwalacz programu Office 365 Outlook zachowuje stan po stronie klienta i śledzi znacznik czasu dla ostatnio odczytywanej wiadomości e-mail, aby uniknąć odczytywania duplikatów.

* W przypadku aplikacji logiki, która działa ze stanem po stronie serwera, można skonfigurować wystąpienia aplikacji logiki tak, aby pełniły role [aktywne/aktywne,](#roles) w których działają jako konkurujący odbiorcy, lub role [aktywne/pasywne,](#roles) gdzie alternatywne wystąpienie czeka, aż wystąpienie podstawowe przejedzie w tryb pracy w trybie pracy w trybie alternatywnym.

  Na przykład odczytywanie z kolejki komunikatów, takiej jak kolejka Azure Service Bus, używa stanu po stronie serwera, ponieważ usługa kolejkowania utrzymuje blokady komunikatów, aby uniemożliwić innym klientom odczytywanie tych samych komunikatów.

  > [!NOTE]
  > Jeśli aplikacja logiki musi odczytywać komunikaty w określonej kolejności, na przykład z kolejki Service Bus, możesz użyć wzorca konkurującego klienta, ale tylko w połączeniu z sesjami programu Service Bus, co jest również znane jako wzorzec sekwencyjnego [ *konwoju*](/azure/architecture/patterns/sequential-convoy). W przeciwnym razie należy skonfigurować wystąpienia aplikacji logiki z rolami aktywne/pasywne.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacz żądania

Wyzwalacz **Żądanie** sprawia, że aplikacja logiki jest wywoływalna z innych aplikacji, usług i systemów i jest zwykle używana do zapewnienia tych możliwości:

* Bezpośredni interfejs API REST dla aplikacji logiki, który inne osoby mogą wywołać

  Na przykład użyj wyzwalacza Żądanie, aby uruchomić aplikację logiki, aby inne aplikacje logiki mogą wywołać wyzwalacz przy użyciu akcji Wywołaj przepływ pracy **— Logic Apps** logiki.

* Mechanizm [webhook](#webhook-trigger) lub wywołania zwrotnego dla aplikacji logiki

* Sposób ręcznego uruchamiania operacji lub procedur użytkownika w celu wywołania aplikacji logiki, na przykład za pomocą skryptu programu PowerShell, który wykonuje określone zadanie

Z perspektywy odzyskiwania po awarii wyzwalacz Żądania jest odbiornikiem pasywnym, ponieważ aplikacja logiki nie robi żadnej pracy i czeka, aż inna usługa lub system jawnie wywoła wyzwalacz. Jako pasywny punkt końcowy możesz skonfigurować wystąpienia podstawowe i pomocnicze w następujący sposób:

* [Aktywny-aktywny:](#roles)oba wystąpienia aktywnie obsługują żądania lub wywołania. Wywołujący lub router równoważy lub dystrybuuje ruch między tymi wystąpieniami.

* [Aktywne/pasywne:](#roles)tylko wystąpienie podstawowe jest aktywne i obsługuje całą pracę, podczas gdy wystąpienie pomocnicze czeka na podstawowe zakłócenia lub awarie. Wywołujący lub router określa, kiedy należy wywołać wystąpienie pomocnicze.

Jako zalecana architektura możesz użyć usługi Azure API Management jako serwera proxy dla aplikacji logiki, które używają wyzwalaczy żądania. API Management zapewnia wbudowaną odporność między regionami i możliwość rozsyłania ruchu [między wieloma punktami końcowymi.](../api-management/api-management-howto-deploy-multi-region.md)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Wyzwalacz webhook

Wyzwalacz *webhook* zapewnia aplikacji logiki możliwość subskrybowania usługi przez przekazanie adresu *URL* wywołania zwrotnego do tej usługi. Aplikacja logiki może następnie nasłuchiwać i czekać na określone zdarzenie w tym punkcie końcowym usługi. W przypadku wystąpienia zdarzenia usługa wywołuje wyzwalacz webhook przy użyciu adresu URL wywołania zwrotnego, który następnie uruchamia aplikację logiki. Po włączeniu wyzwalacz webhook subskrybuje usługę. Po wyłączeniu wyzwalacz anuluje subskrypcję usługi.

Z perspektywy odzyskiwania po awarii skonfiguruj wystąpienia podstawowe i pomocnicze, które używają wyzwalaczy elementy webhook do odtwarzania ról aktywne/pasywne, ponieważ tylko jedno wystąpienie powinno odbierać zdarzenia lub komunikaty z subskrybowanego punktu końcowego.

## <a name="assess-primary-instance-health"></a>Ocena kondycji wystąpienia podstawowego

Aby strategia odzyskiwania po awarii działała, rozwiązanie musi mieć sposoby na wykonanie tych zadań:

* [Sprawdzanie dostępności wystąpienia podstawowego](#check-primary-availability)
* [Monitorowanie kondycji wystąpienia podstawowego](#monitor-primary-health)
* [Aktywowanie wystąpienia pomocniczego](#activate-secondary)

W tej sekcji opisano jedno z rozwiązań, których można użyć od podstaw do własnego projektu. Oto ogólny przegląd wizualizacji dla tego rozwiązania:

![Tworzenie aplikacji logiki watchdog, która monitoruje aplikację logiki kontroli kondycji w lokalizacji podstawowej](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Sprawdzanie dostępności wystąpienia podstawowego

Aby określić, czy wystąpienie podstawowe jest dostępne, uruchomione i może działać, możesz utworzyć aplikację logiki "health-check", która znajduje się w tej samej lokalizacji co wystąpienie podstawowe. Następnie możesz wywołać tę aplikację kontroli kondycji z lokalizacji alternatywnej. Jeśli aplikacja kontroli kondycji pomyślnie odpowie, podstawowa infrastruktura usługi Azure Logic Apps w tym regionie jest dostępna i działa. Jeśli aplikacja do sprawdzania kondycji nie odpowie, można założyć, że lokalizacja nie jest już w dobrej kondycji.

W tym zadaniu utwórz podstawową aplikację logiki sprawdzania kondycji, która wykonuje następujące zadania:

1. Odbiera wywołanie z aplikacji watchdog przy użyciu wyzwalacza Żądania.

1. Odpowiedz ze stanem wskazującym, czy sprawdzona aplikacja logiki nadal działa przy użyciu akcji Odpowiedź.

   > [!IMPORTANT]
   > Aplikacja logiki kontroli kondycji musi używać akcji Odpowiedź, aby aplikacja odpowiadała synchronicznie, a nie asynchronicznie.

1. Opcjonalnie, aby dodatkowo określić, czy lokalizacja podstawowa jest w dobrej kondycji, można w tym miejscu wywłasz sprawdzić kondycję innych usług, które współdziałają z docelową aplikacją logiki. Po prostu rozwiń aplikację logiki kontroli kondycji, aby ocenić kondycję również dla tych innych usług.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Tworzenie aplikacji logiki usługi Watchdog

Aby monitorować kondycję wystąpienia podstawowego i wywołać aplikację logiki sprawdzania kondycji, utwórz aplikację logiki "watchdog" w lokalizacji *alternatywnej.* Na przykład można skonfigurować aplikację logiki usługi Watchdog tak, aby w przypadku niepowodzenia wywołania logiki kontroli kondycji usługa watchdog wysyłała alert do zespołu ds. operacji, aby mogli zbadać awarię i dlaczego wystąpienie podstawowe nie odpowiada.

> [!IMPORTANT]
> Upewnij się, że aplikacja logiki usługi Watchdog znajduje się w lokalizacji, *która różni się od lokalizacji podstawowej*. Jeśli usługa Logic Apps w lokalizacji podstawowej ma problemy, aplikacja logiki usługi Watchdog może nie być uruchamiana.

Dla tego zadania w lokalizacji pomocniczej utwórz aplikację logiki usługi Watchdog, która wykonuje następujące zadania:

1. Uruchamiany na podstawie stałego lub zaplanowanego cyklu przy użyciu wyzwalacza Cykl.

   Można ustawić cykl na wartość, która jest poniżej poziomu tolerancji dla celu czasu odzyskiwania (RTO).

1. Wywołaj aplikację logiki sprawdzania kondycji w lokalizacji podstawowej przy użyciu akcji HTTP, na przykład:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktywowanie wystąpienia pomocniczego

Aby automatycznie aktywować wystąpienie pomocnicze, możesz utworzyć aplikację logiki, która wywołuje interfejs API zarządzania, taki jak łącznik usługi [Azure Resource Manager,](/connectors/arm/) w celu aktywowania odpowiednich aplikacji logiki w lokalizacji pomocniczej. Możesz rozszerzyć aplikację usługi Watchdog, aby wywołać tę aplikację logiki aktywacji po określonej liczbie błędów.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Zbieranie danych diagnostycznych

Możesz skonfigurować rejestrowanie dla przebiegów aplikacji logiki i wysłać wynikowe dane diagnostyczne do usług takich jak Azure Storage, Azure Event Hubs i Azure Log Analytics w celu dalszej obsługi i przetwarzania.

* Jeśli chcesz używać tych danych z usługą Azure Log Analytics, możesz udostępnić dane dla lokalizacji podstawowej  i pomocniczej, ustawiając ustawienia diagnostyczne aplikacji logiki i wysyłając dane do wielu obszarów roboczych usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Set up Azure Monitor logs and collect diagnostics data for Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Jeśli chcesz wysłać dane do usługi Azure Storage lub Azure Event Hubs, możesz udostępnić dane dla lokalizacji podstawowej i pomocniczej, ustawiając nadmiarowość geograficzną. Więcej informacji można znaleźć w następujących artykułach:<p>

  * [Azure Blob Storage odzyskiwania po awarii i trybu failover konta](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geograficznego odzyskiwania po awarii](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Następne kroki

* [Omówienie odporności platformy Azure](/azure/architecture/framework/resiliency/overview)
* [Lista kontrolna dotycząca odporności dla określonych usług platformy Azure](/azure/architecture/checklist/resiliency-per-service)
* [Zarządzanie danymi w celu zapewnienia odporności na platformie Azure](/azure/architecture/framework/resiliency/data-management)
* [Tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Odzyskiwanie po obejmujących cały region zakłóceniach usługi](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Umowy dotyczące poziomu usług (SLA) firmy Microsoft dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/)
