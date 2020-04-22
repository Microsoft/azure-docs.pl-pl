---
title: Informacje o usłudze Azure Migrate
description: Dowiedz się więcej o usłudze Migracji platformy Azure.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768402"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera szybkie omówienie usługi migracji platformy Azure.

Usługa Azure Migrate udostępnia scentralizowane centrum do oceny i migracji na lokalne serwery, infrastrukturę, aplikacje i dane na platformie Azure.

Usługa Azure Migrate udostępnia następujące funkcje:

- **Ujednolicona platforma migracji:** pojedynczy portal do uruchamiania, uruchamiania i śledzenia migracji na platformę Azure.
- **Zakres narzędzi**: Szereg narzędzi do oceny i migracji. Narzędzia obejmują migrację platformy Azure: ocena serwera i migrację platformy Azure: migracja serwera. Usługa Azure Migrate integruje się z innymi usługami platformy Azure oraz z innymi narzędziami i ofertami niezależnego dostawcy oprogramowania (ISV).
- **Ocena i migracja:** W centrum migracji platformy Azure można ocenić i przeprowadzić migrację:
    - **Serwery:** Oceń serwery lokalne i migruj je na maszyny wirtualne platformy Azure.
    - **Bazy danych:** Oceniaj lokalne bazy danych i migruj je do usługi Azure SQL Database lub do wystąpienia zarządzanego usługi Azure SQL Database.
    - **Aplikacje sieci Web:** Oceniaj lokalne aplikacje sieci Web i migruj je do usługi Azure App Service przy użyciu Asystenta migracji usługi Azure App Service.
    - **Pulpity wirtualne:** Oceń lokalną infrastrukturę pulpitu wirtualnego (VDI) i migruj ją na pulpit wirtualny systemu Windows na platformie Azure.
    - **Dane:** szybko i tanio migruj duże ilości danych na platformę Azure przy użyciu produktów usługi Azure Data Box.

## <a name="integrated-tools"></a>Zintegrowane narzędzia

Centrum migracji platformy Azure zawiera następujące narzędzia:

**Narzędzie** | **Ocena i migracja** | **Szczegóły**
--- | --- | ---
**Migracja platformy Azure: ocena serwera** | Ocena serwerów. | Odnajduj i oceniaj lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V i serwery fizyczne w ramach przygotowań do migracji na platformę Azure.
**Migracja platformy Azure: migracja serwera** | Migrowanie serwerów. | Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V, serwerów fizycznych, innych maszyn zwirtualizowanych i maszyn wirtualnych w chmurze publicznej na platformę Azure.
**Asystent migracji danych** | Oceniaj lokalne bazy danych programu SQL Server pod kątem migracji do bazy danych SQL Azure, wystąpienia zarządzanego bazy danych SQL platformy Azure lub maszyn wirtualnych platformy Azure z uruchomionym programem SQL Server. | Asystent migracji danych pomaga zidentyfikować potencjalne problemy z blokowaniem migracji. Identyfikuje nieobsługiwał funkcje, nowe funkcje, które mogą przynieść korzyści po migracji, oraz właściwą ścieżkę migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrowanie lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi wystąpieniami zarządzanymi programu SQL Server, Azure SQL Database lub Azure SQL Database. | [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview) o usłudze migracji baz danych.
**Movere** | Ocena serwerów. | [Dowiedz się więcej](#movere) o Movere.
**Asystent migracji aplikacji sieci Web** | Oceń lokalne aplikacje sieci Web i migruj je na platformę Azure. |  Asystent migracji usługi Azure App Service służy do oceny lokalnych witryn sieci Web w celu migracji do usługi Azure App Service.<br/><br/> Użyj Asystenta migracji, aby przeprowadzić migrację aplikacji sieci web .NET i PHP na platformę Azure. [Dowiedz się więcej](https://appmigration.microsoft.com/) o Asystencie migracji usługi Azure App Service.
**Azure Data Box** | Migrowanie danych w trybie offline. | Użyj produktów usługi Azure Data Box, aby przenieść duże ilości danych w trybie offline na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Jeśli korzystasz z platformy Azure dla instytucji rządowych, zewnętrzne zintegrowane narzędzia i oferty sieci web nie mogą wysyłać danych do projektów migracji platformy Azure. Narzędzia można używać niezależnie.

## <a name="isv-integration"></a>Integracja z isv

Usługa Azure Migrate integruje się z kilkoma ofertami isv. 

**Isv**    | **Funkcja**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrowanie serwerów.
[Cloudamize](https://www.cloudamize.com/platform) | Ocena serwerów.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Oceniaj i migruj serwery.
[Device42](https://docs.device42.com/) | Ocena serwerów.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocena VDI.
[RackWare (Wyroby s](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrowanie serwerów.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocena serwerów.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocena serwerów i baz danych.

## <a name="azure-migrate-server-assessment-tool"></a>Migracja platformy Azure: narzędzie do oceny serwera

Narzędzie Do migracji platformy Azure: Ocena serwera odnajduje i ocenia lokalne maszyny wirtualne VMware, maszyny wirtualne z hiper vm i serwery fizyczne do migracji na platformę Azure.

Oto, co robi narzędzie:

- **Gotowość platformy Azure:** ocenia, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Rozmiary platformy Azure:** szacuje rozmiar maszyn wirtualnych platformy Azure po migracji.
- **Szacowanie kosztów platformy Azure:** szacowane koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Analiza zależności:** Identyfikuje zależności między serwerami i strategie optymalizacji dotyczące przenoszenia współzależnych serwerów na platformę Azure. Dowiedz się więcej o ocenie serwera z [analizą zależności](concepts-dependency-visualization.md).

Usługa Server Assessment używa lekkiego [urządzenia migracji platformy Azure,](migrate-appliance.md) które można wdrożyć lokalnie.

- Urządzenie działa na maszynie Wirtualnej lub na serwerze fizycznym. Można go łatwo zainstalować za pomocą pobranego szablonu.
- Urządzenie odnajduje maszyny lokalne. To również stale wysyła metadane komputera i dane wydajności do usługi Azure Migrate.
- Odnajdowanie urządzenia jest bez agenta. Nic nie jest zainstalowane na odnalezionych maszynach.
- Po odnajdowaniu urządzeń można zebrać odnalezione maszyny do grup i uruchomić oceny dla każdej grupy.

## <a name="azure-migrate-server-migration-tool"></a>Narzędzie migracji platformy Azure: narzędzie do migracji serwera

Narzędzie Migracji platformy Azure: Migracja serwera ułatwia migrację na platformę Azure:

- Lokalne maszyny wirtualne VMware
- Maszyny wirtualne funkcji Hyper-V
- Serwery fizyczne
- Inne maszyny zwirtualizowane
- Maszyny wirtualne w chmurze publicznej

Można migrować maszyny po ich ocenie lub migrować bez oceny.

W przypadku bezagentowej migracji maszyn wirtualnych vmware i migracji maszyn wirtualnych funkcji Hyper-V migracja serwera używa urządzenia migracji platformy Azure wdrażanego lokalnie. Urządzenie jest również używane podczas konfigurowania oceny serwera. Jest to opisane w poprzedniej sekcji.

## <a name="selecting-assessment-and-migration-tools"></a>Wybór narzędzi oceny i migracji

W centrum migracji platformy Azure wybierz narzędzie, którego chcesz użyć do oceny lub migracji, i dodaj je do projektu migracji platformy Azure. W przypadku dodania narzędzia niezależnego systemu isv lub movere:

- Aby rozpocząć, uzyskaj licencję lub zarejestruj się w celu bezpłatnej wersji próbnej, postępując zgodnie z instrukcjami narzędzia. Każdy użytkownik z systemu isv lub narzędzie określa licencjonowanie narzędzi.
- Każde narzędzie ma opcję łączenia się z usługą Azure Migrate. Postępuj zgodnie z instrukcjami narzędzia, aby się połączyć.
- Śledzenie migracji we wszystkich narzędziach z poziomu projektu migracji platformy Azure.

## <a name="movere"></a>Movere

Movere to platforma oprogramowania jako usługi (SaaS). Zwiększa inteligencję biznesową, dokładnie prezentując całe środowiska IT w ciągu jednego dnia. Organizacje i przedsiębiorstwa rozwijają się, zmieniają i optymalizują cyfrowo. W ten sposób Movere zapewnia im potrzebne zaufanie do zobaczenia i kontrolowania ich środowisk, niezależnie od platformy, aplikacji lub geografii.

Microsoft [nabył](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) Movere i nie jest już sprzedawany jako samodzielna oferta. Movere jest dostępny za pośrednictwem programu Microsoft Solution Assessment i Microsoft Cloud Economics Program. [Dowiedz się więcej](https://www.movere.io) o Movere.

Zachęcamy również do zapoznania się z usługą Azure Migrate, naszą wbudowaną usługą migracji. Usługa Azure Migrate zapewnia centralne centrum, aby uprościć migrację do chmury. Centrum kompleksowo obsługuje obciążenia, takie jak serwery fizyczne i wirtualne, bazy danych i aplikacje. Kompleksowa widoczność umożliwia łatwe śledzenie postępu podczas odnajdywania, oceny i migracji.

Dzięki wbudowanym narzędziom niezależnego systemu Azure i niezależnego systemu web dla partnerów usługa Azure Migrate oferuje szeroką gamę funkcji, w tym:

- Odnajdowanie serwerów wirtualnych i fizycznych.
- Prawa oparte na wydajności.
- Planowanie kosztów.
- Oceny oparte na przywozie.
- Analiza zależności aplikacji bez agenta.

Jeśli szukasz fachowej pomocy na rozpoczęcie pracy, firma Microsoft ma wykwalifikowanych [dostawców usług zarządzanych azure expert,](https://azure.microsoft.com/partners) którzy cię poprowadzą. Zapoznaj się z [witryną sieci Web usługi Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Wersje migracji platformy Azure

Istnieją dwie wersje usługi Azure Migrate.

- **Bieżąca wersja:** Użyj tej wersji do tworzenia projektów migracji platformy Azure, odnajdywania maszyn lokalnych oraz organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) o nowościach w tej wersji.
- **Poprzednia wersja**: Poprzednia wersja usługi Azure Migrate obsługuje tylko ocenę lokalnych maszyn wirtualnych VMware. Jeśli użyto poprzedniej wersji, należy teraz użyć bieżącej wersji. Nie można już tworzyć projektów migracji platformy Azure przy użyciu poprzedniej wersji. I zalecamy, aby nie robić nowych odkryć z nim.

    Aby uzyskać dostęp do istniejących projektów w witrynie Azure portal, wyszukaj i wybierz **pozycję Migracja platformy Azure**. Pulpit nawigacyjny migracji platformy Azure ma powiadomienie i **łącze umożliwiające** dostęp do starych projektów migracji platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki do oceny [maszyn wirtualnych VMware,](tutorial-prepare-vmware.md) [maszyn wirtualnych hyper-V](tutorial-prepare-hyper-v.md)lub [serwerów fizycznych.](tutorial-prepare-physical.md)
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
