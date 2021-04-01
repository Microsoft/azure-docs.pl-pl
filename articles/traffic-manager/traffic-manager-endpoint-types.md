---
title: Typy punktów końcowych Traffic Manager | Microsoft Docs
description: W tym artykule opisano różne typy punktów końcowych, które mogą być używane z usługą Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051231"
---
# <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

Microsoft Azure Traffic Manager pozwala kontrolować sposób dystrybucji ruchu sieciowego do wdrożeń aplikacji działających w różnych centrach danych. Każde wdrożenie aplikacji należy skonfigurować jako „punkt końcowy” w usłudze Traffic Manager. Gdy usługa Traffic Manager odbierze żądanie DNS, wybiera dostępny punkt końcowy, który ma zostać zwrócony w odpowiedzi DNS. Usługa Traffic Manager opiera się na wyborze stanu bieżącego punktu końcowego i metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [jak działa Traffic Manager](traffic-manager-how-it-works.md).

Istnieją trzy typy punktów końcowych obsługiwanych przez usługę Traffic Manager:

* **Punkty końcowe platformy Azure** są używane na potrzeby usług hostowanych na platformie Azure.
* **Zewnętrzne punkty końcowe** są używane w przypadku adresów IPv4/IPv6, nazw FQDN lub usług hostowanych poza platformą Azure. Te usługi mogą być lokalne lub przy użyciu innego dostawcy hostingu.
* **Zagnieżdżone punkty końcowe** służą do łączenia profili usługi Traffic Manager w celu tworzenia bardziej elastycznych schematów routingu ruchu na potrzeby obsługi większych, bardziej złożonych wdrożeń.

Nie ma żadnego ograniczenia dotyczącego sposobu łączenia punktów końcowych różnych typów w pojedynczym profilu Traffic Manager. Każdy profil może zawierać kilka różnych typów punktów końcowych.

W poniższych sekcjach opisano każdy typ punktu końcowego o większej głębokości.

## <a name="azure-endpoints"></a>Punkty końcowe platformy Azure

Punkty końcowe platformy Azure są używane dla usług opartych na platformie Azure w Traffic Manager. Obsługiwane są następujące typy zasobów platformy Azure:

* Usługi w chmurze PaaS.
* Web Apps
* Miejsca aplikacji sieci Web
* PublicIPAddress zasoby (które mogą być połączone z maszynami wirtualnymi bezpośrednio lub za pośrednictwem Azure Load Balancer). PublicIpAddress musi mieć przypisaną nazwę DNS do użycia w profilu Traffic Manager.

Zasoby PublicIPAddress są zasobami Azure Resource Manager. Nie istnieją one w klasycznym modelu wdrażania i są obsługiwane tylko w Azure Resource Manager środowiska Traffic Manager. Inne typy punktów końcowych są obsługiwane za pośrednictwem zarówno Menedżer zasobów, jak i klasycznego modelu wdrażania.

W przypadku korzystania z punktów końcowych platformy Azure Traffic Manager wykrywa, kiedy aplikacja sieci Web jest zatrzymana i uruchomiona. Ten stan jest uwzględniany w stanie punktu końcowego. Aby uzyskać szczegółowe informacje, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md#endpoint-and-profile-status) . Gdy podstawowa usługa zostanie zatrzymana, Traffic Manager nie sprawdza kondycji punktu końcowego ani nie kieruje ruchu do punktu końcowego. Brak Traffic Manager zdarzeń rozliczeń dla zatrzymanego wystąpienia. Po ponownym uruchomieniu usługi zostanie wznowione rozliczenia, a punkt końcowy będzie uprawniony do odbierania ruchu. To wykrywanie nie ma zastosowania do punktów końcowych PublicIpAddress.

## <a name="external-endpoints"></a>Zewnętrzne punkty końcowe

Zewnętrzne punkty końcowe są używane dla adresów IPv4/IPv6, nazw FQDN lub usług poza platformą Azure. Użycie punktów końcowych adresów IPv4/IPv6 umożliwia usłudze Traffic Manager Sprawdzanie kondycji punktów końcowych bez konieczności używania dla nich nazwy DNS. W efekcie Traffic Manager może odpowiadać na zapytania z rekordami/AAAA podczas zwracania tego punktu końcowego w odpowiedzi. Usługi poza platformą Azure mogą obejmować usługę hostowaną lokalnie lub z innym dostawcą. Zewnętrzne punkty końcowe mogą być używane osobno lub w połączeniu z Punkty końcowe platformy Azure w tym samym profilu Traffic Manager. Wyjątek jest przeznaczony dla punktów końcowych, które są określone jako adresy IPv4 lub IPv6, które mogą być tylko zewnętrznymi punktami końcowymi. Łączenie punktów końcowych platformy Azure z zewnętrznymi punktami końcowymi umożliwia wykonywanie różnych scenariuszy:

* Zapewnianie zwiększonej nadmiarowości dla istniejącej aplikacji lokalnej w modelu trybu failover aktywny-aktywny lub aktywny-pasywny przy użyciu platformy Azure. 
* Kierowanie ruchu do punktów końcowych, do których nie skojarzono nazwy DNS. Zmniejsza również całkowite opóźnienie wyszukiwania DNS przez usunięcie konieczności uruchomienia drugiego zapytania DNS w celu uzyskania adresu IP zwróconej nazwy DNS.
* Ogranicz opóźnienia aplikacji dla użytkowników na całym świecie, rozszerzając istniejącą aplikację lokalną na inne lokalizacje geograficzne na platformie Azure. Aby uzyskać więcej informacji, zobacz [Traffic Manager "wydajność" Routing ruchu](traffic-manager-routing-methods.md#performance).
* Zapewniaj większą pojemność istniejącej aplikacji lokalnej — w sposób ciągły lub jako rozwiązanie "z serii do chmury", aby zaspokoić popyt na żądanie przy użyciu platformy Azure.

W niektórych przypadkach warto używać zewnętrznych punktów końcowych do odwoływania się do usług platformy Azure. Przykłady można znaleźć w [sekcji często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints) . Kontrole kondycji są rozliczane według stawki punktów końcowych platformy Azure, a nie od stawki zewnętrznych punktów końcowych. W przeciwieństwie do punktów końcowych platformy Azure, jeśli zatrzymasz lub usuniesz podstawową usługę, opłaty za sprawdzanie kondycji są kontynuowane. Rozliczenie zostanie zatrzymane po wyłączeniu lub usunięciu punktu końcowego w Traffic Manager.

## <a name="nested-endpoints"></a>Zagnieżdżone punkty końcowe

Zagnieżdżone punkty końcowe łączą wiele profili Traffic Manager, aby utworzyć elastyczne schematy routingu ruchu w celu obsługi potrzeb większych i złożonych wdrożeń. W przypadku zagnieżdżonych punktów końcowych profil "podrzędny" jest dodawany jako punkt końcowy do profilu "Parent". Zarówno profil podrzędny, jak i nadrzędny mogą zawierać inne punkty końcowe dowolnego typu, w tym inne profile zagnieżdżone. 

Aby uzyskać więcej informacji, zobacz [profile Traffic Manager zagnieżdżonych](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps jako punkty końcowe

Podczas konfigurowania Web Apps jako punktów końcowych w Traffic Manager należy zastosować kilka dodatkowych kwestii:

1. Tylko Web Apps w jednostce SKU "standard" lub nowszym kwalifikują się do użycia z Traffic Manager. Próba dodania aplikacji sieci Web o mniejszej jednostce SKU nie powiedzie się. Obniżenie wersji jednostki SKU istniejącej aplikacji sieci Web spowoduje, że Traffic Manager nie będzie już wysyłać ruchu do tej aplikacji sieci Web. Aby uzyskać więcej informacji o obsługiwanych planach, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Gdy punkt końcowy odbiera żądanie HTTP, używa nagłówka "host" w żądaniu, aby określić, która aplikacja sieci Web powinna obsługiwać żądanie. Nagłówek hosta zawiera nazwę DNS służącą do uruchamiania żądania, na przykład "contosoapp.azurewebsites.net". Aby użyć innej nazwy DNS w aplikacji sieci Web, nazwa DNS musi być zarejestrowana jako niestandardowa nazwa domeny dla aplikacji. W przypadku dodawania punktu końcowego aplikacji sieci Web jako punktu końcowego platformy Azure nazwa DNS profilu Traffic Manager jest automatycznie rejestrowana dla aplikacji. Ta rejestracja jest automatycznie usuwana po usunięciu punktu końcowego.
3. Każdy profil Traffic Manager może mieć co najwyżej jeden punkt końcowy aplikacji sieci Web w każdym regionie świadczenia usługi Azure. Aby obejść to ograniczenie, można skonfigurować aplikację sieci Web jako zewnętrzny punkt końcowy. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Włączanie i wyłączanie punktów końcowych

Wyłączenie punktu końcowego w Traffic Manager może być przydatne do tymczasowego usunięcia ruchu z punktu końcowego, który jest w trybie konserwacji lub jest ponownie wdrażany. Po ponownym uruchomieniu punktu końcowego można go ponownie włączyć.

Punkty końcowe można włączać lub wyłączać za pośrednictwem portalu Traffic Manager, programu PowerShell, interfejsu wiersza polecenia lub API REST.

> [!NOTE]
> Wyłączenie punktu końcowego platformy Azure nie ma nic do zrobienia ze stanem wdrożenia na platformie Azure. Usługa platformy Azure (na przykład maszyna wirtualna lub aplikacja sieci Web pozostaje uruchomiona i może odbierać ruch nawet po wyłączeniu w Traffic Manager. Ruch może być skierowany bezpośrednio do wystąpienia usługi, a nie za pośrednictwem nazwy DNS profilu Traffic Manager. Aby uzyskać więcej informacji, zobacz [jak działa Traffic Manager](traffic-manager-how-it-works.md).

Bieżące uprawnienie każdego punktu końcowego do odbierania ruchu zależy od następujących czynników:

* Stan profilu (włączony/wyłączony)
* Stan punktu końcowego (włączony/wyłączony)
* Wyniki kontroli kondycji dla tego punktu końcowego

Aby uzyskać szczegółowe informacje, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Ponieważ Traffic Manager działa na poziomie systemu DNS, nie ma wpływu na istniejące połączenia z żadnym punktem końcowym. Gdy punkt końcowy jest niedostępny, Traffic Manager kieruje nowe połączenia do innego dostępnego punktu końcowego. Jednak host za wyłączonym lub w złej kondycji punkt końcowy może nadal odbierać ruch przez istniejące połączenia do momentu zakończenia tych sesji. Aplikacje powinny ograniczać czas trwania sesji, aby zezwalać na opróżnianie ruchu z istniejących połączeń.

Jeśli wszystkie punkty końcowe w profilu zostaną wyłączone lub jeśli sam profil zostanie wyłączony, Traffic Manager wysyła odpowiedź "NXDOMAIN" do nowego zapytania DNS.

## <a name="faqs"></a>Często zadawane pytania

* [Czy można używać Traffic Manager z punktami końcowymi z wielu subskrypcji?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Czy można użyć Traffic Manager z gniazdami "przemieszczania" usługi w chmurze?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Czy Traffic Manager obsługuje punkty końcowe protokołu IPv6?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Czy można użyć Traffic Manager z więcej niż jedną aplikacją sieci Web w tym samym regionie?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Jak mogę przenieść punkty końcowe platformy Azure profilu Traffic Manager do innej grupy zasobów?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak działa Traffic Manager](traffic-manager-how-it-works.md).
* Informacje na temat Traffic Manager [monitorowania punktów końcowych i automatycznego przełączania do trybu failover](traffic-manager-monitoring.md).
* Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)Traffic Manager.