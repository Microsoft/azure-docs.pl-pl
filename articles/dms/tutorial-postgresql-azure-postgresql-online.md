---
title: 'Samouczek: Migrowanie PostgreSQL do Azure Database for PostgreSQL online za pośrednictwem interfejsu wiersza polecenia platformy Azure'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z PostgreSQL lokalnego do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service za pośrednictwem interfejsu wiersza polecenia.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: fb303054a3026a305831309413c51c061a68c5d6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608055"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Samouczek: Migrowanie PostgreSQL do usługi Azure DB for PostgreSQL online za pomocą usługi DMS za pośrednictwem interfejsu wiersza polecenia platformy Azure

Za pomocą Azure Database Migration Service można migrować bazy danych z lokalnego wystąpienia PostgreSQL, aby [Azure Database for PostgreSQL](../postgresql/index.yml) z minimalnym przestojem. Innymi słowy, migracja może zostać osiągnięta przy minimalnym przestoju aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **najmu dysku DVD** z lokalnego wystąpienia PostgreSQL 9,6 do Azure Database for PostgreSQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migruj Przykładowy schemat przy użyciu narzędzia pg_dump.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Szyfrujemy dysk, aby zapobiec kradzieży danych podczas procesu migracji.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj program [PostgreSQL Community Edition](https://www.postgresql.org/download/) w wersji 9.5, 9.6 lub 10. Źródłowy serwer PostgreSQL Server musi być w wersji 9.5.11, 9.6.7, 10 lub nowszej. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).

    Należy również pamiętać, że wersja Azure Database for PostgreSQL docelowej musi być równa lub późniejsza niż lokalna wersja PostgreSQL. Na przykład PostgreSQL 9,6 można migrować tylko do Azure Database for PostgreSQL 9,6, 10 lub 11, ale nie do Azure Database for PostgreSQL 9,5.

* [Utwórz wystąpienie w Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) lub [Utwórz serwer Azure Database for PostgreSQL-Citus](../postgresql/quickstart-create-hyperscale-portal.md).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej (sieciowej grupy zabezpieczeń) nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera PostgreSQL, który domyślnie jest portem TCP 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera dla Azure Database for PostgreSQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* Istnieją dwie metody wywoływania interfejsu wiersza polecenia:

  * W prawym górnym rogu Azure Portal wybierz przycisk Cloud Shell:

       ![Przycisk Cloud Shell w witrynie Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Lokalnie zainstaluj i uruchom interfejs wiersza polecenia. Interfejs wiersza polecenia w wersji 2.0 to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure.

       Aby pobrać interfejs wiersza polecenia, postępuj zgodnie z instrukcjami podanymi w artykule [Install Azure CLI 2.0 (Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0)](/cli/azure/install-azure-cli?view=azure-cli-latest). Artykuł zawiera również listę platform, które obsługują interfejs wiersza polecenia w wersji 2.0.

       Aby skonfigurować podsystem Windows dla systemu Linux (WSL), postępuj zgodnie z instrukcjami w [Przewodniku instalacji systemu Windows 10](/windows/wsl/install-win10)

* Włącz replikację logiczną w pliku postgresql.config i ustaw następujące parametry:

  * wal_level = **logical**
  * max_replication_slots = [liczba gniazd], zalecamy ustawienie na **pięć gniazd**
  * max_wal_senders = [liczba współbieżnych zadań] — parametr max_wal_senders ustawia liczbę współbieżnych zadań, które można uruchomić, przy czym zalecane ustawienie to **10 zadań**

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych.

1. Użyj polecenia -s pg_dump, aby utworzyć plik zrzutu schematu dla bazy danych. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład aby wykonać zrzut pliku schematu bazy danych dvdrental:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Aby uzyskać więcej informacji na temat korzystania z narzędzia pg_dump, zobacz przykłady w samouczku narzędzia [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Utwórz pustą bazę danych w swoim środowisku docelowym, czyli w usłudze Azure Database for PostgreSQL.

    Aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych, zobacz artykuł [Tworzenie serwera Azure Database for PostgreSQL w Azure Portal](../postgresql/quickstart-create-server-database-portal.md) lub [Tworzenie serwera Azure Database for PostgreSQL-Citus w Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

3. Zaimportuj schemat do docelowej bazy danych utworzonej przez przywrócenie pliku zrzutu schematu.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Uruchom następujący skrypt w narzędziu PgAdmin lub psql w celu wyodrębnienia skryptu docelowego klucza obcego i dodania skryptu klucza obcego w miejscu docelowym (usłudze Azure Database for PostgreSQL).
  
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

    Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

5. Wyzwalacze w danych (wyzwalacze wstawiania lub aktualizacji) wymuszą integralność danych w miejscu docelowym wcześniej replikowanych danych ze źródła. Zaleca się wyłączenie wyzwalaczy we wszystkich tabelach **w miejscu docelowym** podczas migracji, a następnie ponowne włączenie wyzwalaczy po zakończeniu migracji.

    Aby wyłączyć wyzwalacze w docelowej bazie danych, użyj następującego polecenia:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Jeśli w tabelach istnieją typy danych ENUM, zaleca się tymczasowe zaktualizowanie jej do typu danych "charactering" w tabeli docelowej. Po zakończeniu replikacji danych przywróć typ danych ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Aprowizowanie wystąpienia usługi DMS przy użyciu interfejsu wiersza polecenia

1. Zainstaluj rozszerzenie synchronizacji usługi dms:
   * Uruchom następujące polecenia, aby zalogować się na platformie Azure:
       ```azurecli
       az login
       ```

   * Po wyświetleniu monitu otwórz przeglądarkę internetową i wprowadź kod w celu uwierzytelnienia swojego urządzenia. Wykonaj wyświetlone instrukcje.
   * Dodaj rozszerzenie usługi dms:
       * Aby wyświetlić listę dostępnych rozszerzeń, uruchom następujące polecenie:

           ```azurecli
           az extension list-available –otable
           ```

       * Aby zainstalować rozszerzenie, uruchom następujące polecenie:

           ```azurecli
           az extension add –n dms-preview
           ```

   * Aby sprawdzić, czy rozszerzenie usługi dms zostało poprawnie zainstalowane, uruchom następujące polecenie:

       ```azurecli
       az extension list -otable
       ```
       Powinny zostać wyświetlone następujące dane wyjściowe:

       ```output
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

      > [!IMPORTANT]
      > Upewnij się, że wersja rozszerzenia jest większa niż 0.11.0.

   * W dowolnym momencie wyświetl wszystkie polecenia obsługiwane w usłudze DMS, uruchamiając polecenie:

       ```azurecli
       az dms -h
       ```

   * Jeśli masz wiele subskrypcji platformy Azure, uruchom poniższe polecenie, aby ustawić subskrypcję, której chcesz użyć do aprowizacji wystąpienia usługi DMS.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Rozpocznij aprowizację wystąpienia usługi DMS, uruchamiając następujące polecenie:

   ```azurecli
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Na przykład poniższe polecenie utworzy usługę w:

   * Lokalizacja: Wschodnie stany USA 2
   * Subskrypcja: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Nazwa grupy zasobów: PostgresDemo
   * Nazwa usługi DMS: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   Utworzenie wystąpienia usługi DMS trwa około 10–12 minut.

3. W celu zidentyfikowania adresu IP agenta usługi DMS, aby można go było dodać do pliku pg_hba.conf Postgres, uruchom następujące polecenie:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Przykład:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Powinien zostać uzyskany wynik podobny do następującego adresu: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Dodaj adres IP agenta usługi DMS do pliku Postgres pg_hba.conf.

    * Zwróć uwagę na adres IP usługi DMS po zakończeniu aprowizowania usługi DMS.
    * Dodaj adres IP do pliku pg_hba.conf dla źródła podobny do następującego wpisu:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. Następnie utwórz projekt migracji PostgreSQL, uruchamiając następujące polecenie:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Na przykład następujące polecenie tworzy projekt przy użyciu tych parametrów:

   * Lokalizacja: Zachodnio-środkowe stany USA
   * Nazwa grupy zasobów: PostgresDemo
   * Nazwa usługi DMS: PostgresCLI
   * Nazwa projektu: PGMigration
   * Platforma źródłowa: PostgreSQL
   * Platforma docelowa: AzureDbForPostgreSql

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Utwórz zadanie migracji PostgreSQL, wykonując następujące kroki.

    Ten krok obejmuje wykorzystanie źródłowego adresu IP, identyfikatora użytkownika i hasła, docelowego adresu IP, identyfikatora użytkownika, hasła i typu zadania do ustanowienia połączenia.

   * Aby wyświetlić pełną listę opcji, uruchom polecenie:

       ```azurecli
       az dms project task create -h
       ```

       Zarówno dla połączenia elementu źródłowego, jak i docelowego parametr wejściowy odwołuje się do pliku json, który ma listę obiektów.

       Format obiektu JSON połączenia dla połączeń PostgreSQL.
        
       ```json
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Istnieje również plik JSON z opcją bazy danych, który zawiera listę obiektów JSON. W przypadku PostgreSQL format obiektu JSON opcji bazy danych został pokazany poniżej:

       ```json
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Utwórz plik json przy użyciu Notatnika, skopiuj następujące polecenia i wklej je do pliku, a następnie zapisz plik w C:\DMS\source.json.

        ```json
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Utwórz kolejny plik o nazwie target.json i zapisz go jako C:\DMS\target.json. Dołącz następujące polecenia:

       ```json
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Utwórz plik json opcji bazy danych zawierający spis jako bazę danych do migracji:

       ```json
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Uruchom następujące polecenie, które używa elementu źródłowego, docelowego i plików json opcji bazy danych.

       ```azurecli
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     W tym momencie zadanie migracji zostało pomyślnie przesłane.

7. Aby wyświetlić postęp zadania, uruchom następujące polecenie:

   ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   LUB

    ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Możesz też wysyłać zapytanie dotyczące wartości migrationState z danych wyjściowych rozszerzenia:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

W pliku wyjściowym istnieje kilka parametrów, które wskazują postęp migracji. Na przykład zobacz plik wyjściowy poniżej:

  ```output
    "output": [                                 // Database Level
          {
            "appliedChanges": 0,         // Total incremental sync applied after full load
            "cdcDeleteCounter": 0        // Total delete operation  applied after full load
            "cdcInsertCounter": 0,       // Total insert operation applied after full load
            "cdcUpdateCounter": 0,       // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0,     //Number of tables that contain migration error
            "fullLoadLoadingTables": 0,     //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,      //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,           //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",    //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                        // Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",    //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                     //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,            //Volume in Bytes in full load
            "id": "or|inventory|public|actor",
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                       //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                    //Total sync changes that applied after full load
          },
          {                                            //Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                                      // DMS migration task state
        "state": "Running",    //Migration task state – Running means it is still listening to any changes that might come in
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
  ```

## <a name="cutover-migration-task"></a>Zadanie migracji uruchomienia produkcyjnego

Baza danych jest gotowa do uruchomienia produkcyjnego, gdy pełne ładowanie zostało zakończone. W zależności od zajętości serwera źródłowego nowymi przychodzącymi transakcjami zadanie usługi DMS może nadal stosować zmiany po zakończeniu pełnego ładowania.

Aby upewnić się, że wszystkie dane zostały przechwycone, przeprowadź weryfikację liczby wierszy między źródłową i docelową bazą danych. Na przykład możesz użyć poniższego polecenia:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0, //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0, //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112, //full load for table 2
```

1. Wykonaj zadanie migracji bazy danych uruchomienia produkcyjnego, używając następującego polecenia:

    ```azurecli
    az dms project task cutover -h
    ```

    Przykład:

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. Aby monitorować postęp uruchomienia produkcyjnego, uruchom następujące polecenie:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```
3. Gdy stan migracji bazy danych jest **zakończony**, [Utwórz ponownie sekwencje](https://wiki.postgresql.org/wiki/Fixing_Sequences) (jeśli dotyczy) i Połącz swoje aplikacje z nowym docelowym wystąpieniem Azure Database for PostgreSQL.

## <a name="service-project-task-cleanup"></a>Usługa, projekt, oczyszczanie zadania

Jeśli potrzebujesz anulować lub usunąć jakiekolwiek zadanie usługi DMS, projekt lub usługę, przeprowadź anulowanie w następującej kolejności:

* Anuluj wszelkie uruchomione zadania
* Usuń zadanie
* Usuń projekt
* Usuń usługę DMS

1. Aby anulować bieżące zadanie, użyj następującego polecenia:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Aby usunąć bieżące zadanie, użyj następującego polecenia:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Aby anulować bieżący projekt, użyj następującego polecenia:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Aby usunąć bieżący projekt, użyj następującego polecenia:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Aby usunąć usługę DMS, użyj następującego polecenia:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje na temat Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](../postgresql/overview.md).