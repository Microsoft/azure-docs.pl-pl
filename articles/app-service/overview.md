---
title: Omówienie
description: Dowiedz się, w jaki sposób usługa Azure App Service ułatwia tworzenie i hostowanie aplikacji internetowych
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767232"
---
# <a name="app-service-overview"></a>Omówienie usługi App Service

*Azure App Service* jest usługą opartą na protokółie HTTP do hostowania aplikacji internetowych, interfejsów API REST i zakańczy mobilnych. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje można łatwo uruchamiać i skalować w środowiskach opartych na systemach Windows [i Linux.](#app-service-on-linux)

Usługa App Service nie tylko dodaje do aplikacji możliwości platformy Microsoft Azure, takie jak zabezpieczenia, równoważenie obciążenia, automatyczne skalowanie i automatyczne zarządzanie. Można również korzystać z jej funkcji metodyki DevOps, takich jak ciągłe wdrażanie z usług Azure DevOps, GitHub, Docker Hub i innych źródeł, zarządzanie pakietami, środowiska przejściowe, domena niestandardowa i certyfikaty TLS/SSL. 

Korzystając z usługi App Service, płacisz tylko za faktycznie wykorzystane zasoby obliczeniowe platformy Azure. Zasoby obliczeniowe, których używasz, są określane przez _App Service,_ w których są uruchamiane aplikacje. Aby uzyskać więcej informacji, zobacz [Omówienie planów usługi App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Dlaczego warto korzystać z usługi App Service?

Poniżej przedstawiono kilka najważniejszych funkcji usługi App Service:

* **Wiele języków i platform** — usługa App Service zapewnia najwyższej jakości obsługę środowisk ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP i Python. Można również uruchamiać [program PowerShell oraz inne skrypty lub pliki wykonywalne](webjobs-create.md) jako usługi w tle.
* **Zarządzane środowisko produkcyjne** — App Service automatycznie poprawek i obsługuje struktury systemu operacyjnego i [języka.](overview-patch-os-runtime.md) Poświęć czas na pisanie wspaniałych aplikacji i pozwól platformie Azure martwić się o platformę.
* **Konteneryzacja i docker —** dockerize aplikacji i hostowanie niestandardowego kontenera systemu Windows lub Linux w App Service. Uruchamianie aplikacji z wieloma kontenerami za pomocą Docker Compose. Migruj swoje umiejętności z platformy Docker bezpośrednio do App Service.
* **Optymalizacja metodyki DevOps** — konfigurowanie [ciągłej integracji i wdrażania](deploy-continuous-deployment.md) za pomocą usług Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Promowanie aktualizacji za pośrednictwem [środowisk testowych i przejściowych](deploy-staging-slots.md). Zarządzaj aplikacjami w usłudze App Service za pomocą programu [Azure PowerShell](/powershell/azure/) lub [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/install-azure-cli).
* **Globalne skalowanie i wysoka dostępność** — ręczne lub automatyczne skalowanie [w pionie](manage-scale-up.md) lub [w poziomie](../azure-monitor/autoscale/autoscale-get-started.md). Hostuj aplikacje w dowolnym miejscu globalnej infrastruktury centrum danych firmy Microsoft i korzystaj z wysokiej dostępności usługi App Service zapewnianej w umowie [SLA](https://azure.microsoft.com/support/legal/sla/app-service/).
* **Połączenia z platformami SaaS i danymi lokalnymi** — możliwość wyboru spośród ponad 50 [łączników](../connectors/apis-list.md) obsługujących systemy dla przedsiębiorstw (takie jak SAP), usługi SaaS (takie jak Salesforce) oraz serwisy internetowe (takie jak Facebook). Dostęp do danych lokalnych przy użyciu [połączeń hybrydowych](app-service-hybrid-connections.md) i [sieci wirtualnych platformy Azure](web-sites-integrate-with-vnet.md).
* **Bezpieczeństwo i zgodność** — usługa App Service jest [zgodna ze standardami ISO, SOC i PCI](https://www.microsoft.com/en-us/trustcenter). Uwierzytelnianie użytkowników za [Azure Active Directory,](configure-authentication-provider-aad.md) [Google,](configure-authentication-provider-google.md) [Facebook,](configure-authentication-provider-facebook.md) [Twitter](configure-authentication-provider-twitter.md) [lub konto Microsoft.](configure-authentication-provider-microsoft.md) Utwórz [ograniczenia adresów IP](app-service-ip-restrictions.md) i [zarządzaj tożsamościami usługi](overview-managed-identity.md).
* **Szablony aplikacji** — możliwość wyboru z obszernej listy szablonów aplikacji dostępnych w portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/), takich jak WordPress, Joomla i Drupal.
* **Visual Studio i Visual Studio Code** — dedykowane narzędzia w usługach Visual Studio i Visual Studio Code usprawniają pracę tworzenia, wdrażania i debugowania.
* **Interfejs API i funkcje mobilne** — usługa App Service zapewnia gotową do użycia obsługę mechanizmu CORS w scenariuszach interfejsów API RESTful i upraszcza scenariusze aplikacji mobilnych dzięki włączeniu uwierzytelniania, synchronizacji danych w trybie offline, powiadomień push itd.
* **Kod niewymagający serwera** — możliwość uruchamiania fragmentów kodu lub skryptów na żądanie bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią, dzięki czemu płacisz tylko za czas obliczeniowy faktycznie używany przez kod (zobacz [Usługa Azure Functions](../azure-functions/index.yml)).

Oprócz usługi App Service platforma Azure oferuje inne usługi, które mogą służyć do hostingu witryn i aplikacji internetowych. W przypadku większości scenariuszy najlepszym wyborem jest usługa App Service.  W przypadku architektury mikrousług rozważ [usługę Azure Spring-Cloud Service](../spring-cloud/index.yml) [lub Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Jeśli potrzebujesz większej kontroli nad maszynami wirtualnych, na których działa kod, rozważ [usługę Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Aby uzyskać więcej informacji o tym, jak dokonać wyboru między usługami Azure, zobacz [Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison](/azure/architecture/guide/technology-choices/compute-decision-tree) (Porównanie usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services).

## <a name="app-service-on-linux"></a>Usługa App Service w systemie Linux

App Service hostuje również aplikacje internetowe natywnie w systemie Linux dla obsługiwanych stosów aplikacji. Może również uruchamiać niestandardowe kontenery systemu Linux (znane również jako Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Wbudowane języki i struktury

App Service dla systemu Linux obsługuje wiele wbudowanych obrazów specyficznych dla języka. Wystarczy wdrożyć kod. Obsługiwane języki to: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core i Ruby. Uruchom [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) , aby wyświetlić najnowsze języki i obsługiwane wersje. Jeśli środowisko uruchomieniowe wymagane przez aplikację nie jest obsługiwane we wbudowanych obrazach, możesz wdrożyć je przy użyciu kontenera niestandardowego.

Nieaktualne środowiska uruchomieniowe są okresowo usuwane z Web Apps tworzenia i konfiguracji w portalu. Te środowiska uruchomieniowe są ukryte przed portalem, gdy są przestarzałe przez organizację konserwacyjną lub mają znaczące luki w zabezpieczeniach. Te opcje są ukryte, aby kierować klientów do najnowszych środowisk uruchomieniowych, w których będą najskuteczniejsze. 

Gdy nieaktualne środowisko uruchomieniowe jest ukryte w portalu, wszystkie istniejące witryny korzystające z tej wersji będą nadal działać. Jeśli środowisko uruchomieniowe zostanie całkowicie usunięte z platformy App Service, właściciele subskrypcji platformy Azure otrzymają powiadomienie e-mail przed usunięciem.

Jeśli musisz utworzyć inną aplikację internetową z nieaktualną wersją środowiska uruchomieniowego, która nie jest już wyświetlana w portalu, zobacz przewodniki konfiguracji języka, aby uzyskać instrukcje dotyczące sposobu uzyskania wersji środowiska uruchomieniowego witryny. Za pomocą interfejsu wiersza polecenia platformy Azure możesz utworzyć inną witrynę z tym samym środowiskiem uruchomieniowym. Możesz też użyć przycisku Eksportuj szablon w bloku aplikacji internetowej w portalu, aby wyeksportować szablon arm witryny.  Możesz ponownie użyć tego szablonu, aby wdrożyć nową lokację z tym samym środowiskiem uruchomieniowym i konfiguracją.

### <a name="limitations"></a>Ograniczenia

- App Service dla systemu Linux nie jest obsługiwana w [warstwie cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/) Współdzielona. 
- Nie można mieszać aplikacji dla systemów Windows i Linux w tym samym App Service planie.  
- W przeszłości nie można mieszać aplikacji dla systemów Windows i Linux w tej samej grupie zasobów. Jednak wszystkie grupy zasobów utworzone w dniu 21 stycznia 2021 r. lub później obsługują ten scenariusz. W przypadku grup zasobów utworzonych przed 21 stycznia 2021 r. możliwość dodawania wdrożeń na platformach mieszanych zostanie wkrótce wywłaszczana w regionach świadczenia usługi Azure (w tym w krajowych regionach chmury).
- W Azure Portal są tylko funkcje, które obecnie działają w przypadku aplikacji systemu Linux. Po włączeniu funkcji są one aktywowane w portalu.
- Po wdrożeniu na wbudowanych obrazach kod i zawartość są przydzielane do woluminu magazynu zawartości internetowej, który jest zapasowy przez usługę Azure Storage. Opóźnienie dysku tego woluminu jest większe i bardziej zmienne niż opóźnienie systemu plików kontenera. Aplikacje wymagające intensywnego dostępu tylko do odczytu do plików zawartości mogą korzystać z opcji kontenera niestandardowego, która umieszcza pliki w systemie plików kontenera, a nie na woluminie zawartości.

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację internetową.

> [!div class="nextstepaction"]
> [ASP.NET Core (w systemie Windows lub Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (w systemie Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (w systemie Windows lub Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (w systemie Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (w systemie Windows lub Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (w systemie Windows lub Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (w systemie Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (w systemie Windows lub Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Kontener niestandardowy (Windows lub Linux)](tutorial-custom-container.md)
