---
title: Wprowadzenie — Przewodnik dla deweloperów na platformie Azure | Microsoft Docs
description: Ten artykuł zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Microsoft Azure na potrzeby projektowania.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 8694c403b14234a70b0a67f9f4defb7817ba3ae3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372212"
---
# <a name="get-started-guide-for-azure-developers"></a>Wprowadzenie — przewodnik dla deweloperów platformy Azure

## <a name="what-is-azure"></a>Czym jest platforma Azure?

Azure to kompletna platforma w chmurze, która może obsługiwać istniejące aplikacje i usprawnia tworzenie nowych aplikacji. Platforma Azure może nawet rozszerzać aplikacje lokalne. Platforma Azure integruje się z usługami w chmurze, które są potrzebne do tworzenia, testowania i wdrażania aplikacji oraz zarządzania nimi, a jednocześnie korzystając z zalet chmury obliczeniowej.

Hostowanie aplikacji na platformie Azure pozwala na szybkie i łatwe skalowanie aplikacji wraz ze wzrostem zapotrzebowania klientów. Platforma Azure oferuje również niezawodność, która jest wymagana w przypadku aplikacji o wysokiej dostępności, nawet w tym tryb failover między różnymi regionami. [Azure Portal](https://portal.azure.com) pozwala łatwo zarządzać wszystkimi usługami platformy Azure. Usługami można także zarządzać programowo przy użyciu interfejsów API i szablonów specyficznych dla usługi.

Ten przewodnik zawiera wprowadzenie do platformy Azure dla deweloperów aplikacji. Zawiera ona wskazówki i kierunek, które należy zacząć tworzyć nowe aplikacje na platformie Azure lub migrować istniejące aplikacje na platformę Azure.

## <a name="where-do-i-start"></a>Od czego zacząć?

Dzięki wszystkim usługom oferowanym przez platformę Azure może być zastraszanie zadanie pozwalające ustalić, które usługi są potrzebne do obsługi architektury rozwiązania. W tej sekcji przedstawiono usługi platformy Azure powszechnie używane przez deweloperów. Aby zapoznać się z listą wszystkich usług platformy Azure, zobacz [dokumentację platformy Azure](../../index.yml).

Najpierw należy zdecydować, jak hostować aplikację na platformie Azure. Czy musisz zarządzać całą infrastrukturą jako maszyną wirtualną? Czy można korzystać z funkcji zarządzania platformą udostępnianych przez platformę Azure? Być może potrzebujesz platformy bezserwerowej tylko do hostowania wykonywania kodu?

Twoja aplikacja wymaga magazynu w chmurze, w którym platforma Azure oferuje kilka opcji. Możesz skorzystać z zalet uwierzytelniania przedsiębiorstwa na platformie Azure. Istnieją także narzędzia do programowania i monitorowania w chmurze, a większość usług hostingu oferuje integrację DevOps.

Teraz przyjrzyjmy się niektórym konkretnym usługom, które zalecamy badanie aplikacji.

### <a name="application-hosting"></a>Hosting aplikacji

Platforma Azure udostępnia kilka opartych na chmurze ofert obliczeniowych do uruchamiania aplikacji, dzięki czemu nie trzeba martwić się o szczegóły infrastruktury. Zasoby można łatwo skalować w górę lub w poziomie w miarę wzrostu użycia aplikacji.

Platforma Azure oferuje usługi obsługujące potrzeby projektowania aplikacji i hostingu. Platforma Azure udostępnia infrastrukturę jako usługę (IaaS) w celu zapewnienia pełnej kontroli nad hostingiem aplikacji. Oferty platformy Azure jako usługi (PaaS) zapewniają w pełni zarządzane usługi, które są używane do włączania aplikacji. Istnieje nawet prawdziwe hosting bez serwera na platformie Azure, gdzie wszystko, co trzeba zrobić, napisanie kodu.

![Opcje hostingu aplikacji platformy Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Aby uzyskać najszybszą ścieżkę publikowania projektów opartych na sieci Web, należy rozważyć Azure App Service. App Service ułatwia rozbudowanie aplikacji sieci Web w celu obsługi klientów mobilnych i publikowanie łatwo używanych interfejsów API REST. Ta platforma zapewnia uwierzytelnianie przy użyciu dostawców społecznościowych, automatycznego skalowania opartego na ruchu, testowania w środowisku produkcyjnym oraz wdrożeń ciągłych i opartych na kontenerach.

Możesz tworzyć aplikacje sieci Web, zaplecza aplikacji mobilnych i aplikacje interfejsu API.

Ponieważ wszystkie trzy typy aplikacji współdzielą środowisko uruchomieniowe App Service, można hostować witrynę sieci Web, obsługiwać klientów mobilnych i udostępniać interfejsy API na platformie Azure, a wszystko to w tym samym projekcie lub w tym samym rozwiązaniu. Aby dowiedzieć się więcej na temat App Service, zobacz [co to jest usługa Azure Web Apps](../../app-service/overview.md).

App Service został zaprojektowany z myślą o DevOps. Obsługuje on różne narzędzia do publikowania i ciągłej integracji wdrożeń. Te narzędzia obejmują elementy webhook usługi GitHub, Jenkins, Azure DevOps, TeamCity i inne.

Istniejące aplikacje można migrować do App Service za pomocą [Narzędzia migracji online](https://appmigration.microsoft.com/).

> **Kiedy używać**: App Service podczas migrowania istniejących aplikacji sieci Web na platformę Azure i gdy potrzebna jest w pełni zarządzana platforma hostingu dla aplikacji sieci Web. Możesz również użyć App Service, gdy musisz obsługiwać klientów mobilnych lub udostępniać interfejsy API REST w aplikacji.
>
> **Wprowadzenie**: App Service ułatwia tworzenie i wdrażanie pierwszej [aplikacji sieci Web](../../app-service/quickstart-dotnetcore.md), [aplikacji mobilnej](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)lub [aplikacji interfejsu API](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Wypróbuj teraz**: App Service umożliwia obsługę krótkoterminowej aplikacji do wypróbowania platformy bez konieczności rejestrowania się w celu uzyskania konta platformy Azure. Wypróbuj platformę i [Utwórz aplikację Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako dostawca infrastruktury jako usługi (IaaS), platforma Azure umożliwia wdrażanie aplikacji lub Migrowanie jej na maszyny wirtualne z systemem Windows lub Linux. Wraz z usługą Azure Virtual Network platforma Azure Virtual Machines obsługuje wdrażanie maszyn wirtualnych z systemem Windows lub Linux na platformie Azure. W przypadku maszyn wirtualnych masz całkowitą kontrolę nad konfiguracją maszyny. W przypadku korzystania z maszyn wirtualnych użytkownik jest odpowiedzialny za wszystkie instalacje oprogramowania serwera, konfigurację, konserwację i poprawki systemu operacyjnego.

Ze względu na poziom kontroli z maszynami wirtualnymi można uruchamiać szeroką gamę obciążeń serwera na platformie Azure, które nie mieszczą się w modelu PaaS. Te obciążenia obejmują serwery baz danych, Active Directory systemu Windows Server i program Microsoft SharePoint. Aby uzyskać więcej informacji, zobacz dokumentację Virtual Machines dla systemu [Linux](../../virtual-machines/linux/index.yml) lub [Windows](../../virtual-machines/windows/index.yml).

> **Kiedy używać**: należy używać Virtual Machines, gdy chcesz mieć pełną kontrolę nad infrastrukturą aplikacji lub migrować obciążenia aplikacji lokalnych na platformę Azure bez konieczności wprowadzania zmian.
>
> **Wprowadzenie**: Tworzenie [maszyny wirtualnej systemu Linux](../../virtual-machines/linux/quick-create-portal.md) lub [maszyny wirtualnej z systemem Windows](../../virtual-machines/windows/quick-create-portal.md) na podstawie Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (bezserwerowo)

Zamiast martwić się o tworzenie całej aplikacji lub infrastruktury do uruchamiania kodu oraz zarządzanie nią, co zrobić, jeśli udało Ci się napisać kod i uruchomić go w odpowiedzi na zdarzenia lub zgodnie z harmonogramem?  [Azure Functions](../../azure-functions/functions-overview.md) to oferta w stylu "bezserwerowa", która pozwala pisać tylko kod, którego potrzebujesz. Za pomocą funkcji można wyzwolić wykonywanie kodu z żądaniami HTTP, elementami webhook, zdarzeniami usługi w chmurze lub zgodnie z harmonogramem. Możesz zakodować w wybranym języku programistycznym, takim jak C \# , F \# , Node.js, Python lub php. Korzystając z rozliczeń, płacisz tylko za czas wykonywania kodu, a platforma Azure skaluje się w razie potrzeby.

> **Kiedy używać**: Użyj Azure Functions, gdy masz kod, który jest wyzwalany przez inne usługi platformy Azure, za pośrednictwem zdarzeń opartych na sieci Web lub zgodnie z harmonogramem. Możesz również użyć funkcji, jeśli nie potrzebujesz nakładu pracy kompletnego hostowanego projektu lub jeśli chcesz zanieść tylko za czas działania kodu. Aby dowiedzieć się więcej, zobacz [Azure Functions Omówienie](../../azure-functions/functions-overview.md).
>
> **Wprowadzenie**: Postępuj zgodnie z samouczkiem Szybki Start, aby [utworzyć pierwszą funkcję](../../azure-functions/functions-create-first-azure-function.md) z portalu.
>
> **Wypróbuj teraz**: Azure Functions umożliwia uruchomienie kodu bez konieczności rejestrowania się w celu uzyskania konta platformy Azure. Wypróbuj teraz i [Utwórz pierwszą funkcję platformy Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric to platforma systemów rozproszonych. Ta platforma ułatwia tworzenie, pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Zapewnia również kompleksowe funkcje zarządzania aplikacjami, takie jak:

* Inicjowanie obsługi
* Wdrażanie
* Monitorowanie
* Uaktualnianie/stosowanie poprawek
* Usuwanie

Aplikacje, które są uruchamiane w udostępnionej puli maszyn, mogą być uruchamiane w miarę potrzeby na setkach lub tysiącach maszyn.

Service Fabric obsługuje WebAPI z otwartym interfejsem sieci Web dla platformy .NET (OWIN) i ASP.NET Core. Udostępnia zestawy SDK do kompilowania usług w systemie Linux w oprogramowaniu .NET Core i Java. Aby dowiedzieć się więcej na temat Service Fabric, zapoznaj się z [dokumentacją Service Fabric](../../service-fabric/index.yml).

> **Kiedy używać:** Service Fabric jest dobrym wyborem podczas tworzenia aplikacji lub zapisywania istniejącej aplikacji w celu użycia architektury mikrousług. Należy używać Service Fabric, gdy potrzebna jest większa kontrola nad podstawową infrastrukturą lub bezpośredni dostęp do niej.
>
> **Wprowadzenie:** [Tworzenie pierwszej aplikacji usługi Azure Service Fabric](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

### <a name="enhance-your-applications-with-azure-services"></a>Ulepszanie aplikacji dzięki usługom platformy Azure

Wraz z hostingiem aplikacji platforma Azure oferuje oferty usług, które mogą zwiększyć funkcjonalność. Platforma Azure może również ulepszyć programowanie i konserwację aplikacji zarówno w chmurze, jak i lokalnie.

#### <a name="hosted-storage-and-data-access"></a>Hostowany magazyn i dostęp do danych

Większość aplikacji musi przechowywać dane, dlatego należy zdecydować, aby hostować aplikację na platformie Azure. należy wziąć pod uwagę co najmniej jedną z następujących usług magazynu i danych.

- **Azure Cosmos DB**: globalnie dystrybuowana, wielomodelowa usługa bazy danych. Ta baza danych pozwala elastycznie skalować przepływność i magazyn w dowolnej liczbie regionów geograficznych z kompleksową umową SLA.

  > **Kiedy używać:** Gdy aplikacja wymaga baz danych dokumentów, tabel lub grafów, w tym baz danych MongoDB, z wieloma dobrze zdefiniowanymi modelami spójności.
  >
  > **Wprowadzenie**: [Kompilowanie aplikacji sieci Web Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Jeśli jesteś deweloperem programu MongoDB, zobacz [Tworzenie aplikacji sieci Web MongoDB za pomocą Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: oferuje trwały magazyn o wysokiej dostępności dla obiektów blob, kolejek, plików i innych rodzajów danych nierelacyjnych. Magazyn udostępnia podstawę magazynu dla maszyn wirtualnych.

  > **Kiedy używać**: aplikacja przechowuje dane nierelacyjne, takie jak pary klucz-wartość (tabele), obiekty blob, udziały plików lub wiadomości (kolejki).
  >
  > **Wprowadzenie**: Wybierz jeden z następujących typów magazynów: [obiekty blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [tabele](../../cosmos-db/tutorial-develop-table-dotnet.md), [kolejki](../../storage/queues/storage-dotnet-how-to-use-queues.md)lub [pliki](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: oparta na platformie Azure wersja aparatu Microsoft SQL Server do przechowywania relacyjnych danych tabelarycznych w chmurze. SQL Database zapewnia przewidywalną wydajność, skalowalność bez przestojów, ciągłość działania i ochronę danych.

  > **Kiedy używać**: gdy aplikacja wymaga magazynu danych z integralnością referencyjną, obsługą transakcyjną i obsługą zapytań TSQL.
  >
  > **Wprowadzenie**: [Tworzenie bazy danych w Azure SQL Database w kilka minut przy użyciu Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).


Za pomocą [Azure Data Factory](../../data-factory/introduction.md) można przenieść istniejące dane lokalne na platformę Azure. Jeśli nie jesteś gotowy do przenoszenia danych do chmury, [połączenia hybrydowe](../../app-service/app-service-hybrid-connections.md) w Azure App Service umożliwia połączenie App Service hostowanej aplikacji z zasobami lokalnymi. Możesz również nawiązać połączenie z usługami Azure Data and Storage z aplikacji lokalnych.

#### <a name="docker-support"></a>Obsługa platformy Docker

Kontenery platformy Docker, forma wirtualizacji systemu operacyjnego, umożliwiają wdrażanie aplikacji w bardziej wydajny i przewidywalny sposób. Aplikacja kontenera działa w taki sam sposób jak w systemach deweloperskich i testowych. Kontenerami można zarządzać za pomocą standardowych narzędzi platformy Docker. Korzystając z posiadanych umiejętności i popularnych narzędzi open source, można wdrażać aplikacje oparte na kontenerach i zarządzać nimi na platformie Azure.

Platforma Azure oferuje kilka sposobów korzystania z kontenerów w aplikacjach.


- **Usługa Azure Kubernetes**: umożliwia tworzenie i Konfigurowanie klastra maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania aplikacji kontenerowych, oraz zarządzanie nim. Aby dowiedzieć się więcej o usłudze Azure Kubernetes, zobacz [wprowadzenie do usługi Azure Kubernetes](../../aks/intro-kubernetes.md).

  > **Kiedy należy używać**: Kiedy trzeba tworzyć gotowe do użycia środowisko produkcyjne, które zapewnia dodatkowe narzędzia do planowania i zarządzania, lub gdy wdrażasz klaster Docker Swarm.
  >
  > **Wprowadzenie**: [wdrażanie klastra usługi Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Maszyna platformy Docker**: umożliwia zainstalowanie aparatu platformy Docker na hostach wirtualnych i zarządzanie nim przy użyciu poleceń platformy Docker-Machine.

  >**Kiedy należy używać**: gdy chcesz szybko prototypować aplikację, tworząc pojedynczy Host platformy Docker.

- **Niestandardowy obraz platformy Docker dla App Service**: umożliwia korzystanie z kontenerów platformy Docker z rejestru kontenerów lub kontenera klienta podczas wdrażania aplikacji sieci Web w systemie Linux.

  > **Kiedy używać**: podczas wdrażania aplikacji sieci Web w systemie Linux do obrazu platformy Docker.
  >
  > **Wprowadzenie**: [Użyj niestandardowego obrazu platformy docker dla App Service w systemie Linux](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Authentication

Niezwykle ważne jest, aby nie tylko wiedzieć, kto korzysta z aplikacji, ale również aby uniemożliwić nieautoryzowany dostęp do zasobów. Platforma Azure oferuje kilka metod uwierzytelniania klientów aplikacji.

- **Azure Active Directory (Azure AD)**: Usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft. Możesz dodać Logowanie jednokrotne (SSO) do aplikacji przez integrację z usługą Azure AD. Dostęp do właściwości katalogu można uzyskać za pomocą interfejs API programu Graph usługi Azure AD bezpośrednio lub Microsoft Graph interfejsu API. Program można zintegrować z obsługą usługi Azure AD w ramach platformy autoryzacji OAuth 2.0 oraz z usługą Open ID Connect przy użyciu natywnych punktów końcowych HTTP/REST i wieloplatformowych bibliotek uwierzytelniania usługi Azure AD.

  > **Kiedy używać**: gdy chcesz zapewnić obsługę logowania jednokrotnego, Pracuj z danymi opartymi na grafie lub Uwierzytelniaj użytkowników opartych na domenie.
  >
  > **Wprowadzenie**: Aby dowiedzieć się więcej, zobacz [przewodnik dewelopera Azure Active Directory](../../active-directory/develop/v2-overview.md).

- **Uwierzytelnianie App Service**: w przypadku wybrania App Service do hostowania aplikacji można także skorzystać z wbudowanej obsługi uwierzytelniania dla usługi Azure AD oraz dostawców tożsamości społecznościowych — w tym Facebook, Google, Microsoft i Twitter.

  > **Kiedy używać**: Jeśli chcesz włączyć uwierzytelnianie w aplikacji App Service przy użyciu usługi Azure AD, dostawców tożsamości społecznościowych lub obu tych programów.
  >
  > **Wprowadzenie**: Aby dowiedzieć się więcej o uwierzytelnianiu w App Service, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](../../app-service/overview-authentication-authorization.md).

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń na platformie Azure, zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorowanie

Gdy aplikacja działa na platformie Azure, musisz monitorować wydajność, śledzić problemy i zobaczyć, jak klienci korzystają z aplikacji. Platforma Azure oferuje kilka opcji monitorowania.

-   **Application Insights**: hostowana na platformie Azure Usługa analityczna Analytics zintegrowana z programem Visual Studio do monitorowania aktywnych aplikacji sieci Web. Zapewnia to dane, które są potrzebne do ciągłego ulepszania wydajności i użyteczności aplikacji. To ulepszenie ma miejsce, gdy aplikacje są hostowane na platformie Azure.

    >**Wprowadzenie**: Postępuj zgodnie z [samouczkiem Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: usługa ułatwiająca wizualizację, wykonywanie zapytań, kierowanie i archiwizowanie metryk i dzienników generowanych w ramach infrastruktury i zasobów platformy Azure. Monitor to pojedyncze źródło do monitorowania zasobów platformy Azure i zawiera widoki danych, które są widoczne w Azure Portal.

    >**Wprowadzenie:** wprowadzenie [do Azure monitor](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>Integracja DevOps

Niezależnie od tego, czy udostępniasz maszyny wirtualne, czy publikujesz aplikacje sieci Web z ciągłą integracją, platforma Azure integruje się z większością popularnych narzędzi DevOps. Możesz korzystać z narzędzi, które już masz, i zmaksymalizować istniejące środowisko, korzystając z pomocy technicznej dla narzędzi takich jak:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Wprowadzenie**: Aby wyświetlić opcje DevOps aplikacji App Service, zobacz [ciągłe wdrażanie do Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Wypróbuj teraz:** [Wypróbuj kilka DevOps integracji](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas aprowizacji usługi, aplikacji lub maszyny wirtualnej na platformie Azure zostanie wyświetlony monit o wybranie regionu. Ten region reprezentuje konkretne centrum danych, w którym jest uruchomiona aplikacja lub gdzie przechowywane są dane. Regiony te odpowiadają konkretnym lokalizacjom publikowanym na stronie [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Wybieranie najlepszego regionu dla aplikacji i danych

Jedną z zalet korzystania z platformy Azure jest możliwość wdrażania aplikacji w różnych centrach danych na całym świecie. Wybrany region może mieć wpływ na wydajność aplikacji. Na przykład lepiej jest wybrać region, który jest bliżej większości klientów, aby zmniejszyć opóźnienia w żądaniach sieci. Możesz również wybrać region spełniający wymagania prawne dotyczące dystrybucji aplikacji w określonych krajach/regionach. Najlepszym rozwiązaniem jest przechowywanie w tym samym centrum danych aplikacji, które znajdują się na tym samym serwerze, w tym samym, jak to możliwe, w tym samym obszarze, w którym znajduje się aplikacja.

### <a name="multi-region-apps"></a>Aplikacje wieloregionowe

Chociaż jest to mało prawdopodobne, nie jest możliwe, aby całe centrum danych przeszedł do trybu offline z powodu zdarzenia, takiego jak klęska żywiołowa lub błąd internetowy. Najlepszym rozwiązaniem jest hostowanie ważnych aplikacji firmowych w więcej niż jednym centrum danych w celu zapewnienia maksymalnej dostępności. Korzystanie z wielu regionów może również ograniczyć opóźnienia dla użytkowników globalnych i zapewnić dodatkowe możliwości w zakresie elastyczności podczas aktualizowania aplikacji.

W przypadku niektórych usług, takich jak maszyna wirtualna i App Services, użyj [usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , aby umożliwić obsługę wieloregionowej pracy w trybie failover między regionami w celu obsługi aplikacji dla przedsiębiorstw o wysokiej dostępności. Aby zapoznać się z przykładem, zobacz [Architektura referencyjna platformy Azure: uruchamianie aplikacji sieci Web w wielu regionach](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kiedy używać**: Jeśli masz aplikacje korporacyjne i o wysokiej dostępności, które korzystają z trybu failover i replikacji.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak zarządzać własnymi aplikacjami i projektami?

Platforma Azure oferuje bogaty zestaw środowisk umożliwiających tworzenie zasobów platformy Azure, aplikacji i projektów oraz zarządzanie nimi — zarówno programowo, jak i w [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfejsy wiersza polecenia i program PowerShell

Platforma Azure oferuje dwa sposoby zarządzania aplikacjami i usługami z poziomu wiersza polecenia. Możesz użyć narzędzi, takich jak bash, Terminal, wiersz polecenia lub narzędzia wiersza polecenia. Zazwyczaj można wykonać te same zadania z poziomu wiersza polecenia, tak jak w Azure Portal, takich jak tworzenie i Konfigurowanie maszyn wirtualnych, sieci wirtualnych, aplikacji sieci Web i innych usług.

-   [Interfejs azure Command-Line Interface (CLI)](/cli/azure/install-azure-cli): umożliwia łączenie się z subskrypcją platformy Azure i wykonywanie różnych zadań w odniesieniu do zasobów platformy Azure z poziomu wiersza polecenia.

-   [Azure PowerShell](/powershell/azure/): zawiera zestaw modułów z poleceniami cmdlet, które umożliwiają zarządzanie zasobami platformy Azure przy użyciu programu Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

[Azure Portal](https://portal.azure.com) jest aplikacją opartą na sieci Web. Za pomocą Azure Portal można tworzyć i usuwać zasoby i usługi platformy Azure oraz zarządzać nimi. Obejmuje:

* Konfigurowalny pulpit nawigacyjny
* Narzędzia do zarządzania zasobami platformy Azure
* Dostęp do ustawień subskrypcji i informacji dotyczących rozliczeń. Aby uzyskać więcej informacji, zobacz [omówienie Azure Portal](https://azure.microsoft.com/features/azure-portal/).

### <a name="rest-apis"></a>Interfejsy API REST

Platforma Azure jest oparta na zestawie interfejsów API REST, które obsługują interfejs użytkownika Azure Portal. Większość z tych interfejsów API REST jest również obsługiwana, aby umożliwić programistyczne Inicjowanie obsługi zasobów i aplikacji platformy Azure oraz zarządzanie nimi z dowolnego urządzenia z obsługą Internetu. Aby zapoznać się z kompletnym zestawem dokumentacji interfejsu API REST, zobacz Dokumentacja [zestawu SDK REST platformy Azure](/rest/api/).

### <a name="apis"></a>Interfejsy API

Oprócz interfejsów API REST wiele usług platformy Azure umożliwia programistyczne zarządzanie zasobami z aplikacji przy użyciu zestawów SDK platformy Azure specyficznych dla platformy, w tym zestawów SDK dla następujących platform deweloperskich:

-   [.NET](/dotnet/api/)
-   [Node.js](/azure/developer/javascript/)
-   [Java](/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Przejdź](/azure/go)

Usługi, takie jak [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) i [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) udostępniają zestawy SDK po stronie klienta, aby umożliwić dostęp do usług z aplikacji klienckich dla sieci Web i urządzeń przenośnych.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Uruchomienie aplikacji na platformie Azure wymaga, aby pracować z wieloma usługami platformy Azure. Te usługi są zgodne z tym samym cyklem życia i można je traktować jako jednostkę logiczną. Na przykład aplikacja sieci Web może używać Web Apps, SQL Database, magazynu, pamięci podręcznej platformy Azure dla Redis i usług Azure Content Delivery Network. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) pozwala na współpracę z zasobami w aplikacji jako grupą. Można wdrożyć, zaktualizować lub usunąć wszystkie zasoby w jednej, skoordynowanej operacji.

Oprócz logicznego grupowania i zarządzania powiązanymi zasobami Azure Resource Manager obejmuje możliwości wdrażania umożliwiające dostosowanie wdrożenia i konfiguracji powiązanych zasobów. Można na przykład użyć Menedżer zasobów wdrożyć i skonfigurować aplikację. Ta aplikacja może składać się z wielu maszyn wirtualnych, modułu równoważenia obciążenia i bazy danych w Azure SQL Database jako pojedyncza jednostka.

Te wdrożenia są opracowywane przy użyciu szablonu Azure Resource Manager, który jest dokumentem w formacie JSON. Szablony umożliwiają zdefiniowanie wdrożenia i zarządzanie aplikacjami przy użyciu szablonów deklaratywnych zamiast skryptów. Szablony mogą współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Na przykład możesz użyć szablonów, aby dodać przycisk do repozytorium GitHub, które wdraża kod w repozytorium w ramach zestawu usług platformy Azure za pomocą jednego kliknięcia.

> **Kiedy używać**: użyj szablonów Menedżer zasobów, jeśli chcesz, aby wdrożenie oparte na szablonie dla aplikacji było zarządzane programowo przy użyciu interfejsów API REST, interfejsu wiersza polecenia platformy Azure i Azure PowerShell.
>
> **Wprowadzenie: aby**rozpocząć korzystanie z szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Omówienie kont, subskrypcji i rozliczeń

Jako deweloperzy chcemy szczegółowe bezpośrednio do kodu i próbować szybko rozpocząć pracę, jak to możliwe dzięki uruchomieniu naszych aplikacji. Mamy na pewno zachęcamy do rozpoczęcia pracy na platformie Azure tak szybko, jak to możliwe. Aby ułatwić korzystanie z tej funkcji, platforma Azure oferuje [bezpłatną wersję próbną](https://azure.microsoft.com/free/). Niektóre usługi mają nawet funkcje "Wypróbuj bezpłatnie", takie jak [Azure App Service](https://tryappservice.azure.com/), które nie wymagają nawet utworzenia konta. Tak długo, jak szczegółowe się na kodowanie i wdrażanie aplikacji na platformie Azure, ważne jest również, aby zrozumieć, jak działa platforma Azure. Należy zwrócić uwagę na to, jak działa z punktu widzenia kont użytkowników, subskrypcji i rozliczeń.

### <a name="what-is-an-azure-account"></a>Co to jest konto platformy Azure?

Aby utworzyć subskrypcję platformy Azure lub korzystać z niej, musisz mieć konto platformy Azure. Konto platformy Azure to po prostu tożsamość w usłudze Azure AD lub w katalogu, takim jak organizacja służbowa, relacje zaufania usługi Azure AD. Jeśli nie należysz do takiej organizacji, możesz zawsze utworzyć subskrypcję za pomocą konta Microsoft, które jest zaufane przez usługę Azure AD. Aby dowiedzieć się więcej o integrowaniu lokalnych Active Directory systemu Windows Server z usługą Azure AD, zobacz [integrowanie tożsamości lokalnych z Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Aby dowiedzieć się więcej, zobacz [jak subskrypcje platformy Azure są skojarzone z Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

A także Definiowanie indywidualnych tożsamości konta platformy Azure, nazywanych również *użytkownikami*, można definiować *grupy* w usłudze Azure AD. Tworzenie grup użytkowników jest dobrym sposobem na zarządzanie dostępem do zasobów w ramach subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak tworzyć grupy, zobacz [Tworzenie grupy w programie Azure Active Directory Preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Można także tworzyć grupy i zarządzać nimi za [pomocą programu PowerShell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Zarządzanie subskrypcjami

Subskrypcja to logiczna Grupa usług platformy Azure, która jest połączona z kontem platformy Azure. Pojedyncze konto platformy Azure może zawierać wiele subskrypcji. Rozliczanie usług platformy Azure odbywa się na podstawie subskrypcji. Aby uzyskać listę dostępnych ofert subskrypcji według typu, zobacz [Microsoft Azure Szczegóły oferty](https://azure.microsoft.com/support/legal/offer-details/). Subskrypcje platformy Azure mają administratora konta, który ma pełną kontrolę nad subskrypcją. Mają także administratora usługi, który ma kontrolę nad wszystkimi usługami w ramach subskrypcji. Aby uzyskać informacje na temat klasycznych administratorów subskrypcji, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Indywidualnym kontom można udzielić szczegółowej kontroli nad zasobami platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC) platformy Azure](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupy zasobów

W przypadku inicjowania obsługi administracyjnej nowych usług platformy Azure należy to zrobić w ramach danej subskrypcji. Poszczególne usługi platformy Azure, które są nazywane również zasobami, są tworzone w kontekście grupy zasobów. Grupy zasobów ułatwiają wdrażanie zasobów aplikacji i zarządzanie nimi. Grupa zasobów powinna zawierać wszystkie zasoby dla aplikacji, które mają być używane jako jednostka. Zasoby można przenosić między grupami zasobów, a nawet w różnych subskrypcjach. Aby dowiedzieć się więcej o przenoszeniu zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Resource Explorer to doskonałe narzędzie do wizualizacji zasobów, które zostały już utworzone w ramach subskrypcji. Aby dowiedzieć się więcej, zobacz [używanie Azure Resource Explorer do wyświetlania i modyfikowania zasobów](/rest/api/).

#### <a name="grant-access-to-resources"></a>Przyznanie dostępu do zasobów

Gdy zezwolisz na dostęp do zasobów platformy Azure, zawsze najlepszym rozwiązaniem jest zapewnienie użytkownikom najniższych uprawnień, które są wymagane do wykonania danego zadania.

- **Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)**: na platformie Azure można udzielić dostępu do kont użytkowników (podmiotów zabezpieczeń) w określonym zakresie: subskrypcji, grupy zasobów lub poszczególnych zasobów. Usługa Azure RBAC umożliwia wdrażanie zasobów w grupie zasobów i udzielanie uprawnień określonym użytkownikowi lub grupie. Pozwala również ograniczyć dostęp tylko do zasobów należących do docelowej grupy zasobów. Możesz również udzielić dostępu do pojedynczego zasobu, na przykład maszyny wirtualnej lub sieci wirtualnej. Aby udzielić dostępu, należy przypisać rolę do użytkownika, grupy lub nazwy głównej usługi. Istnieje wiele wstępnie zdefiniowanych ról i można także definiować własne role niestandardowe. Aby dowiedzieć się więcej, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md).

  > **Kiedy należy używać**: Jeśli potrzebujesz szczegółowych zarządzania dostępem dla użytkowników i grup lub jeśli chcesz, aby użytkownik był właścicielem subskrypcji.
  >
  > **Wprowadzenie**: Aby dowiedzieć się więcej, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Obiekty główne usługi**: wraz z zapewnianiem dostępu do podmiotów zabezpieczeń i grup użytkowników można przyznać ten sam dostęp do nazwy głównej usługi.

  > **Kiedy używać**programu: gdy program programowo zarządza zasobami platformy Azure lub udziela dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie Active Directory aplikacji i nazwy głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tagi

Azure Resource Manager pozwala przypisywać niestandardowe znaczniki do poszczególnych zasobów. Tagi, które są parami klucz-wartość, mogą być przydatne, gdy trzeba zorganizować zasoby na potrzeby rozliczeń lub monitorowania. Tagi umożliwiają śledzenie zasobów między wieloma grupami zasobów. Można przypisać Tagi w następujący sposób:

* W portalu
* W szablonie Azure Resource Manager
* Korzystanie z interfejsu API REST
* Przy użyciu interfejsu wiersza polecenia platformy Azure
* Korzystanie z programu PowerShell

Do każdego zasobu można przypisać wiele tagów. Aby dowiedzieć się więcej, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../../azure-resource-manager/management/tag-resources.md).

### <a name="billing"></a>Rozliczenia

W przypadku przenoszenia z zasobów lokalnych do usług hostowanych w chmurze śledzenie i ocenianie użycia usługi oraz kosztów pokrewnych ma duże znaczenie. Ważne jest, aby oszacować, jakie nowe zasoby są uruchamiane co miesiąc. Możesz również projektować, w jaki sposób rozliczenia są sprawdzane w danym miesiącu, na podstawie bieżących wydatków.

#### <a name="get-resource-usage-data"></a>Pobierz dane użycia zasobów

Platforma Azure udostępnia zestaw interfejsów API REST rozliczeń, które zapewniają dostęp do zasobów i informacji o metadanych dla subskrypcji platformy Azure. Te interfejsy API rozliczeń zapewniają możliwość lepszego przewidywania kosztów platformy Azure i zarządzania nimi. Wydatki można śledzić i analizować w przyrostach godzinowych i tworzyć alerty wydatków. Możesz również przewidzieć przyszłe rozliczenia w oparciu o bieżące trendy użycia.

>**Wprowadzenie**: Aby dowiedzieć się więcej o korzystaniu z interfejsy API rozliczeń, zobacz temat [Omówienie użycia rozliczeń platformy Azure i interfejsów API RateCard](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Przewidywanie przyszłych kosztów

Chociaż nie jest to trudne do oszacowania kosztów z wyprzedzeniem, platforma Azure oferuje narzędzia, które mogą pomóc. Ma [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) , który pomaga oszacować koszt wdrożonych zasobów. Możesz również użyć zasobów rozliczeń w portalu i interfejsów API REST rozliczeń, aby oszacować przyszłe koszty na podstawie bieżącego zużycia.

>**Wprowadzenie**: zobacz [Omówienie użycia rozliczeń platformy Azure i interfejsów API RateCard](../../cost-management-billing/manage/usage-rate-card-overview.md).