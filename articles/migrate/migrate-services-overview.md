---
title: Informacje o usłudze Azure Migrate
description: Dowiedz się więcej o usłudze Migracji platformy Azure.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aa999b874b6e8b0075ab905a72ff04f03cb39ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537903"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera szybkie omówienie usługi migracji platformy Azure.

Usługa Azure Migrate udostępnia scentralizowane centrum do oceny i migracji lokalnych serwerów, infrastruktury, aplikacji i danych na platformę Azure. Usługa Azure Migrate udostępnia następujące funkcje:

- **Ujednolicona platforma migracji:** pojedynczy portal do uruchamiania, uruchamiania i śledzenia podróży migracji na platformę Azure.
- **Zakres narzędzi**: Szereg narzędzi do oceny i migracji. Narzędzia obejmują migrację platformy Azure: ocena serwera i migrację usługi Azure: migracja serwera. Usługa Azure Migrate integruje się z innymi usługami platformy Azure oraz z innymi narzędziami i ofertami niezależnego dostawcy oprogramowania (ISV).
- **Ocena i migracja:** W centrum migracji platformy Azure można ocenić i przeprowadzić migrację:
    - **Serwery:** Oceniaj i migruj serwery lokalne na maszyny wirtualne platformy Azure.
    - **Bazy danych:** Oceniaj i migruj lokalne bazy danych do usługi Azure SQL DB lub do wystąpienia zarządzanego sql platformy Azure.
    - **Aplikacje sieci Web:** oceniaj i migruj lokalne aplikacje sieci web do usługi Azure App Service przy użyciu Asystenta usługi Azure App Service.
    - **Pulpity wirtualne:** oceń i przenieś lokalną infrastrukturę pulpitu wirtualnego (VDI) na pulpit wirtualny systemu Windows na platformie Azure.
    - **Dane:** szybko i tanio migruj duże ilości danych na platformę Azure przy użyciu produktów usługi Azure Data Box. 


## <a name="integrated-tools"></a>Zintegrowane narzędzia

Centrum migracji platformy Azure zawiera te narzędzia.

**Narzędzie** | **Oceniaj/migruj** | **Szczegóły**
--- | --- | ---
**Migracja platformy Azure:Ocena serwera** | Ocena serwerów. | Odnajduj i oceniaj lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V i serwery fizyczne w ramach przygotowań do migracji na platformę Azure.
**Migracja platformy Azure:migracja serwera** | Migrowanie serwerów. | Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V, serwerów fizycznych, innych maszyn zwirtualizowanych i maszyn wirtualnych w chmurze publicznej na platformę Azure. 
**Asystent migracji bazy danych (DMA)** | Oceniaj lokalne bazy danych programu SQL Server pod kątem migracji do usługi Azure SQL DB, wystąpienia zarządzanego sql platformy Azure lub do maszyn wirtualnych platformy Azure z uruchomionym programem SQL Server. | DMA pomaga zidentyfikować potencjalne problemy z blokowaniem migracji. Identyfikuje nieobsługiwał funkcje, nowe funkcje, które można korzystać z po migracji i pomaga zidentyfikować właściwą ścieżkę migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Usługa migracji bazy danych (DMS)** | Migrowanie lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi wystąpieniami zarządzanymi SQL, Azure SQL DB i Azure SQL. | [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview) o DMS.
**Movere** | Ocena serwerów. | [Dowiedz się więcej](#movere) o Movere.
**Asystent migracji aplikacji sieci Web** | Oceniaj i migruj lokalne aplikacje sieci Web na platformę Azure. |  Asystent migracji usługi Azure App Service służy do oceny lokalnych witryn sieci Web w celu migracji do usługi Azure App Service.<br/><br/> Użyj Asystenta do migracji aplikacji sieci web .NET i PHP na platformę Azure. [Dowiedz się więcej](https://appmigration.microsoft.com/) o Asystencie migracji usługi Azure App Service.
**Azure Data Box** | Migracja danych w trybie offline. | Użyj produktów usługi Azure Data Box, aby przenieść duże ilości danych w tryb offline na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Jeśli korzystasz z platformy Azure dla instytucji rządowych, zewnętrzne zintegrowane narzędzia i oferty sieci web nie mogą wysyłać danych do projektów migracji platformy Azure. Narzędzia można używać niezależnie.

## <a name="isv-integration"></a>Integracja z isv

Usługa Azure Migrate integruje się z wieloma ofertami isv. 

**Isv**    | **Funkcja**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrowanie serwerów
[Cloudamize](https://www.cloudamize.com/platform) | Ocena serwerów
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Ocenianie i migrowanie serwerów
[Urządzenie 42](https://docs.device42.com/) | Ocena serwerów
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Oceń VDI
[RackWare (Wyroby s](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrowanie serwerów
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocena serwerów
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocena serwerów i baz danych


## <a name="azure-migrate-server-assessment-tool"></a>Narzędzie oceny programu Azure Migrate Server

Narzędzie Azure Migrate:Server Assessment odnajduje i ocenia lokalne maszyny wirtualne VMware, maszyny wirtualne hyper i serwery fizyczne do migracji na platformę Azure. Oto, co robi narzędzie:

- **Gotowość platformy Azure:** Ocenia, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Rozmiary platformy Azure:** Szacuje rozmiar maszyn wirtualnych platformy Azure po migracji.
- **Szacowanie kosztów platformy Azure:** Szacowane koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Analiza zależności:** Jeśli używasz oceny serwera z [analizą zależności,](concepts-dependency-visualization.md)można skutecznie zidentyfikować zależności między serwerami i zoptymalizować strategie przenoszenia serwerów współzależnych na platformę Azure.


Usługa Server Assessment używa lekkiego [urządzenia migracji platformy Azure,](migrate-appliance.md) które można wdrożyć lokalnie.

- Urządzenie działa na maszynie Wirtualnej lub na serwerze fizycznym. Można go łatwo zainstalować za pomocą pobranego szablonu.
- Urządzenie odnajduje maszyny lokalne i stale wysyła metadane maszyny i dane o wydajności do usługi Azure Migrate.
- Odnajdowanie urządzenia jest bez agenta. Nic nie jest zainstalowane na odnalezionych maszynach.
- Po odnajdowaniu urządzeń można zebrać odnalezione maszyny do grup i uruchomić oceny dla grupy.

## <a name="azure-migrate-server-migration-tool"></a>Narzędzie migracji serwera usługi Azure Migrate

Narzędzie Migracji platformy Azure:Migracja serwera ułatwia migrację lokalnych maszyn wirtualnych VMware, maszyn wirtualnych hyper-VM, serwerów fizycznych, innych maszyn zwirtualizowanych i maszyn wirtualnych w chmurze publicznej na platformę Azure. Można migrować maszyny po ich ocenie lub migrować bez oceny. 

W przypadku bezagentowej migracji maszyn wirtualnych vmware i migracji maszyn wirtualnych funkcji Hyper-V migracja serwera używa urządzenia migracji platformy Azure wdrażanego lokalnie. Urządzenie jest również używane, jeśli skonfigurowano ocenę serwera i jest opisane w poprzedniej sekcji.


## <a name="selecting-assessmentmigration-tools"></a>Wybór narzędzi oceny/migracji

W centrum migracji platformy Azure wybierz narzędzie, którego chcesz użyć do oceny lub migracji, i dodaj je do projektu migracji platformy Azure. W przypadku dodania narzędzia niezależnego systemu isv lub movere:

- Aby rozpocząć, możesz uzyskać licencję lub zarejestrować się w celu bezpłatnej wersji próbnej, zgodnie z instrukcjami narzędzia. Licencjonowanie narzędzi jest określane przez isv lub narzędzia. 
- W każdym narzędziu istnieje opcja, aby połączyć się z usługi Azure Migrate. Postępuj zgodnie z instrukcjami narzędzia, aby się połączyć.
- Możesz śledzić podróż migracji z poziomu projektu migracji platformy Azure we wszystkich narzędziach.


## <a name="movere"></a>Movere

Movere to platforma SaaS, która zwiększa analitykę biznesową poprzez dokładne zaprezentowanie całych środowisk IT w ciągu jednego dnia. W miarę rozwoju, zmiany i optymalizacji cyfrowej, rozwiązanie zapewnia przedsiębiorstwom pewność, której potrzebują, aby mieć wgląd i kontrolę nad swoimi środowiskami, niezależnie od platformy, aplikacji lub lokalizacji geograficznej. Movere został [przejęty](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) przez Microsoft i nie jest już sprzedawany jako samodzielna oferta.  Movere jest dostępny za pośrednictwem programów Microsoft Solution Assessment and Cloud Economics. [Dowiedz się więcej](https://www.movere.io) o Movere. 

Zachęcamy również do zapoznania się z usługą Azure Migrate, naszą wbudowaną usługą migracji. Usługa Azure Migrate zapewnia centralne centrum, aby uprościć migrację do chmury. Centrum oferuje kompleksową obsługę różnych obciążeń, w tym serwerów fizycznych i wirtualnych, baz danych i aplikacji. Widoczność end-to-end ułatwia śledzenie postępu w trakcie odnajdywania, oceny i migracji. Dzięki wbudowanym narzędziom niezależnego dostępu do sieci Web platformy Azure i partnerów usługa Azure Migrate oferuje również szeroki zakres funkcji, w tym odnajdowanie serwerów wirtualnych i fizycznych, rozmiary praw opartych na wydajności, planowanie kosztów, oceny oparte na imporcie i analizę zależności aplikacji bez agenta. Jeśli szukasz fachowej pomocy na rozpoczęcie pracy, firma Microsoft ma wykwalifikowanego [dostawcę usług zarządzanych azure expert,](https://azure.microsoft.com/partners) który poprowadzi Cię w trakcie podróży. Zapoznaj się z [witryną sieci Web usługi Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Wersje migracji platformy Azure

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja:** Użyj tej wersji do tworzenia projektów migracji platformy Azure, odnajdywania maszyn lokalnych oraz organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) o nowościach w tej wersji.
- **Poprzednia wersja**: Jeśli była używana poprzednia wersja usługi Azure Migrate (obsługiwano tylko ocenę lokalnych maszyn wirtualnych VMware), należy teraz użyć bieżącej wersji. Nie można już tworzyć projektów migracji platformy Azure przy użyciu poprzedniej wersji i zaleca się, aby nie wykonywać nowych odnajdek. Aby uzyskać dostęp do istniejących projektów, w witrynie Azure portal wyszukaj i wybierz **pozycję Migracja platformy Azure**. Na **pulpicie** nawigacyjnym migracji platformy Azure znajduje się powiadomienie i łącze umożliwiające dostęp do starych projektów migracji platformy Azure.



## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki do oceny [maszyn wirtualnych VMware,](tutorial-prepare-vmware.md) [maszyn wirtualnych hyper-V](tutorial-prepare-hyper-v.md)lub [serwerów fizycznych.](tutorial-prepare-physical.md)
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
