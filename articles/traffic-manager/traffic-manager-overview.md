---
title: Azure Traffic Manager | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Traffic Manager. Sprawdź, czy jest to właściwy wybór dla ruchu użytkowników z równoważeniem obciążenia dla aplikacji.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 09b82eed5ad6a9ad121ca56d197eb9c003d027f5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624913"
---
# <a name="what-is-traffic-manager"></a>Co to jest Traffic Manager?
Azure Traffic Manager to moduł równoważenia obciążenia opartego na systemie DNS. Ta usługa umożliwia dystrybucję ruchu do publicznych aplikacji w regionach platformy Azure. Traffic Manager udostępnia również publiczne punkty końcowe o wysokiej dostępności i szybkiej odpowiedzi.

Traffic Manager używa systemu DNS do kierowania żądań klientów do odpowiedniego punktu końcowego usługi na podstawie metody routingu ruchu. Menedżer ruchu udostępnia również monitorowanie kondycji dla każdego punktu końcowego. Punktem końcowym może być dowolna usługa internetowa hostowana w ramach platformy Azure lub poza nią. Usługa Traffic Manager udostępnia szereg [metod routingu ruchu](traffic-manager-routing-methods.md) oraz [opcji monitorowania punktów końcowych](traffic-manager-monitoring.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli chcesz zakończyć protokół zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) lub przetwarzanie poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Application Gateway](../application-gateway/overview.md). Jeśli chcesz równoważyć obciążenie w poszczególnych regionach, zapoznaj się z tematem dotyczącym usługi [Load Balancer](../load-balancer/load-balancer-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager oferuje następujące funkcje:

## <a name="increase-application-availability"></a>Zwiększanie dostępności aplikacji

Usługa Traffic Manager zapewnia wysoką dostępność aplikacji o krytycznym znaczeniu dzięki możliwości monitorowania punktów końcowych i zapewniania automatycznego trybu failover, gdy punkt końcowy ulegnie awarii.
    
## <a name="improve-application-performance"></a>Zwiększanie wydajności aplikacji

System Azure umożliwia uruchamianie usług w chmurze i witryn sieci Web w centrach danych znajdujących się na całym świecie. Traffic Manager może zwiększyć czas odpowiedzi witryny sieci Web przez kierowanie ruchu do punktu końcowego z najniższym opóźnieniem.

## <a name="service-maintenance-without-downtime"></a>Konserwacja usług bez przestojów

Możesz zaplanować konserwację aplikacji bez przestojów. W czasie konserwacji usługa Traffic Manager może kierować ruch do alternatywnych punktów końcowych.

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