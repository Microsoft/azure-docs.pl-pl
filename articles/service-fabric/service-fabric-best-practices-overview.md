---
title: Najlepsze rozwiązania dotyczące aplikacji i klastrów na platformie Azure Service Fabric
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania klastrami, aplikacjami i usługami przy użyciu usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75551781"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Najlepsze rozwiązania dotyczące aplikacji i klastrów na platformie Azure Service Fabric

Ten artykuł zawiera linki do najlepszych rozwiązań związanych z zarządzaniem klastrami i aplikacjami Service Fabric platformy Azure. Zdecydowanie zalecamy wdrożenie tych zasad w celu zoptymalizowania niezawodności środowiska produkcyjnego. Aby rozpocząć projektowanie rozwiązania produkcyjnego, użyj jednego z [Service Fabric szablonów klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates) lub zaktualizuj istniejący szablon w celu uwzględnienia tych praktyk.

## <a name="security"></a>Zabezpieczenia

* [Najlepsze rozwiązania dotyczące zabezpieczeń](service-fabric-best-practices-security.md)

## <a name="networking"></a>Networking

* [Najlepsze rozwiązania dotyczące sieci](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planowanie i skalowanie obliczeń

* [Najlepsze rozwiązania dotyczące skalowania obliczeń](service-fabric-best-practices-capacity-scaling.md)
* [Planowanie pojemności obliczeniowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kod

* [Najlepsze rozwiązania dotyczące implementowania infrastruktury jako kodu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

* [Najlepsze rozwiązania dotyczące monitorowania i diagnostyki klastra](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Projekt aplikacji

* [Najlepsze rozwiązania dotyczące projektowania aplikacji](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista kontrolna

Po zaimplementowaniu praktyk zalecanych w poprzednich sekcjach upewnij się, że wszystkie najlepsze rozwiązania zostały zintegrowane z listą kontrolną gotowości do produkcji:
* [Lista kontrolna gotowości produkcyjnej platformy Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów Service Fabric: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)