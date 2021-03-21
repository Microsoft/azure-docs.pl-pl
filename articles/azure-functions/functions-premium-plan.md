---
title: Plan Azure Functions Premium
description: Szczegóły i opcje konfiguracji (Sieć wirtualna, brak nieograniczonego czasu wykonywania) dla planu Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708717"
---
# <a name="azure-functions-premium-plan"></a>Plan Azure Functions Premium

Plan Azure Functions Premium (nazywany czasem elastycznym planem Premium) jest opcją hostingu dla aplikacji funkcji. Aby poznać inne opcje planu hostingu, zobacz [artykuł plan hostingu](functions-scale.md).

Hosting planu Premium zapewnia następujące korzyści dla funkcji:

* Unikaj zimnego uruchamiania z wystąpieniami bezterminowo-ciepłymi
* Łączność sieci wirtualnej.
* Nieograniczony czas wykonywania w przypadku 60 minut.
* Rozmiary wystąpienia Premium: jeden rdzeń, dwa rdzenie i cztery rdzenie.
* Bardziej przewidywalne ceny w porównaniu z planem zużycia.
* Alokacja aplikacji o wysokiej gęstości dla planów z wieloma aplikacjami funkcji.

W przypadku korzystania z planu Premium wystąpienia hosta Azure Functions są dodawane i usuwane na podstawie liczby zdarzeń przychodzących, podobnie jak w przypadku [planu zużycia](consumption-plan.md). Wiele aplikacji funkcji można wdrożyć w tym samym planie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowego, rozmiaru planu bazowego i maksymalnego rozmiaru planu. 

## <a name="billing"></a>Rozliczenia

Opłaty za plan Premium są naliczane na podstawie liczby podstawowych sekund i przydziałów pamięci w różnych wystąpieniach. Ta rozliczenia różni się od planu zużycia, który jest rozliczany za wykonywanie i zużywaną pamięć. Z planem Premium nie jest naliczana opłata za wykonanie. Co najmniej jedno wystąpienie musi być przydzieloną przez cały czas dla każdego planu. Ta rozliczenia skutkuje minimalnym miesięcznym kosztem dla aktywnego planu, niezależnie od tego, czy funkcja jest aktywna, czy bezczynna. Należy pamiętać, że wszystkie aplikacje funkcji w planie Premium współdzielą wystąpienia przydzielono. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Podczas tworzenia aplikacji funkcji w Azure Portal jest to domyślny plan zużycia. Aby utworzyć aplikację funkcji, która jest uruchamiana w planie Premium, należy jawnie utworzyć plan App Service przy użyciu jednej z elastycznych jednostek SKU w _warstwie Premium_ . Utworzona aplikacja funkcji jest następnie hostowana w tym planie. Azure Portal ułatwia tworzenie zarówno planu Premium, jak i aplikacji funkcji w tym samym czasie. W tym samym planie Premium można uruchomić więcej niż jedną aplikację funkcji, ale obie te funkcje są uruchamiane w tym samym systemie operacyjnym (Windows lub Linux). 

W poniższych artykułach pokazano, jak utworzyć aplikację funkcji z planem Premium (programowo lub w Azure Portal):

+ [Witryna Azure Portal](create-premium-plan-function-app-portal.md)
+ [Interfejs wiersza polecenia platformy Azure](scripts/functions-cli-create-premium-plan.md)
+ [Szablon usługi Azure Resource Manager](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Eliminowanie zimnego startu

Gdy zdarzenia lub wykonania nie wystąpią w planie zużycia, aplikacja może skalować do zero wystąpień. Gdy nowe zdarzenia są dostępne, nowe wystąpienie z uruchomioną aplikacją musi być wyspecjalizowane. Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji. To dodatkowe opóźnienie pierwszego wywołania jest często nazywane aplikacją _zimnego startu_.

Plan Premium oferuje dwie funkcje, które współpracują ze sobą w celu efektywnego wyeliminowania zimnego startu w funkcjach: _zawsze gotowe wystąpienia_ i _wstępnie rozgrzane wystąpienia_. 

### <a name="always-ready-instances"></a>Zawsze gotowe wystąpienia

W planie Premium aplikacja zawsze będzie gotowa na określoną liczbę wystąpień. Maksymalna liczba wystąpień zawsze gotowe jest równa 20. Gdy zdarzenia zaczynają wyzwalać aplikację, są one najpierw kierowane do zawsze przygotowanych wystąpień. Gdy funkcja zostanie uaktywniona, kolejne wystąpienia zostaną rozgrzane jako bufor. Ten bufor zapobiega zimnemu uruchomieniu dla nowych wystąpień wymaganych podczas skalowania. Te buforowane wystąpienia są nazywane [wstępnie rozgrzanymi wystąpieniami](#pre-warmed-instances). Wraz z kombinacją zawsze przygotowanych wystąpień i wstępnie wygrzanego buforu aplikacja może skutecznie wyeliminować zimne uruchomienie.

> [!NOTE]
> Każdy plan Premium ma co najmniej jedno aktywne (rozliczane) wystąpienie przez cały czas.

# <a name="portal"></a>[Portal](#tab/portal)

Liczbę zawsze przygotowanych wystąpień można skonfigurować w Azure Portal przez wybranie **aplikacja funkcji**, przejście na kartę **funkcje platformy** i wybranie opcji **skalowania w poziomie** . W oknie Edycja aplikacji funkcji zawsze gotowe wystąpienia są specyficzne dla tej aplikacji.

![Ustawienia skalowania elastycznego](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Możesz również skonfigurować zawsze gotowe wystąpienia dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Wystąpienia przed wystąpieniem

Wystąpienia z góry są rozgrzane jako bufory podczas zdarzeń skalowania i aktywacji. Wystąpienia przed osiągnięciem maksymalnego limitu skalowania są nadal buforowane. Domyślna liczba wystąpień wstępnie rozgrzanych to 1, a w przypadku większości scenariuszy ta wartość powinna pozostać równa 1.

Gdy aplikacja ma długotrwałą rozgrzewanie (na przykład niestandardowy obraz kontenera), może być konieczne zwiększenie tego buforu. Wystąpienie preinstalowane stanie się aktywne dopiero po wystarczająco że wszystkie aktywne wystąpienia zostaną odpowiednio wykorzystane.

Rozważmy ten przykład, jak zawsze gotowe wystąpienia i wstępnie rozgrzane wystąpienia współpracują ze sobą. Aplikacja funkcji Premium ma skonfigurowane pięć zawsze przygotowanych wystąpień i domyślnie jedno wystąpienie wstępne. Gdy aplikacja jest bezczynna i żadne zdarzenia nie są wyzwalane, aplikacja zostanie zainicjowana i uruchomiona z pięcioma wystąpieniami. W tej chwili nie są naliczane opłaty za wystąpienie z góry, ponieważ wystąpienia zawsze gotowe nie są używane i nie przydzielono żadnego wystąpienia.

Zaraz po otrzymaniu pierwszego wyzwalacza, pięć wystąpień zawsze gotowych staje się aktywny i przydzielono wystąpienie z góry. Aplikacja działa teraz z sześcioma zainicjowanymi wystąpieniami: pięć teraz — aktywne zawsze gotowe wystąpienia i szósty, wstępnie rozgrzany i nieaktywny bufor. Jeśli szybkość wykonywania nadal rośnie, pięć aktywnych wystąpień jest ostatecznie używanych. Gdy Platforma zdecyduje się skalować więcej niż pięć wystąpień, skaluje się do wystąpienia wstępnego. W takim przypadku dostępne są sześć aktywnych wystąpień, a siódme wystąpienie jest natychmiast udostępniane i wypełnia bufor przedgrzany. Ta sekwencja skalowania i wstępnego rozgrzewania jest kontynuowana do momentu osiągnięcia maksymalnej liczby wystąpień aplikacji. Żadne wystąpienia nie są wstępnie rozgrzane lub aktywowane poza wartością maksymalną.

Liczbę wstępnie rozgrzanych wystąpień aplikacji można zmodyfikować przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Maksymalna liczba wystąpień aplikacji funkcji

Oprócz [maksymalnej liczby wystąpień planu](#plan-and-sku-settings)można skonfigurować wartość maksymalną dla aplikacji. Maksymalną liczbę aplikacji można skonfigurować przy użyciu [limitu skalowania aplikacji](./event-driven-scaling.md#limit-scale-out).

## <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Aplikacje funkcji wdrożone w ramach planu Premium mogą korzystać z [integracji sieci wirtualnej dla usługi Web Apps](../app-service/web-sites-integrate-with-vnet.md). Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczonymi za pośrednictwem punktów końcowych usługi. Ograniczenia adresów IP są również dostępne w aplikacji w celu ograniczenia ruchu przychodzącego.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium potrzebna jest podsieć z wystarczającą liczbą adresów IP dla każdego potencjalnego wystąpienia. Wymagamy bloku IP z co najmniej 100 dostępnych adresów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

## <a name="rapid-elastic-scale"></a>Szybka Skala elastycznych

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane do aplikacji przy użyciu tej samej logiki szybkiego skalowania jako planu zużycia. Aplikacje w tej samej App Service planuje się niezależnie od siebie w zależności od potrzeb poszczególnych aplikacji. Jednak aplikacje działające w tym samym App Service planuje udostępnianie zasobów maszyn wirtualnych, aby pomóc w obniżeniu kosztów, gdy jest to możliwe. Liczba aplikacji skojarzonych z maszyną wirtualną zależy od wielkości poszczególnych aplikacji i rozmiaru maszyny wirtualnej.

Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [skalowanie oparte na zdarzeniach w Azure Functions](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Dłuższy czas trwania

Azure Functions w planie zużycia są ograniczone do 10 minut w przypadku pojedynczego wykonania. W planie Premium wartość czasu trwania przebiegu jest domyślnie równa 30 minut, aby uniemożliwić przemijające wykonania. Można jednak [zmodyfikować host.jsw konfiguracji](./functions-host-json.md#functiontimeout) , aby zapewnić nieograniczony czas trwania dla aplikacji planu Premium. W przypadku ustawienia nieograniczonego czasu trwania aplikacja funkcji jest gwarantowana przez co najmniej 60 minut. 

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu dostępne są dwa ustawienia rozmiaru planu: minimalna liczba wystąpień (lub rozmiar planu) i maksymalny limit.

Jeśli aplikacja wymaga wystąpień poza wystąpieniami zawsze gotowe, można nadal skalować w poziomie, dopóki liczba wystąpień osiągnie maksymalny limit. Opłaty są naliczane za wystąpienia wykraczające poza rozmiar planu tylko wtedy, gdy są uruchomione i przyliczane do Ciebie, co sekundę. Platforma sprawia, że najlepszym rozwiązaniem jest skalowanie aplikacji do zdefiniowanego maksymalnego limitu.

Rozmiar planu i maksymalne wartości można skonfigurować w Azure Portal, wybierając opcje **skalowania w poziomie** w ramach planu lub aplikacji funkcji wdrożonej w ramach tego planu (w obszarze **funkcje platformy**).

Możesz również zwiększyć maksymalny limit obciążeń z poziomu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Minimum dla każdego planu będzie co najmniej jednym wystąpieniem. Rzeczywista minimalna liczba wystąpień zostanie automatycznie skonfigurowana dla użytkownika w oparciu o zawsze gotowe wystąpienia wymagane przez aplikacje w planie. Na przykład jeśli aplikacja A zwróci pięć zawsze gotowe wystąpienia, a aplikacja B żąda dwóch zawsze przygotowanych wystąpień w tym samym planie, minimalny rozmiar planu zostanie obliczony jako pięć. Aplikacja A będzie działać na wszystkich 5, a aplikacja B będzie działać tylko na 2.

> [!IMPORTANT]
> Opłaty są naliczane za każde wystąpienie przydzieloną w minimalnej liczbie wystąpień niezależnie od tego, czy funkcje są wykonywane, czy nie.

W większości przypadków wystarcza wartość minimalna. Jednak skalowanie wykraczające poza minimum występuje w najlepszym wysiłku. Istnieje prawdopodobieństwo, że jest mało prawdopodobne, że w określonym czasie skalowanie może zostać opóźnione, jeśli dodatkowe wystąpienia są niedostępne. Ustawiając wartość minimalną wyższą niż wartość minimalna obliczana automatycznie, należy zarezerwować wystąpienia z wyprzedzeniem.

Zwiększenie obliczonego minimum dla planu można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU wystąpienia

Podczas tworzenia lub skalowania planu można wybrać jeden z trzech rozmiarów wystąpień. Opłaty są naliczane za łączną liczbę rdzeni i pamięci, która została przypisana do każdego wystąpienia. Aplikacja może automatycznie skalować w poziomie do wielu wystąpień stosownie do potrzeb.

|SKU|Rdzenie|Pamięć|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250|
|EP2|2|7GB|250|
|EP3|4|14 GB|250|

### <a name="memory-usage-considerations"></a>Zagadnienia dotyczące użycia pamięci

Uruchamianie na komputerze z większą ilością pamięci nie zawsze oznacza, że aplikacja funkcji korzysta ze wszystkich dostępnych pamięci.

Na przykład aplikacja funkcji JavaScript jest ograniczona przez domyślny limit pamięci w Node.js. Aby zwiększyć ten limit pamięci ustalonej, Dodaj ustawienie aplikacji `languageWorkers:node:arguments` z wartością `--max-old-space-size=<max memory in MB>` .

W przypadku planów mających więcej niż 4 GB pamięci upewnij się, że ustawienie platformy bitów jest ustawione na `64 Bit` [Ustawienia ogólne](../app-service/configure-common.md#configure-general-settings).

## <a name="region-max-scale-out"></a>Maksymalny rozmiar regionu w poziomie

Poniżej znajdują się obecnie obsługiwane maksymalne wartości skalowania w poziomie dla pojedynczego planu w każdym regionie i konfiguracji systemu operacyjnego. Aby zażądać zwiększenia, możesz otworzyć bilet pomocy technicznej.

Zapoznaj się z pełną regionalną dostępnością funkcji w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Region (Region)| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| 100 | Niedostępny |
|Australia Środkowa 2| 100 | Niedostępny |
|Australia Wschodnia| 100 | 20 |
|Australia Południowo-Wschodnia | 100 | 20 |
|Brazylia Południowa| 100 | 20 |
|Kanada Środkowa| 100 | 20 |
|Central US| 100 | 20 |
|Chiny Wschodnie 2| 100 | 20 |
|Chiny Północne 2| 100 | 20 |
|Azja Wschodnia| 100 | 20 |
|East US | 100 | 20 |
|Wschodnie stany USA 2| 100 | 20 |
|Francja Środkowa| 100 | 20 |
|Niemcy Środkowo-Zachodnie| 100 | Niedostępny |
|Japonia Wschodnia| 100 | 20 |
|Japonia Zachodnia| 100 | 20 |
|Korea Środkowa| 100 | 20 |
|Korea Południowa| Niedostępny | 20 |
|Północno-środkowe stany USA| 100 | 20 |
|Europa Północna| 100 | 20 |
|Norwegia Wschodnia| 100 | 20 |
|South Central US| 100 | 20 |
|Indie Południowe | 100 | Niedostępny |
|Southeast Asia| 100 | 20 |
|Szwajcaria Północna| 100 | Niedostępny |
|Szwajcaria Zachodnia| 100 | Niedostępny |
|Południowe Zjednoczone Królestwo| 100 | 20 |
|Zachodnie Zjednoczone Królestwo| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Wirginia| 100 | 20 |
|USNat wschód| 100 | Niedostępny |
|USNat zachodni| 100 | Niedostępny |
|West Europe| 100 | 20 |
|Indie Zachodnie| 100 | 20 |
|Zachodnio-środkowe stany USA| 100 | 20 |
|Zachodnie stany USA| 100 | 20 |
|Zachodnie stany USA 2| 100 | 20 |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o opcjach hostingu Azure Functions](functions-scale.md)