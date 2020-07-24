---
title: Omówienie
description: Dowiedz się, w jaki sposób usługa Azure App Service ułatwia tworzenie i hostowanie aplikacji internetowych
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 04/30/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: f4b42f2ed43da68daabb3bd334a362071202be42
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073717"
---
# <a name="app-service-overview"></a>Omówienie usługi App Service

*Azure App Service* to usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i zaplecza mobilnego. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje można łatwo uruchamiać i skalować w środowiskach opartych zarówno na systemie Windows, jak i Linux. W przypadku środowisk opartych na systemie Linux zobacz [Usługa App Service w systemie Linux](containers/app-service-linux-intro.md). 

Usługa App Service nie tylko dodaje do aplikacji możliwości platformy Microsoft Azure, takie jak zabezpieczenia, równoważenie obciążenia, automatyczne skalowanie i automatyczne zarządzanie. Możesz również skorzystać z możliwości DevOps, takich jak ciągłe wdrażanie z usługi Azure DevOps, GitHub, Hub Docker i innych źródeł, zarządzania pakietami, środowisk przejściowych, domeny niestandardowej i certyfikatów TLS/SSL. 

Korzystając z usługi App Service, płacisz tylko za faktycznie wykorzystane zasoby obliczeniowe platformy Azure. Używane zasoby obliczeniowe są określane przez _plan App Service_ , w którym uruchamiane są aplikacje. Aby uzyskać więcej informacji, zobacz [Omówienie planów usługi App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Dlaczego warto korzystać z usługi App Service?

Poniżej przedstawiono kilka najważniejszych funkcji usługi App Service:

* **Wiele języków i platform** — usługa App Service zapewnia najwyższej jakości obsługę środowisk ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP i Python. Można również uruchamiać [program PowerShell oraz inne skrypty lub pliki wykonywalne](webjobs-create.md) jako usługi w tle.
* **Zarządzane środowisko produkcyjne** — App Service automatycznie [poprawki i obsługuje platformy systemu operacyjnego i języka](overview-patch-os-runtime.md) . Poświęcaj czas na pisanie doskonałych aplikacji i zezwól platformie Azure na działanie platformy.
* **Optymalizacja metodyki DevOps** — konfigurowanie [ciągłej integracji i wdrażania](deploy-continuous-deployment.md) za pomocą usług Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Promowanie aktualizacji za pośrednictwem [środowisk testowych i przejściowych](deploy-staging-slots.md). Zarządzaj aplikacjami w usłudze App Service za pomocą programu [Azure PowerShell](/powershell/azure/) lub [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/install-azure-cli).
* **Globalne skalowanie i wysoka dostępność** — ręczne lub automatyczne skalowanie [w pionie](manage-scale-up.md) lub [w poziomie](../monitoring-and-diagnostics/insights-how-to-scale.md). Hostuj aplikacje w dowolnym miejscu globalnej infrastruktury centrum danych firmy Microsoft i korzystaj z wysokiej dostępności usługi App Service zapewnianej w umowie [SLA](https://azure.microsoft.com/support/legal/sla/app-service/).
* **Połączenia z platformami SaaS i danymi lokalnymi** — możliwość wyboru spośród ponad 50 [łączników](../connectors/apis-list.md) obsługujących systemy dla przedsiębiorstw (takie jak SAP), usługi SaaS (takie jak Salesforce) oraz serwisy internetowe (takie jak Facebook). Dostęp do danych lokalnych przy użyciu [połączeń hybrydowych](app-service-hybrid-connections.md) i [sieci wirtualnych platformy Azure](web-sites-integrate-with-vnet.md).
* **Bezpieczeństwo i zgodność** — usługa App Service jest [zgodna ze standardami ISO, SOC i PCI](https://www.microsoft.com/en-us/trustcenter). Uwierzytelnianie użytkowników za pomocą [usługi Azure Active Directory](configure-authentication-provider-aad.md) lub danych logowania z serwisów społecznościowych ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) i [ Microsoft](configure-authentication-provider-microsoft.md)). Utwórz [ograniczenia adresów IP](app-service-ip-restrictions.md) i [zarządzaj tożsamościami usługi](overview-managed-identity.md).
* **Szablony aplikacji** — możliwość wyboru z obszernej listy szablonów aplikacji dostępnych w portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/), takich jak WordPress, Joomla i Drupal.
* **Integracja z programem Visual Studio** —dedykowane narzędzia w programie Visual Studio usprawniają procesy tworzenia, wdrażania i debugowania.
* **Interfejs API i funkcje mobilne** — usługa App Service zapewnia gotową do użycia obsługę mechanizmu CORS w scenariuszach interfejsów API RESTful i upraszcza scenariusze aplikacji mobilnych dzięki włączeniu uwierzytelniania, synchronizacji danych w trybie offline, powiadomień push itd.
* **Kod niewymagający serwera** — możliwość uruchamiania fragmentów kodu lub skryptów na żądanie bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią, dzięki czemu płacisz tylko za czas obliczeniowy faktycznie używany przez kod (zobacz [Usługa Azure Functions](/azure/azure-functions/)).

Oprócz usługi App Service platforma Azure oferuje inne usługi, które mogą służyć do hostingu witryn i aplikacji internetowych. W przypadku większości scenariuszy najlepszym wyborem jest usługa App Service.  W przypadku architektury mikrousług należy rozważyć usługę [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Aby uzyskać więcej informacji o tym, jak dokonać wyboru między usługami Azure, zobacz [Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison](overview-compare.md) (Porównanie usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services).

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację internetową.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (w systemie Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (w systemie Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
