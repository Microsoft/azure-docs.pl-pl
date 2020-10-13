---
title: Ciągłość działania i odzyskiwanie po awarii
description: Zaprojektuj strategię do ochrony danych, szybko Odzyskaj ze zdarzeń zakłócających, Przywróć zasoby wymagane przez krytyczne funkcje biznesowe i Zachowaj ciągłość biznesową dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658201"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Ciągłość działania i odzyskiwanie po awarii dla Azure Logic Apps

Aby zmniejszyć wpływ i wpływ na nieprzewidywalne zdarzenia w firmie i klientach, upewnij się, że istnieje rozwiązanie [ *odzyskiwania po awarii* (Dr)](https://en.wikipedia.org/wiki/Disaster_recovery) , dzięki któremu możesz chronić dane, szybko przywracać zasoby obsługujące krytyczne funkcje biznesowe i utrzymywać działania, aby zachować [ *ciągłość działalności biznesowej* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Zakłócenia mogą na przykład obejmować awarie, straty w podstawowej infrastrukturze lub składnikach, takie jak magazyn, sieć lub zasoby obliczeniowe, niemożliwe do odzyskania aplikacje, a nawet pełne utratę centrum danych. Dzięki przygotowaniu ciągłości biznesowej i odzyskiwania po awarii (BCDR) firma lub organizacja może szybciej reagować na przerwy, planowane lub nieplanowane i zmniejszać czas przestoju dla klientów.

Ten artykuł zawiera wskazówki i strategie BCDR, które można stosować podczas tworzenia zautomatyzowanych przepływów pracy przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Przepływy pracy aplikacji logiki ułatwiają łatwą integrację i organizowanie danych między aplikacjami, usługami w chmurze i systemami lokalnymi przez zredukowanie ilości kodu, który trzeba napisać. Planując BCDR, pamiętaj, aby nie tylko aplikacje logiki, ale również te zasoby platformy Azure, które są używane z aplikacjami logiki:

* [Połączenia](../connectors/apis-list.md) tworzone z aplikacji logiki do innych aplikacji, usług i systemów. Aby uzyskać więcej informacji, zobacz [połączenia z zasobami](#resource-connections) w dalszej części tego tematu.

* [Lokalne bramy danych](../logic-apps/logic-apps-gateway-connection.md) , które są zasobami platformy Azure, które są tworzone i używane w usłudze Logic Apps do uzyskiwania dostępu do danych w systemach lokalnych. Każdy zasób bramy reprezentuje oddzielną [instalację bramy danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym. Aby uzyskać więcej informacji, zobacz [lokalne bramy danych](#on-premises-data-gateways) w dalszej części tego tematu.

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , w których definiujesz i przechowujesz artefakty, które są używane przez aplikacje logiki w scenariuszach [integracji przedsiębiorstwa z firmą B2B (Business-to-Business)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Na przykład można [skonfigurować odzyskiwanie po awarii między regionami dla kont integracji](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Środowiska usługi integracji (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , w których tworzysz aplikacje logiki, które są uruchamiane w izolowanym wystąpieniu Logic Apps środowiska uruchomieniowego w ramach sieci wirtualnej platformy Azure. Te aplikacje logiki mogą następnie uzyskiwać dostęp do zasobów chronionych za zaporą w tej sieci wirtualnej.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Lokalizacje główne i pomocnicze

Każda aplikacja logiki musi określić lokalizację, która ma zostać użyta do wdrożenia. Ta lokalizacja to publiczny region w globalnie wielodostępnej platformie Azure, na przykład "zachodnie stany USA" lub środowisko usługi integracji (ISE), które zostało wcześniej utworzone i wdrożone w sieci wirtualnej platformy Azure. Uruchamianie aplikacji logiki w ISE jest podobne do uruchamiania aplikacji logiki w globalnym regionie platformy Azure, co oznacza, że strategia odzyskiwania po awarii może być stosowana do obu tych scenariuszy. Jednak ISEs mają inne zagadnienia, takie jak Konfigurowanie dostępu do zasobów, które są dostępne tylko dla ISEs.

> [!NOTE]
> Jeśli aplikacja logiki działa również z artefaktami B2B, takimi jak partnerzy handlowi, umowy, schematy, mapy i certyfikaty, które są przechowywane na koncie integracji, zarówno konto integracji, jak i Aplikacje logiki muszą określać tę samą lokalizację.

Ta strategia odzyskiwania po awarii koncentruje się na konfigurowaniu podstawowej aplikacji logiki w celu przełączenia w [*tryb failover*](https://en.wikipedia.org/wiki/Failover) lub w aplikacji logiki tworzenia kopii zapasowej w lokalizacji alternatywnej, w której jest również dostępna Azure Logic Apps. W ten sposób, jeśli podstawowa pogorszy się straty, zakłócenia lub niepowodzenia, pomocnicza może potrwać pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne zostały już wdrożone i przygotowane w lokalizacji alternatywnej.

Jeśli przestrzegasz dobrych praktyk DevOps, używasz już [szablonów Azure Resource Manager](../azure-resource-manager/management/overview.md) do definiowania i wdrażania aplikacji logiki oraz ich zasobów zależnych. Szablony Menedżer zasobów umożliwiają użycie pojedynczej definicji wdrożenia, a następnie użycie plików parametrów w celu zapewnienia wartości konfiguracyjnych używanych dla każdego miejsca docelowego wdrożenia. Ta możliwość oznacza, że można wdrożyć tę samą aplikację logiki w różnych środowiskach, na przykład na potrzeby tworzenia, testowania i produkcji. Tę samą aplikację logiki można także wdrożyć w różnych regionach świadczenia usługi Azure lub ISEs, które obsługują strategie odzyskiwania po awarii korzystające z [sparowanych regionów](../best-practices-availability-paired-regions.md).

W przypadku strategii trybu failover aplikacje i lokalizacje logiki muszą spełniać następujące wymagania:

* Dodatkowe wystąpienie aplikacji logiki ma dostęp do tych samych aplikacji, usług i systemów co główne wystąpienie aplikacji logiki.

* Oba wystąpienia aplikacji logiki mają ten sam typ hosta. Z tego względu oba wystąpienia są wdrażane w regionach na globalnym wielodostępnym platformie Azure lub oba wystąpienia są wdrażane w ISEs, co umożliwia aplikacjom logiki bezpośredni dostęp do zasobów w sieci wirtualnej platformy Azure. Aby uzyskać najlepsze rozwiązania i uzyskać więcej informacji na temat sparowanych regionów dla BCDR, zobacz temat [ciągłość działania i odzyskiwanie po awarii (BCDR): wielosparowane regiony platformy Azure](../best-practices-availability-paired-regions.md).

  Na przykład zarówno lokalizacja podstawowa, jak i pomocnicza muszą być ISEs, gdy podstawowa aplikacja logiki działa w ISE i używa [łączników z wersjami ISE](../connectors/apis-list.md#ise-connectors), akcji HTTP do wywoływania zasobów w sieci wirtualnej platformy Azure lub obu tych usług. W tym scenariuszu pomocnicza aplikacja logiki musi również mieć podobną konfigurację w lokalizacji pomocniczej jako podstawową aplikację logiki.

  > [!NOTE]
  > W przypadku bardziej zaawansowanych scenariuszy możesz mieszać zarówno platformę Azure, jak i ISE jako lokalizacje. Należy jednak wziąć pod uwagę i zrozumieć [różnice między działaniem aplikacji logiki w ISE a wielodostępnym systemie Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Jeśli używasz ISEs, upewnij się, [że są skalowane lub mają wystarczającą pojemność](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) do obsługi obciążenia.

#### <a name="example-multi-tenant-azure"></a>Przykład: Azure z wieloma dzierżawcami

W tym przykładzie przedstawiono podstawowe i pomocnicze wystąpienia aplikacji logiki, które są wdrażane w oddzielnym regionie platformy Azure z wieloma dzierżawcami w tym scenariuszu. Pojedynczy [szablon Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definiuje zarówno wystąpienia aplikacji logiki, jak i zasoby zależne wymagane przez te aplikacje logiki. Osobne pliki parametrów określają wartości konfiguracji, które mają być używane dla każdej lokalizacji wdrożenia:

![Główne i pomocnicze wystąpienia aplikacji logiki w oddzielnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Przykład: środowisko usługi integracji

Ten przykład pokazuje poprzednie główne i pomocnicze wystąpienia aplikacji logiki, ale wdrożone w celu oddzielenia ISEs. Pojedynczy szablon Menedżer zasobów definiuje zarówno wystąpienia aplikacji logiki, zależne zasoby wymagane przez te aplikacje logiki, jak i ISEs jako lokalizacje wdrożenia. Osobne pliki parametrów definiują wartości konfiguracji, które mają być używane do wdrożenia w poszczególnych lokalizacjach:

![Podstawowe i pomocnicze Aplikacje logiki w różnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Połączenia z zasobami

Azure Logic Apps udostępnia [wbudowane wyzwalacze i akcje oraz setki zarządzanych łączników](../connectors/apis-list.md) , które mogą być używane przez aplikację logiki do pracy z innymi aplikacjami, usługami, systemami i innymi zasobami, takimi jak konta usługi Azure Storage, bazy danych SQL Server, służbowe konta e-mail i tak dalej. Jeśli aplikacja logiki potrzebuje dostępu do tych zasobów, tworzysz połączenia uwierzytelniające dostęp do tych zasobów. Każde połączenie jest osobnym zasobem platformy Azure, który istnieje w określonej lokalizacji i nie może być używany przez zasoby w innych lokalizacjach.

W przypadku strategii odzyskiwania po awarii należy wziąć pod uwagę lokalizacje, w których istnieją zasoby zależne względem wystąpień aplikacji logiki:

* Twoje wystąpienie podstawowe i zasoby zależne istnieją w różnych lokalizacjach. W takim przypadku wystąpienie pomocnicze może połączyć się z tymi samymi zasobami zależnymi lub punktami końcowymi. Należy jednak utworzyć połączenia przeznaczone dla wystąpienia pomocniczego. Dzięki temu, jeśli lokalizacja główna będzie niedostępna, nie ma to wpływu na połączenia pomocnicze.

  Załóżmy na przykład, że podstawowa aplikacja logiki nawiązuje połączenie z usługami zewnętrznymi, takimi jak Salesforce. Zwykle dostępność i lokalizacja usługi zewnętrznej są niezależne od dostępności aplikacji logiki. W takim przypadku wystąpienie pomocnicze może połączyć się z tą samą usługą, ale powinno mieć własne połączenie.

* Zarówno wystąpienie podstawowe, jak i zasoby zależne znajdują się w tej samej lokalizacji. W takim przypadku zasoby zależne powinny mieć kopie zapasowe lub zreplikowane wersje w innej lokalizacji, aby wystąpienie pomocnicze nadal mogło uzyskać dostęp do tych zasobów.

  Załóżmy na przykład, że podstawowa aplikacja logiki nawiązuje połączenie z usługą znajdującą się w tej samej lokalizacji lub regionie, na przykład Azure SQL Database. Jeśli ten cały region stanie się niedostępny, usługa Azure SQL Database w tym regionie jest również prawdopodobnie niedostępna. W takim przypadku chcesz, aby wystąpienie pomocnicze używało zreplikowanej bazy danych lub kopii zapasowej wraz z osobnym połączeniem z tą bazą danych.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokalne bramy danych

Jeśli aplikacja logiki działa na platformie Azure z wieloma dzierżawcami i wymaga dostępu do zasobów lokalnych, takich jak bazy danych SQL Server, należy zainstalować lokalną [bramę danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym. Następnie można utworzyć zasób bramy danych w Azure Portal, aby aplikacja logiki mogła używać bramy podczas tworzenia połączenia z zasobem.

Zasób bramy danych jest skojarzony z lokalizacją lub regionem świadczenia usługi Azure, podobnie jak zasób aplikacji logiki. W strategii odzyskiwania po awarii upewnij się, że brama danych jest nadal dostępna do użycia przez aplikację logiki. [Wysoką dostępność bramy można włączyć w](../logic-apps/logic-apps-gateway-install.md#high-availability) przypadku wielu instalacji bramy.

> [!NOTE]
> Jeśli aplikacja logiki działa w środowisku usługi integracji (ISE) i używa tylko łączników z wersjami ISE dla lokalnych źródeł danych, Brama danych nie jest potrzebna, ponieważ łączniki ISE zapewniają bezpośredni dostęp do zasobu lokalnego.
>
> Jeśli dla zasobu lokalnego nie jest dostępny łącznik ISE z wersjami, aplikacja logiki nadal może utworzyć połączenie przy użyciu łącznika innego niż ISE, który działa w globalnym wielodostępnym systemie Azure, a nie na ISE. To połączenie wymaga jednak lokalnej bramy danych.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Role Active-Active i Active-pasywne

Można skonfigurować lokalizacje podstawowe i dodatkowe, aby wystąpienia aplikacji logiki w tych lokalizacjach mogły odgrywać następujące role:

| Rola podstawowa — pomocnicza | Opis |
|------------------------|-------------|
| *Aktywne-aktywne* | Główne i pomocnicze wystąpienia aplikacji logiki w obu lokalizacjach aktywnie obsługują żądania, wykonując jeden z następujących wzorców: <p><p>- *Równoważenie obciążenia*: możliwe, że oba wystąpienia nasłuchują punktu końcowego i równoważenia obciążenia ruchem do każdego wystąpienia, w razie potrzeby. <p>- *Konkurujący konsumenci*: można mieć oba wystąpienia jako konkurujących odbiorców, aby wystąpienia konkurują o komunikaty z kolejki. Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienie przejmuje obciążenie. |
| *Aktywne/pasywne* | Główne wystąpienie aplikacji logiki aktywnie obsługuje całe obciążenie, podczas gdy wystąpienie pomocnicze jest pasywne (wyłączone lub nieaktywne). Pomocniczy czeka na sygnał, że podstawowy jest niedostępny lub nie działa z powodu przerwy lub awarii i przejmuje obciążenie jako aktywne wystąpienie. |
| Kombinacja | Niektóre aplikacje logiki odgrywają rolę Active-Active, podczas gdy inne aplikacje logiki odgrywają rolę Active-pasywną. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Przykłady aktywne-aktywne

W tych przykładach przedstawiono instalację Active-Active, w której oba wystąpienia aplikacji logiki aktywnie obsługują żądania lub komunikaty. Inny system lub usługa dystrybuuje żądania lub komunikaty między wystąpieniami, na przykład jedną z następujących opcji:

* "Fizyczny" moduł równoważenia obciążenia, na przykład sprzęt, który kieruje ruchem

* "Miękki" moduł równoważenia obciążenia, taki jak [Azure Load Balancer](../load-balancer/load-balancer-overview.md) lub [Azure API Management](../api-management/api-management-key-concepts.md). Za pomocą API Management można określić zasady, które określają, jak równoważyć obciążenie ruchem przychodzącym. Można też użyć usługi obsługującej śledzenie stanu, na przykład [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Chociaż ten przykład zawiera głównie Azure Load Balancer, można użyć opcji, która najlepiej odpowiada wymaganiom Twojego scenariusza:

  ![Instalator "Active-Active" korzystający z modułu równoważenia obciążenia lub usługi stanowej](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Każde wystąpienie aplikacji logiki działa jako klient, a oba wystąpienia konkurują o komunikaty z kolejki:

  ![Instalator "Active-Active", który używa "konkurujących klientów"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Przykłady aktywne-pasywne

W tym przykładzie przedstawiono konfigurację Active-pasywną, w której podstawowe wystąpienie aplikacji logiki jest aktywne w jednej lokalizacji, podczas gdy wystąpienie pomocnicze pozostaje nieaktywne w innej lokalizacji. Jeśli podstawową usterką jest zakłócenie lub niepowodzenie, można uruchomić skrypt, który aktywuje dodatkową, aby przejąć obciążenie.

![Instalator "Active-pasywny", który używa "konkurujących klientów"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Połączenie z usługą Active-Active i aktywny-pasywny

W tym przykładzie przedstawiono łączną konfigurację, w której lokalizacja podstawowa ma oba aktywne wystąpienia aplikacji logiki, podczas gdy lokacja dodatkowa ma aktywne i pasywne wystąpienia aplikacji logiki. Jeśli w podstawowej lokalizacji występuje zakłócenie lub niepowodzenie, aktywna aplikacja logiki w lokalizacji pomocniczej, która już obsługuje część obciążenia, może zająć cały nakład pracy.

* W lokalizacji podstawowej aktywna aplikacja logiki nasłuchuje Azure Service Busj kolejki komunikatów, podczas gdy inna aktywna aplikacja logiki sprawdza wiadomości e-mail przy użyciu wyzwalacza sondowania programu Outlook w programie Office 365.

* W lokalizacji pomocniczej aktywna aplikacja logiki współpracuje z aplikacją logiki w lokalizacji podstawowej przez nasłuchiwanie i konkurowanie komunikatów z tej samej kolejki Service Bus. W międzyczasie pasywna nieaktywna aplikacja logiki czeka na gotowość do sprawdzenia wiadomości e-mail, gdy lokalizacja podstawowa stanie się niedostępna, ale jest *wyłączona* , aby uniknąć odczytania wiadomości e-mail.

![Kombinacja "Active-pasywna" i "Active-pasywny", która używa wyzwalaczy cykli](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stan i historia aplikacji logiki

Gdy aplikacja logiki zostanie wyzwolona i uruchomiona, stan aplikacji jest przechowywany w tej samej lokalizacji, w której uruchomiono aplikację, i nie można jej przenieść do innej lokalizacji. Jeśli wystąpi awaria lub zakłócenie, wszelkie wystąpienia przepływu pracy w toku są porzucane. W przypadku skonfigurowania lokalizacji podstawowej i dodatkowej nowe wystąpienia przepływu pracy są uruchamiane w dodatkowej lokalizacji.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Zmniejsz liczbę porzuconych wystąpień w toku

Aby zminimalizować liczbę porzuconych wystąpień przepływu pracy w toku, można wybrać spośród różnych wzorców komunikatów, które można zaimplementować, na przykład:

* [Wzorzec dokumentu stałego routingu](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ten wzorzec komunikatu przedsiębiorstwa dzielący proces biznesowy na mniejsze etapy. Dla każdego etapu należy skonfigurować aplikację logiki, która obsługuje obciążenie dla tego etapu. Aby komunikować się ze sobą, Aplikacje logiki używają protokołu obsługi komunikatów asynchronicznych, takiego jak kolejki Azure Service Bus lub tematy. Podział procesu na mniejsze etapy zmniejsza liczbę procesów, które mogą zostać zablokowane w przypadku wystąpienia niepowodzenia aplikacji logiki. Aby uzyskać ogólne informacje na temat tego wzorca, zobacz [wzorce integracji przedsiębiorstwa — Routing](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Ten przykład pokazuje wzorzec dokumentu routingu, gdzie każda aplikacja logiki reprezentuje etap i używa kolejki Service Bus do komunikowania się z następną aplikacją logiki w procesie.

  ![Podziel proces biznesowy na etapy reprezentowane przez aplikacje logiki, które komunikują się ze sobą za pomocą kolejek Azure Service Bus](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Jeśli podstawowe i pomocnicze wystąpienia aplikacji logiki są zgodne z tym samym wzorcem dokumentu routingu w swoich lokalizacjach, można zaimplementować [wzór konkurujących odbiorców](/azure/architecture/patterns/competing-consumers) , konfigurując [Role Active-Active](#roles) dla tych wystąpień.

* [Wzorzec Menedżera procesów (Broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Blokada wglądu bez wzorca limitu czasu](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Dostęp do historii wyzwalacza i uruchomienia

Aby uzyskać więcej informacji na temat poprzednich wykonań przepływu pracy aplikacji logiki, możesz przejrzeć wyzwalacz aplikacji i historię uruchomienia. Historia wykonywania historii aplikacji logiki jest przechowywana w tej samej lokalizacji lub regionie, w której uruchomiono aplikację logiki, co oznacza, że nie można migrować tej historii do innej lokalizacji. Jeśli wystąpienie podstawowe przejdzie w tryb failover do wystąpienia pomocniczego, można uzyskać dostęp tylko do wyzwalacza każdego wystąpienia i uruchamiać historię w odpowiednich lokalizacjach, w których uruchomiono te wystąpienia. Można jednak uzyskać informacje o lokalizacji niezależny od na temat historii aplikacji logiki, konfigurując Aplikacje logiki do wysyłania zdarzeń diagnostycznych do obszaru roboczego usługi Azure Log Analytics. Następnie możesz przejrzeć kondycję i historię w aplikacjach logiki, które działają w wielu lokalizacjach.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Wskazówki dotyczące typu wyzwalacza

Typ wyzwalacza, który jest używany w usłudze Logic Apps, określa opcje konfigurowania aplikacji logiki w różnych lokalizacjach w ramach strategii odzyskiwania po awarii. Oto dostępne typy wyzwalaczy, których można używać w usłudze Logic Apps:

* [Wyzwalacz cyklu](#recurrence-trigger)
* [Wyzwalacz sondowania](#polling-trigger)
* [Wyzwalacz żądania](#request-trigger)
* [Wyzwalacz elementu webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Wyzwalacz cyklu

Wyzwalacz **cyklu** jest niezależny od określonej usługi lub punktu końcowego i jest uruchamiany wyłącznie na podstawie określonego harmonogramu i nie ma innych kryteriów, na przykład:

* Stała częstotliwość i interwał, na przykład co 10 minut
* Bardziej zaawansowany harmonogram, taki jak ostatni poniedziałek każdego miesiąca o godzinie 5:00 PM

Gdy aplikacja logiki rozpoczyna się od wyzwalacza cyklicznego, należy skonfigurować podstawowe i pomocnicze wystąpienia aplikacji logiki przy użyciu [ról aktywnych-pasywnych](#roles). Aby skrócić *cel czasu odzyskiwania* (RTO), który odnosi się do docelowego czasu trwania przywracania procesu biznesowego po zakłóceniu lub awarii, można skonfigurować wystąpienia aplikacji logiki za pomocą kombinacji [aktywnych](#roles) i [aktywnych ról pasywnych](#roles). W tej konfiguracji można podzielić harmonogram na lokalizacje.

Załóżmy na przykład, że masz aplikację logiki, która musi być uruchamiana co 10 minut. Aplikacje i lokalizacje logiki można skonfigurować w taki sposób, aby w przypadku niedostępności podstawowej lokalizacji pomocniczej lokalizacja mogła zająćć następujące czynności:

![Kombinacja "Active-pasywna" i "pasywne", która używa wyzwalaczy cykli](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* W lokalizacji podstawowej Skonfiguruj [Role Active-pasywne](#roles) dla tych aplikacji logiki:

  * W przypadku *aktywnej* włączonej aplikacji logiki Ustaw wyzwalacz cykliczny na początku godziny i powtórz co 20 minut, na przykład 9:00 am, 9:20 am itd.

  * W przypadku *pasywnej* wyłączonej aplikacji logiki Ustaw wyzwalacz cyklu na ten sam harmonogram, ale Zacznij od 10 minut od godziny i Powtarzaj co 20 minut, na przykład 9:10 am, 9:30 am i tak dalej.

* W lokalizacji dodatkowej Skonfiguruj [pasywne i aktywne](#roles) dla tych aplikacji logiki:

  * W przypadku *pasywnej* wyłączonej aplikacji logiki Ustaw wyzwalacz cyklu na ten sam harmonogram jak aktywna aplikacja logiki w lokalizacji podstawowej, która jest w górnej części godziny i Powtarzaj co 20 minut, na przykład 9:00 am, 9:10 am itd.

  * W przypadku *aktywnej* włączonej aplikacji logiki Ustaw wyzwalacz cyklu na ten sam harmonogram jak pasywna aplikacja logiki w lokalizacji głównej, która będzie uruchamiana od 10 minut po godzinie i Powtarzaj co 20 minut, na przykład 9:10 am, 9:20 am itd.

Teraz, jeśli w lokalizacji głównej wystąpi zdarzenie zakłócające, Aktywuj pasywną aplikację logiki w lokalizacji alternatywnej. W ten sposób, jeśli znalezienie błędu nie powiodło się, ta konfiguracja ogranicza liczbę pominiętych cykli w tym opóźnieniu.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Wyzwalacz sondowania

Aby regularnie sprawdzać, czy nowe dane do przetwarzania są dostępne z określonej usługi lub punktu końcowego, aplikacja logiki może używać wyzwalacza *sondowania* , który wielokrotnie wywołuje usługę lub punkt końcowy w oparciu o ustalony harmonogram cykli. Dane dostarczane przez usługę lub punkt końcowy mogą mieć jeden z następujących typów:

* Dane statyczne, które opisują dane, które są zawsze dostępne do odczytu
* Dane nietrwałe, które opisują dane, które nie są już dostępne po odczycie

Aby uniknąć wielokrotnego odczytywania tych samych danych, aplikacja logiki musi pamiętać, które dane zostały wcześniej odczytane przez utrzymywanie stanu po stronie klienta lub na serwerze, usłudze lub stronie systemowej.

* Aplikacje logiki, które współpracują z wyzwalaczami użycia stanu po stronie klienta, które mogą zachować stan.

  Na przykład wyzwalacz, który odczytuje nową wiadomość ze skrzynki odbiorczej poczty e-mail, wymaga, aby wyzwalacz mógł zapamiętać ostatnio czytany komunikat. Dzięki temu wyzwalacz uruchamia aplikację logiki tylko wtedy, gdy zostanie odebrany Następny nieprzeczytany komunikat.

* Aplikacje logiki działające z serwerem, usługą lub stanem po stronie systemu korzystają z wartości właściwości lub ustawień, które znajdują się na serwerze, usłudze lub stronie systemowej.

  Na przykład wyzwalacz oparty na zapytaniach, który odczytuje wiersz z bazy danych, wymaga, aby wiersz miał `isRead` kolumnę ustawioną na `FALSE` . Za każdym razem, gdy wyzwalacz odczytuje wiersz, aplikacja logiki aktualizuje ten wiersz, zmieniając `isRead` kolumnę z `FALSE` na `TRUE` .

  To podejście po stronie serwera działa podobnie do Service Bus kolejek lub tematów, które mają semantykę kolejkowania, w której wyzwalacz może odczytywać i blokować komunikat, podczas gdy aplikacja logiki przetwarza komunikat. Gdy aplikacja logiki kończy przetwarzanie, wyzwalacz usuwa komunikat z kolejki lub tematu.

W perspektywie odzyskiwania po awarii, podczas konfigurowania wystąpień głównych i pomocniczych aplikacji logiki należy upewnić się, że są one używane w zależności od tego, czy aplikacja logiki śledzi stan po stronie klienta czy po stronie serwera:

* W przypadku aplikacji logiki, która współpracuje z stanem po stronie klienta, upewnij się, że aplikacja logiki nie odczytuje tego samego komunikatu więcej niż jeden raz. Tylko jedna lokalizacja może mieć aktywne wystąpienie aplikacji logiki w określonym czasie. Upewnij się, że wystąpienie aplikacji logiki w lokalizacji alternatywnej jest nieaktywne lub wyłączone, aż wystąpienie podstawowe przejdzie w tryb failover do lokalizacji alternatywnej.

  Na przykład wyzwalacz Office 365 Outlook zachowuje stan po stronie klienta i śledzi sygnaturę czasową dla ostatnio odczytywanej wiadomości e-mail, aby uniknąć odczytu duplikatu.

* W przypadku aplikacji logiki, która współpracuje z stanem po stronie serwera, można skonfigurować wystąpienia aplikacji logiki, aby odtworzyć [role aktywne-aktywne](#roles) , w których działają jako konkurujące odbiorcy lub [aktywne pasywne role](#roles) , w których wystąpienie alternatywne czeka, aż wystąpienie podstawowe przejdzie w tryb failover do lokalizacji alternatywnej.

  Na przykład odczytywanie z kolejki komunikatów, takiej jak Kolejka Azure Service Bus, używa stanu po stronie serwera, ponieważ usługa kolejkowania zachowuje blokady komunikatów, aby uniemożliwić innym klientom odczytywanie tych samych komunikatów.

  > [!NOTE]
  > Jeśli aplikacja logiki musi odczytywać wiadomości w określonej kolejności, na przykład z kolejki Service Bus, można użyć konkurencyjnego wzorca klienta, ale tylko w połączeniu z sesjami Service Bus, który jest również znany jako [ *sekwencyjny wzorzec konwoju* ](/azure/architecture/patterns/sequential-convoy). W przeciwnym razie należy skonfigurować wystąpienia aplikacji logiki przy użyciu ról aktywnych-pasywnych.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacz żądania

Wyzwalacz **żądania** sprawia, że aplikacja logiki jest wywoływana z innych aplikacji, usług i systemów i jest zazwyczaj używana do udostępniania następujących funkcji:

* Bezpośredni interfejs API REST dla aplikacji logiki, która może być wywoływana przez inne osoby

  Na przykład użyj wyzwalacza żądania, aby uruchomić aplikację logiki, aby inne aplikacje logiki mogły wywołać wyzwalacz przy użyciu akcji **wywołania przepływu pracy Logic Apps** .

* Mechanizm [elementu webhook](#webhook-trigger) lub wywołania zwrotnego dla aplikacji logiki

* Sposób ręcznego uruchamiania operacji lub procedur użytkownika w celu wywołania aplikacji logiki, na przykład przy użyciu skryptu programu PowerShell, który wykonuje określone zadanie

Z punktu widzenia odzyskiwania po awarii wyzwalacz żądania jest odbiornikiem pasywnym, ponieważ aplikacja logiki nie wykonuje żadnych zadań i czeka, aż inna usługa lub system jawnie wywoła wyzwalacz. Jako pasywny punkt końcowy możesz skonfigurować wystąpienia podstawowe i pomocnicze w następujący sposób:

* [Aktywne-aktywne](#roles): oba wystąpienia aktywnie obsługują żądania lub wywołania. Obiekt wywołujący lub router rozkłada lub dystrybuuje ruch między tymi wystąpieniami.

* [Aktywne-pasywne](#roles): tylko wystąpienie podstawowe jest aktywne i obsługuje całą pracę, podczas gdy wystąpienie pomocnicze czeka na zakłócenia lub awarię podstawowego środowiska. Obiekt wywołujący lub router określa, kiedy należy wywołać wystąpienie pomocnicze.

W ramach zalecanej architektury można użyć usługi Azure API Management jako serwera proxy dla aplikacji logiki, które używają wyzwalaczy żądań. API Management zapewnia [wbudowaną elastyczność międzyregionalną oraz możliwość kierowania ruchu do wielu punktów końcowych](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Wyzwalacz elementu webhook

Wyzwalacz *elementu webhook* umożliwia aplikacji logiki Subskrybowanie usługi przez przekazanie *adresu URL wywołania zwrotnego* do tej usługi. Aplikacja logiki może następnie nasłuchiwać i czekać na określone zdarzenie w tym punkcie końcowym usługi. Gdy wystąpi zdarzenie, usługa wywołuje wyzwalacz elementu webhook przy użyciu adresu URL wywołania zwrotnego, który następnie uruchamia aplikację logiki. Po włączeniu wyzwalacz elementu webhook subskrybuje usługę. Po wyłączeniu wyzwalacz anuluje subskrypcję usługi.

Z perspektywy odzyskiwania po awarii Skonfiguruj wystąpienia podstawowe i pomocnicze, które korzystają z wyzwalaczy elementu webhook w celu odtwarzania ról aktywnych pasywnie, ponieważ tylko jedno wystąpienie powinno odbierać zdarzenia lub komunikaty z subskrybowanego punktu końcowego.

## <a name="assess-primary-instance-health"></a>Oceń kondycję wystąpienia podstawowego

Aby Strategia odzyskiwania po awarii działała, Twoje rozwiązanie wymaga sposobów wykonania następujących zadań:

* [Sprawdź dostępność wystąpienia podstawowego](#check-primary-availability)
* [Monitorowanie kondycji wystąpienia podstawowego](#monitor-primary-health)
* [Aktywuj wystąpienie pomocnicze](#activate-secondary)

W tej sekcji opisano jedno rozwiązanie, którego można użyć w sposób niewłaściwy lub jako podstawę do własnego projektu. Poniżej przedstawiono ogólne omówienie wizualizacji dla tego rozwiązania:

![Utwórz aplikację logiki licznika alarm, która monitoruje aplikację logiki sprawdzania kondycji w lokalizacji podstawowej](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Sprawdź dostępność wystąpienia podstawowego

Aby określić, czy wystąpienie podstawowe jest dostępne, uruchomione i może działać, można utworzyć aplikację logiki "Sprawdzanie kondycji", która znajduje się w tej samej lokalizacji co wystąpienie podstawowe. Następnie można wywołać ten test kondycji aplikacji z lokalizacji alternatywnej. Jeśli aplikacja test kondycji pomyślnie odpowie, podstawowa infrastruktura dla usługi Azure Logic Apps w tym regionie jest dostępna i działa. Jeśli sprawdzanie kondycji aplikacji nie powiedzie się, możesz założyć, że lokalizacja nie jest już w dobrej kondycji.

W przypadku tego zadania Utwórz podstawową aplikację logiki sprawdzania kondycji, która wykonuje następujące zadania:

1. Odbiera wywołanie z aplikacji alarmowej przy użyciu wyzwalacza żądania.

1. Odpowiedź ze stanem wskazującym, czy sprawdzona aplikacja logiki nadal działa przy użyciu akcji odpowiedzi.

   > [!IMPORTANT]
   > W aplikacji logiki sprawdzania kondycji musi być używana akcja odpowiedzi, dzięki czemu aplikacja będzie odpowiadać synchronicznie, a nie asynchronicznie.

1. Opcjonalnie, aby dodatkowo określić, czy lokalizacja podstawowa jest w dobrej kondycji, można zwiększyć kondycję wszystkich innych usług, które współdziałają z docelową aplikacją logiki w tej lokalizacji. Po prostu rozwiń aplikację logiki Sprawdzanie kondycji, aby ocenić również kondycję tych innych usług.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Tworzenie aplikacji logiki licznika alarmowego

Aby monitorować kondycję wystąpienia podstawowego i wywoływać test kondycji aplikacji logiki, Utwórz aplikację logiki "alarm" w *alternatywnej lokalizacji*. Na przykład możesz skonfigurować aplikację logiki licznika alarmowego, aby w przypadku niepowodzenia wywoływania logiki kontroli kondycji licznik alarmowy może wysłać Alert do zespołu operacji, aby mógł zbadać błąd i dlaczego wystąpienie podstawowe nie odpowiada.

> [!IMPORTANT]
> Upewnij się, że aplikacja logiki licznika alarm znajduje się w *lokalizacji, która różni się od lokalizacji podstawowej*. Jeśli usługa Logic Apps w lokalizacji głównej napotyka problemy, aplikacja logiki licznika alarmowego może nie zostać uruchomiona.

W przypadku tego zadania w lokalizacji dodatkowej Utwórz aplikację logiki licznika alarmowego, która wykonuje następujące zadania:

1. Uruchamiana na podstawie ustalonego lub zaplanowanego cyklu przy użyciu wyzwalacza cyklu.

   Możesz ustawić dla cyklu wartość, która jest niższa od poziomu tolerancji dla celu czasu odzyskiwania (RTO).

1. Wywołaj aplikację logiki Sprawdź kondycję w lokalizacji podstawowej przy użyciu akcji HTTP, na przykład:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktywuj wystąpienie pomocnicze

Aby automatycznie aktywować wystąpienie pomocnicze, można utworzyć aplikację logiki, która wywołuje interfejs API zarządzania, taki jak [łącznik Azure Resource Manager](/connectors/arm/) , aby aktywować odpowiednie Aplikacje logiki w dodatkowej lokalizacji. Możesz rozszerzyć aplikację licznika alarmowego, aby wywołać tę aplikację logiki aktywacji po wystąpieniu określonej liczby błędów.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Zbieranie danych diagnostycznych

Istnieje możliwość skonfigurowania rejestrowania dla aplikacji logiki i wysłania danych diagnostycznych do usług takich jak Azure Storage, Azure Event Hubs i Azure Log Analytics do dalszej obsługi i przetwarzania.

* Jeśli chcesz używać tych danych w usłudze Azure Log Analytics, możesz udostępnić dane zarówno dla lokacji głównej, jak i dodatkowej, konfigurując **Ustawienia diagnostyczne** aplikacji logiki i wysyłając dane do wielu log Analytics obszarów roboczych. Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure monitor dzienników i zbieranie danych diagnostycznych dotyczących Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Jeśli chcesz wysłać dane do usługi Azure Storage lub Azure Event Hubs, możesz udostępnić dane zarówno dla lokacji głównej, jak i dodatkowej, konfigurując nadmiarowość geograficzną. Więcej informacji można znaleźć w następujących artykułach:<p>

  * [Odzyskiwanie po awarii i tryb failover na platformie Azure Blob Storage](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geograficznie — odzyskiwanie po awarii](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Następne kroki

* [Omówienie odporności dla platformy Azure](/azure/architecture/framework/resiliency/overview)
* [Lista kontrolna dotycząca odporności dla określonych usług platformy Azure](/azure/architecture/checklist/resiliency-per-service)
* [Zarządzanie danymi pod kątem odporności na platformie Azure](/azure/architecture/framework/resiliency/data-management)
* [Tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Odzyskiwanie po obejmujących cały region zakłóceniach usługi](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Umowy dotyczące poziomu usług (umowy SLA) firmy Microsoft dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/)
