---
title: 'Samouczek: Migrowanie usług RDS MySQL online do Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację w trybie online z usług RDS MySQL do Azure Database for MySQL przy użyciu Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: f8948bdeb2f8b82fbabacdbbb73c7b43741c75df
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938444"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: Migrowanie bazy danych RDS MySQL do usługi Azure Database for MySQL w trybie online przy użyciu usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia programu RDS MySQL do [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) , gdy źródłowa baza danych pozostanie w trybie online podczas migracji. Innymi słowy, migracja może zostać osiągnięta przy minimalnym przestoju aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **Employees** z wystąpienia programu RDS MySQL do Azure Database for MySQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
>
> * Przeprowadź migrację przykładowego schematu przy użyciu narzędzi mysqldump i MySQL.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji w trybie online z wystąpienia programu RDS MySQL do Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Upewnij się, że źródłowy serwer MySQL korzysta z obsługiwanego programu MySQL Community Edition. Aby określić wersję wystąpienia MySQL, w narzędziu MySQL lub MySQL Workbench Uruchom polecenie:

    ```
    SELECT @@version;
    ```

    Aby uzyskać więcej informacji, zobacz artykuł [obsługiwane Azure Database for MySQL wersje](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Pobierz i zainstaluj [przykładową bazę danych programu MySQL **Employees** ](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Utwórz wystąpienie [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.
* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Skonfiguruj [zaporę systemu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (lub zaporę z systemem Linux), aby umożliwić dostęp do aparatu bazy danych. W przypadku serwera MySQL Zezwól na port 3306 na potrzeby łączności.

> [!NOTE]
> Azure Database for MySQL obsługuje tylko tabele InnoDB. Aby przekonwertować tabele MyISAM na InnoDB, zapoznaj się z artykułem [konwertowanie tabel z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurowanie usługi AWS RDS MySQL na potrzeby replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami dostarczonymi przez AWS w [plikach dziennika bazy danych MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)w sekcji **Format rejestrowania binarnego** .
2. Utwórz nową grupę parametrów z następującą konfiguracją:
    * log_bin = włączone
    * binlog_format = row
    * binlog_checksum = brak
3. Zapisz nową grupę parametrów.
4. Skojarz nową grupę parametrów z wystąpieniem usług RDS MySQL. Może być wymagane ponowne uruchomienie komputera.
5. Gdy grupa parametrów jest na miejscu, Połącz się z wystąpieniem programu MySQL i [Ustaw wartość przechowywania binlog](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) na co najmniej 5 dni.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Wyodrębnij schemat ze źródłowej bazy danych i zastosuj go do docelowej bazy danych, aby ukończyć migrację wszystkich obiektów bazy danych, takich jak schematy tabel, indeksy i procedury składowane.

    Najprostszym sposobem migrowania tylko schematu jest użycie mysqldump z parametrem--No-Data. Polecenie migracji schematu jest następujące:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Na przykład aby zrzucić plik schematu dla bazy danych **Employees** , użyj następującego polecenia:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Zaimportuj schemat do usługi docelowej, który jest Azure Database for MySQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Na przykład, aby zaimportować schemat dla bazy danych **Employees** :

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skrypt klucza obcego i dodać skrypt klucza obcego w miejscu docelowym (Azure Database for MySQL), uruchom następujący skrypt w programie MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Uruchom klucz obcy (który jest drugą kolumną) w wyniku zapytania, aby porzucić klucz obcy.

> [!NOTE]
> Usługa Azure DMS nie obsługuje akcji referencyjnej CASCADE, która pomaga automatycznie usuwać lub aktualizować pasujący wiersz w tabeli podrzędnej po usunięciu lub zaktualizowaniu wiersza w tabeli nadrzędnej. Aby uzyskać więcej informacji, zobacz sekcję działania referencyjne w temacie [ograniczenia klucza obcego](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)artykułu.
> Usługa Azure DMS wymaga porzucenia ograniczeń klucza obcego na docelowym serwerze bazy danych podczas początkowego ładowania danych i nie można używać akcji referencyjnych. Jeśli obciążenie zależy od aktualizowania powiązanej tabeli podrzędnej za pomocą tej akcji referencyjnej, zalecamy wykonanie [zrzutu i przywrócenie](https://docs.microsoft.com/azure/mysql/concepts-migrate-dump-restore) zamiast tego. 

5. Jeśli w danych są Wyzwalacze (INSERT lub Update Trigger), spowoduje to wymuszenie integralności danych w miejscu docelowym przed replikowaniem danych ze źródła. Zalecane jest, aby wyłączyć wyzwalacze we wszystkich tabelach w *miejscu docelowym* podczas migracji, a następnie włączyć Wyzwalacze po zakończeniu migracji.

    Aby wyłączyć Wyzwalacze w docelowej bazie danych:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Jeśli istnieją wystąpienia typu danych ENUM w dowolnych tabelach, zalecamy tymczasowe aktualizowanie do typu elementu DataType "charactering" w tabeli docelowej. Po zakończeniu replikacji danych Przywróć typ danych do WYLICZENIA.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration**wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service.

5. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego wystąpienia programu MySQL i docelowego wystąpienia Azure Database for MySQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową; w przypadku migracji w trybie online należy wybrać warstwę cenową Premium: 4vCores.

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MySQL**, a następnie w polu tekstowym **Typ serwera docelowego** wybierz pozycję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **migracja danych w trybie online**.

    > [!IMPORTANT]
    > Upewnij się, że wybrano opcję **migracja danych w trybie online**; migracje w trybie offline nie są obsługiwane w tym scenariuszu.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Zanotuj wymagania wstępne wymagane do skonfigurowania migracji online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **szczegóły źródła migracji** Określ szczegóły połączenia dla źródłowego wystąpienia programu MySQL.

   ![Szczegóły źródła](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz pozycję **Zapisz**, a następnie na ekranie **szczegóły elementu docelowego** Określ szczegóły połączenia dla docelowego serwera Azure Database for MySQL, który jest wstępnie zainicjowany i ma wdrożony schemat **pracownicy** przy użyciu MySQLDump.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — uruchomiony](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. W obszarze **Nazwa bazy danych**wybierz określoną bazę danych, aby przejść do stanu migracji **pełnych operacji ładowania danych** i **przyrostowej synchronizacji danych** .

    **Pełne ładowanie danych** pokazuje stan początkowej migracji ładowania, podczas gdy **Synchronizacja danych przyrostowych** pokazuje stan funkcji przechwytywania zmian danych.

    ![Ekran spisu — pełne ładowanie danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Ekran spisu — synchronizacja danych przyrostowych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczane jako **gotowe do uruchomienie produkcyjne**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpocznij obcinanie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Po **zakończeniu**wyświetlania stanu migracji bazy danych Połącz swoje aplikacje z nową docelową bazą danych Azure Database for MySQL.

Migracja w trybie online lokalnego wystąpienia MySQL do Azure Database for MySQL jest teraz zakończona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat Azure Database for MySQL, zobacz artykuł [co to jest Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* W przypadku innych pytań Wyślij wiadomość e-mail do aliasu [zadawaj migracje bazy danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
