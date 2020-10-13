---
title: Skalowanie i hosting usługi Azure Functions
description: Dowiedz się, jak wybierać między planem zużycia Azure Functions a planem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5dd703851054b058d96440a3a994b9d10eecfa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372667"
---
# <a name="azure-functions-scale-and-hosting"></a>Skalowanie i hosting usługi Azure Functions

Podczas tworzenia aplikacji funkcji na platformie Azure musisz wybrać plan hostingu dla swojej aplikacji. Istnieją trzy podstawowe plany hostingu dostępne dla Azure Functions: [Plan zużycia](#consumption-plan), [Plan Premium](#premium-plan)i [dedykowany plan (App Service)](#app-service-plan). Wszystkie plany hostingu są ogólnie dostępne (GA) na maszynach wirtualnych z systemem Linux i Windows.

Wybrany plan hostingu wymusza następujące zachowania:

* Sposób skalowania aplikacji funkcji.
* Zasoby dostępne dla każdego wystąpienia aplikacji funkcji.
* Obsługa zaawansowanych funkcji, takich jak łączność z platformą Azure Virtual Network.

Plany zużycia i Premium automatycznie dodają moc obliczeniową, gdy kod jest uruchomiony. Aplikacja jest skalowana w poziomie, gdy jest wymagana do obsługi obciążenia i skalowana w czasie, gdy kod przestanie działać. W przypadku planu zużycia nie trzeba również uiszczać opłat za bezczynne maszyny wirtualne lub zarezerwować pojemność.  

Plan Premium oferuje dodatkowe funkcje, takie jak wystąpienia obliczeniowe w warstwie Premium, możliwość utrzymywania nieokreślonych wystąpień i łączności między sieciami wirtualnymi.

Plan App Service umożliwia korzystanie z dedykowanej infrastruktury zarządzanej przez użytkownika. Aplikacja funkcji nie jest skalowana na podstawie zdarzeń, co oznacza, że nigdy nie skaluje się do zera. (Wymaga, aby [zawsze](#always-on) włączony).

Aby uzyskać szczegółowe porównanie między różnymi planami hostingu (w tym hostingiem opartym na Kubernetes), zobacz [sekcję porównanie planów hostingu](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Plan Zużycie

Gdy używasz planu zużycia, wystąpienia hosta Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Ten plan bezserwerowy wykorzystuje automatyczne skalowanie, a opłaty są naliczane za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchamiane. W ramach Planu użycia limit czasu wykonywania funkcji zostaje przekroczony po konfigurowalnym okresie czasu.

Rozliczenia zależą od liczby wykonań, czasu wykonania oraz użytej pamięci. Użycie jest agregowane we wszystkich funkcjach w ramach aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plan zużycia jest domyślnym planem hostingu i oferuje następujące korzyści:

* Płacisz tylko wtedy, gdy funkcje są uruchomione
* Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia

Aplikacje funkcji w tym samym regionie mogą być przypisane do tego samego planu zużycia. Nie ma Minusem ani wpływu na wiele aplikacji uruchomionych w tym samym planie zużycia. Przypisywanie wielu aplikacji do tego samego planu zużycia nie ma wpływu na odporność, skalowalność ani niezawodność każdej aplikacji.

Aby dowiedzieć się więcej na temat szacowania kosztów podczas pracy w planie zużycia, zobacz [Opis kosztów planu zużycia](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plan Premium

W przypadku korzystania z planu Premium wystąpienia hosta Azure Functions są dodawane i usuwane na podstawie liczby zdarzeń przychodzących, podobnie jak w przypadku planu zużycia.  Plan Premium obsługuje następujące funkcje:

* Bezterminowo podgrzewane wystąpienia, aby uniknąć dowolnego zimnego startu
* Łączność z siecią wirtualną
* Nieograniczony czas wykonywania (gwarantowane 60 minut)
* Rozmiary wystąpienia Premium (jeden rdzeń, dwa rdzenie i cztery podstawowe wystąpienia)
* Bardziej przewidywalny Cennik
* Alokacja aplikacji o wysokiej gęstości dla planów z wieloma aplikacjami funkcji

Aby dowiedzieć się, jak utworzyć aplikację funkcji w planie Premium, zobacz [Azure Functions plan Premium](functions-premium-plan.md).

Zamiast naliczania opłat za wykonanie i zużywaną pamięć, rozliczenia dla planu Premium bazują na liczbie podstawowych sekund i ilości pamięci przydzieloną między wystąpieniami.  Z planem Premium nie jest naliczana opłata za wykonanie. Co najmniej jedno wystąpienie musi być przydzieloną przez cały czas dla każdego planu. Powoduje to minimalny miesięczny koszt według aktywnego planu, niezależnie od tego, czy funkcja jest aktywna, czy bezczynna. Należy pamiętać, że wszystkie aplikacje funkcji w planie Premium współdzielą wystąpienia przydzielono.

Zapoznaj się z planem Azure Functions Premium w następujących sytuacjach:

* Aplikacje funkcji działają w sposób ciągły lub niemal nieustannie.
* Masz dużą liczbę niewielkich wykonań i masz rozliczenie o dużym obciążeniu, ale niski GB drugiego rachunku w planie zużycia.
* Potrzebujesz więcej opcji dotyczących procesora CPU lub pamięci niż to, co jest dostępne w ramach planu zużycia.
* Twój kod musi działać dłużej niż [Maksymalny dozwolony czas wykonywania](#timeout) w planie zużycia.
* Wymagane są funkcje, które są dostępne tylko w planie Premium, takie jak łączność sieci wirtualnej. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plan dedykowany (App Service)

Aplikacje funkcji można również uruchamiać na tych samych dedykowanych maszynach wirtualnych, co inne aplikacje App Service (wersje Basic, standard, Premium i izolowane).

Należy wziąć pod uwagę plan App Service w następujących sytuacjach:

* Istnieją już istniejące, nieużywane maszyny wirtualne, na których uruchomiono inne wystąpienia App Service.
* Chcesz udostępnić niestandardowy obraz, na którym będą uruchamiane funkcje.

Płacisz za aplikacje funkcji w planie App Service, tak jak w przypadku innych zasobów App Service, takich jak aplikacje sieci Web. Aby uzyskać szczegółowe informacje na temat działania planu App Service, zobacz [szczegółowe Omówienie planów Azure App Service](../app-service/overview-hosting-plans.md).

Korzystając z planu App Service, można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych. Możesz również włączyć funkcję automatycznego skalowania, chociaż automatyczne skalowanie będzie wolniejsze niż elastyczne skalowanie planu Premium. Aby uzyskać więcej informacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Możesz również skalować w górę, wybierając inny plan App Service. Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](../app-service/manage-scale-up.md). 

Podczas uruchamiania funkcji JavaScript w planie App Service należy wybrać plan, który ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybieranie planów App Service z pojedynczym rdzeniem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

Uruchamianie w [App Service Environment](../app-service/environment/intro.md) (ASE) pozwala w pełni izolować funkcje i korzystać z większej liczby wystąpień niż plan App Service.

### <a name="always-on"></a><a name="always-on"></a> Zawsze włączone

Jeśli uruchamiasz plan App Service, należy włączyć ustawienie **zawsze** włączone, aby aplikacja funkcji działała poprawnie. W planie App Service środowisko uruchomieniowe funkcji przechodzi w stan bezczynności po kilku minutach braku aktywności, więc tylko Wyzwalacze HTTP będą wznawiać działanie funkcji. Zawsze włączone jest dostępne tylko w planie App Service. Zgodnie z planem zużycia platforma automatycznie aktywuje aplikacje funkcji.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Nawet przy włączonej opcji zawsze włączone przekroczenie limitu czasu wykonywania poszczególnych funkcji jest kontrolowane przez `functionTimeout` ustawienie w [host.jsw](functions-host-json.md#functiontimeout) pliku projektu.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Określanie planu hostingu istniejącej aplikacji

Aby określić plan hostingu używany przez aplikację funkcji, zapoznaj się z tematem **App Service plan** na karcie **Przegląd** dla aplikacji funkcji w [Azure Portal](https://portal.azure.com). Aby wyświetlić warstwę cenową, wybierz nazwę **planu App Service**, a następnie wybierz pozycję **Właściwości** w okienku po lewej stronie.

![Wyświetlanie planu skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby określić plan w następujący sposób:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Gdy dane wyjściowe tego polecenia to `dynamic` , aplikacja funkcji jest w planie zużycia. Gdy dane wyjściowe tego polecenia to `ElasticPremium` , aplikacja funkcji jest w planie Premium. Wszystkie inne wartości wskazują różne warstwy planu App Service.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

W każdym planie aplikacja funkcji wymaga konta usługi Azure Storage, które obsługuje obiekty blob, kolejki, pliki i tabele usługi Azure Storage. Jest to spowodowane tym, że Azure Functions opiera się na usłudze Azure Storage na potrzeby operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji, ale niektóre konta magazynu nie obsługują kolejek i tabel. Te konta, które obejmują konta magazynu tylko obiektów BLOB (w tym magazyn Premium Storage) i konta magazynu ogólnego przeznaczenia z replikacją magazynu w strefie nadmiarowej, są odfiltrowane z istniejących ustawień **konta magazynu** podczas tworzenia aplikacji funkcji.

To samo konto magazynu używane przez aplikację funkcji może być również używane przez wyzwalacze i powiązania do przechowywania danych aplikacji. Jednak w przypadku operacji intensywnie korzystających z magazynu należy użyć oddzielnego konta magazynu.  

Istnieje możliwość udostępnienia tego samego konta magazynu dla wielu aplikacji funkcji bez żadnych problemów. (Dobrym przykładem jest to, że podczas tworzenia wielu aplikacji w środowisku lokalnym przy użyciu emulatora usługi Azure Storage, który działa jak jedno konto magazynu). 

<!-- JH: Does using a Premium Storage account improve perf? -->

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#core-storage-services).

### <a name="in-region-data-residency"></a>W miejscu zamieszkania danych regionu

Gdy to konieczne, aby wszystkie dane klienta pozostawały w jednym regionie, konto magazynu skojarzone z aplikacją funkcji musi być jednym z [nadmiarowości w regionie](../storage/common/storage-redundancy.md).  Konto magazynu nadmiarowego w regionie musi być również używane z [usługą Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) dla Durable Functions.

Inne dane klienta zarządzane przez platformę będą przechowywane w regionie tylko podczas hostowania w ramach wewnętrznego Load Balancer App Service Environment (lub ILB ASE).  Szczegóły można znaleźć w obszarze [nadmiarowości stref środowiska ASE](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak działają plany Zużycie i Premium

W planach zużycia i Premium infrastruktura Azure Functions skaluje zasoby procesora i pamięci, dodając kolejne wystąpienia hosta funkcji na podstawie liczby zdarzeń wyzwalanych przez jej funkcje. Każde wystąpienie hosta funkcji w planie zużycia jest ograniczone do 1,5 GB pamięci i jednego procesora CPU.  Wystąpieniem hosta jest cała aplikacja funkcji, co oznacza, że wszystkie funkcje w ramach zasobu funkcji udział aplikacji w ramach wystąpienia i skalowania w tym samym czasie. Aplikacje funkcji, które współużytkują ten sam plan zużycia, są skalowane niezależnie.  W planie Premium rozmiar planu określi dostępną pamięć i procesor dla wszystkich aplikacji w tym wystąpieniu.  

Pliki kodu funkcji są przechowywane w udziałach Azure Files na głównym koncie magazynu funkcji. Po usunięciu głównego konta magazynu aplikacji funkcji pliki kodu funkcji zostaną usunięte i nie będzie można go odzyskać.

### <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Azure Functions używa składnika zwanego *kontrolerem skalowania* , aby monitorować częstotliwość zdarzeń i określać, czy skalować w poziomie, czy skalować w poziomie. Kontroler skalowania używa algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza usługi Azure queue storage skaluje się w zależności od długości kolejki i wieku najstarszej wiadomości w kolejce.

Jednostką skalowania Azure Functions jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowana w poziomie, dodatkowe zasoby są przydzieleni do uruchamiania wielu wystąpień hosta Azure Functions. Z drugiej strony, ponieważ zapotrzebowanie obliczeniowe jest ograniczone, kontroler skalowania usuwa wystąpienia hosta funkcji. Liczba wystąpień jest ostatecznie *skalowana* do zera, gdy żadne funkcje nie działają w ramach aplikacji funkcji.

![Skalowanie zdarzeń monitorowania kontrolera i Tworzenie wystąpień](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Zimne uruchomienie

Gdy aplikacja funkcji jest bezczynna przez kilka minut, platforma może skalować liczbę wystąpień, na których aplikacja jest uruchamiana na zero. Następne żądanie ma dodanie opóźnienia skalowania od zera do jednego. To opóźnienie jest określane jako _zimne uruchomienie_. Liczba zależności, które muszą zostać załadowane przez aplikację funkcji, może mieć wpływ na zimny czas rozpoczęcia. Zimny start to więcej problemu w przypadku operacji synchronicznych, takich jak wyzwalacze HTTP, które muszą zwrócić odpowiedź. Jeśli zimne uruchomienie ma wpływ na funkcje, należy rozważyć uruchomienie w planie Premium lub w dedykowanym planie z funkcją zawsze włączone.   

### <a name="understanding-scaling-behaviors"></a>Zrozumienie zachowań skalowania

Skalowanie może się różnić w zależności od liczby czynników i skalować w różny sposób w zależności od wybranego wyzwalacza i języka. Istnieje kilka złożonego zachowań do skalowania:

* Pojedyncza aplikacja funkcji jest skalowana tylko do maksymalnie 200 wystąpień. Pojedyncze wystąpienie może przetwarzać więcej niż jeden komunikat lub żądanie w tym samym czasie, więc nie ma ustawionego limitu liczby współbieżnych wykonań.  Możesz [określić niższą maksymalną](#limit-scale-out) przepustowość, aby ograniczyć skalę w miarę potrzeb.
* W przypadku wyzwalaczy HTTP nowe wystąpienia są przydzielono maksymalnie raz na sekundę.
* W przypadku wyzwalaczy nie korzystających z protokołu HTTP nowe wystąpienia są przydzielono co najwyżej, co 30 sekund. Skalowanie jest szybsze, gdy działa w [planie Premium](#premium-plan).
* W przypadku wyzwalaczy Service Bus Użyj _zarządzania_ prawami do zasobów, aby uzyskać najbardziej wydajne skalowanie. W przypadku praw _nasłuchu_ skalowanie nie jest tak dokładne, ponieważ długość kolejki nie może być używana do informowania o decyzjach o skalowaniu. Aby dowiedzieć się więcej o ustawianiu uprawnień Service Bus zasad dostępu, zobacz [zasady autoryzacji dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Aby uzyskać informacje na temat wyzwalaczy centrum zdarzeń, zobacz [wskazówki dotyczące skalowania](functions-bindings-event-hubs-trigger.md#scaling) w artykule referencyjnym. 

### <a name="limit-scale-out"></a>Ogranicz skalowanie w poziomie

Możesz ograniczyć liczbę wystąpień aplikacji do skalowania w dół.  Jest to najbardziej typowe w przypadku, gdy składnik podrzędny, taki jak baza danych, ma ograniczoną przepływność.  Domyślnie funkcje planu zużycia będą skalowane w poziomie do maksymalnie 200 wystąpień, a funkcje planu Premium będą skalowane do maksymalnie 100 wystąpień.  Możesz określić niższą wartość maksymalną dla konkretnej aplikacji, modyfikując ją `functionAppScaleLimit` .  Wartość `functionAppScaleLimit` można ustawić na 0 lub null dla nieograniczonej lub prawidłowej wartości z przedziału od 1 do maksimum aplikacji.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce dotyczące skalowalnych aplikacji

Istnieje wiele aspektów aplikacji funkcji, która będzie miała wpływ na wydajność skalowania, w tym konfigurację hosta, rozmiar środowiska uruchomieniowego i efektywność zasobów.  Aby uzyskać więcej informacji, zobacz [sekcję skalowalność artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices). Należy również wiedzieć, jak połączenia działają w miarę skalowania aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [jak zarządzać połączeniami w Azure Functions](manage-connections.md).

Aby uzyskać więcej informacji na temat skalowania w języku Python i Node.js, zobacz [Azure Functions Przewodnik dla deweloperów w języku Python — skalowanie i współbieżność](functions-reference-python.md#scaling-and-concurrency) oraz [Azure Functions Node.js Przewodnik dla deweloperów — skalowanie i współbieżność](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Model rozliczania

Rozliczenia dla różnych planów są szczegółowo opisane na [stronie cennika Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Użycie jest agregowane na poziomie aplikacji funkcji i zlicza tylko czas wykonywania kodu funkcji. Następujące jednostki dotyczą rozliczeń:

* **Użycie zasobów w gigabajtach sekund (GB-s)**. Obliczany jako kombinacja rozmiaru pamięci i czasu wykonywania dla wszystkich funkcji w aplikacji funkcji. 
* **Wykonania**. Zliczane za każdym razem, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

Przydatne zapytania i informacje dotyczące sposobu zrozumienia rachunku zużycia można znaleźć [na stronie często zadawanych pytań dotyczących rozliczeń](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Porównanie planów hostingu

W poniższej tabeli porównawczej przedstawiono wszystkie ważne aspekty pomocne w decyzji dotyczącej wyboru planu hostingu aplikacji Azure Functions:

### <a name="plan-summary"></a>Podsumowanie planu
| | |
| --- | --- |  
|**[Plan Zużycie](#consumption-plan)**| Skaluj automatycznie i płacisz tylko za zasoby obliczeniowe, gdy funkcje są uruchomione. W planie zużycia wystąpienia hosta funkcji są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących.<br/> ✔ Domyślnego planu hostingu.<br/>✔ Płacisz tylko wtedy, gdy funkcje są uruchomione.<br/>Automatyczne skalowanie ✔, nawet w okresach dużego obciążenia.|  
|**[Plan Premium](#premium-plan)**|Podczas automatycznego skalowania na podstawie popytu należy używać wstępnie rozgrzanych procesów roboczych do uruchamiania aplikacji bez opóźnień, uruchamiać się na bardziej wydajnych wystąpieniach i łączyć się z sieci wirtualnych. Oprócz wszystkich funkcji planu App Service należy wziąć pod uwagę plan Azure Functions Premium w następujących sytuacjach: <br/>✔ Aplikacje funkcji działają ciągle lub niemal ciągle.<br/>✔ Masz dużą liczbę niewielkich wykonań i masz rozliczenie o dużym obciążeniu, ale niski GB drugiego rachunku w planie zużycia.<br/>✔ Potrzebujesz więcej opcji procesora lub pamięci niż to, co jest dostępne w ramach planu zużycia.<br/>✔ Kod musi być uruchomiony dłużej niż maksymalny dozwolony czas wykonywania w planie zużycia.<br/>✔ Wymagane są funkcje, które są dostępne tylko w planie Premium, takie jak łączność z siecią wirtualną.|  
|**[Dedykowany plan](#app-service-plan)**<sup>1</sup>|Uruchamiaj swoje funkcje w ramach planu App Service według regularnych App Service stawek planu. Dobrze pasuje do długotrwałych operacji, a także wtedy, gdy są wymagane bardziej przewidywalne skalowanie i koszty. Należy wziąć pod uwagę plan App Service w następujących sytuacjach:<br/>✔ Masz istniejące, nieużywane maszyny wirtualne, na których działają już inne wystąpienia App Service.<br/>✔ Chcesz udostępnić niestandardowy obraz, na którym będą uruchamiane funkcje.|  
|**[ASE](#app-service-plan)**<sup>1</sup>|App Service Environment (ASE) to funkcja App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę. Środowisk ASE są odpowiednie dla obciążeń aplikacji, które wymagają: <br/>✔ Bardzo wysoka Skala.<br/>✔ Pełna izolacja obliczeniowa i bezpieczny dostęp do sieci.<br/>✔ Wysokie wykorzystanie pamięci.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes zapewnia w pełni izolowane i dedykowane środowisko działające w oparciu o platformę Kubernetes.  Kubernetes jest odpowiednie dla obciążeń aplikacji, które wymagają: <br/>✔ Niestandardowe wymagania sprzętowe.<br/>✔ Izolacja i bezpieczny dostęp do sieci.<br/>✔ Możliwość uruchamiania w środowisku hybrydowym lub w chmurze.<br/>✔ Uruchamiane razem z istniejącymi aplikacjami i usługami Kubernetes.|  

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="operating-systemruntime"></a>System operacyjny/środowisko uruchomieniowe

| | System Linux<sup>1</sup><br/>Tylko kod | System Windows<sup>2</sup><br/>Tylko kod | Linux<sup>, 1, 3</sup><br/>Kontener platformy Docker |
| --- | --- | --- | --- |
| **[Plan Zużycie](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core | Brak pomocy technicznej  |
| **[Plan Premium](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python  | 
| **[Dedykowany plan](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | nie dotyczy | nie dotyczy |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python |

<sup>1</sup> System Linux jest jedynym obsługiwanym systemem operacyjnym dla stosu środowiska uruchomieniowego języka Python.  
<sup>2</sup> System Windows to jedyny obsługiwany system operacyjny dla stosu środowiska uruchomieniowego programu PowerShell.   
<sup>3</sup> System Linux jest jedynym obsługiwanym systemem operacyjnym dla kontenerów platformy Docker.
<sup>4</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="scale"></a>Skalowanie

| | Skalowanie w poziomie | Maksymalna liczba wystąpień |
| --- | --- | --- |
| **[Plan Zużycie](#consumption-plan)** | Oparte na zdarzeniach. Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia. Azure Functions infrastruktury skaluje zasoby procesora i pamięci, dodając kolejne wystąpienia hosta funkcji na podstawie liczby zdarzeń wyzwalanych przez jej funkcje. | 200 |
| **[Plan Premium](#premium-plan)** | Oparte na zdarzeniach. Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia. Azure Functions infrastruktury skaluje zasoby procesora i pamięci, dodając kolejne wystąpienia hosta funkcji na podstawie liczby zdarzeń wyzwalanych przez jej funkcje. |100|
| **[Dedykowany plan](#app-service-plan)**<sup>1</sup> | Ręczne/automatyczne skalowanie |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> | Ręczne/automatyczne skalowanie |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Skalowanie automatyczne sterowane zdarzeniami dla klastrów Kubernetes przy użyciu [KEDA](https://keda.sh). | Różni się &nbsp; w zależności od &nbsp; klastra.&nbsp;&nbsp;|

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="cold-start-behavior"></a>Zachowanie zimnego uruchamiania

|    |    | 
| -- | -- |
| **[&nbsp;Plan zużycia](#consumption-plan)** | Aplikacje mogą skalować do zera w razie bezczynności przez pewien czas, co oznacza, że niektóre żądania mogą mieć dodatkowe opóźnienia podczas uruchamiania.  Plan zużycia ma pewne optymalizacje, aby ułatwić skrócenie czasu uruchamiania, w tym ściąganie z wbudowanych funkcji symboli zastępczych, które mają już uruchomiony host funkcji i procesy językowe. |
| **[Plan Premium](#premium-plan)** | Bezterminowo podgrzewane wystąpienia, aby uniknąć dowolnego zimnego startu. |
| **[Dedykowany plan](#app-service-plan)**<sup>1</sup> | W przypadku uruchamiania w ramach dedykowanego planu hosty funkcji mogą działać w sposób ciągły, co oznacza, że zimne uruchomienie nie jest naprawdę problemem. |
| **[ASE](#app-service-plan)**<sup>1</sup> | W przypadku uruchamiania w ramach dedykowanego planu hosty funkcji mogą działać w sposób ciągły, co oznacza, że zimne uruchomienie nie jest naprawdę problemem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Zależy od konfiguracji KEDA. Aplikacje można skonfigurować tak, aby zawsze były uruchamiane, a nigdy nie miały zimnego startu lub skonfigurowane do skalowania do zera, co spowoduje zimne rozpoczęcie od nowych zdarzeń. 

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="service-limits"></a>Limity usługi

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Funkcje sieci

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Rozliczenia

| | | 
| --- | --- |
| **[Plan Zużycie](#consumption-plan)** | Płacisz tylko za czas działania funkcji. Rozliczenia zależą od liczby wykonań, czasu wykonania oraz użytej pamięci. |
| **[Plan Premium](#premium-plan)** | Plan Premium jest oparty na liczbie podstawowych sekund i ilości pamięci używanej w ramach wymaganych i wstępnie rozgrzanych wystąpień. Co najmniej jedno wystąpienie na plan musi być utrzymywane w całej porze. Ten plan zapewnia bardziej przewidywalne ceny. |
| **[Dedykowany plan](#app-service-plan)**<sup>1</sup> | Płacisz za aplikacje funkcji w planie App Service, tak jak w przypadku innych zasobów App Service, takich jak aplikacje sieci Web.|
| **[ASE](#app-service-plan)**<sup>1</sup> | Istnieje stała miesięczna stawka za środowisko ASE, która pokrywa się z infrastrukturą i nie zmienia rozmiaru środowiska ASE. Ponadto jest kosztem App Service planu vCPU. Wszystkie aplikacje hostowane w środowisku ASE znajdują się w jednostce SKU wyceny „izolowanej” (Isolated). |
| **[Kubernetes](functions-kubernetes-keda.md)**| Płacisz tylko za koszty klastra Kubernetes; Brak dodatkowych rozliczeń dla funkcji. Aplikacja funkcji działa jako obciążenie aplikacji na klastrze, podobnie jak zwykła aplikacja. |

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="next-steps"></a>Następne kroki

+ [Szybki Start: Tworzenie projektu Azure Functions przy użyciu Visual Studio Code](functions-create-first-function-vs-code.md)
+ [Technologie wdrażania w Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions — przewodnik dla deweloperów](functions-reference.md)
