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
ms.openlocfilehash: a037c903a72ba79b79c7e6b011fe025aefd7b51d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578040"
---
# <a name="azure-functions-premium-plan"></a>Plan Azure Functions Premium

Plan Azure Functions Premium (nazywany czasem elastycznym planem Premium) jest opcją hostingu dla aplikacji funkcji. Plan Premium oferuje funkcje, takie jak łączność sieci wirtualnej, sprzęt zimnego startu i Premium.  Wiele aplikacji funkcji można wdrożyć w tym samym planie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowego, rozmiaru planu bazowego i maksymalnego rozmiaru planu.  Aby zapoznać się z porównaniem planu Premium i innych typów planów i hostingu, zobacz [Funkcja skalowanie i opcje hostingu](functions-scale.md).

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plan Premium można również utworzyć za pomocą polecenia [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) w interfejsie wiersza polecenia platformy Azure. W poniższym przykładzie jest tworzony plan warstwy _Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

W tym przykładzie Zastąp `<RESOURCE_GROUP>` wartość grupą zasobów i `<PLAN_NAME>` nazwą planu, która jest unikatowa w grupie zasobów. Określ [obsługiwane `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Aby utworzyć plan Premium, który obsługuje system Linux, należy uwzględnić `--is-linux` opcję.

Po utworzeniu planu możesz użyć [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) , aby utworzyć aplikację funkcji. W portalu zarówno plan, jak i aplikacja są tworzone w tym samym czasie. Aby zapoznać się z przykładem kompletnego skryptu interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie aplikacji funkcji w planie Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcjonalnych wdrożonych w planie Premium.

### <a name="always-ready-instances"></a>Zawsze gotowe wystąpienia

Jeśli w planie zużycia nie wystąpiły żadne zdarzenia i wykonania, aplikacja może skalować się do zero wystąpień. Gdy pojawią się nowe zdarzenia, nowe wystąpienie musi być wyspecjalizowane dla działającej aplikacji.  Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji.  To dodatkowe opóźnienie pierwszego wywołania jest często nazywane aplikacją zimnego startu.

W planie Premium aplikacja zawsze będzie gotowa na określoną liczbę wystąpień.  Maksymalna liczba wystąpień zawsze gotowe jest równa 20.  Gdy zdarzenia rozpoczną wyzwalanie aplikacji, są one najpierw kierowane do zawsze przygotowanych wystąpień.  Gdy funkcja zostanie uaktywniona, kolejne wystąpienia zostaną rozgrzane jako bufor.  Ten bufor zapobiega zimnemu uruchomieniu dla nowych wystąpień wymaganych podczas skalowania.  Te buforowane wystąpienia są nazywane [wstępnie rozgrzanymi wystąpieniami](#pre-warmed-instances).  Wraz z kombinacją zawsze przygotowanych wystąpień i wstępnie wygrzanego buforu aplikacja może skutecznie wyeliminować zimne uruchomienie.

> [!NOTE]
> Każdy plan Premium będzie miał co najmniej jedno aktywne (rozliczane) wystąpienie przez cały czas.

Liczbę zawsze przygotowanych wystąpień można skonfigurować w Azure Portal przez wybranie **aplikacja funkcji**, przejście na kartę **funkcje platformy** i wybranie opcji **skalowania w poziomie** . W oknie Edycja aplikacji funkcji zawsze gotowe wystąpienia są specyficzne dla tej aplikacji.

![Ustawienia skalowania elastycznego](./media/functions-premium-plan/scale-out.png)

Możesz również skonfigurować zawsze gotowe wystąpienia dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>Wystąpienia przed wystąpieniem

Wystąpienia z góry to liczba wystąpień rozgrzanych jako bufor podczas zdarzeń skalowania i aktywacji.  Wystąpienia przed osiągnięciem maksymalnego limitu skalowania są nadal buforowane.  Domyślna liczba wystąpień wstępnie rozgrzanych to 1, a w przypadku większości scenariuszy powinna pozostać jako 1.  Jeśli aplikacja ma długotrwałą rozgrzewanie (na przykład niestandardowy obraz kontenera), warto zwiększyć ten bufor.  Wystąpienie preinstalowane stanie się aktywne dopiero po wystarczająco wykorzystaniu wszystkich aktywnych wystąpień.

Rozważmy ten przykład, jak zawsze gotowe wystąpienia i wstępnie rozgrzane wystąpienia współpracują ze sobą.  Aplikacja funkcji Premium ma skonfigurowane pięć zawsze przygotowanych wystąpień i domyślnie jedno wystąpienie wstępne.  Gdy aplikacja jest bezczynna i żadne zdarzenia nie są wyzwalane, aplikacja zostanie zainicjowana i uruchomiona na pięciu wystąpieniach.  W tej chwili nie będą naliczane opłaty za wystąpienie z góry, ponieważ zawsze gotowe wystąpienia nie są używane, a żadne wystąpienie z góry nie jest jeszcze przydzielono.

Po powrocie pierwszego wyzwalacza, pięć zawsze gotowych wystąpień staje się aktywny, a wystąpienie jest przydzieloną.  Aplikacja działa teraz z sześcioma zainicjowanymi wystąpieniami: pięć teraz — aktywne zawsze gotowe wystąpienia i szósty, wstępnie rozgrzany i nieaktywny bufor.  Jeśli szybkość wykonywania nadal rośnie, zostaną ostatecznie wykorzystane pięć aktywnych wystąpień.  Gdy Platforma zdecyduje się na skalowanie ponad pięciu wystąpień, zostanie przeskalowana do wystąpienia sprzed wykonania.  Gdy tak się stanie, będą teraz dostępne sześć aktywnych wystąpień, a siódme wystąpienie zostanie natychmiast zainicjowane i wypełnianie buforu z prefiksem.  Ta sekwencja skalowania i wstępnego rozgrzewania będzie kontynuowana do momentu osiągnięcia maksymalnej liczby wystąpień aplikacji.  Żadne wystąpienia nie zostaną wstępnie rozgrzane lub aktywowane poza wartością maksymalną.

Liczbę wstępnie rozgrzanych wystąpień aplikacji można zmodyfikować przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>Maksymalna liczba wystąpień aplikacji

Oprócz [maksymalnej liczby wystąpień planu](#plan-and-sku-settings)można skonfigurować wartość maksymalną dla aplikacji.  Maksymalną liczbę aplikacji można skonfigurować przy użyciu [limitu skalowania aplikacji](./functions-scale.md#limit-scale-out).

### <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Azure Functions wdrożone w planie Premium wykorzystuje [nową integrację sieci wirtualnej dla usługi Web Apps](../app-service/web-sites-integrate-with-vnet.md).  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczonymi za pośrednictwem punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji w celu ograniczenia ruchu przychodzącego.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium potrzebna jest podsieć z wystarczającą liczbą adresów IP dla każdego potencjalnego wystąpienia. Wymagamy bloku IP z co najmniej 100 dostępnych adresów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybka Skala elastycznych

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane do aplikacji przy użyciu tej samej logiki szybkiego skalowania jako planu zużycia. Aplikacje w tej samej App Service planuje się niezależnie od siebie w zależności od potrzeb poszczególnych aplikacji. Jednak aplikacje działające w tym samym App Service planuje udostępnianie zasobów maszyn wirtualnych, aby pomóc w obniżeniu kosztów, gdy jest to możliwe. Liczba aplikacji skojarzonych z maszyną wirtualną zależy od wielkości poszczególnych aplikacji i rozmiaru maszyny wirtualnej.

Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [Funkcja skalowanie i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Dłuższy czas trwania

Azure Functions w planie zużycia są ograniczone do 10 minut w przypadku pojedynczego wykonania.  W planie Premium wartość czasu trwania przebiegu jest domyślnie równa 30 minut, aby uniemożliwić przemijające wykonania. Można jednak [zmodyfikować host.jsw konfiguracji](./functions-host-json.md#functiontimeout) , aby okres trwania nie był powiązany z aplikacjami planu Premium (gwarantowane 60 minut).

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu dostępne są dwa ustawienia rozmiaru planu: minimalna liczba wystąpień (lub rozmiar planu) i maksymalny limit.

Jeśli aplikacja wymaga wystąpień poza zawsze gotowymi wystąpieniami, można nadal skalować w poziomie, dopóki liczba wystąpień osiągnie maksymalny limit.  Opłaty są naliczane za wystąpienia poza rozmiarem planu, tylko wtedy, gdy są uruchomione i przyliczane do Ciebie, co sekundę.  W celu skalowania aplikacji do zdefiniowanego maksymalnego limitu zostanie osiągnięty najlepszy nakład pracy.

Rozmiar planu i maksymalne wartości można skonfigurować w Azure Portal, wybierając opcje **skalowania w poziomie** w ramach planu lub aplikacji funkcji wdrożonej w ramach tego planu (w obszarze **funkcje platformy**).

Możesz również zwiększyć maksymalny limit obciążeń z poziomu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

Minimum dla każdego planu będzie co najmniej jednym wystąpieniem.  Rzeczywista minimalna liczba wystąpień zostanie automatycznie skonfigurowana dla użytkownika w oparciu o zawsze gotowe wystąpienia wymagane przez aplikacje w planie.  Na przykład jeśli aplikacja A zwróci pięć zawsze gotowe wystąpienia, a aplikacja B żąda dwóch zawsze przygotowanych wystąpień w tym samym planie, minimalny rozmiar planu zostanie obliczony jako pięć.  Aplikacja A będzie działać na wszystkich 5, a aplikacja B będzie działać tylko na 2.

> [!IMPORTANT]
> Opłaty są naliczane za każde wystąpienie przydzieloną w minimalnej liczbie wystąpień niezależnie od tego, czy funkcje są wykonywane, czy nie.

W większości przypadków wartość minimalna powinna być wystarczająca.  Jednak skalowanie wykraczające poza minimum występuje w najlepszym wysiłku.  Istnieje prawdopodobieństwo, że jest mało prawdopodobne, że w określonym czasie skalowanie może zostać opóźnione, jeśli dodatkowe wystąpienia są niedostępne.  Ustawiając wartość minimalną wyższą niż wartość minimalna obliczana automatycznie, należy zarezerwować wystąpienia z wyprzedzeniem.

Zwiększenie obliczonego minimum dla planu można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU wystąpienia

Podczas tworzenia lub skalowania planu można wybrać jeden z trzech rozmiarów wystąpień.  Opłaty są naliczane za łączną liczbę rdzeni i pamięci, która została przypisana do każdego wystąpienia.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień stosownie do potrzeb.  

|Jednostka SKU|Rdzenie|Pamięć|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250|
|EP2|2|7GB|250|
|EP3|4|14 GB|250|

### <a name="memory-utilization-considerations"></a>Zagadnienia dotyczące wykorzystania pamięci
Uruchamianie na komputerze z większą ilością pamięci nie zawsze oznacza, że aplikacja funkcji będzie używać całej dostępnej pamięci.

Na przykład aplikacja funkcji JavaScript jest ograniczona przez domyślny limit pamięci w Node.js. Aby zwiększyć ten limit pamięci ustalonej, Dodaj ustawienie aplikacji `languageWorkers:node:arguments` z wartością `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Maksymalny rozmiar regionu w poziomie

Poniżej znajdują się obecnie obsługiwane maksymalne wartości skalowania w poziomie dla pojedynczego planu w każdym regionie i konfiguracji systemu operacyjnego. Aby zażądać zwiększenia, należy otworzyć bilet pomocy technicznej.

Zapoznaj się z pełną regionalną dostępnością funkcji tutaj: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region (Region)| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| 100 | Niedostępny |
|Australia Środkowa 2| 100 | Niedostępny |
|Australia Wschodnia| 100 | 20 |
|Australia Południowo-Wschodnia | 100 | 20 |
|Brazil South| 100 | 20 |
|Kanada Środkowa| 100 | 20 |
|Środkowe stany USA| 100 | 20 |
|Chiny Wschodnie 2| 100 | 20 |
|Chiny Północne 2| 100 | 20 |
|Azja Wschodnia| 100 | 20 |
|East US | 100 | 20 |
|Wschodnie stany USA 2| 100 | 20 |
|Francja Środkowa| 100 | 20 |
|Niemcy Środkowo-Zachodnie| 100 | Niedostępny |
|Japan East| 100 | 20 |
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
> [Informacje na temat Azure Functions skalowanie i opcje hostingu](functions-scale.md)
