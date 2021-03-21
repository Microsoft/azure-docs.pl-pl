---
title: Skalowanie i hosting usługi Azure Functions
description: Dowiedz się, jak wybierać między planem zużycia Azure Functions a planem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936779"
---
# <a name="azure-functions-hosting-options"></a>Opcje hostingu Azure Functions

Podczas tworzenia aplikacji funkcji na platformie Azure musisz wybrać plan hostingu dla swojej aplikacji. Istnieją trzy podstawowe plany hostingu dostępne dla Azure Functions: [Plan zużycia](consumption-plan.md), [Plan Premium](functions-premium-plan.md)i [dedykowany plan (App Service)](dedicated-plan.md). Wszystkie plany hostingu są ogólnie dostępne (GA) na maszynach wirtualnych z systemem Linux i Windows.

Wybrany plan hostingu wymusza następujące zachowania:

* Sposób skalowania aplikacji funkcji.
* Zasoby dostępne dla każdego wystąpienia aplikacji funkcji.
* Obsługa zaawansowanych funkcji, takich jak łączność z platformą Azure Virtual Network.

Ten artykuł zawiera szczegółowe porównanie różnych planów hostingu wraz z hostingiem opartym na Kubernetes.

## <a name="overview-of-plans"></a>Przegląd planów

Poniżej znajduje się Podsumowanie korzyści płynących z trzech głównych planów hostingu dla funkcji:

| | |
| --- | --- |  
|**[Plan Zużycie](consumption-plan.md)**| Skaluj automatycznie i płacisz tylko za zasoby obliczeniowe, gdy funkcje są uruchomione.<br/><br/>W planie zużycia wystąpienia hosta funkcji są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących.<br/><br/> ✔ Domyślnego planu hostingu.<br/>✔ Płacisz tylko wtedy, gdy funkcje są uruchomione.<br/>✔ Skaluje się automatycznie, nawet w okresach dużego obciążenia.|  
|**[Plan Premium](functions-premium-plan.md)**|Automatyczne skalowanie na podstawie popytu przy użyciu wstępnie rozgrzanych procesów roboczych, które uruchamiają aplikacje bez opóźnień po stanie bezczynności, działają na bardziej wydajnych wystąpieniach i nawiązują połączenie z sieciami wirtualnymi. <br/><br/>Zapoznaj się z planem Azure Functions Premium w następujących sytuacjach: <br/><br/>✔ Aplikacje funkcji działają ciągle lub niemal ciągle.<br/>✔ Masz dużą liczbę małych wykonań i rachunku o dużym wykorzystaniu, ale co najmniej GB s w planie zużycia.<br/>✔ Potrzebujesz więcej opcji procesora lub pamięci niż to, co jest dostępne w ramach planu zużycia.<br/>✔ Kod musi być uruchomiony dłużej niż maksymalny dozwolony czas wykonywania w planie zużycia.<br/>✔ Wymagane są funkcje, które nie są dostępne w planie zużycia, takie jak łączność sieci wirtualnej.|  
|**[Dedykowany plan](dedicated-plan.md)** |Uruchamiaj swoje funkcje w ramach planu App Service według regularnych [App Service stawek planu](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Najlepsze dla długotrwałych scenariuszy, w których nie można używać [Durable Functions](durable/durable-functions-overview.md) . Należy wziąć pod uwagę plan App Service w następujących sytuacjach:<br/><br/>✔ Masz istniejące, nieużywane maszyny wirtualne, na których działają już inne wystąpienia App Service.<br/>✔ Chcesz udostępnić niestandardowy obraz, na którym będą uruchamiane funkcje. <br/>✔ Skalowanie predykcyjne i koszty są wymagane.|  

Tabele porównawcze w tym artykule zawierają również następujące opcje hostingu, które zapewniają największą ilość kontroli i izolację, w której można uruchamiać aplikacje funkcji.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (ASE) to funkcja App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę.<br/><br/>Środowisk ASE są odpowiednie dla obciążeń aplikacji, które wymagają: <br/><br/>✔ Bardzo wysoka Skala.<br/>✔ Pełna izolacja obliczeniowa i bezpieczny dostęp do sieci.<br/>✔ Duże użycie pamięci.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes zapewnia w pełni izolowane i dedykowane środowisko działające w oparciu o platformę Kubernetes.<br/><br/> Kubernetes jest odpowiednie dla obciążeń aplikacji, które wymagają: <br/>✔ Niestandardowe wymagania sprzętowe.<br/>✔ Izolacja i bezpieczny dostęp do sieci.<br/>✔ Możliwość uruchamiania w środowisku hybrydowym lub w chmurze.<br/>✔ Uruchamiane razem z istniejącymi aplikacjami i usługami Kubernetes.|  

Pozostałe tabele w tym artykule porównują plany dotyczące różnych funkcji i zachowań. Aby uzyskać porównanie kosztów między dynamicznymi planami hostingu (zużycie i Premium), zobacz [stronę z cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Aby uzyskać cennik różnych opcji planu dedykowanego, zobacz [stronę z cennikiem App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>System operacyjny/środowisko uruchomieniowe

W poniższej tabeli przedstawiono obsługę obsługiwanego systemu operacyjnego i środowiska uruchomieniowego języka dla planów hostingu.

| | System Linux<sup>1</sup><br/>Tylko kod | System Windows<sup>2</sup><br/>Tylko kod | Linux<sup>, 1, 3</sup><br/>Kontener platformy Docker |
| --- | --- | --- | --- |
| **[Plan Zużycie](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core | Brak pomocy technicznej  |
| **[Plan Premium](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python  | 
| **[Dedykowany plan](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | nie dotyczy | nie dotyczy |.NET Core<br/>Node.js<br/>Java<br/>Program PowerShell Core<br/>Python |

<sup>1</sup> Linux to jedyny obsługiwany system operacyjny dla stosu środowiska uruchomieniowego języka Python. <br/>
<sup>2</sup> system Windows to jedyny obsługiwany system operacyjny dla stosu środowiska uruchomieniowego programu PowerShell.<br/>
<sup>3</sup> Linux to jedyny obsługiwany system operacyjny dla kontenerów platformy Docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Skalowanie

Poniższa tabela zawiera porównanie zachowań skalowania różnych planów hostingu.

| | Skalowanie w poziomie | Maksymalna liczba wystąpień |
| --- | --- | --- |
| **[Plan Zużycie](consumption-plan.md)** | [Oparte na zdarzeniach](event-driven-scaling.md). Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia. Azure Functions skaluje zasoby procesora i pamięci przez dodanie dodatkowych wystąpień hosta funkcji na podstawie liczby zdarzeń wyzwalacza przychodzącego. | 200 |
| **[Plan Premium](functions-premium-plan.md)** | [Oparte na zdarzeniach](event-driven-scaling.md). Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia. Azure Functions infrastruktury skaluje zasoby procesora i pamięci, dodając kolejne wystąpienia hosta funkcji na podstawie liczby zdarzeń wyzwalanych przez jej funkcje. |100|
| **[Dedykowany plan](dedicated-plan.md)**<sup>1</sup> | Ręczne/automatyczne skalowanie |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | Ręczne/automatyczne skalowanie |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Skalowanie automatyczne sterowane zdarzeniami dla klastrów Kubernetes przy użyciu [KEDA](https://keda.sh). | Różni się &nbsp; w zależności od &nbsp; klastra&nbsp;&nbsp;|

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Zachowanie zimnego uruchamiania

|    |    | 
| -- | -- |
| **[&nbsp;Plan zużycia](consumption-plan.md)** | Aplikacje mogą skalować do zera w razie bezczynności, co oznacza, że niektóre żądania mogą mieć dodatkowe opóźnienia przy uruchamianiu.  Plan zużycia ma pewne optymalizacje, aby ułatwić skrócenie czasu uruchamiania, w tym ściąganie z wbudowanych funkcji symboli zastępczych, które mają już uruchomiony host funkcji i procesy językowe. |
| **[Plan Premium](functions-premium-plan.md)** | Bezterminowo podgrzewane wystąpienia, aby uniknąć dowolnego zimnego startu. |
| **[Dedykowany plan](dedicated-plan.md)** | W przypadku uruchamiania w ramach dedykowanego planu hosty funkcji mogą działać w sposób ciągły, co oznacza, że zimne uruchomienie nie jest naprawdę problemem. |
| **[ASE](dedicated-plan.md)** | W przypadku uruchamiania w ramach dedykowanego planu hosty funkcji mogą działać w sposób ciągły, co oznacza, że zimne uruchomienie nie jest naprawdę problemem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | W zależności od konfiguracji KEDA aplikacje można skonfigurować tak, aby uniknąć zimnego startu. Jeśli skonfigurowano do skalowania do zera, zimny start występuje dla nowych zdarzeń. 

## <a name="service-limits"></a>Limity usługi

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Funkcje sieci

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Rozliczenia

| | | 
| --- | --- |
| **[Plan Zużycie](consumption-plan.md)** | Płacisz tylko za czas działania funkcji. Rozliczenia zależą od liczby wykonań, czasu wykonania oraz użytej pamięci. |
| **[Plan Premium](functions-premium-plan.md)** | Plan Premium jest oparty na liczbie podstawowych sekund i ilości pamięci używanej w ramach wymaganych i wstępnie rozgrzanych wystąpień. Co najmniej jedno wystąpienie na plan musi być utrzymywane w całej porze. Ten plan zapewnia najwyższy przewidywalność cen. |
| **[Plan dedykowany](dedicated-plan.md)* | Płacisz za aplikacje funkcji w planie App Service, tak jak w przypadku innych zasobów App Service, takich jak aplikacje sieci Web.|
| **[App Service Environment (ASE)](dedicated-plan.md)** | Istnieje stała miesięczna stawka za środowisko ASE, która pokrywa się z infrastrukturą i nie zmienia rozmiaru środowiska ASE. Jest też koszt za App Service planu vCPU. Wszystkie aplikacje hostowane w środowisku ASE znajdują się w jednostce SKU wyceny „izolowanej” (Isolated). |
| **[Kubernetes](functions-kubernetes-keda.md)**| Płacisz tylko za koszty klastra Kubernetes; Brak dodatkowych rozliczeń dla funkcji. Aplikacja funkcji działa jako obciążenie aplikacji na klastrze, podobnie jak zwykła aplikacja. |

## <a name="next-steps"></a>Następne kroki

+ [Technologie wdrażania w Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions — przewodnik dla deweloperów](functions-reference.md)