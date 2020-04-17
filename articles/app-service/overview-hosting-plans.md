---
title: Plany usługi App Service
description: Dowiedz się, jak działają plany usługi App Service w usłudze Azure App Service, jak są rozliczane klientowi i jak je skalować zgodnie z potrzebami.
keywords: usługa aplikacji, usługa aplikacji platformy Azure, skalowanie, skalowalne, skalowalność, plan usługi aplikacji, koszt usługi aplikacji
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: b1c44fb9f44eb75e6d2a766213c5db094ebe79b1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537648"
---
# <a name="azure-app-service-plan-overview"></a>Plan usługi Azure App Service — omówienie

W usłudze App Service aplikacje są uruchamiane w ramach _planu usługi App Service_. Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania. Te zasoby obliczeniowe są analogiczne do [_farmy serwerów_](https://wikipedia.org/wiki/Server_farm) w konwencjonalnym hostingu internetowym. Co najmniej jedna aplikacja może być skonfigurowana do uruchamiania na tych samych zasobach obliczeniowych (lub w tym samym planie usługi App Service).

Podczas tworzenia planu usługi app service w określonym regionie (na przykład Europa Zachodnia), zestaw zasobów obliczeniowych jest tworzony dla tego planu w tym regionie. Niezależnie od aplikacji, które można umieścić w tym planie usługi aplikacji uruchomić na tych zasobów obliczeniowych, zgodnie z definicją w planie usługi app service. Każdy plan usługi App Service definiuje następujące informacje:

- Region (Zachodnie stany USA, Wschodnie stany USA itp.)
- Liczba wystąpień maszyn wirtualnych
- Rozmiar wystąpień maszyn wirtualnych (mały, średni, duży)
- Warstwa cenowa (bezpłatna, udostępniona, podstawowa, standardowa, premium, premiumv2, odizolowana)

_Warstwa cenowa_ planu usługi app service określa, jakie funkcje usługi App Service otrzymujesz i ile płacisz za plan. Warstwy cenowe dzielą się na kilka kategorii:

- **Obliczenia udostępnione:** **Bezpłatne** i **udostępnione**— dwie warstwy podstawowe — uruchamia aplikację na tej samej maszynie Wirtualnej platformy Azure, co inne aplikacje usługi App Service, w tym aplikacje innych klientów. Te warstwy określają limity przydziałów procesora CPU dla każdej aplikacji uruchamianej na udostępnionych zasobach, a zasobów nie można skalować w poziomie.
- **Dedykowane obliczenia:** warstwy **Basic,** **Standard,** **Premium**i **PremiumV2** uruchamiają aplikacje na dedykowanych maszynach wirtualnych platformy Azure. Jedynie aplikacje w tym samym planie usługi App Service współdzielą te same zasoby obliczeniowe. Im wyższa warstwa, tym większa liczba wystąpień maszyn wirtualnych dostępnych na potrzeby zwiększania skali w poziomie.
- **Izolowane:** Ta warstwa uruchamia dedykowane maszyny wirtualne platformy Azure w dedykowanych sieciach wirtualnych platformy Azure. Zapewnia izolację sieci na poziomie izolacji obliczeń do aplikacji. Ta warstwa daje największe możliwości zwiększania skali w poziomie.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każda warstwa zawiera również określony podzbiór funkcji usługi App Service. Funkcje te obejmują domeny niestandardowe i certyfikaty TLS/SSL, skalowanie automatyczne, miejsca wdrożenia, kopie zapasowe, integrację usługi Traffic Manager i inne. Im wyższa warstwa, tym więcej funkcji jest dostępnych. Aby dowiedzieć się, które funkcje są obsługiwane w każdej warstwie cenowej, zobacz [Szczegóły planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nowa warstwa cenowa **PremiumV2** zapewnia [maszyny wirtualne z serii Dv2](../virtual-machines/dv2-dsv2-series.md) z szybszymi procesorami, pamięcią SSD i podwójnym współczynnikiem pamięci do rdzenia w porównaniu z **warstwą standardową.** **PremiumV2** obsługuje również większą skalę dzięki zwiększonej liczbie wystąpień, zapewniając jednocześnie wszystkie zaawansowane funkcje znalezione w planie standardowym. Wszystkie funkcje dostępne w istniejącej warstwie **Premium** są uwzględnione w **wersji PremiumV2.**
>
> Podobnie jak w przypadku innych warstw dedykowanych, dla tej warstwy dostępne są trzy rozmiary maszyn wirtualnych:
>
> - Mały (jeden rdzeń procesora, 3,5 GiB pamięci) 
> - Medium (dwa rdzenie procesora, 7 GiB pamięci) 
> - Duży (cztery rdzenie procesora, 14 GiB pamięci)  
>
> Aby uzyskać informacje o cenach **PremiumV2,** zobacz [Ceny usługi app service](https://azure.microsoft.com/pricing/details/app-service/).
>
> Aby rozpocząć korzystanie z nowej warstwy cenowej **PremiumV2,** zobacz [Konfigurowanie warstwy PremiumV2 dla usługi App Service.](app-service-configure-premium-tier.md)

## <a name="how-does-my-app-run-and-scale"></a>Jak działa i skaluje aplikację?

W warstwach **Bezpłatne** i **Udostępnione** aplikacja odbiera minuty procesora CPU w wystąpieniu udostępnionej maszyny Wirtualnej i nie może skalować w poziomie. W innych warstwach aplikacja jest uruchamiana i skalowana w następujący sposób.

Podczas tworzenia aplikacji w usłudze App Service, jest on umieszczany w planie usługi app service. Po uruchomieniu aplikacji działa na wszystkich wystąpieniach maszyny Wirtualnej skonfigurowanych w planie usługi app service. Jeśli wiele aplikacji znajduje się w tym samym planie usługi App Service, wszystkie one współużytkują te same wystąpienia maszyn wirtualnych. Jeśli masz wiele gniazd wdrażania dla aplikacji, wszystkie gniazda wdrażania również działają na tych samych wystąpieniach maszyny Wirtualnej. Jeśli włączysz dzienniki diagnostyczne, wykonasz kopie zapasowe lub uruchomisz webjobs, użyją one również cykli procesora CPU i pamięci w tych wystąpieniach maszyny Wirtualnej.

W ten sposób plan usługi aplikacji jest jednostką skalowania aplikacji usługi app service. Jeśli plan jest skonfigurowany do uruchamiania pięciu wystąpień maszyn wirtualnych, wszystkie aplikacje w planie są uruchamiane we wszystkich pięciu wystąpieniach. Jeśli plan jest skonfigurowany do skalowania automatycznego, wszystkie aplikacje w planie są skalowane razem na podstawie ustawień skalowania automatycznego.

Aby uzyskać informacje na temat skalowania w poziomie aplikacji, zobacz [Skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Ile kosztuje mój plan usługi App Service?

W tej sekcji opisano sposób rozliczania aplikacji usługi App Service. Aby uzyskać szczegółowe informacje o cenach dla regionu, zobacz [Ceny usługi app service](https://azure.microsoft.com/pricing/details/app-service/).

Z wyjątkiem warstwy **bezpłatna** plan usługi app service zawiera opłatę godzinową od zasobów obliczeniowych, których używa.

- W warstwie **Udostępnione** każda aplikacja otrzymuje przydział minut procesora CPU, więc _każda aplikacja_ jest naliczana co godzinę za przydział procesora CPU.
- W dedykowanych warstwach obliczeniowych **(Podstawowe,** **Standardowe,** **Premium,** **PremiumV2)** plan usługi app service definiuje liczbę wystąpień maszyn wirtualnych, do których aplikacje są skalowane, więc _każde wystąpienie maszyny Wirtualnej_ w planie usługi aplikacji jest obciążone opłatą godzinową. Te wystąpienia maszyn wirtualnych są naliczane tak samo niezależnie od tego, ile aplikacji jest na nich uruchomionych. Aby uniknąć nieoczekiwanych opłat, zobacz [Czyszczenie planu usługi app service](app-service-plan-manage.md#delete).
- W warstwie **Izolowane** środowisko usługi aplikacji definiuje liczbę izolowanych pracowników, którzy uruchamiają aplikacje, a _każdy pracownik_ jest naliczany co godzinę. Ponadto istnieje godzinowa opłata podstawowa za uruchamianie samego środowiska usługi app service.

Nie pobierasz opłat za korzystanie z dostępnych funkcji usługi App Service (konfigurowanie domen niestandardowych, certyfikatów TLS/SSL, gniazd wdrażania, kopii zapasowych itp.). Wyjątkami są:

- Domeny usługi app service — płacisz przy zakupie na platformie Azure i odnawianiu go każdego roku.
- Certyfikaty usługi app service — płacisz przy zakupie na platformie Azure i odnawianiu go każdego roku.
- Połączenia TLS oparte na protoke IP — za każde połączenie TLS oparte na protoke IP pobierana jest opłata godzinowa, ale **niektóre** standardowe lub wyższe warstwy zapewniają bezpłatne jedno połączenie TLS oparte na protokoście. Połączenia TLS oparte na SNI są bezpłatne.

> [!NOTE]
> Jeśli zintegrujesz usługę App Service z inną usługą platformy Azure, może być konieczne rozważenie opłat od tych innych usług. Na przykład jeśli używasz usługi Azure Traffic Manager do skalowania aplikacji geograficznie, usługa Azure Traffic Manager również pobiera opłaty na podstawie użycia. Aby oszacować koszt usług krzyżowych na platformie Azure, zobacz [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co zrobić, jeśli moja aplikacja potrzebuje więcej możliwości lub funkcji?

Plan usługi App Service można skalować w górę i w dół w dowolnym momencie. Jest to tak proste, jak zmiana warstwy cenowej planu. Na początek możesz wybrać niższą warstwę cenową i skalować ją w górę później, gdy będziesz potrzebować więcej funkcji usługi App Service.

Na przykład możesz rozpocząć testowanie aplikacji sieci web w planie **bezpłatnej** usługi app service i nic nie płacić. Jeśli chcesz dodać [niestandardową nazwę DNS](app-service-web-tutorial-custom-domain.md) do aplikacji sieci web, po prostu skaluj plan do **warstwy udostępnionej.** Później, gdy chcesz [utworzyć powiązanie TLS,](configure-ssl-bindings.md)skaluj plan do warstwy **Podstawowa.** Jeśli chcesz mieć [środowiska przejściowe,](deploy-staging-slots.md)skaluj do **warstwy standardowej.** Jeśli potrzebujesz więcej rdzeni, pamięci lub magazynu, skaluj do większego rozmiaru maszyny Wirtualnej w tej samej warstwie.

To samo działa na odwrocie. Jeśli czujesz, że nie potrzebujesz już możliwości lub funkcji wyższej warstwy, możesz skalować w dół do niższego poziomu, co pozwala zaoszczędzić pieniądze.

Aby uzyskać informacje na temat skalowania planu usługi aplikacji, zobacz [Skalowanie aplikacji na platformie Azure](manage-scale-up.md).

Jeśli aplikacja znajduje się w tym samym planie usługi App Service z innymi aplikacjami, możesz poprawić wydajność aplikacji, izolując zasoby obliczeniowe. Można to zrobić, przenosząc aplikację do oddzielnego planu usługi app service. Aby uzyskać więcej informacji, zobacz [Przenoszenie aplikacji do innego planu usługi App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Czy należy umieścić aplikację w nowym planie lub istniejącym planie?

Ponieważ płacisz za zasoby obliczeniowe, które przydziela plan usługi App Service (zobacz [Ile kosztuje mój plan usługi App Service?](#cost)), możesz potencjalnie zaoszczędzić pieniądze, umieszczając wiele aplikacji w jednym planie usługi App Service. Możesz nadal dodawać aplikacje do istniejącego planu, o ile plan ma wystarczającą ilość zasobów do obsługi obciążenia. Należy jednak pamiętać, że aplikacje w tym samym planie usługi App Service mają te same zasoby obliczeniowe. Aby ustalić, czy nowa aplikacja ma niezbędne zasoby, należy znać pojemność istniejącego planu usługi App Service i oczekiwane obciążenie nowej aplikacji. Przeciążanie planu usługi App Service może spowodować przestój dla nowych i istniejących aplikacji.

Aplikację należy izolować w nowym planie usługi App Service w następujących przypadkach:

- Aplikacja jest intensywnie korzystająca z zasobów.
- Chcesz skalować aplikację niezależnie od innych aplikacji w istniejącym planie.
- Aplikacja potrzebuje zasobów w innym regionie geograficznym.

W ten sposób można przydzielić nowy zestaw zasobów dla aplikacji i uzyskać większą kontrolę nad aplikacjami.

## <a name="manage-an-app-service-plan"></a>Zarządzanie planem usługi aplikacji

> [!div class="nextstepaction"]
> [Zarządzanie planem usługi aplikacji](app-service-plan-manage.md)
