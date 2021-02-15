---
title: Informacje o usłudze Azure Migrate
description: Dowiedz się więcej o usłudze Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: a5fa6b1c75bee562394af42fcea9ebfabeb732f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389539"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie usługi Azure Migrate.

Azure Migrate udostępnia scentralizowany centrum do oceny i migracji do lokalnych serwerów, infrastruktury, aplikacji i danych platformy Azure. Dostępne są następujące elementy:

- **Ujednolicona platforma migracji**: pojedynczy Portal do uruchamiania, uruchamiania i śledzenia migracji do platformy Azure.
- **Zakres narzędzi**: szereg narzędzi do oceny i migracji. Narzędzia Azure Migrate obejmują Azure Migrate: Ocena serwera i Azure Migrate: Migracja serwera. Azure Migrate integruje się także z innymi usługami i narzędziami platformy Azure oraz ofertami niezależnych dostawców oprogramowania (ISV).
- **Ocena i migracja**: w centrum Azure Migrate można ocenić i zmigrować:
    - **Serwery**: ocenianie serwerów lokalnych i migrowanie ich do usługi Azure Virtual Machines lub Azure VMware Solution (wersja zapoznawcza).
    - **Bazy danych**: ocenianie lokalnych baz danych i migrowanie ich do Azure SQL Database lub do wystąpienia zarządzanego SQL.
    - **Aplikacje sieci Web**: ocenianie lokalnych aplikacji sieci Web i migrowanie ich do Azure App Service przy użyciu Asystent migracji Azure App Service.
    - **Pulpity wirtualne**: Oceń lokalną infrastrukturę pulpitu wirtualnego (VDI) i Migruj ją do pulpitu wirtualnego systemu Windows na platformie Azure.
    - **Dane**: szybkie i ekonomiczne Migrowanie dużych ilości danych na platformę Azure przy użyciu produktów Azure Data Box.

## <a name="integrated-tools"></a>Zintegrowane narzędzia

Centrum Azure Migrate obejmuje następujące narzędzia:

**Narzędzie** | **Ocenianie i migrowanie** | **Szczegóły**
--- | --- | ---
**Azure Migrate: Ocena serwera** | Ocenianie serwerów. | Wykrywaj i oceniaj lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure.
**Azure Migrate: Migracja serwera** | Migrowanie serwerów. | Migruj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, inne maszyny wirtualne i maszyny wirtualne w chmurze publicznej na platformę Azure.
**Data Migration Assistant** | Oceń SQL Server bazy danych na potrzeby migracji do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. | Data Migration Assistant ułatwia identyfikowanie potencjalnych problemów z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, nowe funkcje, które mogą korzystać z migracji, oraz właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrowanie lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomioną SQL Server, Azure SQL Database lub wystąpieniami zarządzanymi SQL. | [Dowiedz się więcej](../dms/dms-overview.md) o Database Migration Service.
**Movere** | Ocenianie serwerów. | [Dowiedz się więcej](#movere) o obszarze przenoszenia.
**Asystent migracji aplikacji sieci Web** | Oceniaj lokalne aplikacje sieci Web i Migruj je na platformę Azure. |  Użyj Asystent migracji Azure App Service do oceny lokalnych witryn sieci Web na potrzeby migracji do Azure App Service.<br/><br/> Użyj Asystent migracji do migrowania aplikacji sieci Web platformy .NET i PHP na platformę Azure. [Dowiedz się więcej](https://appmigration.microsoft.com/) na temat Asystent migracji Azure App Service.
**Azure Data Box** | Migrowanie danych w trybie offline. | Użyj Azure Data Box produktów, aby przenieść duże ilości danych w trybie offline na platformę Azure. [Dowiedz się więcej](../databox/index.yml).

> [!NOTE]
> Jeśli jesteś w Azure Government, zewnętrzne narzędzia zintegrowane i oferty niezależnych dostawców oprogramowania nie mogą wysyłać danych do Azure Migrate projektów. Narzędzi można używać niezależnie od siebie.

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

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Narzędzie do oceny serwera

Azure Migrate: Narzędzie do oceny serwera wykrywa i ocenia lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure. 

Oto co to jest narzędzie:

- **Gotowość platformy Azure**: ocenia, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Ustalanie rozmiaru platformy Azure**: szacuje rozmiar maszyn wirtualnych platformy Azure lub liczbę węzłów VMware platformy Azure po migracji.
- **Oszacowanie kosztów platformy Azure**: szacuje koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Analiza zależności**: określa zależności między serwerami i strategie optymalizacji do przenoszenia serwerów zależnych na platformę Azure. Więcej informacji o ocenie serwera z [analizą zależności](concepts-dependency-visualization.md).

Ocena serwera korzysta z uproszczonego [urządzenia Azure Migrate](migrate-appliance.md) , które jest wdrażane lokalnie.

- Urządzenie jest uruchamiane na maszynie wirtualnej lub na serwerze fizycznym. Można ją łatwo zainstalować przy użyciu pobranego szablonu.
- Urządzenie umożliwia odnalezienie maszyn lokalnych. Wysyła również do Azure Migrate dane dotyczące wydajności i metadanych maszyn.
- Odnajdowanie urządzeń jest bez agentów. Nic nie jest zainstalowane na odnalezionych maszynach.
- Po rozpoczęciu odnajdywania urządzenia można zbierać odnalezione maszyny w grupach i uruchamiać oceny dla każdej grupy.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Narzędzie do migracji serwera

Azure Migrate: Narzędzie do migracji serwera ułatwia Migrowanie do platformy Azure:

**Migrate** | **Szczegóły**
--- | ---
Lokalne maszyny wirtualne VMware | Migrowanie maszyn wirtualnych na platformę Azure przy użyciu funkcji bezagentowej lub migracji opartej na agentach.<br/><br/> W przypadku migracji bez wykorzystania agentów Migracja serwera używa tego samego urządzenia Azure Migrate, które może być również używane przez ocenę serwera na potrzeby odnajdywania i oceny maszyn wirtualnych VMware.<br/><br/> W przypadku migracji na podstawie agenta Migracja serwera korzysta z urządzenia replikacji.
Lokalne maszyny wirtualne funkcji Hyper-V | Migrowanie maszyn wirtualnych na platformę Azure.<br/><br/> Migracja serwera używa agentów dostawcy zainstalowanych na hoście funkcji Hyper-V na potrzeby migracji.
Lokalne serwery fizyczne | Maszyny fizyczne można migrować do platformy Azure. Można również migrować inne maszyny wirtualne i maszyny wirtualne z innych chmur publicznych, traktując je jako serwery fizyczne na potrzeby migracji. | Migracja serwera używa urządzenia replikacji do migracji.


## <a name="selecting-assessment-and-migration-tools"></a>Wybieranie narzędzi do oceny i migracji

W centrum Azure Migrate wybierz narzędzie, którego chcesz użyć do oceny lub migracji, i Dodaj je do projektu Azure Migrate. W przypadku dodania narzędzia niezależnego dostawcy oprogramowania lub przenoszenia:

- Aby rozpocząć, Uzyskaj licencję lub zarejestruj się w celu uzyskania bezpłatnej wersji próbnej, postępując zgodnie z instrukcjami dotyczącymi narzędzi. Każdy dostawca oprogramowania lub narzędzia określa Licencjonowanie narzędzi.
- Każde narzędzie ma opcję nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami narzędzia, aby nawiązać połączenie.
- Śledź migrację we wszystkich narzędziach z projektu Azure Migrate.

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

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: poprzednia wersja Azure Migrate, znana także jako klasyczna Azure Migrate, obsługuje tylko ocenę lokalnych maszyn wirtualnych programu VMware. Azure Migratee klasyczne jest wycofywane w lutym 2024. Po lutym 2024 wersja klasyczna Azure Migrate nie będzie już obsługiwana i metadane spisu w projektach klasycznych zostaną usunięte. Nie można uaktualnić projektów lub składników w poprzedniej wersji do nowej wersji. Należy [utworzyć nowy projekt Azure Migrate](create-manage-projects.md)i dodać do niego [Narzędzia do oceny i migracji](./create-manage-projects.md) . Skorzystaj z samouczków, aby zrozumieć, jak korzystać z dostępnych narzędzi do oceny i migracji. Jeśli masz obszar roboczy Log Analytics dołączony do projektu klasycznego, możesz dołączyć go do projektu bieżącej wersji po usunięciu projektu klasycznego.

    Aby uzyskać dostęp do istniejących projektów w Azure Portal, Wyszukaj i wybierz pozycję **Azure Migrate**. Pulpit nawigacyjny **Azure Migrate** ma powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](./tutorial-discover-vmware.md), [maszyny wirtualne funkcji Hyper-V](./tutorial-discover-hyper-v.md)lub [serwery fizyczne](./tutorial-discover-physical.md).
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.