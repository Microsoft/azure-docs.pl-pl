---
title: Wprowadzenie — Przewodnik dla operatorów IT platformy Azure | Microsoft Docs
description: Wprowadzenie — Przewodnik dla operatorów IT platformy Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 2508846e5dd2fcc96aade9ce64b599bb4154de00
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203385"
---
# <a name="get-started-for-azure-it-operators"></a>Wprowadzenie do operatorów IT platformy Azure

Ten przewodnik zawiera wprowadzenie do podstawowych pojęć związanych z wdrażaniem infrastruktury Microsoft Azure i zarządzaniem nią. Jeśli dopiero zaczynasz korzystać z chmury obliczeniowej lub platformy Azure, ten przewodnik pomaga szybko rozpocząć pracę z pojęciami, wdrażaniem i szczegółami zarządzania. Wiele sekcji tego przewodnika omawia operacje, takie jak wdrażanie maszyny wirtualnej, a następnie udostępnianie linku do szczegółowych informacji technicznych.

## <a name="cloud-computing-overview"></a>Omówienie obliczeń w chmurze

Chmura obliczeniowa zapewnia nowoczesny alternatywę dla tradycyjnego lokalnego centrum danych. Dostawcy chmury publicznej zapewniają i zarządzają całą infrastrukturą obliczeniową oraz podstawowym oprogramowaniem do zarządzania. Ci dostawcy zapewniają szeroką gamę usług w chmurze. W tym przypadku usługa w chmurze może być maszyną wirtualną, serwerem sieci Web lub aparatem bazy danych hostowanym w chmurze. Jako klient korzystający z dostawcy usług w chmurze dzierżawy te są zależne od tego, co jest potrzebne. W tym celu należy przekonwertować koszty związane z konserwacją sprzętu na koszty operacyjne. Usługa w chmurze zapewnia również następujące korzyści:

- Szybkie wdrażanie dużych środowisk obliczeniowych

- Szybkie cofanie alokacji systemów, które nie są już wymagane

- Łatwe wdrażanie tradycyjnie złożonych systemów takich jak moduły równoważenia obciążenia

- Możliwość zapewnienia elastycznej pojemności obliczeniowej lub skalowania w razie potrzeby

- Bardziej ekonomiczne środowiska obliczeniowe

- Dostęp z dowolnego miejsca przy użyciu portalu opartego na sieci Web lub automatyzacji programistycznej

- Usługi oparte na chmurze, które spełniają większość potrzeb obliczeniowych i aplikacji

Dzięki infrastrukturze lokalnej masz pełną kontrolę nad wdrożonym sprzętem i oprogramowaniem. Historycznie prowadzi do podejmowania decyzji dotyczących zaopatrzenia w sprzęt, które koncentrują się na skalowaniu w górę. Przykładem jest zakup serwera o większej liczbie rdzeni w celu spełnienia wymagań dotyczących wydajności szczytowej. Niestety, ta infrastruktura może być nieużywana poza oknem żądania. Na platformie Azure można wdrożyć tylko potrzebną infrastrukturę i dostosować ją w dowolnym momencie. Prowadzi to do skoncentrowania się na skalowaniu przez wdrożenie dodatkowych węzłów obliczeniowych w celu zaspokojenia potrzeb wydajności. Skalowanie usług w chmurze jest tańsze niż skalowanie w górę za dużo sprzętu.

Firma Microsoft wdrożyła wiele centrów danych platformy Azure na całym świecie z zaplanowanymi planami. Ponadto firma Microsoft zwiększa suwerenne chmury w regionach, takich jak Chiny i Niemcy. Tylko największe przedsiębiorstwa globalne mogą wdrażać centra danych w ten sposób, dzięki czemu korzystanie z systemu Azure ułatwia przedsiębiorstwom dowolnego rozmiaru wdrażanie usług blisko swoich klientów.

W małych firmach platforma Azure umożliwia korzystanie z niskiego punktu wejścia, dzięki czemu można skalować szybko wraz ze wzrostem zapotrzebowania na zasoby obliczeniowe. Pozwala to uniknąć dużej inwestycji kapitałowej w infrastrukturę i zapewnia elastyczność w zakresie architekta i ponownej architektury systemów w miarę potrzeb. Korzystanie z chmury obliczeniowej jest dobrze dostosowane do skalowalnego w poziomie modelu szybkiego uruchamiania oraz szybkiego niepowodzenia.

Aby uzyskać więcej informacji na temat dostępnych regionów świadczenia usługi Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Model przetwarzania w chmurze

Platforma Azure korzysta z modelu przetwarzania w chmurze w oparciu o kategorie usługi udostępniane klientom. Trzy kategorie usługi obejmują infrastrukturę jako usługę (IaaS), platformę jako usługę (PaaS) i oprogramowanie jako usługa (SaaS). Dostawcy udostępniają część lub całość odpowiedzialności za składniki w stosie obliczeniowym w każdej z tych kategorii. Przyjrzyjmy się każdej kategorii w chmurze obliczeniowej.
![Porównanie stosu obliczeń w chmurze](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: infrastruktura jako usługa

Dostawca chmury IaaS działa i zarządza wszystkimi fizycznymi zasobami obliczeniowymi oraz wymaganym oprogramowaniem, aby umożliwić wirtualizację komputera. Klient tej usługi wdraża maszyny wirtualne w tych hostowanych centrach danych. Mimo że maszyny wirtualne znajdują się w centrum danych poza siedzibą firmy, konsument IaaS ma kontrolę nad konfiguracją i zarządzaniem systemem operacyjnym, który opuszcza podstawową infrastrukturę dla dostawcy chmury.

Platforma Azure obejmuje kilka rozwiązań IaaS, w tym maszyn wirtualnych, zestawów skalowania maszyn wirtualnych i powiązanej infrastruktury sieciowej. Usługa Virtual Machines to popularne rozwiązanie do wstępnej migracji usług na platformę Azure, ponieważ umożliwia ona model migracji "Unieś" i "Shift". Można skonfigurować maszynę wirtualną, taką jak infrastruktura, która aktualnie działa z usługami w centrum danych, a następnie przeprowadzić migrację oprogramowania do nowej maszyny wirtualnej. Może być konieczne wprowadzenie aktualizacji konfiguracji, takich jak adresy URL do innych usług lub magazynu, ale w ten sposób można migrować wiele aplikacji.

Zestawy skalowania maszyn wirtualnych są tworzone na podstawie usługi Azure Virtual Machines i umożliwiają łatwe wdrażanie klastrów identycznych maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych obsługują również automatyczne skalowanie, dzięki czemu nowe maszyny wirtualne mogą być wdrażane automatycznie, gdy jest to wymagane. Dzięki temu skalowanie maszyn wirtualnych ustawia idealną platformę do hostowania wielousługowych klastrów obliczeniowych, takich jak Azure Service Fabric i Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: platforma jako usługa

Za pomocą usługi PaaS aplikacja jest wdrażana w środowisku udostępnianym przez dostawcę usług w chmurze. Dostawca wykonuje wszystkie zarządzanie infrastrukturą, dzięki czemu można skupić się na tworzeniu aplikacji i zarządzaniu danymi.

Platforma Azure oferuje kilka ofert obliczeniowych PaaS, w tym Web Apps funkcję Azure App Service i Cloud Services platformy Azure (role sieć Web i proces roboczy). W obu przypadkach deweloperzy mają wiele sposobów wdrażania aplikacji bez znajomości orzechów i piorunów, które je obsługują. Deweloperzy nie muszą tworzyć maszyn wirtualnych, używać Remote Desktop Protocol (RDP) do logowania się do każdego z nich lub instalować aplikacji. Po prostu trafią przycisk (lub blisko niego), a Narzędzia udostępniane przez firmę Microsoft inicjują maszyny wirtualne, a następnie wdrażają i instalują na nich aplikację.

#### <a name="saas-software-as-a-service"></a>SaaS: oprogramowanie jako usługa

SaaS to oprogramowanie, które jest centralnie hostowane i zarządzane. Zwykle bazują na architekturze wielodostępnym — w przypadku wszystkich klientów jest używana jedna wersja aplikacji. Można ją skalować do wielu wystąpień, aby zapewnić najlepszą wydajność we wszystkich lokalizacjach. Oprogramowanie SaaS zazwyczaj jest licencjonowane w miesięcznej lub rocznej subskrypcji. Dostawcy oprogramowania SaaS są odpowiedzialni za wszystkie składniki stosu oprogramowania, co pozwala na zarządzanie usługami.

Microsoft 365 jest dobrym przykładem oferty SaaS. Subskrybenci zwracają miesięczną lub roczną opłatę za subskrypcję i otrzymują program Microsoft Exchange, Microsoft OneDrive i resztę pakietu Microsoft Office jako usługi. Subskrybenci zawsze uzyskują najnowszą wersję, a serwer Exchange jest zarządzany przez Ciebie. W porównaniu do instalowania i uaktualniania pakietu Office co roku jest to tańsze i wymaga mniejszego nakładu pracy.

## <a name="azure-services"></a>Usługi platformy Azure

Platforma Azure oferuje wiele usług w chmurze obliczeniowej. Te usługi obejmują:

### <a name="compute-services"></a>Usługi obliczeniowe

Usługi do hostowania i uruchamiania obciążenia aplikacji:

- Azure Virtual Machines — zarówno Linux, jak i Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps i aplikacje funkcji)

- Azure Batch (w przypadku zadań obliczeniowych o dużej skali równoległych i wsadowych)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Usługi danych

Usługi do przechowywania danych i zarządzania nimi:

- Usługa Azure Storage (obejmuje obiekty blob platformy Azure, kolejki, tabele i usługi plików)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Usługi aplikacji

Usługi do tworzenia i obsługi aplikacji:

- Azure Active Directory (Azure AD)

- Azure Service Bus do łączenia systemów rozproszonych

- Usługa Azure HDInsight do przetwarzania danych Big Data

- Azure Logic Apps w przypadku przepływów pracy integracji i aranżacji

- Azure Media Services

### <a name="network-services"></a>Usługi sieciowe

Usługi dla sieci zarówno na platformie Azure, jak i na platformie Azure i lokalnych centrach danych:

- Azure Virtual Network

- Azure ExpressRoute

- System DNS udostępniony przez platformę Azure

- Azure Traffic Manager

- Azure Content Delivery Network

Aby uzyskać szczegółową dokumentację dotyczącą usług platformy Azure, zobacz [dokumentację usługi platformy Azure](/azure).

## <a name="azure-key-concepts"></a>Podstawowe pojęcia związane z platformą Azure

### <a name="datacenters-and-regions"></a>Centra danych i regiony

Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas aprowizacji usługi, aplikacji lub maszyny wirtualnej na platformie Azure zostanie wyświetlony monit o wybranie regionu. Wybrany region reprezentuje konkretne centrum danych, w którym działa aplikacja. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

Jedną z zalet korzystania z platformy Azure jest możliwość wdrażania aplikacji w różnych centrach danych na całym świecie. Wybrany region może mieć wpływ na wydajność aplikacji. Najlepiej wybrać region, który jest bliżej większości klientów, aby zmniejszyć opóźnienia w żądaniach sieciowych. Możesz również wybrać region spełniający wymagania prawne dotyczące dystrybucji aplikacji w określonych krajach/regionach.

### <a name="azure-portal"></a>Azure Portal

Azure Portal jest aplikacją opartą na sieci Web, która może służyć do tworzenia i usuwania zasobów i usług platformy Azure oraz zarządzania nimi. Azure Portal znajduje się w [Portal.Azure.com](https://portal.azure.com). Obejmuje to dostosowywalny pulpit nawigacyjny i narzędzia służące do zarządzania zasobami platformy Azure. Zawiera również informacje dotyczące rozliczeń i subskrypcji. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Portal Omówienie](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) i [Zarządzanie zasobami platformy Azure za pomocą portalu](../../azure-resource-manager/management/manage-resources-portal.md).

### <a name="resources"></a>Zasoby

Zasoby platformy Azure to pojedyncze usługi obliczeniowe, sieci, dane i aplikacje, które zostały wdrożone w ramach subskrypcji platformy Azure. Niektóre typowe zasoby to maszyny wirtualne, konta magazynu lub bazy danych SQL. Usługi platformy Azure często składają się z kilku powiązanych zasobów platformy Azure. Na przykład maszyna wirtualna platformy Azure może obejmować MASZYNę wirtualną, konto magazynu, kartę sieciową i publiczny adres IP. Te zasoby można tworzyć, zarządzać i usuwać pojedynczo lub jako grupę. Zasoby platformy Azure są omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-groups"></a>Grupy zasobów

Grupa zasobów platformy Azure to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby rozwiązania lub tylko zasoby, które mają być zarządzane jako Grupa. Grupy zasobów platformy Azure zostały omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablon Azure Resource Manager to plik JavaScript Object Notation (JSON), który definiuje co najmniej jeden zasób do wdrożenia w grupie zasobów. Definiuje również zależności między wdrożonymi zasobami. Szablony Menedżer zasobów zostały omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="automation"></a>Automatyzacja

Oprócz tworzenia i usuwania zasobów oraz zarządzania nimi przy użyciu Azure Portal można zautomatyzować te działania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell to zestaw modułów, które udostępniają polecenia cmdlet do zarządzania platformą Azure. Za pomocą poleceń cmdlet można tworzyć i usuwać usługi platformy Azure oraz zarządzać nimi. Polecenia cmdlet mogą pomóc w osiągnięciu spójnych, powtarzalnych i niepraktycznych wdrożeń. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to narzędzie, za pomocą którego można tworzyć i usuwać zasoby platformy Azure oraz zarządzać nimi z poziomu wiersza polecenia. Interfejs wiersza polecenia platformy Azure jest dostępny dla systemów Linux, Mac OS X i Windows. Aby uzyskać więcej informacji i szczegóły techniczne, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Interfejsy API REST

Platforma Azure jest oparta na zestawie interfejsów API REST, które obsługują interfejs użytkownika Azure Portal. Większość z tych interfejsów API REST jest również obsługiwana, aby umożliwić programistyczne udostępnianie zasobów i aplikacji platformy Azure oraz zarządzanie nimi z dowolnego urządzenia z obsługą Internetu. Aby uzyskać więcej informacji, zobacz [Dokumentacja zestawu SDK REST platformy Azure](/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratorzy mogą uzyskać dostęp do Azure PowerShell i interfejsu wiersza polecenia platformy Azure za pomocą środowiska dostępnego dla przeglądarki o nazwie Azure Cloud Shell. Ten interfejs interaktywny udostępnia elastyczne narzędzie dla administratorów systemów Linux i Windows, aby można było korzystać z wybranego interfejsu wiersza polecenia, bash lub PowerShell. Azure Cloud Shell można uzyskać dostęp za pośrednictwem portalu jako autonomiczny interfejs sieci Web w [Shell.Azure.com](https://shell.azure.com)lub z wielu innych punktów dostępu. Aby uzyskać więcej informacji, zobacz [omówienie Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrypcja to logiczna Grupa usług platformy Azure, która jest połączona z kontem platformy Azure. Pojedyncze konto platformy Azure może zawierać wiele subskrypcji. Rozliczanie usług platformy Azure odbywa się na podstawie subskrypcji. Subskrypcje platformy Azure mają administratora konta, który ma pełną kontrolę nad subskrypcją, oraz administratora usługi, który ma kontrolę nad wszystkimi usługami w ramach subskrypcji. Aby uzyskać informacje na temat klasycznych administratorów subskrypcji, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Oprócz administratorów do poszczególnych kont można udzielić szczegółowej kontroli nad zasobami platformy Azure przy użyciu [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Wybieranie i włączanie subskrypcji platformy Azure

Aby móc korzystać z usług platformy Azure, musisz mieć subskrypcję. Dostępne są kilka typów subskrypcji.

**Bezpłatne konta**: link do rejestracji w celu uzyskania bezpłatnego konta znajduje się w [witrynie sieci Web platformy Azure](https://azure.microsoft.com/). Dzięki temu możesz uzyskać środki na korzystanie z 30 dni, aby wypróbować dowolną kombinację zasobów na platformie Azure. W przypadku przekroczenia kwoty środków Twoje konto zostanie zawieszone. Na koniec okresu próbnego usługi zostały zlikwidowane i nie będą już działać. W dowolnym momencie możesz przeprowadzić uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

**Subskrypcje MSDN**: Jeśli masz subskrypcję MSDN, w każdym miesiącu otrzymasz określoną kwotę na środki na korzystanie z platformy Azure. Jeśli na przykład masz Microsoft Visual Studio Enterprise z subskrypcją MSDN, otrzymasz \$ 150 miesięcznie na środki na korzystanie z platformy Azure.

W przypadku przekroczenia kwoty środków usługa zostanie wyłączona do momentu rozpoczęcia następnego miesiąca. Możesz wyłączyć limit wydatków i dodać kartę kredytową, która będzie używana w dodatkowych kosztach. Niektóre z tych kosztów są wliczane do kont MSDN. Na przykład opłata jest naliczana dla maszyn wirtualnych z systemem Windows Server i nie ma dodatkowej opłaty za serwery firmy Microsoft, takie jak Microsoft SQL Server. Dzięki temu konta MSDN idealnie nadają się do tworzenia i testowania scenariuszy.

**Konta BizSpark**: program Microsoft BizSparker oferuje wiele korzyści do uruchomienia. Jedną z tych korzyści jest dostęp do całego oprogramowania firmy Microsoft w środowiskach deweloperskich i testowych w przypadku maksymalnie pięciu kont MSDN. W przypadku każdego z pięciu kont MSDN uzyskasz $150 na kwotę, a opłaty są naliczone za kilka usług platformy Azure, takich jak Virtual Machines.

**Płatność zgodnie z rzeczywistym**użyciem: w przypadku tej subskrypcji płacisz za to, czego używasz, dołączając kartę kredytową lub debetową do konta. Jeśli jesteś organizacją, możesz także zatwierdzić ją do fakturowania.

**Umowy Enterprise**Agreement: w ramach umowy Enterprise — użytkownik zobowiązuje się do korzystania z pewnej liczby usług na platformie Azure w ciągu następnego roku, a ta kwota jest płacona z wyprzedzeniem. Zobowiązania, które należy wprowadzić, są używane w ciągu roku. W przypadku przekroczenia kwoty zobowiązania możesz uregulować nadwyżki w zaległościach. W zależności od ilości zobowiązania otrzymujesz rabat na usługi na platformie Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udzielanie dostępu administracyjnego do subskrypcji platformy Azure

Funkcja RBAC ma kilka wbudowanych ról, których można użyć do przypisywania uprawnień. Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz im rolę [właściciela](../../role-based-access-control/built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełen dostęp do wszystkich zasobów w subskrypcji, w tym prawo do przydzielania dostępu innym osobom.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Wyświetl informacje dotyczące rozliczeń w Azure Portal

Ważnym składnikiem korzystania z platformy Azure jest możliwość wyświetlania informacji dotyczących rozliczeń. Azure Portal zapewnia szczegółowy wgląd w informacje dotyczące rozliczeń platformy Azure.

Aby uzyskać więcej informacji, zobacz [jak pobrać fakturę rozliczeń platformy Azure i dane dziennego użycia](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Uzyskaj informacje dotyczące rozliczeń z interfejsów API rozliczeń

Oprócz wyświetlania rozliczeń w portalu możesz uzyskać dostęp do informacji dotyczących rozliczeń przy użyciu skryptu lub programu za pośrednictwem interfejsów API REST rozliczeń platformy Azure:

- Możesz użyć interfejsu API użycia platformy Azure, aby pobrać dane użycia. Informacje o użyciu rozliczeń można dostosować, dodając znaczniki związane z zasobami platformy Azure. Można na przykład oznaczyć każdy zasób w grupie zasobów przy użyciu nazwy działu lub nazwy projektu, a następnie śledzić koszty związane z tym jednym tagiem.

- Korzystając z interfejsu API usługi Azure rate Card, można wyświetlić listę wszystkich dostępnych zasobów wraz z informacjami dotyczącymi metadanych i cen poszczególnych zasobów.

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie szczegółowych informacji o użyciu zasobów platformy Microsoft Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognoza kosztów za pomocą kalkulatora cen

Cennik dla każdej usługi platformy Azure jest różny. Wiele usług platformy Azure oferuje warstwy Basic, standard i Premium. Zwykle każda warstwa ma kilka poziomów cen i wydajności. Korzystając z [kalkulatora cen online](https://azure.microsoft.com/pricing/calculator), można utworzyć oszacowania cen. Kalkulator zawiera elastyczność do oszacowania kosztów dla pojedynczego zasobu lub grupy zasobów.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager to mechanizm wdrażania, zarządzania i organizacji dla zasobów platformy Azure. Za pomocą Menedżer zasobów można umieścić wiele pojedynczych zasobów w grupie zasobów.

Menedżer zasobów obejmuje również możliwości wdrażania umożliwiające dostosowywalne wdrażanie i konfigurację powiązanych zasobów. Na przykład za pomocą Menedżer zasobów można wdrożyć aplikację, która składa się z wielu maszyn wirtualnych, modułu równoważenia obciążenia i bazy danych w Azure SQL Database jako pojedynczej jednostki. Te wdrożenia są opracowywane przy użyciu szablonu Menedżer zasobów.

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Możesz wielokrotnie wdrożyć rozwiązanie w całym cyklu projektowania i mieć pewność, że zasoby są wdrażane w spójnym stanie.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

- Kontrolę dostępu można stosować do wszystkich usług w grupie zasobów, ponieważ RBAC jest natywnie zintegrowana z platformą zarządzania.

- Możesz zastosować Tagi do zasobów, aby logicznie organizować wszystkie zasoby w subskrypcji.

- Możesz wyjaśnić rozliczenia w organizacji, wyświetlając koszty dla grupy zasobów, które współużytkują ten sam tag.

### <a name="tips-for-creating-resource-groups"></a>Porady dotyczące tworzenia grup zasobów

Podczas podejmowania decyzji dotyczących grup zasobów należy wziąć pod uwagę następujące wskazówki:

- Wszystkie zasoby w grupie zasobów powinny mieć ten sam cykl życia.

- Zasób można przypisać tylko do jednej grupy jednocześnie.

- Możesz w dowolnym momencie dodać lub usunąć zasób z grupy zasobów. Każdy zasób musi należeć do grupy zasobów. Dlatego w przypadku usunięcia zasobu z jednej grupy należy dodać go do innej.

- Większość typów zasobów można przenieść do innej grupy zasobów w dowolnym momencie.

- Zasoby w grupie zasobów mogą znajdować się w różnych regionach.

- Grupy zasobów można użyć do kontrolowania dostępu do zasobów w nim.

### <a name="building-resource-manager-templates"></a>Kompilowanie szablonów Menedżer zasobów

Szablony Menedżer zasobów deklaratywnie definiują zasoby i konfiguracje zasobów, które zostaną wdrożone w jednej grupie zasobów. Za pomocą szablonów Menedżer zasobów można organizować złożone wdrożenia bez konieczności stosowania nadmiarowego skryptu lub konfiguracji ręcznej. Po opracowaniu szablonu można go wdrożyć wiele razy — za każdym razem z identycznym wynikiem.

Szablon Menedżer zasobów składa się z czterech sekcji:

- **Parametry**: te dane są wprowadzane do wdrożenia. Wartości parametrów mogą być dostarczane przez człowieka lub zautomatyzowany proces. Przykładem może być nazwa użytkownika administratora i hasło dla maszyny wirtualnej z systemem Windows. Wartości parametrów są używane w całym wdrożeniu, gdy są one określone.

- **Zmienne**: służą do przechowywania wartości, które są używane w całym wdrożeniu. W przeciwieństwie do parametrów, wartość zmiennej nie jest podawana podczas wdrażania. Zamiast tego są one stałe kodowane lub dynamicznie generowane.

- **Zasoby**: Ta sekcja szablonu służy do definiowania zasobów do wdrożenia, takich jak maszyny wirtualne, konta magazynu i sieci wirtualne.

- **Wynik**: po zakończeniu wdrożenia Menedżer zasobów może zwracać dane, takie jak dynamicznie generowane parametry połączenia.

Dla automatyzacji wdrożenia dostępne są następujące mechanizmy:

- **Funkcje**: można używać kilku funkcji w szablonach Menedżer zasobów. Obejmują one operacje, takie jak konwertowanie ciągu na małe litery, wdrażanie wielu wystąpień zdefiniowanego zasobu i dynamiczne zwracanie docelowej grupy zasobów. Menedżer zasobów funkcje ułatwiają tworzenie wdrożeń dynamicznych.

- **Zależności zasobów**: w przypadku wdrażania wielu zasobów niektóre zasoby będą miały zależność od innych. Aby ułatwić wdrożenie, można użyć deklaracji zależności, aby zasoby zależne zostały wdrożone przed innymi.

- **Łączenie szablonów**: z poziomu jednego szablonu Menedżer zasobów, można połączyć się z innym szablonem. Pozwala to na dekompozycję wdrożenia do zestawu docelowych szablonów specyficznych dla celu.

Szablony Menedżer zasobów można tworzyć w dowolnym edytorze tekstu. Jednak zestaw Azure SDK dla programu Visual Studio zawiera narzędzia, które ułatwią Ci. Za pomocą programu Visual Studio można dodawać zasoby do szablonu za pomocą kreatora, a następnie wdrażać i debugować szablon bezpośrednio z poziomu programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Tworzenie szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

Na koniec można skonwertować istniejące grupy zasobów do szablonu do wielokrotnego użytku z Azure Portal. Może to być przydatne, jeśli chcesz utworzyć szablon do wdrożenia istniejącej grupy zasobów lub po prostu chcesz przejrzeć bazowe dane JSON. Aby wyeksportować grupę zasobów, wybierz przycisk **skryptu automatyzacji** z ustawień grupy zasobów.

## <a name="security-of-azure-resources-rbac"></a>Zabezpieczenia zasobów platformy Azure (RBAC)

Dostęp operacyjny do kont użytkowników można udzielić w określonym zakresie: subskrypcji, grupy zasobów lub poszczególnych zasobów. Oznacza to, że można wdrożyć zestaw zasobów w grupie zasobów, takiej jak maszyna wirtualna i wszystkie powiązane zasoby, a także udzielić uprawnień dla określonego użytkownika lub grupy. Takie podejście ogranicza dostęp tylko do zasobów należących do docelowej grupy zasobów. Możesz również udzielić dostępu do pojedynczego zasobu, na przykład maszyny wirtualnej lub sieci wirtualnej.

Aby udzielić dostępu, należy przypisać rolę do użytkownika lub grupy użytkowników. Istnieje wiele wstępnie zdefiniowanych ról. Możesz również zdefiniować własne role niestandardowe.

Oto kilka przykładowych [wbudowanych ról na platformie Azure](../../role-based-access-control/built-in-roles.md):

- **Właściciel**: użytkownik mający tę rolę może zarządzać wszystkimi wszystkimi, w tym dostępem.

- **Czytelnik**: użytkownik mający tę rolę może odczytywać zasoby wszystkich typów (z wyjątkiem wpisów tajnych), ale nie może wprowadzać zmian.

- **Współautor maszyny wirtualnej**: użytkownik mający tę rolę może zarządzać maszynami wirtualnymi, ale nie może zarządzać siecią wirtualną, z którą są połączone, lub kontem magazynu, na którym znajduje się plik VHD.

- **Współautor bazy danych SQL**: użytkownik mający tę rolę może zarządzać bazami danych SQL, ale nie zasadami związanymi z zabezpieczeniami.

- **Menedżer zabezpieczeń SQL**: użytkownik mający tę rolę może zarządzać zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych.

- **Współautor konta magazynu**: użytkownik mający tę rolę może zarządzać kontami magazynu, ale nie może zarządzać dostępem do kont magazynu.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Usługa Azure Virtual Machines to jedna z centralnych usług IaaS na platformie Azure. Usługa Azure Virtual Machines obsługuje wdrażanie maszyn wirtualnych z systemem Windows lub Linux w centrum danych Microsoft Azure. Za pomocą usługi Azure Virtual Machines masz całkowitą kontrolę nad konfiguracją maszyny wirtualnej i są one odpowiedzialne za instalację, konfigurację i konserwację oprogramowania.

Podczas wdrażania maszyny wirtualnej platformy Azure Możesz wybrać obraz z witryny Azure Marketplace lub udostępnić uogólniony obraz. Ten obraz służy do stosowania systemu operacyjnego i konfiguracji początkowej. Podczas wdrażania Menedżer zasobów będzie obsługiwać niektóre ustawienia konfiguracji, takie jak przypisanie nazwy komputera, poświadczeń administracyjnych i konfiguracji sieci. Rozszerzenia maszyny wirtualnej platformy Azure umożliwiają dalsze Automatyzowanie konfiguracji, takich jak instalacja oprogramowania, konfiguracja programu antywirusowego i rozwiązania do monitorowania.

Można tworzyć maszyny wirtualne w wielu różnych rozmiarach. Rozmiar maszyny wirtualnej wymusza alokację zasobów, taką jak przetwarzanie, pamięć i pojemność magazynu. W niektórych przypadkach określone funkcje, takie jak karty sieciowe z obsługą funkcji RDMA i dyski SSD, są dostępne tylko w przypadku niektórych rozmiarów maszyn wirtualnych. Aby zapoznać się z pełną listą rozmiarów i możliwości maszyn wirtualnych, zobacz sekcję dotyczącą rozmiarów maszyny wirtualnej na platformie Azure dla [systemów Windows](../../virtual-machines/sizes.md) i [Linux](../../virtual-machines/sizes.md).

### <a name="use-cases"></a>Przypadki zastosowań

Ponieważ usługi Azure Virtual Machines zapewniają pełną kontrolę nad konfiguracją, są idealnym rozwiązaniem dla szerokiego zakresu obciążeń serwera, które nie mieszczą się w modelu PaaS. Obciążenia serwera, takie jak serwery baz danych (SQL Server, Oracle lub MongoDB), Windows Server Active Directory, Microsoft SharePoint i wiele innych, można uruchomić na platformie Microsoft Azure. W razie potrzeby można przenieść takie obciążenia z lokalnego centrum danych do jednego lub większej liczby regionów świadczenia usługi Azure bez konieczności ponownej konfiguracji.

### <a name="deployment-of-virtual-machines"></a>Wdrażanie maszyn wirtualnych

Maszyny wirtualne platformy Azure można wdrażać przy użyciu Azure Portal przy użyciu automatyzacji z modułem Azure PowerShell lub za pomocą automatyzacji z międzyplatformowym interfejsem wiersza polecenia.

#### <a name="portal"></a>Portal

Wdrożenie maszyny wirtualnej przy użyciu Azure Portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci Web. Można wybrać wiele różnych obrazów systemu operacyjnego z różnymi konfiguracjami. Wszystkie wymagania dotyczące magazynu i sieci są konfigurowane podczas wdrażania. Aby uzyskać więcej informacji, zobacz "Tworzenie maszyny wirtualnej w Azure Portal" dla [systemów Windows](../../virtual-machines/windows/quick-create-portal.md) i [Linux](../../virtual-machines/linux/quick-create-portal.md).

Oprócz wdrożenia maszyny wirtualnej na podstawie Azure Portal można wdrożyć szablon Azure Resource Manager z poziomu portalu. Spowoduje to wdrożenie i skonfigurowanie wszystkich zasobów zgodnie z definicją w szablonie. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrażanie maszyny wirtualnej platformy Azure za pomocą programu PowerShell pozwala na pełną automatyzację wdrożenia wszystkich powiązanych zasobów maszyny wirtualnej, w tym magazynu i sieci. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu Menedżer zasobów i programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Oprócz wdrażania zasobów obliczeniowych platformy Azure osobno można użyć modułu Azure PowerShell, aby wdrożyć szablon Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia

Podobnie jak w przypadku modułu programu PowerShell, interfejs wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i może być używany w systemach Windows, OS X lub Linux. W przypadku korzystania z polecenia **szybkiego tworzenia maszyny** wirtualnej platformy Azure można wdrożyć wszystkie powiązane zasoby maszyn wirtualnych (w tym magazyn i sieć) oraz maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md).

Podobnie można użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć szablon Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Dostęp i zabezpieczenia dla maszyn wirtualnych

Dostęp do maszyny wirtualnej z Internetu wymaga skojarzonego interfejsu sieciowego lub modułu równoważenia obciążenia, jeśli ma to zastosowanie, do skonfigurowania za pomocą publicznego adresu IP. Publiczny adres IP zawiera nazwę DNS, który zostanie rozpoznany przez maszynę wirtualną lub moduł równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [adresy IP na platformie Azure](../../virtual-network/public-ip-addresses.md).

Dostęp do maszyny wirtualnej można zarządzać za pośrednictwem publicznego adresu IP przy użyciu zasobu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). SIECIOWEJ grupy zabezpieczeń działa jak zapora i zezwala na ruch między interfejsem sieciowym lub podsiecią w zestawie określonych portów lub odmówi go. Na przykład aby utworzyć sesję Pulpit zdalny z maszyną wirtualną platformy Azure, należy skonfigurować sieciowej grupy zabezpieczeń tak, aby zezwalał na ruch przychodzący na porcie 3389. Aby uzyskać więcej informacji, zobacz [otwieranie portów do maszyny wirtualnej na platformie Azure przy użyciu Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Na koniec, podobnie jak w przypadku zarządzania dowolnym systemem komputerowym, należy zapewnić zabezpieczenia maszyny wirtualnej platformy Azure w systemie operacyjnym przy użyciu poświadczeń zabezpieczeń i zapór oprogramowania.

## <a name="azure-storage"></a>Azure Storage

Azure Storage to usługa zarządzana przez firmę Microsoft, która zapewnia trwały, skalowalny i nadmiarowy magazyn. Konto usługi Azure Storage można dodać jako zasób do dowolnej grupy zasobów przy użyciu dowolnej metody wdrażania zasobów. Platforma Azure obejmuje cztery typy magazynów: BLOB Storage, File Storage, Table Storage i queue storage. Podczas wdrażania konta magazynu dostępne są dwa typy kont, ogólnego przeznaczenia i magazynu obiektów BLOB. Konto magazynu ogólnego przeznaczenia zapewnia dostęp do wszystkich czterech typów magazynów. Konta magazynu obiektów BLOB są podobne do kont ogólnego przeznaczenia, ale zawierają wyspecjalizowane obiekty blob, które obejmują warstwy dostępu gorąca i zimna. Aby uzyskać więcej informacji na temat usługi BLOB Storage, zobacz [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md).

Konta usługi Azure Storage można skonfigurować z różnymi poziomami nadmiarowości:

- **Magazyn lokalnie nadmiarowy** zapewnia wysoką dostępność dzięki zapewnieniu, że trzy kopie wszystkich danych są wykonywane synchronicznie, zanim zapis zostanie uznany za pomyślnie. Te kopie są przechowywane w jednej placówce w jednym regionie. Repliki znajdują się w oddzielnych domenach błędów i domenach uaktualnienia. Oznacza to, że dane są dostępne nawet wtedy, gdy węzeł magazynu przechowujący dane ulegnie awarii lub zostanie przełączony w tryb offline w celu zaktualizowania.

- **Magazyn Geograficznie nadmiarowy** tworzy trzy synchroniczne kopie danych w regionie podstawowym w celu zapewnienia wysokiej dostępności, a następnie asynchronicznie tworzy trzy repliki w sparowanym regionie na potrzeby odzyskiwania po awarii.

- **Magazyn Geograficznie nadmiarowy do odczytu** jest magazynem geograficznie nadmiarowym i umożliwia odczytywanie danych w regionie pomocniczym. Zapewnia to możliwość częściowego odzyskiwania po awarii. Jeśli wystąpi problem z regionem podstawowym, możesz zmienić aplikację, tak aby miała dostęp tylko do odczytu do sparowanego regionu.

### <a name="use-cases"></a>Przypadki zastosowań

Każdy typ magazynu ma inny przypadek użycia.

#### <a name="blob-storage"></a>Blob Storage

Obiekt *BLOB* słowa jest akronimem dla *dużego obiektu binarnego*. Obiekty blob są plikami bez struktury, takimi jak te, które są przechowywane na komputerze. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Usługa Azure Blob Storage zawiera również dyski danych Virtual Machines platformy Azure.

Usługa Azure Storage obsługuje trzy typy obiektów blob:

- **Blokowe obiekty blob** są używane do przechowywania zwykłych plików o rozmiarze do 195 GB (4 MB × 50 000 bloków). Głównym zastosowaniem dla blokowych obiektów blob jest przechowywanie plików, które są odczytywane od początku do końca, np. plików multimedialnych lub plików obrazów na potrzeby witryn internetowych. Są one nazwanymi blokami BLOB, ponieważ pliki o rozmiarze większym niż 64 MB muszą zostać przekazane jako małe bloki. Te bloki są następnie konsolidowane (lub zatwierdzane) do końcowego obiektu BLOB.

- **Stronicowe obiekty blob** są używane do przechowywania plików o dostępie losowym o rozmiarze do 1 TB. Stronicowe obiekty blob są używane głównie jako zapasowe magazyny dla wirtualnych dysków twardych, które zapewniają trwałe dyski dla Virtual Machines platformy Azure, usługi obliczeniowej IaaS na platformie Azure. Stronicowe obiekty blob są tak nazywane, ponieważ zapewniają swobodny dostęp do odczytu/zapisu do 512-bajtowych stron.

- **Dołącz obiekty blob** składają się z bloków takich jak blokowe obiekty blob, ale są one zoptymalizowane pod kątem operacji dołączania. Są one często używane do rejestrowania informacji z jednego lub kilku źródeł do tego samego obiektu BLOB. Można na przykład napisać wszystkie dzienniki śledzenia do tego samego obiektu BLOB dołączania dla aplikacji uruchomionej na wielu maszynach wirtualnych. Pojedynczy uzupełniany obiekt blob może mieć rozmiar do 195 GB.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

#### <a name="file-storage"></a>File Storage

Azure File Storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Usługa obsługuje protokoły SMB 2,1 i SMB 3,0. Usługa Azure File Storage umożliwia Migrowanie aplikacji korzystających z udziałów plików na platformę Azure szybko i bez kosztownych ponownych prób. Aplikacje działające na maszynach wirtualnych platformy Azure, w usługach w chmurze lub na klientach lokalnych mogą instalować udziały plików w chmurze. Jest to podobne do sposobu, w jaki aplikacja klasyczna instaluje typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Ponieważ udział magazynu plików jest standardowym udziałem plików SMB, aplikacje działające na platformie Azure mogą uzyskiwać dostęp do danych w udziale za pośrednictwem interfejsów API we/wy systemu plików. Deweloperzy mogą wykorzystać swój istniejący kod i umiejętności do migrowania istniejących aplikacji. Specjaliści IT mogą używać poleceń cmdlet programu PowerShell do tworzenia i instalowania udziałów magazynu plików oraz zarządzania nimi w ramach administrowania aplikacjami platformy Azure.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure File Storage w systemie Windows](../../storage/files/storage-how-to-use-files-windows.md) lub [jak korzystać z usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Magazyn tabel

Magazyn tabel Azure to usługa, która przechowuje dane strukturalne NoSQL w chmurze. Magazyn tabel jest magazynem kluczy/atrybutów z projektem bez schematu. Ponieważ magazyn tabel jest mniejszy od schematu, można łatwo dostosować dane w miarę rozwoju aplikacji. Dostęp do danych jest szybki i ekonomiczny dla wszystkich rodzajów aplikacji. Magazyn tabel jest zwykle znacznie tańszy niż tradycyjne bazy SQL dla podobnych ilości danych.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji internetowych, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek. Konto magazynu może zawierać dowolną liczbę tabel z limitem pojemności dla konta magazynu.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure Table Storage](../../cosmos-db/tutorial-develop-table-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. Podczas projektowania aplikacji do skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Wdrażanie konta magazynu

Istnieje kilka opcji wdrażania konta magazynu.

#### <a name="portal"></a>Portal

Wdrożenie konta magazynu przy użyciu Azure Portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci Web. Nowe konto magazynu można wdrożyć w nowej lub istniejącej grupie zasobów. Po utworzeniu konta magazynu można utworzyć kontener obiektów blob lub udział plików przy użyciu portalu. Jednostki magazynu tabeli i kolejki można tworzyć programowo. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../../storage/common/storage-account-create.md).

Oprócz wdrażania konta magazynu na podstawie Azure Portal można wdrożyć szablon Azure Resource Manager z poziomu portalu. Spowoduje to wdrożenie i skonfigurowanie wszystkich zasobów zgodnie z definicją w szablonie, w tym wszystkich kont magazynu. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrożenie konta usługi Azure Storage za pomocą programu PowerShell umożliwia pełne automatyzację wdrożenia konta magazynu. Aby uzyskać więcej informacji, zobacz [używanie Azure PowerShell z usługą Azure Storage](/powershell/module/az.storage/).

Oprócz wdrażania zasobów platformy Azure za pomocą modułu Azure PowerShell można wdrożyć szablon Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia

Podobnie jak w przypadku modułu programu PowerShell, interfejs wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i może być używany w systemach Windows, OS X lub Linux. Aby utworzyć konto magazynu, można użyć polecenia **Create Account Storage** CLI dla systemu Azure. Aby uzyskać więcej informacji, zobacz [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage.](../../storage/blobs/storage-quickstart-blobs-cli.md)

Podobnie można użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć szablon Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Dostęp i zabezpieczenia usługi Azure Storage

Dostęp do usługi Azure Storage odbywa się na różne sposoby, w Azure Portal tym podczas tworzenia i obsługi maszyn wirtualnych oraz z bibliotek klienta magazynu.

#### <a name="virtual-machine-disks"></a>Dyski maszyny wirtualnej

Podczas wdrażania maszyny wirtualnej należy również utworzyć konto magazynu w celu przechowywania dysku systemu operacyjnego maszyny wirtualnej oraz wszelkich dodatkowych dysków z danymi. Można wybrać istniejące konto magazynu lub utworzyć nowe. Ze względu na to, że maksymalny rozmiar obiektu BLOB to 1 024 GB, rozmiar pojedynczego dysku maszyny wirtualnej wynosi 1 023 GB. Aby skonfigurować większy dysk z danymi, można przedstawić wiele dysków danych do maszyny wirtualnej i wspólnie je dzielić z pojedynczym dyskiem logicznym. Aby uzyskać więcej informacji, zobacz "Zarządzanie dyskami platformy Azure" w [systemach Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) i [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Narzędzia magazynu

Do kont usługi Azure Storage można uzyskać dostęp za pomocą wielu różnych Eksploratora magazynu, takich jak program Visual Studio Cloud Explorer. Te narzędzia pozwalają przeglądać konta magazynu i dane. Aby uzyskać więcej informacji i listę dostępnych Eksploratora magazynów, zobacz [narzędzia klienckie usługi Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Interfejs API usługi Storage

Dostęp do zasobów magazynu można uzyskać za pomocą dowolnego języka, który może wykonywać żądania HTTP/HTTPS. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają pracę z usługą Azure Storage dzięki obsłudze szczegółów, takich jak synchroniczne i asynchroniczne wywoływanie, przetwarzanie wsadowe operacji, Zarządzanie wyjątkami i automatyczne ponawianie prób. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Klucze dostępu do magazynu

Każde konto magazynu ma dwa klucze uwierzytelniania — podstawowy i pomocniczy. Może służyć do operacji dostępu do magazynu. Te klucze magazynu służą do zabezpieczania konta magazynu i są wymagane do programistycznego uzyskiwania dostępu do danych. Istnieją dwa klucze umożliwiające okazjonalne Przerzucanie kluczy w celu zwiększenia bezpieczeństwa. Ochrona kluczy jest niezwykle ważna, ponieważ ich posiadanie wraz z nazwą konta umożliwia nieograniczony dostęp do wszystkich danych na koncie magazynu.

#### <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Jeśli musisz zezwolić użytkownikom na dostęp do zasobów magazynu, możesz utworzyć sygnaturę dostępu współdzielonego. Sygnatura dostępu współdzielonego to token, który można dołączyć do adresu URL, który umożliwia delegowany dostęp do zasobu magazynu. Każda osoba, która dysponuje tokenem, może uzyskać dostęp do zasobu, który wskazuje na określone przez niego uprawnienia, przez okres ważności. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Sieci wirtualne są niezbędne do obsługi komunikacji między maszynami wirtualnymi. Można definiować podsieci, niestandardowy adres IP, ustawienia DNS, filtrowanie zabezpieczeń i równoważenie obciążenia. Platforma Azure obsługuje różne przypadki użycia: sieci tylko w chmurze lub hybrydowe sieci wirtualne.

### <a name="cloud-only-virtual-networks"></a>Sieci wirtualne tylko w chmurze

Usługa Azure Virtual Network jest domyślnie dostępna tylko dla zasobów przechowywanych na platformie Azure. Zasoby połączone z tą samą siecią wirtualną mogą komunikować się ze sobą. Można skojarzyć interfejsy sieciowe maszyny wirtualnej i moduły równoważenia obciążenia z publicznym adresem IP, aby umożliwić dostęp do maszyny wirtualnej za pośrednictwem Internetu. Można zabezpieczyć dostęp do publicznie narażonych zasobów przy użyciu sieciowej grupy zabezpieczeń.

![Virtual Network platformy Azure dla wielowarstwowej aplikacji sieci Web](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybrydowe sieci wirtualne

Możesz połączyć sieć lokalną z siecią wirtualną platformy Azure za pomocą usługi ExpressRoute lub połączenia sieci VPN typu lokacja-lokacja. W tej konfiguracji usługa Azure Virtual Network jest zasadniczo opartym na chmurze rozszerzeniem sieci lokalnej.

Ze względu na to, że sieć wirtualna platformy Azure jest połączona z siecią lokalną, sieci wirtualne obejmujące wiele lokalizacji muszą używać unikatowej części przestrzeni adresowej używanej przez organizację. W taki sam sposób, w jaki różne lokalizacje firmowe mają przypisaną konkretną podsieć IP, platforma Azure jest kolejną lokalizacją dodaną do rozbudowanej sieci.
Istnieje kilka opcji wdrażania sieci wirtualnej.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [Program PowerShell](../../virtual-network/quick-create-powershell.md)

- [Interfejs wiersza polecenia](../../virtual-network/quick-create-cli.md)

- Szablony usługi Azure Resource Manager

> **Kiedy używać**: wszędzie, gdzie pracujesz z maszynami wirtualnymi na platformie Azure, będziesz korzystać z sieci wirtualnych. Pozwala to na segmentację maszyn wirtualnych do publicznych i prywatnych podsieci podobnych do lokalnych centrów danych.
>
> **Wprowadzenie**: wdrażanie sieci wirtualnej platformy Azure przy użyciu Azure Portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci Web. Nową sieć wirtualną można wdrożyć w nowej lub istniejącej grupie zasobów. Podczas tworzenia nowej maszyny wirtualnej z poziomu portalu można wybrać istniejącą sieć wirtualną lub utworzyć nową. Rozpocznij pracę i [Utwórz sieć wirtualną przy użyciu Azure Portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Dostęp i zabezpieczenia dla sieci wirtualnych

Można zabezpieczyć sieci wirtualne platformy Azure przy użyciu sieciowej grupy zabezpieczeń. Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL), które zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej lub go odmawiają. Można skojarzyć sieciowych grup zabezpieczeń z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w tej podsieci. Po skojarzeniu sieciowej grupy zabezpieczeń z podsiecią reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto można dodatkowo ograniczyć ruch do pojedynczej maszyny wirtualnej, kojarząc sieciowej grupy zabezpieczeń bezpośrednio z tą maszyną wirtualną. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie maszyny wirtualnej z systemem Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Tworzenie maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/quick-create-portal.md)