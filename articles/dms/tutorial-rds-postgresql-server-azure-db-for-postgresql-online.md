---
title: 'Samouczek: Migrowanie usług RDS PostgreSQL online do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z usług RDS PostgreSQL do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597474"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Samouczek: Migrowanie usług RDS PostgreSQL do usługi Azure DB for PostgreSQL online przy użyciu systemu DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia RDS PostgreSQL do [Azure Database for PostgreSQL](../postgresql/index.yml) , gdy źródłowa baza danych pozostanie w trybie online podczas migracji. Innymi słowy, migracja może zostać osiągnięta przy minimalnym przestoju aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **najmu dysku DVD** z wystąpienia usług RDS PostgreSQL 9,6 do Azure Database for PostgreSQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Przeprowadź migrację przykładowego schematu przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.
> * Przeprowadź migrację uruchomienie produkcyjne.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service. Szyfrujemy dysk, aby zapobiec kradzieży danych podczas procesu migracji.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji w trybie online z wystąpienia lokalnego PostgreSQL do Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj program [PostgreSQL Community Edition](https://www.postgresql.org/download/) w wersji 9.5, 9.6 lub 10. Źródłowy serwer PostgreSQL Server musi być w wersji 9.5.11, 9.6.7, 10 lub nowszej. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).

   Należy również pamiętać, że wersja Azure Database for PostgreSQL docelowej musi być równa lub późniejsza niż wersja PostgreSQL usług pulpitu zdalnego. Na przykład RDS PostgreSQL 9,6 można migrować tylko do Azure Database for PostgreSQL 9,6, 10 lub 11, ale nie do Azure Database for PostgreSQL 9,5.

* Utwórz wystąpienie [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) lub [Azure Database for PostgreSQL-Citus)](../postgresql/quickstart-create-hyperscale-portal.md). Zapoznaj się z tą [sekcją](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) dokumentu, aby uzyskać szczegółowe informacje na temat nawiązywania połączenia z serwerem PostgreSQL za pomocą usługi pgAdmin.
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.
* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera PostgreSQL, który domyślnie jest portem TCP 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Utwórz [regułę zapory](../postgresql/concepts-firewall-rules.md) na poziomie serwera dla serwera Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurowanie usługi AWS RDS PostgreSQL na potrzeby replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami dostarczonymi przez AWS w artykule [Praca z grupami parametrów bazy danych](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Użyj głównej nazwy użytkownika, aby nawiązać połączenie ze źródłem z Azure Database Migration Service. Jeśli używasz konta innego niż konto użytkownika głównego, konto musi mieć rolę rds_superuser i rolę rds_replication. Rola rds_replication przyznaje uprawnienia do zarządzania gniazdami logicznymi i przesyłania strumieniowego danych przy użyciu gniazd logicznych.
3. Utwórz nową grupę parametrów z następującą konfiguracją:

    a. Ustaw parametr rds.logical_replication w grupie parametrów bazy danych na 1.

    b. max_wal_senders = [liczba zadań współbieżnych] — parametr max_wal_senders ustawia liczbę współbieżnych zadań, które można uruchomić, zaleca 10 zadań.

    c. max_replication_slots – = [liczba gniazd], zalecamy ustawienie pięciu gniazd.

4. Skojarz utworzoną grupę parametrów z wystąpieniem usług RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Wyodrębnij schemat ze źródłowej bazy danych i zastosuj go do docelowej bazy danych, aby ukończyć migrację wszystkich obiektów bazy danych, takich jak schematy tabel, indeksy i procedury składowane.

    Najprostszym sposobem migrowania tylko schematu jest użycie pg_dump z opcją-s. Aby uzyskać więcej informacji, zobacz [przykłady](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) w samouczku pg_dump Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład, aby zrzucić plik schematu dla bazy danych **dvdrental** , użyj następującego polecenia:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Utwórz pustą bazę danych w usłudze docelowej, która jest Azure Database for PostgreSQL. Aby nawiązać połączenie i utworzyć bazę danych, zapoznaj się z jednym z następujących artykułów:

    * [Utwórz serwer Azure Database for PostgreSQL przy użyciu Azure Portal](../postgresql/quickstart-create-server-database-portal.md)
    * [Utwórz serwer Azure Database for PostgreSQL-Citus) przy użyciu Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md)

3. Zaimportuj schemat do usługi docelowej, który jest Azure Database for PostgreSQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Na przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skrypt klucza obcego i dodać skrypt klucza obcego w miejscu docelowym (Azure Database for PostgreSQL), uruchom następujący skrypt w PgAdmin lub PSQL:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Uruchom klucz obcy (który jest drugą kolumną) w wyniku zapytania, aby porzucić klucz obcy.

6. Jeśli w danych są Wyzwalacze (INSERT lub Update Trigger), spowoduje to wymuszenie integralności danych w miejscu docelowym przed replikowaniem danych ze źródła. Zalecane jest, aby wyłączyć wyzwalacze we wszystkich tabelach w *miejscu docelowym* podczas migracji, a następnie włączyć Wyzwalacze po zakończeniu migracji.

    Aby wyłączyć Wyzwalacze w docelowej bazie danych:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service.

5. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego wystąpienia PostgreSQL i docelowego wystąpienia Azure Database for PostgreSQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

6. Wybierz warstwę cenową; w przypadku migracji w trybie online należy wybrać warstwę cenową Premium: 4vCores.

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, wybierz wystąpienie, a następnie wybierz pozycję + **Nowy projekt migracji**.
3. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **AWS RDS dla PostgreSQL**, a następnie w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.
4. W sekcji **Wybierz typ działania** wybierz pozycję **migracja danych w trybie online**.

    > [!IMPORTANT]
    > Upewnij się, że wybrano opcję **migracja danych w trybie online**; migracje w trybie offline nie są obsługiwane w tym scenariuszu.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

5. Wybierz pozycję **Zapisz**.

6. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Zanotuj wymagania wstępne wymagane do skonfigurowania migracji online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **Dodaj szczegóły źródła** Określ szczegóły połączenia dla wystąpienia źródła PostgreSQL.

   ![Szczegóły źródła](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz pozycję **Zapisz**, a następnie na ekranie **szczegóły elementu docelowego** Określ szczegóły połączenia dla docelowego serwera Azure Database for PostgreSQL, który jest wstępnie zainicjowany i ma wdrożony schemat **wynajmu dysku DVD** przy użyciu pg_dump.

    ![Szczegóły elementu docelowego](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — uruchomiony](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby przejść do stanu migracji **pełnych operacji ładowania danych** i **przyrostowej synchronizacji danych** .

    **Pełne ładowanie danych** pokazuje stan początkowej migracji ładowania, podczas gdy **Synchronizacja danych przyrostowych** pokazuje stan funkcji przechwytywania zmian danych.

    ![Ekran spisu — pełne ładowanie danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Ekran spisu — synchronizacja danych przyrostowych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczane jako **gotowe do uruchomienie produkcyjne**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Poczekaj, aż licznik **oczekujące zmiany** wyświetli **wartość 0** , aby upewnić się, że wszystkie przychodzące transakcje do źródłowej bazy danych zostaną zatrzymane, zaznacz pole wyboru **Potwierdź** , a następnie wybierz pozycję **Zastosuj**.

    ![Pełny ekran uruchomienie produkcyjne](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Po **zakończeniu** wyświetlania stanu migracji bazy danych Połącz swoje aplikacje z nową docelową bazą danych Azure Database for PostgreSQL.

Migracja online lokalnego wystąpienia usług RDS PostgreSQL do Azure Database for PostgreSQL została już zakończona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje na temat Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](../postgresql/overview.md).
* W przypadku innych pytań Wyślij wiadomość e-mail do aliasu [zadawaj migracje bazy danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
