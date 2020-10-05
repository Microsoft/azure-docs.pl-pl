---
title: Omówienie siatki Service Fabric platformy Azure
description: Dowiedz się więcej o usłudze Azure Service Fabric Mesh. Za pomocą usługi Service Fabric Mesh możesz wdrożyć i skalować aplikację bez martwienia się o wymagania infrastruktury aplikacji.
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: b7061a178a3f5a9d126afab3fc1f9aa77d5cc050
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87927032"
---
# <a name="what-is-service-fabric-mesh"></a>Co to jest Service Fabric Mesh?

Ten film zawiera krótkie omówienie usługi Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Aplikacje hostowane w usłudze Service Fabric Mesh są uruchamiane i skalowane bez konieczności martwienia się o ich infrastrukturę.  Usługa Service Fabric Mesh składa się z klastrów tysięcy maszyn.  Wszystkie operacje klastrów są ukryte przed deweloperem. Przekaż swój kod i określ potrzebne zasoby, wymagania dotyczące dostępności i limity zasobów.  Usługa Service Fabric Mesh automatycznie alokuje infrastrukturę i obsługuje także błędy infrastruktury, zapewniając wysoką dostępność aplikacji. Musisz zadbać jedynie o kondycję i szybkość reakcji aplikacji, nie martwiąc się o infrastrukturę.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ten artykuł zawiera omówienie najważniejszych korzyści zapewnianych przez usługę Service Fabric Mesh.

## <a name="great-developer-experience"></a>Wspaniałe środowisko programowania

Usługa Service Fabric Mesh obsługuje dowolny język lub strukturę programowania, które można uruchomić w kontenerze. Obsługa narzędzi Visual Studio 2019 i Visual Studio Code zapewnia zaawansowane środowisko edycji i debugowania dla aplikacji .NET i .NET Core. 

Usługa Service Fabric Mesh umożliwia:

- Migrowanie aplikacji metodą „lift-and-shift” do kontenerów w celu modernizacji i uruchamiania istniejących aplikacji na dużą skalę.
- Tworzenie i wdrażanie nowych aplikacji mikrousług na dużą skalę na platformie Azure.  Integrację z innymi usługami platformy Azure lub istniejącymi aplikacjami działającymi w kontenerach. Każda mikrousługa jest częścią bezpiecznej, izolowanej do sieci aplikacji. Mikrousługa ma zasady ładu zasobów zdefiniowane dla rdzeni procesora, pamięci, miejsca na dysku i nie tylko.
- Integrację z istniejącymi aplikacjami i rozszerzenie ich bez wprowadzania zmian w tych aplikacjach. Połączenie istniejącej aplikacji z nową aplikacją za pomocą własnej sieci wirtualnej.  
- Modernizowanie istniejących aplikacji usług Cloud Services przez migrowanie ich do usługi Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Prosty operacyjny cykl życia

Łatwo Zarządzaj uruchomionymi aplikacjami, Monitoruj aplikacje i Debuguj w środowiskach produkcyjnych. To zarządzanie obejmuje uaktualnienia i przechowywanie wersji aplikacji. Te aplikacje mogą składać się z jednej mikrousługi lub wielu mikrousług izolowanych we własnej sieci. Aplikacje działają efektywnie dzięki szybkiemu wdrażaniu, umieszczaniu i przełączaniu w tryb failover.

Usługa Service Fabric Mesh umożliwia:

- Wdrażanie aplikacji i zarządzanie nimi bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią.  Usługa Service Fabric Mesh aprowizuje, uaktualnia, stosuje poprawki i utrzymuje podstawową infrastrukturę za Ciebie.
- Skonfigurowanie ciągłej integracji przy użyciu zintegrowanych narzędzi w celu łatwego pakowania i wdrażania aplikacji.
- Skorzystaj ze wszystkich funkcji zasobów Azure Resource Manager. Przykładami tych funkcji są: dziennik inspekcji i [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/overview.md). Wszystkie zasoby wdrażane w usłudze siatka Service Fabric na platformie Azure są zasobami Azure Resource Manager. Te zasoby obejmują aplikacje, usługi, wpisy tajne i tak dalej.
- Wdrażanie zasobów i zarządzanie nimi za pomocą [witryny Azure Portal](https://portal.azure.com), szablonów usługi Resource Manager lub bibliotek interfejsu wiersza polecenia platformy Azure/programu PowerShell.
- Konfigurowanie operacyjnego monitorowania i zgłaszania alertów za pomocą usługi [Application Insights](/azure/application-insights/) (lub wybranego narzędzia) w celu przechwytywania śladów operacyjnych i diagnostycznych z platformy.
- Uzyskiwanie dostępu do informacji diagnostycznych dotyczących aplikacji emitowanych przez model aplikacji przy użyciu [usługi Application Insights](/azure/application-insights/) lub wybranego narzędzia.
- Optymalizowanie użycia zasobów przez określenie reguł skalowania automatycznego dla usług w definicji aplikacji.

## <a name="mission-critical-platform-capabilities"></a>Możliwości platformy o kluczowym znaczeniu

Usługa Service Fabric Mesh tworzy kolekcję klastrów, które obejmują [strefy dostępności platformy Azure](../availability-zones/az-overview.md) i/lub geopolityczne granice regionalne. Service Fabric siatka zawiera opis aplikacji z zestawem założeń, takich jak skalowanie, wymagania sprzętowe, wymagania dotyczące trwałości i zasady zabezpieczeń.  Po wdrożeniu aplikacji usługa Service Fabric Mesh znajduje optymalne miejsce do jej uruchomienia.

Usługa Service Fabric Mesh umożliwia:

- Korzystanie z wysokiej dostępności, skalowania na zewnątrz/do wewnątrz, możliwości odnajdywania, orkiestracji, routingu komunikatów, niezawodnej obsługi komunikatów, uaktualnień bez przestojów, zarządzania zabezpieczeniami/wpisami tajnymi, odzyskiwania po awarii, zarządzania stanem, zarządzania konfiguracją i transakcji rozproszonych.
- Wybieranie spośród wielu modeli aplikacji podczas tworzenia aplikacji.
- Używanie możliwości platformy udostępnianych przez punkty końcowe REST poprzez korzystanie z powiązań specyficznych dla języka wygenerowanych za pomocą struktury Swagger.
- Wdrażanie aplikacji w [strefach dostępności](../availability-zones/az-overview.md) i w wielu regionach na potrzeby niezawodności replikacji geograficznej.
- Korzystanie ze wszystkich funkcji zabezpieczeń i zgodności udostępnianych przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

Wystarczy parę kroków, aby wdrożyć przykładowy projekt za pomocą programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Tworzenie witryny internetowej platformy ASP.NET Core](service-fabric-mesh-quickstart-dotnet-core.md). 

Znajdź odpowiedzi na [często zadawane pytania](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
