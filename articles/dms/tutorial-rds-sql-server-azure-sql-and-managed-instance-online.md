---
title: 'Samouczek: Migrowanie usług RDS SQL Server online do SQL Database'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację w trybie online z usług RDS SQL Server do Azure SQL Database pojedynczej bazy danych lub wystąpienia zarządzanego przy użyciu Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 484598c7543e146618b6d2ab1f12bdf13710946b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091373"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Samouczek: Migrowanie SQL Server RDS do Azure SQL Database lub wystąpienia zarządzanego Azure SQL w trybie online za pomocą usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia RDS SQL Server do [Azure SQL Database](/azure/sql-database/) lub [wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) o minimalnym przestoju. W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** do wystąpienia usługi RDS SQL Server SQL Server 2012 (lub nowszego) do SQL Database lub wystąpienia zarządzanego SQL przy użyciu Azure Database Migration Service.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Utwórz bazę danych w Azure SQL Database lub wystąpieniu zarządzanym SQL. 
> * Migrowanie przykładowego schematu przy użyciu programu Data Migration Assistant.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.
> * Pobieranie raportu migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie online z usług RDS SQL Server do SQL Database lub wystąpienia zarządzanego SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Utworzenie [bazy danych usług pulpitu zdalnego programu SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Utwórz bazę danych w Azure SQL Database w Azure Portal](../azure-sql/database/single-database-create-quickstart.md) lub [Utwórz bazę danych w wystąpieniu zarządzanym SQL](../azure-sql/managed-instance/instance-create-quickstart.md), a następnie Utwórz pustą bazę danych o nazwie **AdventureWorks2012**. 
* Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) w wersji 3.3 lub nowszej.
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager. Jeśli przeprowadzasz migrację do wystąpienia zarządzanego SQL, upewnij się, że utworzono wystąpienie DMS w tej samej sieci wirtualnej używanej przez wystąpienie zarządzane SQL, ale w innej podsieci.  Alternatywnie, jeśli używasz innej sieci wirtualnej dla usługi DMS, musisz utworzyć komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service nie ma łączności z Internetem. 

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Aby uzyskać SQL Database, Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera, aby umożliwić Azure Database Migration Service dostęp do docelowej bazy danych. Podaj zakres podsieci sieci wirtualnej używanej dla Azure Database Migration Service.
* Upewnij się, że poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem usług pulpitu zdalnego programu SQL Server są powiązane z kontem będącym członkiem roli serwera „Processadmin” i członkiem ról bazy danych „db_owner” dla wszystkich baz danych, które mają zostać poddane migracji.
* Upewnij się, że poświadczenia używane do nawiązania połączenia z docelową bazą danych mają uprawnienia do kontroli bazy danych w docelowej wersji programu w SQL Database i członkiem roli sysadmin w przypadku migrowania do bazy danych w wystąpieniu zarządzanym SQL.
* Źródłowe usługi pulpitu zdalnego programu SQL Server muszą być w wersji SQL Server 2012 lub nowszej. Aby określić wersję uruchomionego wystąpienia programu SQL Server, zobacz artykuł [Jak określić wersję, wydanie i poziom aktualizacji programu SQL Server i jego składników](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Włącz przechwytywanie zmian danych w bazie danych usług pulpitu zdalnego programu SQL Server i wszystkich tabelach użytkownika wybranych do migracji.
    > [!NOTE]
    > Aby włączyć przechwytywanie zmian danych w bazie danych usług pulpitu zdalnego programu SQL Server, możesz użyć poniższego skryptu.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Aby włączyć przechwytywanie zmian danych we wszystkich tabelach, możesz użyć poniższego skryptu.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Wyłącz wyzwalacze bazy danych w docelowej bazie danych.
    > [!NOTE]
    > Wyzwalacze bazy danych można znaleźć w docelowej bazie danych przy użyciu następującej kwerendy:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Aby uzyskać więcej informacji, zobacz artykuł [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu
Przeprowadź migrację schematu przy użyciu DMA.

> [!NOTE]
> Przed utworzeniem projektu migracji w usłudze DMA upewnij się, że została już zainicjowana baza danych w programie SQL Database lub wystąpieniu zarządzanym SQL, jak wspomniano w wymaganiach wstępnych. Na potrzeby tego samouczka przyjmuje się, że nazwa bazy danych to **AdventureWorks2012**, ale można podać dowolną nazwę.

Aby migrować schemat **AdventureWorks2012** , wykonaj następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.

    > [!NOTE]
    > Dla typu serwer docelowy wybierz opcję **Azure SQL Database** migracji do obu Azure SQL Database oraz do wystąpienia zarządzanego SQL.

3. W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu DMA powinien wyglądać tak, jak pokazano na poniższej ilustracji:

    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5. W programie DMA określ szczegóły połączenia ze źródłowym wystąpieniem programu SQL Server, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorks2012**.

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Wybierz pozycję **dalej**, w obszarze **Połącz z serwerem docelowym** Określ szczegóły połączenia docelowego dla bazy danych w SQL Database lub wystąpieniu zarządzanym SQL, wybierz pozycję **Połącz**, a następnie wybierz wstępnie zainicjowaną bazę danych **AdventureWorksAzure** .

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Wybierz pozycję **dalej** , aby przejść do ekranu **Wybieranie obiektów** , na którym można określić obiekty schematu w bazie danych **AdventureWorks2012** , które mają zostać wdrożone.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Wybierz pozycję **Wdróż schemat** , aby wdrożyć schemat, a następnie po wdrożeniu schematu Sprawdź, czy element docelowy zawiera jakiekolwiek anomalie.

    ![Wdrażanie schematu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowych i docelowego SQL Database lub wystąpienia zarządzanego SQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

6. Wybierz warstwę cenową dla tej migracji online, ale pamiętaj o wybraniu warstwy cenowej Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **AWS RDS for SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**.

    > [!NOTE]
    > Dla typu serwer docelowy wybierz opcję **Azure SQL Database** migracji do obu SQL Database oraz do wystąpienia zarządzanego SQL.

5. W sekcji **Wybierz typ działania** wybierz pozycję **migracja danych w trybie online**.

    > [!IMPORTANT]
    > Upewnij się, że wybrano opcję **migracja danych w trybie online**; migracje w trybie offline nie są obsługiwane w tym scenariuszu.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    ![Tworzenie i uruchamianie działania usługi Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia programu SQL Server.

    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na protokole TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz pozycję **Zapisz**, a następnie na ekranie **szczegóły celu migracji** Określ szczegóły połączenia dla docelowej bazy danych na platformie Azure.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Wybierz polecenie **Zapisz**, a następnie na ekranie **Wybierz tabele** rozwiń listę tabel i zapoznaj się z listą odpowiednich pól.

    Azure Database Migration Service wybiera wszystkie puste tabele źródłowe, które istnieją w docelowej bazie danych. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele na tym ekranie.

    ![Wybór tabel](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Wybierz pozycję **Zapisz** po ustawieniu następujących **zaawansowanych ustawień migracji online**.

    | Ustawienie | Opis |
    | ------------- | ------------- |
    | **Maksymalna liczba tabel ładowanych równolegle** | Określa liczbę tabel, które usługa DMS uruchamia równolegle podczas migracji. Wartość domyślna to 5, ale można ją ustawić na wartość optymalną, aby zaspokoić specyficzne potrzeby dotyczące migracji, oparte na dowolnej migracji w ramach weryfikacji koncepcji. |
    | **Gdy tabela źródłowa jest obcinana** | Określa, czy usługa DMS obcina tabelę docelową podczas migracji. To ustawienie może być przydatne, jeśli co najmniej jedna tabela zostanie obcięta w ramach procesu migracji. |
    | **Skonfiguruj ustawienia dla danych obiektów wielkich (LOB)** | Określa, czy usługa DMS migruje nieograniczone dane obiektu LOB, czy też ogranicza migrowane dane obiektu LOB do określonego rozmiaru.  W przypadku przekroczenia limitu migracji danych obiektów LOB wszystkie dane obiektów LOB wykraczające poza ten limit są obcinane. Dla migracji środowiska produkcyjnego zaleca się wybrać pozycję **Zezwalaj na nieograniczone rozmiary obiektu LOB**, aby zapobiec utracie danych. Podczas zezwalania na nieograniczony rozmiar obiektu LOB zaznacz pole wyboru **Migruj dane obiektu LOB w jednym bloku, gdy rozmiar obiektu LOB jest mniejszy (w KB) niż określony**, aby zwiększyć wydajność. |

    ![Ustawianie zaawansowanych ustawień migracji online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

    ![Stan działania — inicjowanie](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

2. Kliknij określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    ![Stan działania — w toku](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych zostanie **zakończony**, Połącz swoje aplikacje z nową docelową bazą danych.

    ![Stan działania — ukończono](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Następne kroki

* Informacje o znanych problemach i ograniczeniach podczas przeprowadzania migracji w trybie online na platformę Azure można znaleźć w artykule [znane problemy i rozwiązania dotyczące migracji w trybie online](known-issues-azure-sql-online.md).
* Aby uzyskać informacje na temat Database Migration Service, zobacz artykuł [co to jest Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje na temat SQL Database, zobacz artykuł [co to jest usługa SQL Database?](../azure-sql/database/sql-database-paas-overview.md).
* Aby uzyskać informacje o wystąpieniach zarządzanych przez program SQL, zobacz artykuł [co to jest wystąpienie zarządzane SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).