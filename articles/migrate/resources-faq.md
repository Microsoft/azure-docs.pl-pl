---
title: Azure Migrate często zadawane pytania
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 6396ca4483f5e9782890123a7c39a0cbcdbd30a3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832576"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate. Jeśli masz pytania po przeczytaniu tego artykułu, możesz je ogłosić na [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Możesz również przejrzeć następujące artykuły:

- Pytania dotyczące [urządzenia Azure Migrate](common-questions-appliance.md)
- Pytania dotyczące [odnajdywania, oceny i wizualizacji zależności](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Co to jest usługa Azure Migrate?

Azure Migrate udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych i publicznych do platformy Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji oraz oferty niezależnych dostawców oprogramowania. [Dowiedz się więcej](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Co mogę zrobić z Azure Migrate?

Użyj Azure Migrate, aby odnajdywać, oceniać i migrować lokalną infrastrukturę, aplikacje i dane na platformę Azure. Azure Migrate obsługuje ocenę i migrację lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych zwirtualizowanych maszyn wirtualnych, baz danych, aplikacji sieci Web i pulpitów wirtualnych. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaka jest różnica między Azure Migrate i Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) udostępnia scentralizowane centrum oceny i migracji na platformę Azure. 

- Używanie Azure Migrate zapewnia współdziałanie i przyszłą rozszerzalność przy użyciu narzędzi Azure Migrate, innych usług platformy Azure i narzędzi innych firm.
- Azure Migrate: Narzędzie migracji serwera jest przeznaczone do migracji serwera na platformę Azure. Jest zoptymalizowany pod kątem migracji. Nie musisz uczyć się o pojęciach i scenariuszach, które nie są bezpośrednio związane z migracją. 
- Nie są naliczane opłaty za użycie narzędzi do migracji przez 180 dni od momentu uruchomienia replikacji dla maszyny wirtualnej. Zapewnia to czas na ukończenie migracji. Płacisz tylko za zasoby magazynowe i sieciowe używane podczas replikacji oraz opłaty za obliczenia używane podczas migracji testowej.
- Azure Migrate obsługuje wszystkie scenariusze migracji obsługiwane przez Site Recovery. Ponadto w przypadku maszyn wirtualnych VMware Azure Migrate zapewnia opcję migracji bez wykorzystania agentów.
- Są dostępne priorytety nowych funkcji migracji dla Azure Migrate: tylko narzędzie migracji serwera. Te funkcje nie są przeznaczone do Site Recovery.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) należy używać tylko w przypadku odzyskiwania po awarii.

Azure Migrate: Narzędzie do migracji serwera używa funkcji Site Recovery zaplecza do migracji niektórych maszyn lokalnych.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Mam projekt z poprzednim klasycznym doświadczeniem Azure Migrate. Jak mogę rozpocząć korzystanie z nowej wersji?

Azure Migratee klasyczne jest wycofywane w lutym 2024. Po lutym 2024 wersja klasyczna Azure Migrate nie będzie już obsługiwana i metadane spisu w projekcie klasycznym zostaną usunięte. Nie można uaktualnić projektów lub składników w poprzedniej wersji do nowej wersji. Należy [utworzyć nowy projekt Azure Migrate](create-manage-projects.md)i dodać do niego [Narzędzia do oceny i migracji](./create-manage-projects.md) . Skorzystaj z samouczków, aby zrozumieć, jak korzystać z dostępnych narzędzi do oceny i migracji. Jeśli masz obszar roboczy Log Analytics dołączony do projektu klasycznego, możesz dołączyć go do projektu bieżącej wersji po usunięciu projektu klasycznego.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między Azure Migrate: Ocena serwera i zestaw narzędzi mapy?

Ocena serwera zapewnia ocenę, aby pomóc w zagotowości do migracji oraz ocenę obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi firmy Microsoft do oceny i planowania (map)](https://www.microsoft.com/download/details.aspx?id=7826) ułatwia wykonywanie innych zadań, w tym planowanie migracji nowszych wersji klienta systemu Windows i systemów operacyjnych serwera oraz śledzenie użycia oprogramowania. W tych scenariuszach Kontynuuj korzystanie z narzędzia MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaka jest różnica między oceną serwera a Site Recovery Planista wdrażania?

Ocena serwera to narzędzie do planowania migracji. Planista wdrażania Site Recovery jest narzędziem do planowania odzyskiwania po awarii.

Wybierz narzędzie w oparciu o to, co chcesz zrobić:

- **Planowanie migracji lokalnej na platformę Azure**: Jeśli planujesz migrację serwerów lokalnych do platformy Azure, użyj oceny serwera na potrzeby planowania migracji. Ocena serwera ocenia obciążenia lokalne i udostępnia wskazówki i narzędzia ułatwiające Migrowanie. Po zakończeniu planowania migracji można używać narzędzi takich jak Azure Migrate: Migracja serwera w celu migrowania maszyn na platformę Azure.
- **Planowanie odzyskiwania po awarii na platformie Azure**: Jeśli planujesz skonfigurować odzyskiwanie po awarii na platformie Azure przy użyciu Site Recovery, użyj Site Recovery planista wdrażania. Planista wdrażania zapewnia głębokiej, Site Recoveryowej oceny środowiska lokalnego na potrzeby odzyskiwania po awarii. Zawiera zalecenia związane z odzyskiwaniem po awarii, takie jak replikacja i tryb failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak działa Migracja serwera z Site Recovery?

- Jeśli używasz Azure Migrate: Migracja serwera w celu przeprowadzenia migracji *bez agentów* lokalnych maszyn wirtualnych VMware, migracja jest natywna dla Azure Migrate i Site Recovery nie jest używana.
- W przypadku korzystania z Azure Migrate: Migracja serwera w celu przeprowadzenia migracji maszyn wirtualnych VMware *na podstawie agenta* lub migracji maszyn wirtualnych lub serwerów fizycznych z funkcją Hyper-V, Azure Migrate: Migracja serwera korzysta z aparatu replikacji Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Które lokalizacje geograficzne są obsługiwane?

Przejrzyj obsługiwane lokalizacje geograficzne [chmur publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [chmur dla instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Zidentyfikuj narzędzie, którego potrzebujesz, a następnie Dodaj narzędzie do projektu Azure Migrate. 

Aby dodać narzędzie do niezależnego dostawcy oprogramowania lub przenoszenia:

1. Zacznij od uzyskania licencji lub zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej, zgodnie z zasadami dotyczącymi narzędzi. Licencjonowanie dla narzędzi jest zgodne z modelem niezależnego dostawcy oprogramowania lub narzędzia.
2. W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z Azure Migrate.

Możesz śledzić swoją drogę migracji z poziomu projektu Azure Migrate, na platformie Azure i w innych narzędziach.

## <a name="how-do-i-delete-a-project"></a>Jak mogę usunąć projekt?

Dowiedz się, jak [usunąć projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).