---
title: Często zadawane pytania dotyczące migracji platformy Azure
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530321"
---
# <a name="azure-migrate-common-questions"></a>Migracja platformy Azure: typowe pytania

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące migracji platformy Azure. Jeśli masz pytania po przeczytaniu tego artykułu, możesz opublikować je na [forum migracji platformy Azure](https://aka.ms/AzureMigrateForum). Możesz również przejrzeć następujące artykuły:

- Pytania dotyczące [urządzenia migracji platformy Azure](common-questions-appliance.md)
- Pytania dotyczące [wizualizacji odnajdowania, oceny i zależności](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Co to jest usługa Azure Migrate?

Usługa Azure Migrate zapewnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych w chmurze prywatnej i publicznej na platformę Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji oraz oferty innych firm z isv. [Dowiedz się więcej](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Co mogę zrobić z programem Azure Migrate?

Użyj usługi Azure Migrate, aby wykrywać, oceniać i migrować infrastrukturę lokalną, aplikacje i dane na platformę Azure. Usługa Azure Migrate obsługuje ocenę i migrację lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych zwirtualizowanych maszyn wirtualnych, baz danych, aplikacji sieci Web i pulpitów wirtualnych. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaka jest różnica między migracją platformy Azure a usługą Azure Site Recovery?

[Usługa Azure Migrate](migrate-services-overview.md) zapewnia scentralizowane centrum oceny i migracji na platformę Azure. 

[Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) to rozwiązanie do odzyskiwania po awarii. 

Narzędzie Migracji platformy Azure: Migracja serwera używa niektórych funkcji odzyskiwania lokacji zaplecza do migracji z funkcją lift-and-shift niektórych komputerów lokalnych.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między programem Azure Migrate: Server Assessment a zestawem narzędzi MAP?

Ocena serwera zapewnia ocenę ułatwiającej gotowość do migracji i ocenę obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi Microsoft Assessment and Planning (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) pomaga w innych zadaniach, w tym planowaniu migracji dla nowszych wersji systemów operacyjnych Windows dla klientów i serwerów oraz śledzeniu użycia oprogramowania. W tych scenariuszach należy nadal używać zestawu narzędzi MAP.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaka jest różnica między oceną serwera a planistą wdrażania odzyskiwania lokacji?

Ocena serwera jest narzędziem planowania migracji. Planer wdrażania odzyskiwania lokacji jest narzędziem do planowania odzyskiwania po awarii.

Wybierz narzędzie na podstawie tego, co chcesz zrobić:

- **Planowanie migracji lokalnej na platformę Azure:** Jeśli planujesz migrację serwerów lokalnych na platformę Azure, użyj oceny serwera do planowania migracji. Server Assessment ocenia obciążenia lokalne i zawiera wskazówki i narzędzia ułatwiające migrację. Po ujmowaniu planu migracji można użyć narzędzi, takich jak migracja usługi Azure Migrate: Server, aby przeprowadzić migrację komputerów na platformę Azure.
- Planowanie odzyskiwania po awarii na **platformie Azure:** Jeśli planujesz skonfigurować odzyskiwanie po awarii z lokalnego na platformę Azure za pomocą usługi Site Recovery, użyj Planera wdrażania odzyskiwania lokacji. Planer wdrażania zapewnia głęboką, specyficzną dla witryny ocenę środowiska lokalnego w celu odzyskiwania po awarii. Zawiera zalecenia dotyczące odzyskiwania po awarii, takie jak replikacja i pracy awaryjnej.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak migracja serwerów działa z odzyskiwaniem witryny?

- Jeśli używasz usługi Azure Migrate: Migracja serwera do wykonywania *bezagentowej* migracji lokalnych maszyn wirtualnych VMware, migracja jest natywna do migracji platformy Azure i usługi Site Recovery nie jest używany.
- Jeśli używasz usługi Azure Migrate: Migracja serwera do przeprowadzania migracji maszyn wirtualnych *opartych na agentach* lub w przypadku migracji maszyn wirtualnych funkcji Hyper-V lub serwerów fizycznych, migracja usługi Azure Migrate: Server migration używa aparatu replikacji usługi Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Które regiony geograficzne są obsługiwane?

Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Zidentyfikuj potrzebne narzędzie, a następnie dodaj je do projektu migracji platformy Azure. 

Aby dodać narzędzie niezależnego niezależnego niezależnego niezależnego narzędzia lub movere:

1. Rozpocznij od uzyskania licencji lub zarejestruj się w celu bezpłatnej wersji próbnej, zgodnie z zasadami narzędzi. Licencjonowanie narzędzi jest zgodne z modelem licencjonowania dostawców usług owych lub narzędzi.
2. W każdym narzędziu istnieje opcja, aby połączyć się z usługi Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z usługą Azure Migrate.

Możesz śledzić podróż migracji z poziomu projektu migracji platformy Azure, na platformie Azure i w innych narzędziach.

## <a name="how-do-i-delete-a-project"></a>Jak usunąć projekt?

Dowiedz się, jak [usunąć projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Następne kroki

Przeczytaj [omówienie migracji platformy Azure](migrate-services-overview.md).
