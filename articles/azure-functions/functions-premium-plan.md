---
title: Plan Azure Functions Premium
description: Szczegóły i opcje konfiguracji (Sieć wirtualna, brak nieograniczonego czasu wykonywania) dla planu Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276909"
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

W tym przykładzie Zastąp `<RESOURCE_GROUP>` wartość grupą zasobów i `<PLAN_NAME>` nazwą planu, która jest unikatowa w grupie zasobów. Określ [obsługiwane `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Aby utworzyć plan Premium, który obsługuje system Linux, należy `--is-linux` uwzględnić opcję.

Po utworzeniu planu możesz użyć [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) , aby utworzyć aplikację funkcji. W portalu zarówno plan, jak i aplikacja są tworzone w tym samym czasie. Aby zapoznać się z przykładem kompletnego skryptu interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie aplikacji funkcji w planie Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcjonalnych wdrożonych w planie Premium.

### <a name="pre-warmed-instances"></a>Wystąpienia przed wystąpieniem

Jeśli w planie zużycia nie wystąpiły żadne zdarzenia i wykonania, aplikacja może skalować się do zero wystąpień. Gdy pojawią się nowe zdarzenia, nowe wystąpienie musi być wyspecjalizowane dla działającej aplikacji.  Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji.  To dodatkowe opóźnienie pierwszego wywołania jest często nazywane aplikacją zimnego startu.

W planie Premium aplikacja została wstępnie przegrzana na określoną liczbę wystąpień do minimalnej wielkości planu.  Wystąpienia przed obciążeniem umożliwiają również wstępne skalowanie aplikacji. Gdy aplikacja jest skalowana w poziomie, najpierw skaluje się do wygrzanych wystąpień. Dodatkowe wystąpienia kontynuują buforowanie i podgrzewanie natychmiast po przygotowaniu dla następnej operacji skalowania. Dzięki buforowi wstępnie wygrzanych wystąpień można skutecznie uniknąć opóźnień zimnego uruchamiania.  Wystąpienia wstępnie działające to funkcja planu Premium, która wymaga, aby co najmniej jedno wystąpienie było uruchomione i dostępne we wszystkich przypadkach, gdy plan jest aktywny.

Liczbę wstępnie rozgrzanych wystąpień można skonfigurować w Azure Portal przez wybranie **aplikacja funkcji**, przejście na kartę **funkcje platformy** i wybranie opcji **skalowania w poziomie** . W oknie Edycja aplikacji funkcji, wstępnie rozgrzane wystąpienia są specyficzne dla tej aplikacji, ale minimalne i maksymalne wystąpienia są stosowane do całego planu.

![Ustawienia skalowania elastycznego](./media/functions-premium-plan/scale-out.png)

Możesz również skonfigurować wstępnie rozgrzane wystąpienia dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Azure Functions wdrożone w planie Premium wykorzystuje [nową integrację sieci wirtualnej dla usługi Web Apps](../app-service/web-sites-integrate-with-vnet.md).  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczonymi za pośrednictwem punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji w celu ograniczenia ruchu przychodzącego.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium potrzebna jest podsieć z wystarczającą liczbą adresów IP dla każdego potencjalnego wystąpienia. Wymagamy bloku IP z co najmniej 100 dostępnych adresów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybka Skala elastycznych

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane do aplikacji przy użyciu tej samej logiki szybkiego skalowania jako planu zużycia.  Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [Funkcja skalowanie i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Dłuższy czas trwania

Azure Functions w planie zużycia są ograniczone do 10 minut w przypadku pojedynczego wykonania.  W planie Premium wartość czasu trwania przebiegu jest domyślnie równa 30 minut, aby uniemożliwić przemijające wykonania. Można jednak [zmodyfikować konfigurację pliku host. JSON](./functions-host-json.md#functiontimeout) w taki sposób, aby nie były one powiązane z aplikacjami planu Premium (gwarantowane 60 minut).

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu można skonfigurować dwa ustawienia: minimalną liczbę wystąpień (lub rozmiar planu) i maksymalny limit.  Minimalna liczba wystąpień jest zarezerwowana i zawsze uruchomiona.

> [!IMPORTANT]
> Opłaty są naliczane za każde wystąpienie przydzieloną w minimalnej liczbie wystąpień niezależnie od tego, czy funkcje są wykonywane, czy nie.

Jeśli aplikacja wymaga wystąpień poza rozmiarem planu, można nadal skalować w poziomie, dopóki liczba wystąpień osiągnie maksymalny limit.  Opłaty są naliczane za wystąpienia poza rozmiarem planu, gdy są one uruchomione i dzierżawione.  W celu skalowania aplikacji do zdefiniowanego maksymalnego limitu zostanie osiągnięty najlepszy nakład pracy, podczas gdy minimalne wystąpienia planu są gwarantowane dla aplikacji.

Rozmiar planu i maksymalne wartości można skonfigurować w Azure Portal, wybierając opcje **skalowania w poziomie** w ramach planu lub aplikacji funkcji wdrożonej w ramach tego planu (w obszarze **funkcje platformy**).

Możesz również zwiększyć maksymalny limit obciążeń z poziomu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU wystąpienia

Podczas tworzenia lub skalowania planu można wybrać jeden z trzech rozmiarów wystąpień.  Opłaty zostaną naliczone za łączną liczbę rdzeni i zużywaną pamięć na sekundę.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień stosownie do potrzeb.  

|SKU|Rdzenie|Memory (Pamięć)|Magazyn|
|--|--|--|--|
|EP1|1|3,5 GB|250|
|EP2|2|7GB|250|
|EP3|4|14 GB|250|

### <a name="memory-utilization-considerations"></a>Zagadnienia dotyczące wykorzystania pamięci
Uruchamianie na komputerze z większą ilością pamięci nie zawsze oznacza, że aplikacja funkcji będzie używać całej dostępnej pamięci.

Na przykład aplikacja funkcji JavaScript jest ograniczona przez domyślny limit pamięci w programie Node. js. Aby zwiększyć ten limit pamięci ustalonej, Dodaj ustawienie `languageWorkers:node:arguments` aplikacji z wartością. `--max-old-space-size=<max memory in MB>`

## <a name="region-max-scale-out"></a>Maksymalny rozmiar regionu w poziomie

Poniżej znajdują się obecnie obsługiwane maksymalne wartości skalowania w poziomie dla pojedynczego planu w każdym regionie i konfiguracji systemu operacyjnego. Aby zażądać zwiększenia, należy otworzyć bilet pomocy technicznej.

Zapoznaj się z pełną regionalną dostępnością funkcji tutaj: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| 20 | Niedostępny |
|Australia Środkowa 2| 20 | Niedostępny |
|Australia Wschodnia| 100 | 20 |
|Australia Południowo-Wschodnia | 100 | 20 |
|Brazylia Południowa| 60 | 20 |
|Kanada Środkowa| 100 | 20 |
|Środkowe stany USA| 100 | 20 |
|Azja Wschodnia| 100 | 20 |
|Wschodnie stany USA | 100 | 20 |
|Wschodnie stany USA 2| 100 | 20 |
|Francja Środkowa| 100 | 20 |
|Niemcy Środkowo-Zachodnie| 100 | Niedostępny |
|Japonia Wschodnia| 100 | 20 |
|Japonia Zachodnia| 100 | 20 |
|Korea Środkowa| 100 | 20 |
|Północno-środkowe stany USA| 100 | 20 |
|Europa Północna| 100 | 20 |
|Norwegia Wschodnia| 20 | 20 |
|Południowo-środkowe stany USA| 100 | 20 |
|Indie Południowe | 100 | Niedostępny |
|Azja Południowo-Wschodnia| 100 | 20 |
|Południowe Zjednoczone Królestwo| 100 | 20 |
|Zachodnie Zjednoczone Królestwo| 100 | 20 |
|Europa Zachodnia| 100 | 20 |
|Indie Zachodnie| 100 | 20 |
|Zachodnio-środkowe stany USA| 20 | 20 |
|Zachodnie stany USA| 100 | 20 |
|Zachodnie stany USA 2| 100 | 20 |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje na temat Azure Functions skalowanie i opcje hostingu](functions-scale.md)
