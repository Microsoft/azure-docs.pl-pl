---
title: 'Samouczek: Migrowanie SQL Server w trybie offline do pojedynczej bazy danych SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację z SQL Server do Azure SQL Database w trybie offline przy użyciu Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 23bc476c0d4fd90e19428d52b1468d090ffe2a1b
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820793"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Samouczek: przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia SQL Server do [Azure SQL Database](/azure/sql-database/). W tym samouczku przeprowadzisz migrację bazy danych [Adventureworks2016](/sql/samples/adventureworks-install-configure?tabs=ssms&view=sql-server-ver15#download-backup-files) do lokalnego wystąpienia SQL Server 2016 (lub nowszego) do pojedynczej bazy danych lub bazy danych w puli w Azure SQL Database przy użyciu Azure Database Migration Service.

Poznasz następujące czynności:
> [!div class="checklist"]
>
> - Oceniaj i Oceń lokalną bazę danych pod kątem problemów z blokowaniem za pomocą Data Migration Assistant.
> - Użyj Data Migration Assistant, aby przeprowadzić migrację przykładowego schematu bazy danych. 
> - Zarejestruj dostawcę zasobów usługi Azure datamigration.
> - Utwórz wystąpienie usługi Azure Database Migration Service.
> - Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> - Uruchamianie migracji.
> - Monitoruj migrację.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie offline z SQL Server do bazy danych w programie Azure SQL Database. Aby uzyskać informacje o migracji online, zobacz [Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie online przy użyciu usługi DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Pobierz i zainstaluj [SQL Server 2016 lub nowszy](https://www.microsoft.com/sql-server/sql-server-downloads).
- Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Utwórz bazę danych w Azure SQL Database, którą wykonasz, postępując zgodnie ze szczegółowymi informacjami w artykule [Tworzenie bazy danych w Azure SQL Database przy użyciu Azure Portal](../azure-sql/database/single-database-create-quickstart.md). Do celów tego samouczka nazwa bazy danych usługi Azure SQL Database to **AdventureWorksAzure**, ale możesz podać dowolną inną nazwę.

    > [!NOTE]
    > Jeśli używasz usług SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację bazy danych katalogu dla projektów/pakietów SSIS (SSISDB) z programu SQL Server do usługi Azure SQL Database, docelowa baza danych SSISDB zostanie utworzona i będzie zarządzana automatycznie w Twoim imieniu po aprowizacji usług SSIS w usłudze Azure Data Factory (ADF). Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](./how-to-migrate-ssis-packages.md).
  
- Pobierz i zainstaluj najnowszą wersję [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
    >
    > - Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > - Punkt końcowy magazynu
    > - Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.
    >
    >Jeśli nie masz łączności między lokacjami w sieci lokalnej i na platformie Azure lub jeśli masz ograniczoną przepustowość połączenia lokacja-lokacja, rozważ użycie Azure Database Migration Service w trybie hybrydowym (wersja zapoznawcza). Tryb hybrydowy wykorzystuje lokalny proces roboczy migracji wraz z wystąpieniem Azure Database Migration Service działającego w chmurze. Aby utworzyć wystąpienie Azure Database Migration Service w trybie hybrydowym, zapoznaj się z artykułem [Tworzenie wystąpienia Azure Database Migration Service w trybie hybrydowym przy użyciu Azure Portal](./quickstart-create-data-migration-service-hybrid-portal.md).

- Upewnij się, że reguły zabezpieczeń ruchu wychodzącego w sieci wirtualnej grupy zabezpieczeń nie blokują następujących portów komunikacyjnych wymaganych dla Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do SQL Server źródłowej, która domyślnie jest portem TCP 1433. Jeśli wystąpienie domyślne nasłuchuje na innym porcie, Dodaj je do zapory.
- Jeśli używasz wielu nazwanych wystąpień SQL Server przy użyciu portów dynamicznych, możesz włączyć usługę SQL Browser i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapór, aby Azure Database Migration Service mógł nawiązać połączenie z nazwanym wystąpieniem na serwerze źródłowym.
- W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
- Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) adresów IP na poziomie serwera dla Azure SQL Database, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Upewnij się, że poświadczenia używane do nawiązania połączenia z docelowym wystąpieniem Azure SQL Database mają uprawnienia do [kontroli bazy danych](/sql/t-sql/statements/grant-database-permissions-transact-sql) w docelowych bazach danych.

## <a name="assess-your-on-premises-database"></a>Ocena lokalnej bazy danych

Aby można było migrować dane z wystąpienia SQL Server do pojedynczej bazy danych lub bazy danych w puli w Azure SQL Database, należy ocenić bazę danych SQL Server dla wszelkich problemów z blokowaniem, które mogą uniemożliwić migrację. Korzystając z Data Migration Assistant, wykonaj kroki opisane w artykule [wykonywanie oceny migracji SQL Server](/sql/dma/dma-assesssqlonprem) w celu ukończenia oceny lokalnej bazy danych. W skrócie należy wykonać następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie wybierz typ projektu **Assessment** (Ocena).
2. Określ nazwę projektu. Z listy rozwijanej **Typ oceny** wybierz pozycję **aparat bazy danych**, w polu **tekstowym typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Utwórz** , aby utworzyć projekt.

    Oceniając źródłową bazę danych programu SQL Server podczas migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), możesz wybrać co najmniej jeden z następujących typów raportów oceny:

   - Check database compatibility (Sprawdzanie zgodności bazy danych)
   - Check feature parity (Sprawdzanie równoważności funkcji)

    Oba typy raportów są domyślnie zaznaczone.

3. W programie Data Migration Assistant na ekranie **Options** (Opcje) wybierz polecenie **Next** (Dalej).
4. Na ekranie **Select sources** (Wybierz źródła) w oknie dialogowym **Connect to a server** (Nawiązywanie połączenia z serwerem) podaj parametry połączenia z programem SQL Server, a następnie wybierz polecenie **Connect** (Połącz).
5. W oknie dialogowym **Dodawanie źródeł** wybierz pozycję **Adventureworks2016**, wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Rozpocznij ocenę**.

    > [!NOTE]
    > Jeśli używasz usług SSIS, program DMA nie obsługuje obecnie oceny źródłowej bazy danych SSISDB. Jednak projekty/pakiety SSIS zostaną ocenione/zweryfikowane, ponieważ są ponownie wdrażane do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](./how-to-migrate-ssis-packages.md).

    Po zakończeniu oceny zostaną wyświetlone wyniki podobne do widocznych na poniższej ilustracji:

    ![Ocena migracji danych](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    W przypadku baz danych w Azure SQL Database oceny wskazują problemy z parzystością funkcji i problemy z blokowaniem migracji w ramach wdrożenia do pojedynczej bazy danych lub bazy danych w puli.

    - Kategoria **SQL Server feature parity** (Równoważność funkcji programu SQL Server) zapewnia rozbudowany zestaw zaleceń, alternatywne metody postępowania dostępne na platformie Azure i czynności korygujące, które pomogą Ci zaplanować projekty migracji.
    - Kategoria **problemy ze zgodnością** identyfikuje częściowo obsługiwane lub Nieobsługiwane funkcje, które odzwierciedlają problemy ze zgodnością, które mogą blokować migrowanie SQL Server baz danych do Azure SQL Database. Przedstawiane są również zalecenia, które pomogą Ci rozwiązać te problemy.

6. Zapoznaj się z wynikami oceny dotyczącymi problemów blokujących migrację oraz równoważności funkcji, wybierając odpowiednie opcje.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Jeśli ocena wypadła dobrze i wiesz, że wybrana baza danych nadaje się do migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), użyj programu DMA, aby przeprowadzić migrację schematu do usługi Azure SQL Database.

> [!NOTE]
> Przed utworzeniem projektu migracji w Data Migration Assistant upewnij się, że została już zainicjowana baza danych na platformie Azure, zgodnie z wymaganiami wstępnymi. 

> [!IMPORTANT]
> Jeśli korzystasz z usług SSIS, program DMA nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](./how-to-migrate-ssis-packages.md).

Aby przeprowadzić migrację schematu **Adventureworks2016** do pojedynczej bazy danych lub bazy danych w puli Azure SQL Database, wykonaj następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.
3. W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu Data Migration Assistant powinien wyglądać tak, jak pokazano na poniższej ilustracji:

    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5. W Data Migration Assistant Określ szczegóły połączenia źródłowego dla SQL Server, wybierz pozycję **Połącz**, a następnie wybierz bazę danych **Adventureworks2016** .

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Wybierz pozycję **dalej**, w obszarze **Połącz z serwerem docelowym** Określ szczegóły połączenia docelowego dla Azure SQL Database, wybierz pozycję **Połącz**, a następnie wybierz bazę danych **AdventureWorksAzure** , która została wstępnie zainicjowana w Azure SQL Database.

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Wybierz pozycję **dalej** , aby przejść do ekranu **Wybieranie obiektów** , na którym można określić obiekty schematu w bazie danych **Adventureworks2016** , które mają zostać wdrożone w Azure SQL Database.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Wybierz polecenie **Deploy schema** (Wdróż schemat), aby wdrożyć schemat do usługi Azure SQL Database, a po wdrożeniu schematu sprawdź serwer docelowy pod kątem nieprawidłowości.

    ![Wdrażanie schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się do witryny Azure Portal. Wyszukaj i wybierz pozycję **subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie wybierz pozycję **zarejestruj** dla elementu **Microsoft. datamigration**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**. Wyszukaj i wybierz **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Na ekranie **Tworzenie podstawy usługi migracji** :

     - Wybierz subskrypcję.
     - Utwórz nową grupę zasobów lub wybierz istniejącą.
     - Określ nazwę wystąpienia Azure Database Migration Service.
     - Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service.
     - Wybierz **platformę Azure** jako tryb usługi.
     - Wybierz warstwę cenową. Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Skonfiguruj ustawienia podstawowe dla Azure Database Migration Service wystąpienia](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

     - Wybierz pozycję **Dalej: Sieć**.

4. Na ekranie **Tworzenie sieci usługi migracji** :

    - Wybierz istniejącą sieć wirtualną lub Utwórz nową. Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowej i docelowego wystąpienia Azure SQL Database. Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurowanie ustawień sieciowych wystąpienia Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings3.png)

    - Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W menu Azure Portal wybierz pozycję **wszystkie usługi**. Wyszukaj i wybierz pozycję **usługi Azure Database Migration Services**.

     ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** wybierz utworzone wystąpienie Azure Database Migration Service.

3. Wybierz pozycję **Nowy projekt migracji**.

     ![Znajdź wystąpienie Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Wybieranie źródła** Określ szczegóły połączenia dla wystąpienia SQL Server źródłowej.

    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server. Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na protokole TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

    > [!IMPORTANT]
    > Jeśli korzystasz z usług SSIS, program DMS nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](./how-to-migrate-ssis-packages.md).

   ![Szczegóły źródła](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

3. Wybierz pozycję **Dalej: Wybierz element docelowy**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Wybieranie elementu docelowego** Określ szczegóły połączenia dla Azure SQL Database docelowej, który jest wstępnie zainicjowaną Azure SQL Database, do którego wdrożono schemat **Adventureworks2016** przy użyciu Data Migration Assistant.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)
    
    > [!NOTE]
    > Połączenia prywatnego punktu końcowego z Azure SQL Databaseami docelowymi są obsługiwane przez Azure Database Migration Service z wyjątkiem sytuacji, gdy używana jest niestandardowa nazwa DNS. 

2. Wybierz pozycję **Dalej: ekran Mapuj na docelowe bazy danych** , Mapuj źródłową i docelową bazę danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Wybierz pozycję **Dalej: ustawienia migracji konfiguracji**, rozwiń listę tabeli, a następnie przejrzyj listę odpowiednich pól.

    Azure Database Migration Service wybiera wszystkie puste tabele źródłowe, które istnieją w docelowym wystąpieniu Azure SQL Database. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele w tym bloku.

    ![Wybór tabel](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Wybierz pozycję **Dalej: Podsumowanie**, Przejrzyj konfigurację migracji, a następnie w polu tekstowym **Nazwa działania** Określ nazwę działania migracji.

    ![Wybieranie opcji weryfikacji](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz pozycję **Rozpocznij migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Oczekujący**.

    ![Stan działania](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

    ![Stan działania — ukończono](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Sprawdź docelowe bazy danych na **Azure SQL Database** docelowym.

### <a name="additional-resources"></a>Dodatkowe zasoby

- [Migracja SQL przy użyciu praktycznej usługi migracji danych platformy Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) .
- Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure SQL Database, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure SQL Database](known-issues-azure-sql-online.md).
- Aby uzyskać informacje na temat Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service?](./dms-overview.md).
- Aby uzyskać informacje o usłudze Azure SQL Database, zobacz artykuł [Co to jest usługa Azure SQL Database?](../azure-sql/database/sql-database-paas-overview.md).
