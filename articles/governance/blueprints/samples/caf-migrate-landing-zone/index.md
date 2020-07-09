---
title: Omówienie przykładu strategii strefy docelowej migracji w przewodniku CAF
description: Omówienie i architektura strategii Cloud Adoption Framework (CAF) dla strefy docelowej usługi Azure Migration z przykładem.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 45479fd6fa3a49d2639e587d3dba2aaaf567d0b1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044825"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Omówienie przykładowej strategii strefy docelowej w przewodniku Microsoft Cloud Adoption Framework dla migracji na platformę Azure

Strategia strefy docelowej w przewodniku Microsoft Cloud Adoption Framework (CAF) na potrzeby migracji na platformę Azure to zestaw infrastruktury, który ułatwia skonfigurowanie migracji pierwszego obciążenia i zarządzanie zasobami w chmurze zgodnie z przewodnikiem CAF.

Przykład strategii [CAF Foundation](../caf-foundation/index.md) stanowi rozszerzenie tego przykładu.

## <a name="architecture"></a>Architektura

Przykład strategii strefy docelowej migracji w przewodniku CAF służy do wdrażania na platformie Azure podstawowych zasobów infrastruktury, które mogą być używane przez organizacje do przygotowywania subskrypcji do migrowania do nich maszyn wirtualnych. Pomaga on również we wdrożeniu kontroli ładu niezbędnych do zarządzania ich zasobami w chmurze. Ten przykład służy do wdrażania i wymuszania zasobów, zasad i szablonów, dzięki którym organizacja będzie mogła zacząć korzystać z platformy Azure w zaufany sposób.

:::image type="content" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="Strefa docelowa migracji w przewodniku CAF — obraz przedstawia, jakie elementy są instalowane w ramach wytycznych przewodnika CAF w początkowej strefie docelowej" border="false":::

To środowisko składa się z kilku usług platformy Azure, które udostępniają bezpieczny, w pełni monitorowany ład możliwy do zastosowania w przedsiębiorstwach. To środowisko zawiera następujące składniki:

- Wystąpienie usługi [Azure Key Vault](../../../../key-vault/general/overview.md) umożliwiające hostowanie wpisów tajnych używanych na potrzeby certyfikatów, kluczy i wpisów tajnych wdrożonych w środowisku usług udostępnionych
- Usługa [Log Analytics](../../../../azure-monitor/overview.md) jest wdrażana, aby upewnić się, że wszystkie akcje i usługi są rejestrowane w centralnej lokalizacji od chwili uruchomienia migracji
- Wdrożenie usługi [Azure Security Center](../../../../security-center/security-center-intro.md) (wersja standardowa) zapewnia ochronę przed zagrożeniami dla zmigrowanych obciążeń.
- Wdrożenie usługi [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) dostarcza izolowaną sieć i podsieci dla Twojej maszyny wirtualnej.
- Wdrożenie [projektu usługi Azure Migrate](../../../../migrate/migrate-services-overview.md) umożliwia odnajdywanie i ocenianie. Dodajemy narzędzia do oceny serwera, migracji serwera, oceny bazy danych i migracji bazy danych.  


Wszystkie te elementy są zgodne ze sprawdzonymi rozwiązaniami opublikowanymi na stronie [Centrum architektury platformy Azure — architektury referencyjne](/azure/architecture/reference-architectures/).

> [!NOTE]
> Strategia migracji w przewodniki CAF określa strefę docelową dla obciążeń. Nadal konieczne jest przeprowadzenie oceny i migracji maszyn wirtualnych/baz danych na tej podstawowej architekturze.

Aby uzyskać więcej informacji, zobacz [Przewodnik Microsoft Cloud Adoption Framework dla platformy Azure — migracja](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Następne kroki

Zostało przeczytane omówienie i architektura przykładowej strategii strefy docelowej migracji w przewodniku CAF.

> [!div class="nextstepaction"]
> [Strategia strefy docelowej migracji w przewodniku CAF — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
