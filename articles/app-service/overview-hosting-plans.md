---
title: Plany usługi App Service
description: Dowiedz się, jak plany App Service działają w Azure App Service, jak są rozliczane dla klienta oraz jak skalować je w swoich potrzebach.
keywords: App Service, Azure App Service, skala, skalowalność, skalowalność, plan usługi App Service, koszt usługi App Service
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 6e5de3cdec7a9c503f4b7bf7056bd62f1ddf682d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594026"
---
# <a name="azure-app-service-plan-overview"></a>Plan usługi Azure App Service — omówienie

W App Service (Web Apps, API Apps lub Mobile Apps) aplikacja zawsze jest uruchamiana w _planie App Service_. Ponadto [Azure Functions](../azure-functions/dedicated-plan.md) ma także opcję uruchamiania w _planie App Service_. Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania. Te zasoby obliczeniowe są analogiczne do [_farmy serwerów_](https://wikipedia.org/wiki/Server_farm) w konwencjonalnym hostingu sieci Web. Co najmniej jedna aplikacja może być skonfigurowana do uruchamiania w tych samych zasobach obliczeniowych (lub w tym samym planie App Service).

Po utworzeniu planu App Service w określonym regionie (na przykład Europa Zachodnia) zestaw zasobów obliczeniowych jest tworzony dla tego planu w tym regionie. Niezależnie od tego, które aplikacje zostały umieszczone w tym App Service planie, są uruchamiane na tych zasobach obliczeniowych zgodnie z planem App Service. Każdy plan usługi App Service definiuje następujące informacje:

- Region (Zachodnie stany USA, Wschodnie stany USA itp.)
- Liczba wystąpień maszyn wirtualnych
- Rozmiar wystąpień maszyn wirtualnych (mały, średni, duży)
- Warstwa cenowa (bezpłatna, współdzielona, podstawowa, standardowa, Premium, PremiumV2, PremiumV3, izolowana)

_Warstwa cenowa_ planu App Service określa, jakie funkcje App Service uzyskasz i ile płacisz za plan. Warstwy cenowe dzielą się na kilka kategorii:

- **Udostępnione obliczenia**: **bezpłatne** i **udostępnione**, dwie warstwy podstawowe, URUCHAMIAJĄ aplikację na tej samej maszynie wirtualnej platformy Azure, co inne App Service aplikacje, w tym aplikacje innych klientów. Te warstwy określają limity przydziałów procesora CPU dla każdej aplikacji uruchamianej na udostępnionych zasobach, a zasobów nie można skalować w poziomie.
- **Dedykowane obliczenia**: warstwy **Basic**, **Standard**, **Premium**, **PremiumV2** i **PremiumV3** uruchamiają aplikacje na dedykowanych maszynach wirtualnych platformy Azure. Jedynie aplikacje w tym samym planie usługi App Service korzystają z tych samych zasobów obliczeniowych. Im wyższa warstwa, tym większa liczba wystąpień maszyn wirtualnych dostępnych na potrzeby zwiększania skali w poziomie.
- **Izolowane**: Ta warstwa służy do uruchamiania dedykowanych maszyn wirtualnych platformy Azure w dedykowanych sieciach wirtualnych platformy Azure. Zapewnia izolację sieci na poziomie izolacji obliczeniowej dla aplikacji. Ta warstwa daje największe możliwości zwiększania skali w poziomie.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każda warstwa udostępnia również określony podzbiór funkcji App Service. Te funkcje obejmują domeny niestandardowe oraz certyfikaty TLS/SSL, Skalowanie automatyczne, miejsca wdrożenia, kopie zapasowe, integrację Traffic Manager i wiele innych. Im wyższa warstwa, tym więcej funkcji jest dostępnych. Aby dowiedzieć się, które funkcje są obsługiwane w każdej warstwie cenowej, zobacz [szczegóły planu App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> Nowa warstwa cenowa **PremiumV3** gwarantuje maszynom z szybszymi procesorami (minimalny 195 [ACU](../virtual-machines/acu.md) na wirtualny procesor CPU), magazyn SSD i czterokrotny stosunek pamięci na rdzeń w porównaniu z warstwą **standardowa** . Usługa **PremiumV3** obsługuje również wyższą skalę dzięki większej liczbie wystąpień, jednocześnie zapewniając jednocześnie wszystkie zaawansowane możliwości dostępne w warstwie **standardowa** . Wszystkie funkcje dostępne w istniejącej warstwie **PremiumV2** są zawarte w **PremiumV3**.
>
> Podobnie jak w przypadku innych dedykowanych warstw, dla tej warstwy są dostępne trzy rozmiary maszyn wirtualnych:
>
> - Małe (2 rdzenie procesora CPU, 8 GiB pamięci) 
> - Średni (4 rdzenie procesora CPU, 16 GiB pamięci) 
> - Duże (8 rdzeni procesora CPU, 32 GiB pamięci)  
>
> Aby uzyskać informacje o cenach **PremiumV3** , zobacz [Cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).
>
> Aby rozpocząć pracę z nową warstwą cenową **PremiumV3** , zobacz [Konfigurowanie warstwy PremiumV3 dla App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak działa i skalowanie mojej aplikacji?

W warstwach **bezpłatna** i **współdzielona** aplikacja otrzymuje minuty procesora CPU w wystąpieniu udostępnionej maszyny wirtualnej i nie może skalować w poziomie. W innych warstwach aplikacja działa i skaluje się w następujący sposób.

Podczas tworzenia aplikacji w App Service jest ona umieszczana w planie App Service. Gdy aplikacja zostanie uruchomiona, zostanie uruchomiona na wszystkich wystąpieniach maszyn wirtualnych skonfigurowanych w planie App Service. Jeśli wiele aplikacji jest w tym samym planie App Service, wszystkie współużytkują te same wystąpienia maszyn wirtualnych. Jeśli masz wiele miejsc wdrożenia dla aplikacji, wszystkie miejsca wdrożenia są również uruchamiane na tych samych wystąpieniach maszyn wirtualnych. W przypadku włączenia dzienników diagnostycznych, wykonywania kopii zapasowych lub uruchamiania zadań WebJob program używa również cykli procesora CPU i pamięci dla tych wystąpień maszyn wirtualnych.

W ten sposób plan App Service jest jednostką skalowania aplikacji App Service. Jeśli plan jest skonfigurowany do uruchamiania pięciu wystąpień maszyn wirtualnych, wszystkie aplikacje w planie są uruchamiane na wszystkich pięciu wystąpieniach. Jeśli plan jest skonfigurowany do automatycznego skalowania, wszystkie aplikacje w planie są skalowane razem na podstawie ustawień skalowania automatycznego.

Aby uzyskać informacje na temat skalowania aplikacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Jak dużo kosztu App Service planować?

W tej sekcji opisano sposób rozliczania App Service aplikacji. Aby uzyskać szczegółowe informacje o cenach specyficznych dla regionu, zobacz [Cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).

Z wyjątkiem warstwy **bezpłatna** w planie App Service są naliczane opłaty za zasoby obliczeniowe, których używa.

- W warstwie **udostępnionej** każda aplikacja otrzymuje limit czasu procesora (w minutach), więc dla _każdej aplikacji_ jest naliczana opłata za przydział procesora CPU.
- W przypadku dedykowanych warstw obliczeniowych (**podstawowa**, **standardowa**, **Premium**, **PremiumV2**, **PremiumV3**) plan App Service definiuje liczbę wystąpień maszyn wirtualnych, do których są skalowane aplikacje, w związku z czym _każde wystąpienie maszyny wirtualnej_ w planie App Service jest obciążane. Te wystąpienia maszyn wirtualnych są obciążane tymi samymi, niezależnie od liczby uruchomionych na nich aplikacji. Aby uniknąć nieoczekiwanych opłat, zobacz [oczyszczanie planu App Service](app-service-plan-manage.md#delete).
- W warstwie **izolowanej** App Service Environment definiuje liczbę izolowanych procesów roboczych, na których działają aplikacje, i _każdy proces roboczy_ jest naliczany. Ponadto istnieje płaska opłata za uruchomienie App Service Environment.

Za korzystanie z funkcji App Service, które są dostępne dla użytkownika (Konfigurowanie domen niestandardowych, certyfikatów TLS/SSL, miejsc wdrożenia, kopii zapasowych itp.), nie jest naliczana opłata. Wyjątki są następujące:

- Domeny App Service — płacisz przy zakupie jednej na platformie Azure i odnowieniu każdego roku.
- Certyfikaty App Service — płacisz przy zakupie jednej na platformie Azure i po odnowieniu każdego roku.
- Połączenia protokołu TLS oparte na protokole IP — jest naliczana opłata godzinowa za każde połączenie protokołu TLS opartego na protokole IP, ale niektóre warstwy **Standard** lub powyżej umożliwiają bezpłatne połączenie TLS oparte na protokole IP. Połączenia TLS oparte na SNI są bezpłatne.

> [!NOTE]
> Jeśli integrujesz App Service z inną usługą platformy Azure, może być konieczne naliczanie opłat za inne usługi. Na przykład, jeśli używasz platformy Azure Traffic Manager do skalowania aplikacji geograficznie, usługa Azure Traffic Manager również naliczane na podstawie użycia. Aby oszacować koszt międzyusług na platformie Azure, zobacz [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/). 

Chcesz zoptymalizować i zapisać wydatki na chmurę?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co zrobić, jeśli moja aplikacja potrzebuje więcej możliwości lub funkcji?

Plan usługi App Service można skalować w górę i w dół w dowolnym momencie. Jest tak proste, jak zmiana warstwy cenowej planu. Na początek możesz wybrać niższą warstwę cenową i skalować ją w górę później, gdy będziesz potrzebować więcej funkcji usługi App Service.

Możesz na przykład rozpocząć testowanie aplikacji sieci Web w ramach planu App Service **bezpłatna** i zwrócić wartość Nothing. Aby dodać [niestandardową nazwę DNS](app-service-web-tutorial-custom-domain.md) do aplikacji sieci Web, wystarczy przeskalować swój plan do warstwy **udostępnionej** . Później, jeśli chcesz [utworzyć powiązanie TLS](configure-ssl-bindings.md), Skaluj plan do warstwy **podstawowa** . Jeśli chcesz mieć [środowiska przejściowe](deploy-staging-slots.md), Skaluj do warstwy **standardowa** . Jeśli potrzebujesz więcej rdzeni, pamięci lub magazynu, możesz skalować w górę do większego rozmiaru maszyny wirtualnej w tej samej warstwie.

To samo działa w odwrocie. Jeśli uważasz, że nie potrzebujesz już możliwości lub funkcji wyższego poziomu, możesz skalować w dół do niższej warstwy, co pozwala zaoszczędzić pieniądze.

Aby uzyskać informacje na temat skalowania planu App Service, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).

Jeśli aplikacja jest w tym samym planie App Service z innymi aplikacjami, możesz zwiększyć wydajność aplikacji, izolując zasoby obliczeniowe. Możesz to zrobić, przenosząc aplikację do oddzielnego planu App Service. Aby uzyskać więcej informacji, zobacz [przenoszenie aplikacji do innego planu App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Czy należy umieścić aplikację w nowym planie czy istniejącym planie?

Ponieważ płacisz za zasoby obliczeniowe przydzielane przez App Service planu (Zobacz, [ile kosztuje usługa my App Service plan?](#cost)), możesz zaoszczędzić pieniądze, umieszczając wiele aplikacji w jednym planie App Service. Można nadal dodawać aplikacje do istniejącego planu, o ile plan ma wystarczającą ilość zasobów do obsłużenia obciążenia. Należy jednak pamiętać, że aplikacje w tym samym App Service planować wszystkie te same zasoby obliczeniowe. Aby ustalić, czy nowa aplikacja ma niezbędne zasoby, należy znać pojemność istniejącego planu usługi App Service i oczekiwane obciążenie nowej aplikacji. Przeciążanie planu usługi App Service może spowodować przestój dla nowych i istniejących aplikacji.

Aplikację należy izolować w nowym planie usługi App Service w następujących przypadkach:

- Aplikacja wymaga dużej ilości zasobów.
- Chcesz skalować aplikację niezależnie od innych aplikacji w istniejącym planie.
- Aplikacja wymaga zasobu w innym regionie geograficznym.

W ten sposób można przydzielić nowy zestaw zasobów dla aplikacji i uzyskać większą kontrolę nad aplikacjami.

## <a name="manage-an-app-service-plan"></a>Zarządzanie planem App Service

> [!div class="nextstepaction"]
> [Zarządzanie planem App Service](app-service-plan-manage.md)