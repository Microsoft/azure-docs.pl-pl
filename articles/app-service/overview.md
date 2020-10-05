---
title: Omówienie
description: Dowiedz się, w jaki sposób usługa Azure App Service ułatwia tworzenie i hostowanie aplikacji internetowych
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961486"
---
# <a name="app-service-overview"></a>Omówienie usługi App Service

*Azure App Service* to usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i zaplecza mobilnego. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje są uruchamiane i skalowane z łatwością w przypadku środowisk opartych na systemie Windows i [Linux](#app-service-on-linux).

Usługa App Service nie tylko dodaje do aplikacji możliwości platformy Microsoft Azure, takie jak zabezpieczenia, równoważenie obciążenia, automatyczne skalowanie i automatyczne zarządzanie. Możesz również skorzystać z możliwości DevOps, takich jak ciągłe wdrażanie z usługi Azure DevOps, GitHub, Hub Docker i innych źródeł, zarządzania pakietami, środowisk przejściowych, domeny niestandardowej i certyfikatów TLS/SSL. 

Korzystając z usługi App Service, płacisz tylko za faktycznie wykorzystane zasoby obliczeniowe platformy Azure. Używane zasoby obliczeniowe są określane przez _plan App Service_ , w którym uruchamiane są aplikacje. Aby uzyskać więcej informacji, zobacz [Omówienie planów usługi App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Dlaczego warto korzystać z usługi App Service?

Poniżej przedstawiono kilka najważniejszych funkcji usługi App Service:

* **Wiele języków i platform** — usługa App Service zapewnia najwyższej jakości obsługę środowisk ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP i Python. Można również uruchamiać [program PowerShell oraz inne skrypty lub pliki wykonywalne](webjobs-create.md) jako usługi w tle.
* **Zarządzane środowisko produkcyjne** — App Service automatycznie [poprawki i obsługuje platformy systemu operacyjnego i języka](overview-patch-os-runtime.md) . Poświęcaj czas na pisanie doskonałych aplikacji i zezwól platformie Azure na działanie platformy.
* **Kontenerach i Docker** -przekształcać aplikację i obsługują niestandardowy kontener systemu Windows lub Linux w App Service. Uruchom aplikacje z obsługą kontenerów z Docker Compose. Przeprowadź migrację swoich umiejętności platformy Docker bezpośrednio do App Service.
* **Optymalizacja metodyki DevOps** — konfigurowanie [ciągłej integracji i wdrażania](deploy-continuous-deployment.md) za pomocą usług Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Promowanie aktualizacji za pośrednictwem [środowisk testowych i przejściowych](deploy-staging-slots.md). Zarządzaj aplikacjami w usłudze App Service za pomocą programu [Azure PowerShell](/powershell/azure/) lub [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/install-azure-cli).
* **Globalne skalowanie i wysoka dostępność** — ręczne lub automatyczne skalowanie [w pionie](manage-scale-up.md) lub [w poziomie](../azure-monitor/platform/autoscale-get-started.md). Hostuj aplikacje w dowolnym miejscu globalnej infrastruktury centrum danych firmy Microsoft i korzystaj z wysokiej dostępności usługi App Service zapewnianej w umowie [SLA](https://azure.microsoft.com/support/legal/sla/app-service/).
* **Połączenia z platformami SaaS i danymi lokalnymi** — możliwość wyboru spośród ponad 50 [łączników](../connectors/apis-list.md) obsługujących systemy dla przedsiębiorstw (takie jak SAP), usługi SaaS (takie jak Salesforce) oraz serwisy internetowe (takie jak Facebook). Dostęp do danych lokalnych przy użyciu [połączeń hybrydowych](app-service-hybrid-connections.md) i [sieci wirtualnych platformy Azure](web-sites-integrate-with-vnet.md).
* **Bezpieczeństwo i zgodność** — usługa App Service jest [zgodna ze standardami ISO, SOC i PCI](https://www.microsoft.com/en-us/trustcenter). Uwierzytelnianie użytkowników za pomocą [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md)lub [konto Microsoft](configure-authentication-provider-microsoft.md). Utwórz [ograniczenia adresów IP](app-service-ip-restrictions.md) i [zarządzaj tożsamościami usługi](overview-managed-identity.md).
* **Szablony aplikacji** — możliwość wyboru z obszernej listy szablonów aplikacji dostępnych w portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/), takich jak WordPress, Joomla i Drupal.
* **Program Visual Studio i narzędzie Visual Studio Code Integration** — dedykowane narzędzia w programie Visual studio i Visual Studio Code usprawniają tworzenie, wdrażanie i debugowanie.
* **Interfejs API i funkcje mobilne** — usługa App Service zapewnia gotową do użycia obsługę mechanizmu CORS w scenariuszach interfejsów API RESTful i upraszcza scenariusze aplikacji mobilnych dzięki włączeniu uwierzytelniania, synchronizacji danych w trybie offline, powiadomień push itd.
* **Kod niewymagający serwera** — możliwość uruchamiania fragmentów kodu lub skryptów na żądanie bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią, dzięki czemu płacisz tylko za czas obliczeniowy faktycznie używany przez kod (zobacz [Usługa Azure Functions](../azure-functions/index.yml)).

Oprócz usługi App Service platforma Azure oferuje inne usługi, które mogą służyć do hostingu witryn i aplikacji internetowych. W przypadku większości scenariuszy najlepszym wyborem jest usługa App Service.  W przypadku architektury mikrousług należy rozważyć użycie [usługi Azure wiosennej](../spring-cloud/index.yml) lub [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Jeśli potrzebujesz większej kontroli nad maszynami wirtualnymi, na których działa kod, weź pod uwagę [Virtual Machines platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Aby uzyskać więcej informacji o tym, jak dokonać wyboru między usługami Azure, zobacz [Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison](/azure/architecture/guide/technology-choices/compute-decision-tree) (Porównanie usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services).

## <a name="app-service-on-linux"></a>Usługa App Service w systemie Linux

App Service mogą również hostować aplikacje sieci Web w systemie Linux w przypadku obsługiwanych stosów aplikacji. Może również uruchamiać niestandardowe kontenery systemu Linux (znane również jako Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Wbudowane Języki i struktury

App Service w systemie Linux obsługuje wiele wbudowanych obrazów specyficznych dla języka. Po prostu Wdróż swój kod. Obsługiwane języki to: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core i Ruby. Uruchom, [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) Aby wyświetlić najnowsze Języki i obsługiwane wersje. Jeśli środowisko uruchomieniowe wymagane przez aplikację nie jest obsługiwane w wbudowanych obrazach, można je wdrożyć z kontenerem niestandardowym.

### <a name="limitations"></a>Ograniczenia

- App Service w systemie Linux nie jest obsługiwana w warstwie cenowej [udostępnionej](https://azure.microsoft.com/pricing/details/app-service/plans/) . 
- Nie można mieszać aplikacji systemu Windows i Linux w ramach tego samego planu App Service.  
- W tej samej grupie zasobów nie można mieszać aplikacji systemu Windows i Linux w tym samym regionie.
- W Azure Portal są wyświetlane tylko funkcje, które obecnie działają w przypadku aplikacji systemu Linux. Gdy funkcje są włączone, są aktywowane w portalu.
- Po wdrożeniu wbudowanych obrazów do kodu i zawartości są przydzielone woluminy magazynu dla zawartości sieci Web, obsługiwane przez usługę Azure Storage. Opóźnienie dysku tego woluminu jest większe i bardziej zmienne niż opóźnienie systemu plików kontenera. Aplikacje wymagające dużego dostępu tylko do odczytu do plików zawartości mogą korzystać z opcji niestandardowego kontenera, która umieszcza pliki w systemie plików kontenera zamiast na woluminie zawartości.

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
> [Kontener niestandardowy (system Windows lub Linux)](tutorial-custom-container.md)