---
title: Informacje o usłudze Azure Migrate
description: Dowiedz się więcej o usłudze Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870776"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie usługi Azure Migrate.

Azure Migrate udostępnia scentralizowany centrum do oceny i migracji do lokalnych serwerów, infrastruktury, aplikacji i danych platformy Azure. Dostępne są następujące elementy:

- **Ujednolicona platforma migracji**: pojedynczy Portal do uruchamiania, uruchamiania i śledzenia migracji do platformy Azure.
- **Zakres narzędzi**: szereg narzędzi do oceny i migracji. Narzędzia Azure Migrate obejmują Azure Migrate: odnajdywanie i ocenianie oraz Azure Migrate: Migracja serwera. Azure Migrate integruje się także z innymi usługami i narzędziami platformy Azure oraz ofertami niezależnych dostawców oprogramowania (ISV).
- **Ocena i migracja**: w centrum Azure Migrate można ocenić i zmigrować:
    - **Windows, Linux i SQL Server**: ocenianie serwerów lokalnych, w tym SQL Server wystąpień, i migrowanie ich do usługi Azure Virtual Machines lub Azure VMware Solution (wersja zapoznawcza).
    - **Bazy danych**: ocenianie lokalnych baz danych i migrowanie ich do Azure SQL Database lub do wystąpienia zarządzanego SQL.
    - **Aplikacje sieci Web**: ocenianie lokalnych aplikacji sieci Web i migrowanie ich do Azure App Service przy użyciu Asystent migracji Azure App Service.
    - **Pulpity wirtualne**: Oceń lokalną infrastrukturę pulpitu wirtualnego (VDI) i Migruj ją do pulpitu wirtualnego systemu Windows na platformie Azure.
    - **Dane**: szybkie i ekonomiczne Migrowanie dużych ilości danych na platformę Azure przy użyciu produktów Azure Data Box.

## <a name="integrated-tools"></a>Zintegrowane narzędzia

Centrum Azure Migrate obejmuje następujące narzędzia:

**Narzędzie** | **Ocenianie i migrowanie** | **Szczegóły**
--- | --- | ---
**Azure Migrate: odnajdywanie i Ocena** | Odnajdywanie i ocenianie serwerów, w tym SQL | Wykrywaj i oceniaj lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure.
**Azure Migrate: Migracja serwera** | Migrowanie serwerów | Migruj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, inne serwery zwirtualizowane i maszyny wirtualne w chmurze publicznej na platformę Azure.
**Data Migration Assistant** | Oceń SQL Server bazy danych na potrzeby migracji do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. | Data Migration Assistant to autonomiczne narzędzie do oceny programu SQL Severs.It ułatwiające lokalizowanie potencjalnych problemów z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, nowe funkcje, które mogą korzystać z migracji, oraz właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](/sql/dma/dma-overview).
**Azure Database Migration Service** | Migrowanie lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomioną SQL Server, Azure SQL Database lub wystąpieniami zarządzanymi SQL | [Dowiedz się więcej](../dms/dms-overview.md) o Database Migration Service.
**Movere** | Ocenianie serwerów | [Dowiedz się więcej](#movere) o obszarze przenoszenia.
**Asystent migracji aplikacji sieci Web** | Oceniaj lokalne aplikacje sieci Web i Migruj je na platformę Azure. |  Użyj Asystent migracji Azure App Service do oceny lokalnych witryn sieci Web na potrzeby migracji do Azure App Service.<br/><br/> Użyj Asystent migracji do migrowania aplikacji sieci Web platformy .NET i PHP na platformę Azure. [Dowiedz się więcej](https://appmigration.microsoft.com/) na temat Asystent migracji Azure App Service.
**Azure Data Box** | Migrowanie danych w trybie offline | Użyj Azure Data Box produktów, aby przenieść duże ilości danych w trybie offline na platformę Azure. [Dowiedz się więcej](../databox/index.yml).

> [!NOTE]
> Jeśli jesteś w Azure Government, zewnętrzne narzędzia zintegrowane i oferty niezależnych dostawców oprogramowania nie mogą wysyłać danych do Azure Migrate. Narzędzi można używać niezależnie od siebie.

## <a name="isv-integration"></a>Integracja z niezależnego dostawcy oprogramowania

Azure Migrate integruje się z kilkoma ofertami niezależnych dostawców oprogramowania. 

**ISV**    | **Funkcja**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrowanie serwerów.
[Cloudamize](https://www.cloudamize.com/platform) | Ocenianie serwerów.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Ocenianie i Migrowanie serwerów.
[Device42](https://docs.device42.com/) | Ocenianie serwerów.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocena infrastruktury VDI.
[Stojaki](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrowanie serwerów.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocenianie serwerów.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocenianie serwerów i baz danych.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migrowanie serwerów.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: Narzędzie do odnajdywania i oceny

Azure Migrate: narzędzie odnajdywania i oceny umożliwia odnajdywanie i ocenianie lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i serwerów fizycznych na potrzeby migracji na platformę Azure. 

Oto co to jest narzędzie:

- **Gotowość platformy Azure**: ocenia, czy serwery lokalne są gotowe do migracji na platformę Azure.
- **Ustalanie rozmiaru platformy Azure**: szacuje rozmiar maszyn wirtualnych platformy Azure/konfiguracja usługi Azure SQL/liczba węzłów rozwiązań VMware platformy Azure po migracji.
- **Oszacowanie kosztów platformy Azure**: szacuje koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Analiza zależności**: określa zależności między serwerami i strategie optymalizacji do przenoszenia serwerów zależnych na platformę Azure. Dowiedz się więcej o odnajdywaniu i ocenie przy użyciu [analizy zależności](concepts-dependency-visualization.md).

Funkcja odnajdywania i oceny korzysta z uproszczonego [urządzenia Azure Migrate](migrate-appliance.md) , które jest wdrażane lokalnie.

- Urządzenie jest uruchamiane na maszynie wirtualnej lub na serwerze fizycznym. Można ją łatwo zainstalować przy użyciu pobranego szablonu.
- Urządzenie odnajduje serwery lokalne. Przesyła również metadane serwera i dane wydajności do Azure Migrate.
- Odnajdowanie urządzeń jest bez agentów. Nic nie jest zainstalowane na odnalezionych serwerach.
- Po rozpoczęciu odnajdywania urządzenia można zbierać odnalezione serwery w grupach i uruchamiać oceny dla każdej grupy.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Narzędzie do migracji serwera

Azure Migrate: Narzędzie do migracji serwera ułatwia Migrowanie serwerów na platformę Azure:

**Migrate** | **Szczegóły**
--- | ---
Lokalne maszyny wirtualne VMware | Migrowanie maszyn wirtualnych na platformę Azure przy użyciu funkcji bezagentowej lub migracji opartej na agentach.<br/><br/> W przypadku migracji bez wykorzystania agentów Migracja serwera używa tego samego urządzenia Azure Migrate, które może być również używane przez odnajdywanie i ocenianie w celu odnajdywania i oceny maszyn wirtualnych VMware.<br/><br/> W przypadku migracji na podstawie agenta Migracja serwera korzysta z urządzenia replikacji.
Lokalne maszyny wirtualne funkcji Hyper-V | Migrowanie maszyn wirtualnych na platformę Azure.<br/><br/> Migracja serwera używa agentów dostawcy zainstalowanych na hoście funkcji Hyper-V na potrzeby migracji.
Lokalne serwery fizyczne lub serwery hostowane w innych chmurach | Serwery fizyczne można migrować do platformy Azure. Można również migrować inne serwery zwirtualizowane i maszyny wirtualne z innych chmur publicznych, traktując je jako serwery fizyczne na potrzeby migracji. Migracja serwera używa urządzenia replikacji do migracji.


## <a name="selecting-assessment-and-migration-tools"></a>Wybieranie narzędzi do oceny i migracji

W centrum Azure Migrate wybierz narzędzie do oceny lub migracji i Dodaj je do projektu. W przypadku dodania narzędzia niezależnego dostawcy oprogramowania lub przenoszenia:

- Aby rozpocząć, Uzyskaj licencję lub zarejestruj się w celu uzyskania bezpłatnej wersji próbnej, postępując zgodnie z instrukcjami dotyczącymi narzędzi. Każdy dostawca oprogramowania lub narzędzia określa Licencjonowanie narzędzi.
- Każde narzędzie ma opcję nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami narzędzia, aby nawiązać połączenie.
- Śledź migrację przez wszystkie narzędzia z projektu.

## <a name="movere"></a>Movere

Program do przenoszenia jest platformą oprogramowania jako usługi (SaaS). Zwiększa to analizę biznesową przez dokładne prezentowanie całego środowiska IT w jednym dniu. Organizacje i przedsiębiorstwa rosną, zmieniają i optymalizują cyfrowo. W takim przypadku środowisko przenoszenia zapewnia im niezbędny poziom zaufania, aby zobaczyć i kontrolować swoje środowiska, niezależnie od platformy, aplikacji lub lokalizacji geograficznej.

Firma Microsoft [pozyska](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) produkt i nie jest już sprzedawana jako oferta samodzielna. Program do przenoszenia jest dostępny w ramach oceny rozwiązań firmy Microsoft i programu Microsoft Cloud ekonomii. [Dowiedz się więcej](https://www.movere.io) o obszarze przenoszenia.

Zachęcamy również do Azure Migrate, naszej wbudowanej usługi migracji. Azure Migrate udostępnia centralne centrum upraszczające migrację w chmurze. Centrum zapewnia kompleksową obsługę obciążeń, takich jak serwery fizyczne i wirtualne, bazy danych i aplikacje. Kompleksowa widoczność pozwala łatwo śledzić postęp w zakresie odnajdywania, oceny i migracji.

Korzystając z wbudowanych narzędzi platformy Azure i partnerów niezależnych dostawców oprogramowania, Azure Migrate ma szeroką gamę funkcji, w tym:

- Odnajdywanie serwerów wirtualnych i fizycznych.
- Odpowiedniej zmiany oparte na wydajności.
- Planowanie kosztów.
- Oceny oparte na imporcie.
- Analiza zależności aplikacji bez agentów.

Jeśli szukasz pomocy eksperta, firma Microsoft ma wykwalifikowanych [dostawców usług zarządzanych przez ekspertów platformy Azure](https://azure.microsoft.com/partners) . Zapoznaj się z [witryną sieci web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate.

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów, odnajdywania serwerów lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: poprzednia wersja Azure Migrate, znana także jako klasyczna Azure Migrate, obsługuje tylko ocenę lokalnych maszyn wirtualnych programu VMware. Azure Migratee klasyczne jest wycofywane w lutym 2024. Po lutym 2024 wersja klasyczna Azure Migrate nie będzie już obsługiwana i metadane spisu w projektach klasycznych zostaną usunięte. Nie można uaktualnić projektów lub składników w poprzedniej wersji do nowej wersji. Należy [utworzyć nowy projekt](create-manage-projects.md)i dodać do niego [Narzędzia do oceny i migracji](./create-manage-projects.md) . Skorzystaj z samouczków, aby zrozumieć, jak korzystać z dostępnych narzędzi do oceny i migracji. Jeśli masz obszar roboczy Log Analytics dołączony do projektu klasycznego, możesz dołączyć go do projektu bieżącej wersji po usunięciu projektu klasycznego.

    Aby uzyskać dostęp do istniejących projektów w Azure Portal, Wyszukaj i wybierz pozycję **Azure Migrate**. Pulpit nawigacyjny **Azure Migrate** ma powiadomienie i link umożliwiający dostęp do starych projektów.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](./tutorial-discover-vmware.md), [maszyny wirtualne funkcji Hyper-V](./tutorial-discover-hyper-v.md)lub [serwery fizyczne](./tutorial-discover-physical.md).
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.