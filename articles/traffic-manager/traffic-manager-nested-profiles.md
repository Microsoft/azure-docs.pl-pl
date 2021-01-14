---
title: Zagnieżdżone profile Traffic Manager na platformie Azure
titleSuffix: Azure Traffic Manager
description: W tym artykule objaśniono funkcję "Profile zagnieżdżone" Traffic Manager platformy Azure
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185584"
---
# <a name="nested-traffic-manager-profiles"></a>Zagnieżdżone profile usługi Traffic Manager

Traffic Manager obejmuje szereg metod routingu ruchu, które umożliwiają kontrolowanie sposobu, w jaki Traffic Manager decyduje o tym, który punkt końcowy powinien odbierać ruch od każdego użytkownika końcowego. Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu ruchu sieciowego](traffic-manager-routing-methods.md).

Każdy profil Traffic Manager określa metodę routingu pojedynczego ruchu sieciowego. Istnieją jednak scenariusze, które wymagają bardziej zaawansowanego routingu ruchu niż Routing dostarczany przez pojedynczy profil Traffic Manager. Profile Traffic Manager można zagnieżdżać, aby łączyć korzyści z więcej niż jednej metody routingu ruchu. Profile zagnieżdżone umożliwiają zastąpienie domyślnego zachowania Traffic Manager w celu obsługi większych i bardziej złożonych wdrożeń aplikacji.

W poniższych przykładach pokazano, jak używać zagnieżdżonych profilów Traffic Manager w różnych scenariuszach.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Przykład 1: łączenie routingu ruchu "Performance" i "ważone"

Załóżmy, że aplikacja została wdrożona w następujących regionach świadczenia usługi Azure: zachodnie stany USA, Europa Zachodnia i Azja Wschodnia. Używasz metody routingu ruchu "Performance" Traffic Manager, aby dystrybuować ruch do regionu najbliżej użytkownika.

![Profil pojedynczego Traffic Manager][4]

Teraz Załóżmy, że chcesz przetestować aktualizację do usługi przed jej przeprowadzeniem. Chcesz użyć metody routingu ruchu "ważone", aby skierować mały procent ruchu do wdrożenia testowego. Wdrożenie testowe można skonfigurować wraz z istniejącym wdrożeniem produkcyjnym w regionie Europa Zachodnia.

Nie można połączyć jednocześnie routingu "ważone" i "ruch sieciowy" w pojedynczym profilu. Aby obsłużyć ten scenariusz, należy utworzyć profil Traffic Manager przy użyciu dwóch punktów końcowych Europa Zachodnia i metody routingu ruchu "ważone". Następnie należy dodać ten profil "podrzędny" jako punkt końcowy do profilu "Parent". Profil nadrzędny nadal używa metody routingu ruchu sieciowego i zawiera inne globalne wdrożenia jako punkty końcowe.

Poniższy diagram ilustruje ten przykład:

![Zagnieżdżone profile usługi Traffic Manager][2]

W tej konfiguracji ruch kierowany za pośrednictwem profilu nadrzędnego dystrybuuje ruch między regionami w normalny sposób. W regionie Europa Zachodnia profil zagnieżdżony dystrybuuje ruch do punktów końcowych produkcyjnych i testowych zgodnie z przypisanymi wagami.

Gdy profil nadrzędny używa metody routingu ruchu "Performance", każdy punkt końcowy musi mieć przypisaną lokalizację. Lokalizacja jest przypisywana podczas konfigurowania punktu końcowego. Wybierz region platformy Azure znajdujący się najbliżej wdrożenia. Regiony platformy Azure to wartości lokalizacji obsługiwane przez tabelę opóźnienia Internetu. Aby uzyskać więcej informacji, zobacz [Traffic Manager metodzie routingu ruchu "Performance"](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Przykład 2: monitorowanie punktu końcowego w profilach zagnieżdżonych

Traffic Manager aktywnie monitoruje kondycję każdego punktu końcowego usługi. Jeśli punkt końcowy jest w złej kondycji, Traffic Manager kieruje użytkowników do alternatywnych punktów końcowych w celu zachowania dostępności usługi. Takie monitorowanie punktu końcowego i zachowanie trybu failover mają zastosowanie do wszystkich metod routingu ruchu. Aby uzyskać więcej informacji, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md). Monitorowanie punktów końcowych działa inaczej w przypadku profilów zagnieżdżonych. W przypadku profilów zagnieżdżonych profil nadrzędny nie przeprowadza bezpośrednio kontroli kondycji elementu podrzędnego. W zamian kondycja punktów końcowych w profilu podrzędnym jest używana do obliczania ogólnej kondycji profilu podrzędnego. Te informacje o kondycji są propagowane w hierarchii zagnieżdżonych profilów. Profil nadrzędny używa tej zagregowanej kondycji, aby określić, czy ruch do niego ma być kierowany do profilu podrzędnego. Zapoznaj się z [często zadawanymi pytaniami](traffic-manager-FAQs.md#traffic-manager-nested-profiles) dotyczącymi monitorowania kondycji zagnieżdżonych profilów.

Powracając do poprzedniego przykładu, Załóżmy, że wdrożenie produkcyjne w regionie Europa Zachodnia zakończy się niepowodzeniem. Domyślnie profil "podrzędny" kieruje cały ruch do wdrożenia testowego. Jeśli test nie powiedzie się również, profil nadrzędny określa, że profil podrzędny nie powinien odbierać ruchu, ponieważ wszystkie podrzędne punkty końcowe są w złej kondycji. Następnie profil nadrzędny dystrybuuje ruch do innych regionów.

![Zagnieżdżony profil trybu failover (zachowanie domyślne)][3]

To rozwiązanie może być zadowalające. Może być również konieczne, aby cały ruch dla Europa Zachodnia przechodził do wdrożenia testowego zamiast ograniczonego ruchu sieciowego. Bez względu na kondycję wdrożenia testowego, przechodzenie w tryb failover do innych regionów w przypadku niepowodzenia wdrożenia produkcyjnego w Europie Zachodniej. Aby włączyć tę pracę w trybie failover, można określić parametr "MinChildEndpoints" podczas konfigurowania profilu podrzędnego jako punktu końcowego w profilu nadrzędnym. Parametr określa minimalną liczbę dostępnych punktów końcowych w profilu podrzędnym. Wartość domyślna to "1". W tym scenariuszu wartość MinChildEndpoints jest ustawiana na 2. Poniżej wartości progowej profil nadrzędny uwzględnia cały profil podrzędny jako niedostępny i kieruje ruch do innych punktów końcowych.

Na poniższej ilustracji przedstawiono tę konfigurację:

![Zagnieżdżony profil trybu failover z opcją "MinChildEndpoints" = 2][4]

> [!NOTE]
> Metoda routingu ruchu "Priority" dystrybuuje cały ruch do pojedynczego punktu końcowego. Z tego względu w ustawieniu MinChildEndpoints innym niż "1" dla profilu podrzędnego istnieje niewielki cel.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Przykład 3: priorytetowe regiony trybu failover w routingu ruchu "Performance"

Domyślne zachowanie metody routingu ruchu "Performance" ma miejsce, gdy punkty końcowe znajdują się w różnych lokalizacjach geograficznych, a użytkownicy końcowi są kierowani do punktu końcowego "najbliższy" w warunkach najmniejszego opóźnienia sieci.

Załóżmy jednak, że preferowany jest tryb failover ruchu Europa Zachodnia w regionie zachodnie stany USA, a ruch jest kierowany tylko do innych regionów, gdy oba punkty końcowe są niedostępne. To rozwiązanie można utworzyć przy użyciu profilu podrzędnego z metodą routingu ruchu "Priority".

![Routing ruchu "Performance" z preferencyjną trybem failover][6]

Ponieważ punkt końcowy Europa Zachodnia ma wyższy priorytet niż zachodni punkt końcowy USA, cały ruch jest wysyłany do punktu końcowego Europa Zachodnia, gdy oba punkty końcowe są w trybie online. Jeśli Europa Zachodnia nie powiedzie się, jego ruch jest kierowany do regionu zachodnie stany USA. W przypadku profilu zagnieżdżonego ruch jest kierowany do Azja Wschodnia tylko wtedy, gdy Europa Zachodnia i zachodnie stany USA kończą się niepowodzeniem.

Można powtórzyć ten wzorzec dla wszystkich regionów. Zastąp wszystkie trzy punkty końcowe w profilu nadrzędnym trzema profilami podrzędnymi, z których każda oferuje priorytetową sekwencję trybu failover.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Przykład 4: kontrolowanie routingu ruchu "Performance" między wieloma punktami końcowymi w tym samym regionie

Załóżmy, że metoda routingu ruchu "Performance" jest używana w profilu, który ma więcej niż jeden punkt końcowy w określonym regionie. Domyślnie ruch kierowany do tego regionu jest dystrybuowany równomiernie między wszystkimi dostępnymi punktami końcowymi w tym regionie.

![Dystrybucja ruchu w regionie "wydajność" (zachowanie domyślne)][7]

Zamiast dodawania wielu punktów końcowych w Europie Zachodniej, te punkty końcowe są ujęte w oddzielny profil podrzędny. Profil podrzędny jest dodawany do elementu nadrzędnego jako jedyny punkt końcowy w regionie Europa Zachodnia. Ustawienia w profilu podrzędnym mogą kontrolować dystrybucję ruchu przy użyciu Europa Zachodnia, włączając w tym regionie Routing ruchu oparty na priorytetach lub ważony.

![Routing ruchu "Performance" z niestandardowym rozkładem ruchu w regionie][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Przykład 5: ustawienia monitorowania dla punktów końcowych

Załóżmy, że używasz Traffic Manager, aby bezproblemowo migrować ruch ze starszej lokalnej witryny sieci Web do nowej, opartej na chmurze wersji hostowanej na platformie Azure. W przypadku starszej witryny należy użyć identyfikatora URI strony głównej do monitorowania kondycji lokacji. Jednak w przypadku nowej wersji opartej na chmurze jest wdrażana niestandardowa strona monitorowania (ścieżka "/monitor.aspx"), która zawiera dodatkowe testy.

![Traffic Manager monitorowania punktów końcowych (zachowanie domyślne)][9]

Ustawienia monitorowania w profilu Traffic Manager mają zastosowanie do wszystkich punktów końcowych w ramach pojedynczego profilu. W przypadku profilów zagnieżdżonych można użyć innego profilu podrzędnego dla każdej lokacji, aby zdefiniować różne ustawienia monitorowania.

![Traffic Manager monitorowania punktów końcowych za pomocą ustawień dla punktów końcowych][10]

## <a name="faqs"></a>Często zadawane pytania

* [Jak mogę skonfigurować profile zagnieżdżone?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Ile warstw zagnieżdżenia jest obsługiwana przez program Traffic Manager?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Czy można mieszać inne typy punktów końcowych z zagnieżdżonymi profilami podrzędnymi w tym samym profilu Traffic Manager?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Jak ma zastosowanie model rozliczeń dla zagnieżdżonych profilów?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Czy istnieje wpływ na wydajność profilów zagnieżdżonych?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Jak Traffic Manager obliczać kondycję zagnieżdżonego punktu końcowego w profilu nadrzędnym?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [profilach Traffic Manager](traffic-manager-overview.md)

Dowiedz się, jak [utworzyć profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png