---
title: Azure Traffic Manager | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Traffic Manager. Sprawdź, czy jest ona dobrym rozwiązaniem w przypadku równoważenia obciążenia ruchu użytkownika w aplikacji.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: e2a4db1404709dadb2500df29f3f7acf8787c2b2
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185735"
---
# <a name="what-is-traffic-manager"></a>Co to jest Traffic Manager?
Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi.

Usługa Traffic Manager używa systemu DNS do kierowania żądań klientów do najodpowiedniejszego punktu końcowego usługi w oparciu o metodę routingu ruchu i kondycję punktów końcowych. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Usługa Traffic Manager udostępnia szereg [metod routingu ruchu](traffic-manager-routing-methods.md) oraz [opcji monitorowania punktów końcowych](traffic-manager-monitoring.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli chcesz zakończyć protokół zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) lub przetwarzanie poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Application Gateway](../application-gateway/overview.md). Jeśli chcesz równoważyć obciążenie w poszczególnych regionach, zapoznaj się z tematem dotyczącym usługi [Load Balancer](../load-balancer/load-balancer-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager oferuje następujące funkcje:

## <a name="increase-application-availability"></a>Zwiększanie dostępności aplikacji

Usługa Traffic Manager zapewnia wysoką dostępność aplikacji o krytycznym znaczeniu dzięki możliwości monitorowania punktów końcowych i zapewniania automatycznego trybu failover, gdy punkt końcowy ulegnie awarii.
    
## <a name="improve-application-performance"></a>Zwiększanie wydajności aplikacji

Platforma Azure umożliwia uruchamianie usług w chmurze lub witryn internetowych w centrach danych, które znajdują się w różnych częściach świata. Usługa Traffic Manager skraca czas odpowiedzi aplikacji, kierując ruch do punktu końcowego z najniższym opóźnieniem sieci dla klienta.

## <a name="perform-service-maintenance-without-downtime"></a>Przeprowadzanie konserwacji usługi bez przestojów

Operacje planowanej konserwacji aplikacji można przeprowadzać bez przestojów. W czasie konserwacji usługa Traffic Manager może kierować ruch do alternatywnych punktów końcowych.

## <a name="combine-hybrid-applications"></a>Tworzenie aplikacji hybrydowych

Usługa Traffic Manager obsługuje zewnętrzne punkty końcowe poza platformą Azure, dzięki czemu może być używana we wdrożeniach w chmurze hybrydowej i lokalnych, w tym w scenariuszach „[rozszerzania możliwości chmury](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”, „migracji do chmury” i „pracy w trybie failover w chmurze”.

## <a name="distribute-traffic-for-complex-deployments"></a>Dystrybuowanie ruchu w przypadku wdrożeń złożonych

Dzięki użyciu [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md) można łączyć wiele metod routingu w celu tworzenia zaawansowanych i elastycznych reguł, które pozwalają na skalowanie pod kątem potrzeb większych i bardziej złożonych wdrożeń.

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [cennik usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil usługi Traffic Manager](./quickstart-create-traffic-manager-profile.md).
- Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md).
- Zapoznaj się z [często zadawanymi pytaniami](traffic-manager-FAQs.md) dotyczącymi usługi Traffic Manager.