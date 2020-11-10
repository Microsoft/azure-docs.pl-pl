---
title: Opcje sieciowe usługi Azure Functions
description: Przegląd wszystkich opcji sieciowych dostępnych w Azure Functions.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: jehollan
ms.openlocfilehash: 691fbf3be4e39a724a8a290c3ec147a679013cba
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413092"
---
# <a name="azure-functions-networking-options"></a>Opcje sieciowe usługi Azure Functions

W tym artykule opisano funkcje sieciowe dostępne w obszarze Opcje hostingu dla Azure Functions. Wszystkie poniższe opcje sieciowe zapewniają pewne możliwości dostępu do zasobów bez używania adresów z obsługą sieci Internet lub w celu ograniczenia dostępu do Internetu do aplikacji funkcji.

Modele hostingu mają różne poziomy izolacji sieci. Wybranie odpowiedniej opcji pomaga spełnić wymagania dotyczące izolacji sieci.

Aplikacje funkcji można hostować na kilka sposobów:

* Możesz wybrać spośród opcji planu, które są uruchamiane w infrastrukturze wielodostępnej, z różnymi poziomami łączności sieci wirtualnej i opcji skalowania:
    * [Plan zużycia](functions-scale.md#consumption-plan) jest skalowany dynamicznie w odpowiedzi na obciążenie i oferuje minimalne opcje izolacji sieci.
    * [Plan Premium](functions-scale.md#premium-plan) również skaluje się dynamicznie i oferuje bardziej kompleksową izolację sieci.
    * [Plan App Service](functions-scale.md#app-service-plan) platformy Azure działa ze stałą skalą i oferuje izolację sieci podobną do planu Premium.
* Funkcje można uruchamiać w [App Service Environment](../app-service/environment/intro.md). Ta metoda wdraża funkcję w sieci wirtualnej i oferuje pełną kontrolę sieci i izolację.

## <a name="matrix-of-networking-features"></a>Macierz funkcji sieciowych

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>Ograniczenia przychodzącego adresu IP

Za pomocą ograniczeń adresów IP można zdefiniować uporządkowaną według priorytetu listę adresów IP, które są dozwolone lub odrzucane przez dostęp do aplikacji. Lista może zawierać adresy IPv4 i IPv6. W przypadku co najmniej jednego wpisu na końcu listy występuje niejawne "odmowa wszystkich". Ograniczenia adresów IP działają ze wszystkimi opcjami hostingu funkcji.

> [!NOTE]
> W przypadku ograniczeń sieci można używać edytora portalu tylko z poziomu sieci wirtualnej lub po umieszczeniu adresu IP komputera, którego używasz, aby uzyskać dostęp do Azure Portal na liście bezpiecznych adresatów. Można jednak nadal uzyskiwać dostęp do dowolnych funkcji na karcie **funkcje platformy** z dowolnego komputera.

Aby dowiedzieć się więcej, zobacz [Azure App Service ograniczenia dostępu statycznego](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Integracja z siecią wirtualną umożliwia aplikacji funkcji dostęp do zasobów w sieci wirtualnej.
Azure Functions obsługuje dwa rodzaje integracji sieci wirtualnej:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integracja sieci wirtualnej w programie Azure Functions używa infrastruktury udostępnionej z App Service aplikacjami sieci Web. Aby dowiedzieć się więcej o dwóch typach integracji z siecią wirtualną, zobacz:

* [Integracja regionalnej sieci wirtualnej](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integracja z siecią wirtualną wymagana przez bramę](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Aby dowiedzieć się, jak skonfigurować integrację sieci wirtualnej, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną platformy Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integracja regionalnej sieci wirtualnej

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Łączenie z zabezpieczonymi zasobami punktu końcowego usługi

Aby zapewnić wyższy poziom zabezpieczeń, można ograniczyć liczbę usług platformy Azure do sieci wirtualnej za pomocą punktów końcowych usługi. Następnie należy zintegrować aplikację funkcji z tą siecią wirtualną, aby uzyskać dostęp do zasobu. Ta konfiguracja jest obsługiwana we wszystkich planach, które obsługują integrację z siecią wirtualną.

Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network-preview"></a>Ogranicz konto magazynu do sieci wirtualnej (wersja zapoznawcza)

Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel.  Możesz zamienić to konto magazynu na takie, które jest zabezpieczone za pomocą punktów końcowych usługi lub prywatnego punktu końcowego.  Ta funkcja w wersji zapoznawczej obecnie działa tylko z planami systemu Windows Premium w regionie Europa Zachodnia.  Aby skonfigurować funkcję z kontem magazynu ograniczonym do sieci prywatnej:

> [!NOTE]
> Ograniczanie konta magazynu aktualnie działa tylko w przypadku funkcji premium przy użyciu systemu Windows w Europie zachodniej

1. Utwórz funkcję z kontem magazynu, które nie ma włączonych punktów końcowych usługi.
1. Skonfiguruj funkcję do łączenia się z siecią wirtualną.
1. Utwórz lub skonfiguruj inne konto magazynu.  To konto magazynu jest zabezpieczane za pomocą punktów końcowych usługi i łącz naszą funkcję.
1. [Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md#create-file-share) na koncie bezpiecznego magazynu.
1. Włącz punkty końcowe usługi lub prywatny punkt końcowy dla konta magazynu.  
    * Należy pamiętać, aby włączyć podsieć dedykowaną aplikacjom funkcji, jeśli jest używany punkt końcowy usługi.
    * Pamiętaj, aby utworzyć rekord DNS i skonfigurować aplikację do [pracy z prywatnymi](#azure-dns-private-zones) punktami końcowymi punktów końcowych, jeśli używany jest prywatny punkt końcowy.  Konto magazynu będzie wymagało prywatnego punktu końcowego dla `file` `blob` zasobów podrzędnych i.  W przypadku korzystania z pewnych funkcji, takich jak Durable Functions, będzie również potrzebne `queue` i `table` dostępne za pośrednictwem połączenia prywatnego punktu końcowego.
1. Obowiązkowe Skopiuj zawartość pliku i obiektu BLOB z konta magazynu aplikacji funkcji na zabezpieczone konto magazynu i udział plików.
1. Skopiuj parametry połączenia dla tego konta magazynu.
1. Zaktualizuj **Ustawienia aplikacji** w obszarze **Konfiguracja** dla aplikacji funkcji w następujący sposób:
    - `AzureWebJobsStorage` do parametrów połączenia dla zabezpieczonego konta magazynu.
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` do parametrów połączenia dla zabezpieczonego konta magazynu.
    - `WEBSITE_CONTENTSHARE` Nazwa udziału plików utworzonego na koncie bezpiecznego magazynu.
    - Utwórz nowe ustawienie o nazwie `WEBSITE_CONTENTOVERVNET` i wartości `1` .
1. Zapisz ustawienia aplikacji.  

Aplikacja funkcji zostanie uruchomiona ponownie i zostanie teraz połączona z bezpiecznym kontem magazynu.

## <a name="use-key-vault-references"></a>Używanie odwołań do usługi Key Vault

Możesz użyć odwołań Azure Key Vault, aby użyć wpisów tajnych z Azure Key Vault w aplikacji Azure Functions bez konieczności wprowadzania żadnych zmian w kodzie. Azure Key Vault to usługa zapewniająca scentralizowane zarządzanie kluczami tajnymi, z pełną kontrolą nad zasadami dostępu i historią inspekcji.

Obecnie [odwołania Key Vault](../app-service/app-service-key-vault-references.md) nie będą działały, jeśli Magazyn kluczy jest zabezpieczony za pomocą punktów końcowych usługi. Aby nawiązać połączenie z magazynem kluczy przy użyciu integracji z siecią wirtualną, należy wywołać Key Vault w kodzie aplikacji.

## <a name="virtual-network-triggers-non-http"></a>Wyzwalacze sieci wirtualnej (bez protokołu HTTP)

Obecnie można używać funkcji wyzwalacza innego niż HTTP z poziomu sieci wirtualnej na jeden z dwóch sposobów:

+ Uruchom aplikację funkcji w planie Premium i Włącz obsługę wyzwalacza sieci wirtualnej.
+ Uruchom aplikację funkcji w planie App Service lub App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan Premium z wyzwalaczami sieci wirtualnej

Po uruchomieniu planu Premium można połączyć funkcje wyzwalacza inne niż HTTP z usługami, które działają w sieci wirtualnej. W tym celu należy włączyć obsługę wyzwalacza sieci wirtualnej dla aplikacji funkcji. Ustawienie **monitorowania skali środowiska uruchomieniowego** znajduje się w [Azure Portal](https://portal.azure.com) w **Configuration** obszarze  >  **Ustawienia środowiska uruchomieniowego funkcji** konfiguracji.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Wyzwalacze sieci wirtualnej można również włączyć za pomocą następującego polecenia interfejsu CLI platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Włączenie wyzwalaczy sieci wirtualnej może mieć wpływ na wydajność aplikacji, ponieważ wystąpienia planu App Service muszą monitorować Wyzwalacze w celu określenia czasu skalowania. Ten wpływ prawdopodobnie jest bardzo mały.

Wyzwalacze sieci wirtualnej są obsługiwane w wersji 2. x i powyżej środowiska uruchomieniowego funkcji. Obsługiwane są następujące typy wyzwalaczy inne niż HTTP.

| Rozszerzenie | Minimalna wersja |
|-----------|---------| 
|[Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 lub nowszy |
|[Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 lub nowszy|

> [!IMPORTANT]
> Po włączeniu obsługi wyzwalacza sieci wirtualnej tylko typy wyzwalaczy widoczne w poprzedniej tabeli są dynamicznie skalowane z aplikacją. Nadal można używać wyzwalaczy, które nie znajdują się w tabeli, ale nie są skalowane poza liczbą wystąpień sprzed obgrzanych. Aby uzyskać pełną listę wyzwalaczy, zobacz [wyzwalacze i powiązania](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service planowanie i App Service Environment przy użyciu wyzwalaczy sieci wirtualnej

Gdy aplikacja funkcji działa w planie App Service lub App Service Environment, można użyć funkcji wyzwalacza innego niż HTTP. Aby funkcje zostały wyzwolone prawidłowo, musisz mieć połączenie z siecią wirtualną z dostępem do zasobu zdefiniowanego w połączeniu wyzwalacza.

Załóżmy na przykład, że chcesz skonfigurować Azure Cosmos DB, aby akceptować tylko ruch z sieci wirtualnej. W takim przypadku należy wdrożyć aplikację funkcji w planie App Service, która zapewnia integrację sieci wirtualnej z tą siecią wirtualną. Integracja umożliwia wyzwolenie funkcji przez ten Azure Cosmos DB zasób.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

[Połączenia hybrydowe](../azure-relay/relay-hybrid-connections-protocol.md) to funkcja Azure Relay, z której można korzystać w celu uzyskania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można używać go do uzyskiwania dostępu do aplikacji. Połączenia hybrydowe jest dostępny dla funkcji uruchamianych w systemie Windows we wszystkich, ale w planie zużycia.

Jak w Azure Functions każde połączenie hybrydowe jest skorelowane z pojedynczym hostem TCP i kombinacją portów. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, o ile jest uzyskiwany dostęp do portu nasłuchiwania protokołu TCP. Funkcja Połączenia hybrydowe nie wie ani nie ma informacji o tym, czym jest lub do czego masz dostęp. Zapewnia dostęp do sieci.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją dotyczącą [App Service połączenia hybrydowe](../app-service/app-service-hybrid-connections.md). Te same kroki konfiguracji obsługują Azure Functions.

>[!IMPORTANT]
> Połączenia hybrydowe jest obsługiwana tylko w planach systemu Windows. System Linux nie jest obsługiwany.

## <a name="outbound-ip-restrictions"></a>Ograniczenia wychodzącego adresu IP

Ograniczenia wychodzącego adresu IP są dostępne w planie Premium, planie App Service lub App Service Environment. Można skonfigurować ograniczenia ruchu wychodzącego dla sieci wirtualnej, w której wdrożono App Service Environment.

W przypadku integrowania aplikacji funkcji w planie Premium lub planu App Service z siecią wirtualną aplikacja nadal może domyślnie nawiązywać połączenia wychodzące do Internetu. Po dodaniu ustawienia aplikacji `WEBSITE_VNET_ROUTE_ALL=1` wymusisz, aby cały ruch wychodzący był wysyłany do sieci wirtualnej, w którym można używać zasad grupy zabezpieczeń sieci do ograniczania ruchu.

## <a name="automation"></a>Automation
Poniższe interfejsy API umożliwiają programowe zarządzanie integracją regionalnej sieci wirtualnej:

+ **Interfejs wiersza polecenia platformy Azure** : Użyj [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) poleceń do dodawania, wyświetlania lub usuwania integracji regionalnej sieci wirtualnej.  
+ **Szablony usługi ARM** : integracja regionalnej sieci wirtualnej można włączyć przy użyciu szablonu Azure Resource Manager. Aby zapoznać się z pełnymi przykładami, zobacz [ten szablon funkcji szybkiego startu](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sieci i Azure Functions:

* [Postępuj zgodnie z samouczkiem dotyczącym rozpoczynania pracy z integracją z siecią wirtualną](./functions-create-vnet.md)
* [Przeczytaj informacje o usłudze Functions — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej o integracji z siecią wirtualną za pomocą App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączanie większej liczby funkcji sieciowych i kontroli w środowiskach App Service](../app-service/environment/intro.md)
* [Nawiąż połączenie z lokalnymi zasobami bez zmian w zaporze przy użyciu Połączenia hybrydowe](../app-service/app-service-hybrid-connections.md)
