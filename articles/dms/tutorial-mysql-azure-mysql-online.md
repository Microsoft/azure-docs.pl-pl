---
title: 'Samouczek: Migrowanie bazy danych MySQL online do Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację w trybie online z lokalnego programu MySQL do Azure Database for MySQL przy użyciu Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: b7e9491f3ddc49d49cf5301bba9d4f51fc9dd008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282344"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: migrowanie programu MySQL do usługi Azure Database for MySQL w trybie online przy użyciu usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z lokalnego wystąpienia programu MySQL do [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) z minimalnym przestojem. Innymi słowy migrację można przeprowadzić przy minimalnych przestojach w działaniu aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **Employees** z lokalnego wystąpienia MySQL 5,7 do Azure Database for MySQL przy użyciu działania migracji w trybie online w programie Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu za pomocą narzędzia mysqldump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

> [!NOTE]
> Komunikacja bezpłatna bez opłat
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word _podrzędny_. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to obecnie słowo, które jest wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobrany i zainstalowany program [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) w wersji 5.6 lub 5.7. Wersja lokalnego programu MySQL musi być zgodna z wersją usługi Azure Database for MySQL. Na przykład program MySQL 5.6 może być migrowany tylko do usługi Azure Database for MySQL 5.6, a nie do nowszej wersji 5.7.
* [Utwórz wystąpienie usługi Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Zapoznaj się z artykułem [Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań przy użyciu środowiska roboczego bazy danych MySQL](https://docs.microsoft.com/azure/mysql/connect-workbench), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu witryny Azure Portal.  
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu Azure Resource Manager model wdrażania, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas instalacji usługi Virtual networkNet używasz usługi ExpressRoute z siecią równorzędną firmy Microsoft, Dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera MySQL, który domyślnie jest portem TCP 3306.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla Azure Database for MySQL, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na potrzeby Azure Database Migration Service.
* Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:

    ```
    SELECT @@version;
    ```

* Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Włącz rejestrowanie binarne w pliku my.ini (Windows) lub my.cnf (Unix) źródłowej bazy danych przy użyciu następującej konfiguracji:

  * **server_id** = 1 lub wyższa wartość (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * **log-bin** = \<path> (dotyczy tylko programu MySQL 5,6)    Na przykład: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (nie zaleca się używania zera; dotyczy tylko programu MySQL 5,6)
  * **Binlog_row_image** = full (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * **log_slave_updates** = 1

* Użytkownik musi mieć rolę ReplicationAdmin z następującymi uprawnieniami:

  * **KLIENT REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **REPLIKA REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **SUPER** — wymagane tylko w wersjach wcześniejszych niż MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem `--no-data`.

Przy założeniu, że masz przykładową bazę danych programu MySQL **Employees** w systemie lokalnym, polecenie do migracji schematu przy użyciu mysqldump jest następujące:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Na przykład:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Aby zaimportować schemat do docelowej usługi Azure Database for MySQL, uruchom następujące polecenie:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Na przykład:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem.  Wykonaj następujący skrypt w programie MySQL Workbench, aby wyodrębnić skrypt klucza obcego i dodać skrypt klucza obcego.

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

Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania w celu usunięcia klucza obcego.

> [!NOTE]
> Usługa Azure DMS nie obsługuje akcji referencyjnej CASCADE, która pomaga automatycznie usuwać lub aktualizować pasujący wiersz w tabeli podrzędnej po usunięciu lub zaktualizowaniu wiersza w tabeli nadrzędnej. Aby uzyskać więcej informacji, zobacz sekcję działania referencyjne w temacie [ograniczenia klucza obcego](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)artykułu.
> Usługa Azure DMS wymaga porzucenia ograniczeń klucza obcego na docelowym serwerze bazy danych podczas początkowego ładowania danych i nie można używać akcji referencyjnych. Jeśli obciążenie zależy od aktualizowania powiązanej tabeli podrzędnej za pomocą tej akcji referencyjnej, zalecamy wykonanie [zrzutu i przywrócenie](https://docs.microsoft.com/azure/mysql/concepts-migrate-dump-restore) zamiast tego. 


> [!IMPORTANT]
> W przypadku importowania danych przy użyciu kopii zapasowej Usuń polecenia tworzenia definicji ręcznie lub za pomocą polecenia--Skip-define przy wykonywaniu mysqldump. ZDEFINIOWAnie wymaga uprawnień do tworzenia i jest ograniczone w Azure Database for MySQL.

Jeśli masz wyzwalacz w danych (wyzwalacz INSERT lub Update), będzie wymuszać integralność danych w miejscu docelowym przed replikowanymi danymi ze źródła. W czasie trwania migracji zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w docelowej lokalizacji. Po jej zakończeniu wyzwalacze należy ponownie włączyć.

Aby wyłączyć Wyzwalacze w docelowej bazie danych, użyj następującego polecenia:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration**wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowej i docelowego wystąpienia Azure SQL Database.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, a następnie wybierz wystąpienie.

     ![Znajdź wystąpienie Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MySQL**, a w polu tekstowym **Typ serwera docelowego** wybierz pozycję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać opcję **Utwórz projekt tylko** w celu utworzenia projektu migracji, a następnie wykonać migrację później.

6. Wybierz pozycję **Zapisz**, pamiętając o uwzględnieniu wymagań niezbędnych do prawidłowego korzystania z usługi DMS na potrzeby migracji danych, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia programu MySQL.

    ![Ekran Dodawanie szczegółów źródła](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły elementu docelowego** określ szczegóły połączenia dla docelowego serwera usługi Azure Database for MySQL, czyli dla wstępnie zaaprowizowanego wystąpienia bazy danych Azure Database for MySQL, w której wdrożono schemat **Employees** za pomocą narzędzia mysqldump.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Chociaż w tym kroku możesz wybrać wiele baz danych, każde wystąpienie Azure Database Migration Service obsługuje maksymalnie cztery bazy danych na potrzeby równoczesnej migracji. Ponadto istnieje limit dwóch wystąpień Azure Database Migration Service na region w ramach subskrypcji. Na przykład jeśli masz 40 baz danych do migracji, można migrować tylko osiem z nich jednocześnie i tylko wtedy, gdy utworzono dwa wystąpienia Azure Database Migration Service.

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji oraz **stan** **inicjowania**działania.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Stan działania — ukończono](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for MySQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for MySQL](known-issues-azure-mysql-online.md).
* Aby uzyskać informacje na temat Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat Azure Database for MySQL, zobacz artykuł [co to jest Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
