---
title: Najlepsze rozwiązania dotyczące aplikacji i klastrów na platformie Azure Service Fabric
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania klastrami, aplikacjami i usługami przy użyciu usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261091"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Najlepsze rozwiązania dotyczące aplikacji i klastrów na platformie Azure Service Fabric

Ten artykuł zawiera linki do najlepszych rozwiązań związanych z zarządzaniem klastrami i aplikacjami Service Fabric platformy Azure. Zdecydowanie zalecamy wdrożenie tych zasad w celu zoptymalizowania niezawodności środowiska produkcyjnego. Aby rozpocząć projektowanie rozwiązania produkcyjnego, użyj jednego z [Service Fabric szablonów klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates) lub zaktualizuj istniejący szablon w celu uwzględnienia tych praktyk.

## <a name="security"></a>Zabezpieczenia

* [Najlepsze rozwiązania dotyczące zabezpieczeń](service-fabric-best-practices-security.md)

## <a name="networking"></a>Networking

* [Najlepsze rozwiązania dotyczące sieci](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planowanie i skalowanie obliczeń

* [Najlepsze rozwiązania dotyczące skalowania obliczeń](service-fabric-best-practices-capacity-scaling.md)
* [Planowanie pojemności obliczeniowej](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kod

* [Najlepsze rozwiązania dotyczące implementowania infrastruktury jako kodu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

* [Najlepsze rozwiązania dotyczące monitorowania i diagnostyki klastra](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Projekt aplikacji

* [Najlepsze rozwiązania dotyczące projektowania aplikacji](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista kontrolna

Po zaimplementowaniu praktyk zalecanych w poprzednich sekcjach upewnij się, że wszystkie najlepsze rozwiązania zostały zintegrowane z listą kontrolną gotowości do produkcji:
* [Lista kontrolna gotowości produkcyjnej platformy Azure Service Fabric](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów Service Fabric: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
