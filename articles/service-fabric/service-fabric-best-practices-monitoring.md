---
title: Najlepsze rozwiązania dotyczące monitorowania Service Fabric platformy Azure
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania w zakresie monitorowania klastrów i aplikacji przy użyciu usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529254"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Najlepsze rozwiązania dotyczące monitorowania i diagnostyki dla Service Fabric platformy Azure

[Monitorowanie i Diagnostyka](./service-fabric-diagnostics-overview.md) mają kluczowe znaczenie dla opracowywania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Można na przykład śledzić sposób używania aplikacji, działania podejmowane przez platformę Service Fabric, wykorzystanie zasobów z licznikami wydajności oraz ogólną kondycję klastra. Te informacje służą do diagnozowania i rozwiązywania problemów i zapobiegania ich występowaniu w przyszłości.

## <a name="application-monitoring"></a>Monitorowanie aplikacji

Monitorowanie aplikacji śledzi sposób używania funkcji i składników aplikacji. Monitoruj aplikacje, aby upewnić się, że wystąpiły problemy mające wpływ na użytkowników. Monitorowanie aplikacji jest odpowiedzialne za tworzenie aplikacji i jej usług, ponieważ jest ona unikatowa dla logiki biznesowej aplikacji. Zaleca się skonfigurowanie monitorowania aplikacji przy użyciu [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), narzędzia do monitorowania aplikacji platformy Azure.

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

Jednym z celów Service Fabric jest to, że aplikacje są odporne na awarie sprzętu. Ten cel jest realizowany przez funkcję usługi systemowej platformy do wykrywania problemów z infrastrukturą oraz szybkiego obciążenia pracą w trybie failover z innymi węzłami w klastrze. Ale co zrobić, jeśli usługa systemowa ma problemy? Czy w przypadku próby wdrożenia lub przeniesienia obciążenia reguły dotyczące rozmieszczenia usług są naruszane? Service Fabric zapewnia diagnostykę tych i innych problemów, aby upewnić się, że informacje o tym, jak Platforma Service Fabric współpracuje z aplikacjami, usługami, kontenerami i węzłami.

W przypadku klastrów systemu Windows zaleca się skonfigurowanie monitorowania klastra przy użyciu [agenta diagnostyki](./service-fabric-diagnostics-event-aggregation-wad.md) i [dzienników Azure monitor](./service-fabric-diagnostics-oms-setup.md).

W przypadku klastrów systemu Linux dzienniki Azure Monitor są również Zalecanym narzędziem do monitorowania infrastruktury i platformy Azure. Diagnostyka platformy Linux wymaga innej konfiguracji, która została zanotowana w [Service Fabric zdarzenia klastra systemu Linux w dzienniku](./service-fabric-diagnostics-oms-syslog.md)systemowym.

## <a name="infrastructure-monitoring"></a>Monitorowanie infrastruktury

[Dzienniki Azure monitor](./service-fabric-diagnostics-oms-agent.md) są zalecane do monitorowania zdarzeń na poziomie klastra. Po skonfigurowaniu agenta Log Analytics z obszarem roboczym, zgodnie z opisem w poprzednim łączu, będzie można zbierać metryki wydajności, takie jak użycie procesora CPU, liczniki wydajności .NET, takie jak użycie procesora CPU na poziomie procesu, Service Fabric liczniki wydajności, takie jak liczba wyjątków od niezawodnej usługi i metryki kontenerów, takie jak użycie procesora CPU.  Należy napisać dzienniki kontenera do stdout lub stderr, aby były dostępne w dziennikach Azure Monitor.

## <a name="watchdogs"></a>Licznik alarmy

Ogólnie rzecz biorąc, licznik alarm jest oddzielną usługą, która obserwuje kondycję i obciążenie między usługami, wysyła pakiety ping do punktów końcowych i zgłasza nieoczekiwane zdarzenia kondycji w klastrze. Może to pomóc zapobiec błędom, które mogą nie zostać wykryte na podstawie wydajności pojedynczej usługi. Licznik alarmy jest również dobrym miejscem do kodu hosta, który wykonuje czynności zaradcze, które nie wymagają interakcji z użytkownikiem, np. Czyszczenie plików dziennika w magazynie w określonych przedziałach czasowych. Zapoznaj się z przykładową implementacją usługi strażnika w  [Service Fabric zdarzenia klastra systemu Linux w dzienniku](https://github.com/Azure-Samples/service-fabric-watchdog-service)systemowym.

## <a name="next-steps"></a>Następne kroki

* Rozpocznij instrumentację aplikacji: Tworzenie [zdarzeń na poziomie aplikacji i generowanie dzienników](service-fabric-diagnostics-event-generation-app.md).
* Wykonaj kroki, aby skonfigurować Application Insights aplikacji przy użyciu [monitorowania i diagnozowania aplikacji ASP.NET Core w Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się więcej o monitorowaniu platformy i Service Fabric zdarzeń zapewnia następujące informacje: na [poziomie platformy i generowanie dzienników](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurowanie integracji dzienników Azure Monitor przy użyciu Service Fabric: [Konfigurowanie dzienników Azure monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Dowiedz się, jak skonfigurować Azure Monitor dzienników do monitorowania kontenerów: [monitorowanie i Diagnostyka dla kontenerów systemu Windows w usłudze Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zobacz przykładowe problemy diagnostyczne i rozwiązania Service Fabric: [diagnozowanie typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md)
* Poznaj ogólne zalecenia dotyczące monitorowania zasobów platformy Azure: [najlepsze rozwiązania — monitorowanie i Diagnostyka](/azure/architecture/best-practices/monitoring).
