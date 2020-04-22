---
title: Co nowego w usłudze Azure Migrate
description: Dowiedz się więcej o nowościach i najnowszych aktualizacjach w usłudze Migracji platformy Azure.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677241"
---
# <a name="whats-new-in-azure-migrate"></a>Co nowego w usłudze Azure Migrate

[Usługa Azure Migrate](migrate-services-overview.md) ułatwia odnajdywanie, ocenę i migrację lokalnych serwerów, aplikacji i danych do chmury platformy Microsoft Azure. W tym artykule podsumowano nowe wersje i funkcje w usłudze Azure Migrate.


## <a name="update-april-2020"></a>Aktualizacja (kwiecień 2020 r.)

Usługa Azure Migrate obsługuje wdrożenia w usłudze Azure Dla Instytucji. 

- Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne można wykrywać i oceniać.
- Maszyny wirtualne vmware, maszyny wirtualne funkcji Hyper V i serwery fizyczne można migrować na platformę Azure.
- W przypadku migracji VMware można użyć migracji bez agenta lub opartej na agentach. [Dowiedz się więcej](server-migrate-overview.md).
- [Przejrzyj](migrate-support-matrix.md#supported-geographies-azure-government) obsługiwane regiony geograficzne i regiony dla platformy Azure dla instytucji rządowych.
- [Analiza zależności oparta na agentach](concepts-dependency-visualization.md#agent-based-analysis) nie jest obsługiwana w usłudze Azure Government.
- Funkcje w wersji zapoznawczej są obsługiwane w usłudze Azure Government, w szczególności [w bezagentowej analizie zależności](concepts-dependency-visualization.md#agentless-analysis)i [odnajdowaniu aplikacji.](how-to-discover-applications.md)

## <a name="update-march-2020"></a>Aktualizacja (marzec 2020 r.)

Instalacja oparta na skryptach jest teraz dostępna do skonfigurowania [urządzenia migracji platformy Azure:](migrate-appliance.md)

- Instalacja oparta na skryptach jest alternatywą dla pliku . instalacji urządzenia OVA (VMware)/VHD (Hyper-V).
- Udostępnia skrypt instalatora programu PowerShell, który może służyć do konfigurowania urządzenia dla VMware/Hyper-V na istniejącym komputerze z systemem Windows Server 2016.

## <a name="update-november-2019"></a>Aktualizacja (listopad 2019)

Do usługi Azure Migrate dodano szereg nowych funkcji:

- **Ocena serwera fizycznego**. Ocena lokalnych serwerów fizycznych jest teraz obsługiwana, oprócz migracji serwera fizycznego, która jest już obsługiwana.
- **Ocena oparta na imporcie**. Ocena maszyn przy użyciu metadanych i danych dotyczących wydajności podanych w pliku CSV jest teraz obsługiwana.
- **Odnajdowanie aplikacji:** Usługa Azure Migrate obsługuje teraz odnajdowanie aplikacji, ról i funkcji na poziomie aplikacji przy użyciu urządzenia migracji platformy Azure. Jest to obecnie obsługiwane tylko dla maszyn wirtualnych VMware i jest ograniczone tylko do odnajdowania (ocena nie jest obecnie obsługiwana). [Dowiedz się więcej](how-to-discover-applications.md)
- **Wizualizacja zależności bez agenta:** nie trzeba już jawnie instalować agentów do wizualizacji zależności. Obsługiwane są teraz zarówno bez agenta, jak i oparte na agentach.
- **Pulpit wirtualny:** Użyj narzędzi isv do oceny i migracji lokalnej infrastruktury pulpitu wirtualnego (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
- **Aplikacja sieci Web:** Asystent migracji usługi Azure App Service, używany do oceny i migracji aplikacji sieci web, jest teraz zintegrowany z programem Azure Migrate.

Nowe narzędzia oceny i migracji zostały dodane do usługi Azure Migrate:

- **Rackware**: Oferuje migrację do chmury.
- **Movere**: Ocena oferty.

[Dowiedz się więcej](migrate-services-overview.md) o używaniu narzędzi i ofert internetowych do oceny i migracji w usłudze Azure Migrate.

## <a name="azure-migrate-current-version"></a>Migracja do bieżącej wersji usługi Azure

Bieżąca wersja usługi Azure Migrate (wydana w lipcu 2019 r.) zawiera szereg nowych funkcji:

- **Ujednolicona platforma migracji:** usługa Azure Migrate zapewnia teraz jeden portal do scentralizowania, zarządzania i śledzenia podróży migracji na platformę Azure dzięki ulepszonemu przepływowi wdrażania i doświadczeniu portalu.
- **Narzędzia do oceny i migracji:** Usługa Azure Migrate udostępnia narzędzia natywne i integruje się z innymi usługami platformy Azure, a także z narzędziami niezależnego dostawcy oprogramowania (ISV). [Dowiedz się więcej](migrate-services-overview.md#isv-integration) o integracji z siecią ISV.
- **Ocena migracji platformy Azure:** za pomocą narzędzia oceny serwera migracji usługi Azure można ocenić maszyny wirtualne i maszyny wirtualne z użyciem technologii Hyper-V do migracji na platformę Azure. Można również ocenić migracji przy użyciu innych usług platformy Azure i narzędzi isv.
- **Migracja za pomocą usługi Azure Migrate**Server za pomocą narzędzia Do migracji usługi Azure można migrować lokalne maszyny wirtualne vmware i maszyny wirtualne funkcji Hyper V na platformę Azure, a także serwery fizyczne, inne serwery zwirtualizowane oraz maszyny wirtualne chmury prywatnej i publicznej. Ponadto można przeprowadzić migrację na platformę Azure przy użyciu narzędzi isv.
- **Urządzenie migracji platformy Azure:** usługa Azure Migrate wdraża lekkie urządzenie do odnajdowania i oceny lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V.
    - To urządzenie jest używane przez usługę Azure Migrate Server Assessment i migrację serwera migracji usługi Azure Migrate Server do migracji bez agenta.
    - Urządzenie stale odnajduje metadane serwera i dane dotyczące wydajności, do celów oceny i migracji.  
- **Migracja maszyn wirtualnych VMware:** Migracja serwera migracji usługi Azure udostępnia kilka metod migracji lokalnych maszyn wirtualnych VMware na platformę Azure.  Migracja bez agenta przy użyciu urządzenia migracji platformy Azure i migracja oparta na agentach, która używa urządzenia replikacji i wdraża agenta na każdej maszynie wirtualnej, którą chcesz przeprowadzić migrację. [Dowiedz się więcej](server-migrate-overview.md)
 - **Ocena i migracja bazy danych:** Z usługi Azure Migrate można ocenić lokalne bazy danych do migracji na platformę Azure przy użyciu Usługi Azure Database Migration Assistant. Bazy danych można migrować przy użyciu usługi migracji bazy danych azure.
- **Migracja aplikacji sieci Web:** Aplikacje sieci Web można oceniać przy użyciu publicznego adresu URL punktu końcowego za pomocą usługi Azure App Service. W przypadku migracji wewnętrznych aplikacji platformy .NET można pobrać i uruchomić Asystenta migracji usługi App Service.
- **Pole danych:** Importowanie dużych ilości danych w trybie offline na platformę Azure przy użyciu usługi Azure Data Box w usłudze Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Migracja usługi Azure do poprzedniej wersji

Jeśli używasz poprzedniej wersji usługi Azure Migrate (obsługiwano tylko ocenę lokalnych maszyn wirtualnych VMware), należy teraz użyć bieżącej wersji. W poprzedniej wersji nie można już tworzyć nowych projektów migracji platformy Azure ani wykonywać nowych odnajdek. Nadal można uzyskać dostęp do istniejących projektów. Aby to zrobić w witrynie Azure portal > **wszystkie usługi,** wyszukaj **usługę Azure Migrate**. W powiadomieniach migracji platformy Azure istnieje łącze umożliwiające dostęp do starych projektów migracji platformy Azure.



## <a name="next-steps"></a>Następne kroki

- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Wypróbuj nasze samouczki do oceny [maszyn wirtualnych VMware](tutorial-assess-vmware.md) i [maszyn wirtualnych z oprogramowaniem Hyper-V.](tutorial-assess-hyper-v.md)
