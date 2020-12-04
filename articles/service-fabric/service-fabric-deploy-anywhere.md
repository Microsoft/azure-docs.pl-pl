---
title: Omówienie klastrów Service Fabric platformy Azure i autonomicznych
description: Klastry Service Fabric można tworzyć na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux. Oznacza to, że można wdrażać i uruchamiać aplikacje Service Fabric w dowolnym środowisku, w którym znajduje się zestaw komputerów z systemem Windows Server lub Linux, które są połączone lokalnie, Microsoft Azure lub z dowolnym dostawcą usług w chmurze.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576132"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porównywanie klastrów platformy Azure i autonomicznych Service Fabric w systemach Windows Server i Linux

Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem klastra. Klastry mogą być skalowane do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, Service Fabric ponownie zrównoważą repliki partycji usługi i wystąpienia w większej liczbie węzłów. Ogólna wydajność aplikacji zwiększa się i rywalizacja o zmniejszenie ilości pamięci. Jeśli węzły w klastrze nie są efektywnie używane, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie zrównoważy repliki partycji i wystąpienia na zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Service Fabric umożliwia tworzenie klastrów Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux. Oznacza to, że można wdrażać i uruchamiać aplikacje Service Fabric w dowolnym środowisku, w którym istnieje zestaw komputerów z systemem Windows Server lub Linux, które są połączone, muszą być lokalne, Microsoft Azure lub z dowolnego dostawcy chmury.

## <a name="benefits-of-clusters-on-azure"></a>Zalety klastrów na platformie Azure

Na platformie Azure zapewniamy integrację z innymi funkcjami i usługami platformy Azure, dzięki czemu operacje i zarządzanie klastrem są łatwiejsze i bardziej niezawodne.

* **Azure Portal:** Azure Portal ułatwia tworzenie klastrów i zarządzanie nimi.
* **Azure Resource Manager:** Użycie Azure Resource Manager umożliwia łatwe zarządzanie wszystkimi zasobami używanymi przez klaster jako jednostką i upraszcza śledzenie kosztów i rozliczeń.
* **Service Fabric klaster jako zasób platformy Azure** Klaster Service Fabric jest zasobem platformy Azure, dzięki czemu można modelować go tak jak inne zasoby na platformie Azure.
* **Integracja z infrastrukturą platformy Azure** Service Fabric koordynuje z podstawową infrastrukturą platformy Azure dla systemu operacyjnego, sieci i innych uaktualnień, aby zwiększyć dostępność i niezawodność aplikacji.  
* **Diagnostyka:** Na platformie Azure zapewniamy integrację z usługą Azure Diagnostics i dziennikami Azure Monitor.
* **Skalowanie automatyczne:** W przypadku klastrów na platformie Azure zapewniamy wbudowaną funkcję skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych. W środowiskach lokalnych i innych w chmurze należy utworzyć własną funkcję automatycznego skalowania lub skalować ją ręcznie przy użyciu interfejsów API, które Service Fabric uwidacznia na potrzeby skalowania klastrów.

## <a name="benefits-of-standalone-clusters"></a>Zalety klastrów autonomicznych

* Możesz wybrać dowolnego dostawcę usług w chmurze, który będzie hostować klaster.
* Aplikacje Service Fabric, po ich zapisaniu, można uruchamiać w wielu środowiskach hostingu z minimalnym brakiem zmian.
* Znajomość tworzenia aplikacji Service Fabric przenoszone z jednego środowiska hostingu do innego.
* Środowisko operacyjne uruchamiania i zarządzania klastrami Service Fabric przenoszone z jednego środowiska do innego.
* Rozległy zasięg klienta jest niepowiązany przez ograniczenia środowiska hostingu.
* Istnieje dodatkowa warstwa niezawodności i ochrony przed powszechnym przestojem, ponieważ można przenieść usługi do innego środowiska wdrażania, jeśli centrum danych lub dostawca chmury ma niedostępność.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z omówieniem [klastrów Service Fabric na platformie Azure](service-fabric-azure-clusters-overview.md)
* Zapoznaj się z omówieniem [Service Fabric klastrów autonomicznych](service-fabric-standalone-clusters-overview.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)