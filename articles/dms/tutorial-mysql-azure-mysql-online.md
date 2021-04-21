---
title: 'Samouczek: migrowanie programu MySQL w trybie online do Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z lokalnego programu MySQL do programu Azure Database for MySQL przy użyciu Azure Database Migration Service.
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
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818070"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: migrowanie programu MySQL do usługi Azure Database for MySQL w trybie online przy użyciu usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z lokalnego wystąpienia programu MySQL do programu [Azure Database for MySQL](../mysql/index.yml) z minimalnym przestojem. Innymi słowy migrację można przeprowadzić przy minimalnych przestojach w działaniu aplikacji. W tym samouczku zmigrujesz przykładową bazę danych **Employees** z lokalnego wystąpienia programu MySQL 5.7 do programu Azure Database for MySQL przy użyciu działania migracji online w programie Azure Database Migration Service.

> [!IMPORTANT]
> Scenariusz migracji online "MySQL do Azure Database for MySQL" został zastąpiony równoległym, wysoce wydajnym scenariuszem migracji w trybie offline 1 czerwca 2021 r. W przypadku migracji online możesz użyć tej nowej oferty wraz z [replikacją typu data-in.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Alternatywnie możesz użyć narzędzi typu open source, takich jak [MyDumper/MyLoader,](https://centminmod.com/mydumper.html) z replikacją typu data-in na użytek migracji online. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu za pomocą narzędzia mysqldump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

> [!NOTE]
> Przeprowadzenie Azure Database Migration Service online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby uzyskać optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie świadczenia usługi Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

> [!NOTE]
> Komunikacja bez stronniczości
>
> Firma Microsoft obsługuje zróżnicowane i inkluzywne środowisko. Ten artykuł zawiera odwołania do słowa _podrzędnego_. Przewodnik po stylu firmy Microsoft [dotyczący komunikacji bez stronniczości](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) rozpoznaje to jako słowo wykluczeń. Słowo to jest używane w tym artykule w celu zachowania spójności, ponieważ jest to obecnie słowo, które pojawia się w oprogramowaniu. Po zaktualizowaniu oprogramowania w celu usunięcia tego wyrazu ten artykuł zostanie zaktualizowany w celu dostosowania.
>


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobrany i zainstalowany program [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) w wersji 5.6 lub 5.7. Wersja lokalnego programu MySQL musi być zgodna z wersją usługi Azure Database for MySQL. Na przykład program MySQL 5.6 może być migrowany tylko do usługi Azure Database for MySQL 5.6, a nie do nowszej wersji 5.7. Migracje do lub z usługi MySQL 8.0 nie są obsługiwane.
* [Utwórz wystąpienie usługi Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Zapoznaj się z artykułem [Use MySQL Workbench to](../mysql/connect-workbench.md) connect and query data (Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu aplikacji Workbench.  
* Utwórz Microsoft Azure Virtual Network dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub sieci [VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz dokumentację usługi [Virtual Network, a](../virtual-network/index.yml)zwłaszcza artykuły Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z sieciową komunikacja równorzędną z firmą Microsoft, dodaj następujące punkty końcowe usługi do podsieci, w której zostanie aprowizowana usługa: [](../virtual-network/virtual-network-service-endpoints-overview.md)
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy i tak dalej)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest niezbędna, Azure Database Migration Service nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 tagu usługi ServiceTag dla usług ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł Filtrowanie ruchu [sieciowego przy użyciu sieciowych grup zabezpieczeń.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby Azure Database Migration Service dostęp do źródłowego serwera MySQL, czyli domyślnie portu TCP 3306.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
* Utwórz regułę zapory na [poziomie serwera dla](../azure-sql/database/firewall-configure.md) Azure Database for MySQL, aby Azure Database Migration Service do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej na Azure Database Migration Service.
* Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:

    ```
    SELECT @@version;
    ```

* Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Włącz rejestrowanie binarne w pliku my.ini (Windows) lub my.cnf (Unix) źródłowej bazy danych przy użyciu następującej konfiguracji:

  * **server_id** = 1 lub wyższa wartość (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * **log-bin** = \<path> (dotyczy tylko mysql 5.6)    Na przykład: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (nie zaleca się używania zera; ma zastosowanie tylko w przypadku mysql 5.6)
  * **Binlog_row_image** = full (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * **log_slave_updates** = 1

* Użytkownik musi mieć rolę ReplicationAdmin z następującymi uprawnieniami:

  * **KLIENT REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **REPLIKA REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **SUPER** — wymagane tylko w wersjach wcześniejszych niż MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem `--no-data`.

Zakładając, że masz przykładową bazę danych MySQL **Employees** w systemie lokalnym, polecenie do migracji schematu przy użyciu narzędzia mysqldump jest:

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

Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem.  Wykonaj następujący skrypt w programie MySQL Workbench, aby wyodrębnić skrypt drop klucza obcego i dodać skrypt klucza obcego.

```sql
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
> Usługa Azure DMS nie obsługuje akcji referencyjnej CASCADE, która pomaga automatycznie usuwać lub aktualizować pasujący wiersz w tabeli podrzędnej, gdy wiersz zostanie usunięty lub zaktualizowany w tabeli nadrzędnej. Aby uzyskać więcej informacji, zobacz sekcję Akcje referencyjne w artykule [OGRANICZENIA KLUCZA OBCEGO](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)w dokumentacji programu MySQL.
> Usługa Azure DMS wymaga, aby podczas początkowego ładowania danych usunąć ograniczenia klucza obcego na docelowym serwerze bazy danych i nie można używać akcji odwołującej się. Jeśli obciążenie zależy od zaktualizowania powiązanej tabeli podrzędnej za pomocą tej akcji referencyjnej, zalecamy zamiast tego wykonanie [zrzutu i przywrócenia.](../mysql/concepts-migrate-dump-restore.md) 


> [!IMPORTANT]
> W przypadku importowania danych przy użyciu kopii zapasowej usuń polecenia CREATE DEFINER ręcznie lub za pomocą polecenia --skip-definer podczas wykonywania polecenia mysqldump. Definiowanie wymaga superunifikatorów do utworzenia i jest ograniczone w Azure Database for MySQL.

Jeśli w bazie danych masz wyzwalacze, wymusi to integralność danych w celu przed pełną migracją danych ze źródła. Zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w docelowej tabeli podczas migracji, a następnie włączenie wyzwalaczy po zakończeniu migracji.

Wykonaj następujący skrypt w aplikacji MySQL Workbench w docelowej bazie danych, aby wyodrębnić skrypt wyzwalacza upuszczania i dodać skrypt wyzwalacza.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Uruchom wygenerowane zapytanie wyzwalacza upuszczania (kolumnę DropQuery) w wyniku, aby usunąć wyzwalacze w docelowej bazie danych. Można zapisać zapytanie dodawania wyzwalacza, które będzie używane po zakończeniu migracji danych.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów.**

    ![Wyświetlanie dostawców zasobów](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie witryny **Microsoft.DataMigration** wybierz **pozycję Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Tworzenie Database Migration Service danych

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz warstwę cenową i przejdź do ekranu sieci. Funkcja migracji offline jest dostępna zarówno w warstwie cenowej Standardowa, jak i Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie Azure Database Migration Service ustawień podstawowych](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Wybierz istniejącą sieć wirtualną z listy lub podaj nazwę nowej sieci wirtualnej, która ma zostać utworzona. Przejdź do ekranu przeglądu i tworzenia. Opcjonalnie możesz dodać tagi do usługi przy użyciu ekranu tagów.

    Sieć wirtualna zapewnia Azure Database Migration Service dostęp do źródła i SQL Server docelowego Azure SQL Database wystąpienia.

    ![Konfigurowanie Azure Database Migration Service sieci](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł Create [a virtual network using](../virtual-network/quick-create-portal.md)the Azure Portal (Tworzenie sieci wirtualnej przy użyciu Azure Portal).

6. Przejrzyj konfiguracje i wybierz **pozycję Utwórz,** aby utworzyć usługę.
    
    ![Azure Database Migration Service tworzenia](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.  

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Wybierz wystąpienie usługi migracji z wyników wyszukiwania, a następnie wybierz pozycję + **Nowy projekt migracji.**
    
    ![Tworzenie nowego projektu migracji](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Na  ekranie Nowy projekt migracji określ nazwę projektu, w polu wyboru Typ serwera źródłowego  wybierz pozycję **MySQL,** w polu wyboru Typ serwera docelowego wybierz pozycję **Azure Database for MySQL,** a następnie w polu **Wybór** typu działania Migracja wybierz pozycję Migracja danych w trybie **online.**  Wybierz pozycję **Utwórz i uruchom działanie**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać pozycję **Utwórz tylko projekt,** aby utworzyć projekt migracji teraz i wykonać migrację później.

## <a name="configure-migration-project"></a>Konfigurowanie projektu migracji

1. Na **ekranie Wybierz źródło** określ szczegóły połączenia dla źródłowego wystąpienia mySQL, a następnie wybierz pozycję **Dalej: Wybierz nazwę>>**

    ![Ekran dodawania szczegółów źródła](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. Na **ekranie Wybieranie obiektu** docelowego określ szczegóły połączenia dla wystąpienia Azure Database for MySQL docelowego, a następnie wybierz pozycję **Dalej: Wybierz bazy danych>>**

    ![Ekran dodawania szczegółów obiektu docelowego](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Na **ekranie Wybieranie baz** danych zamapuj źródło i docelową bazę danych na migrację, a następnie wybierz pozycję **Dalej: Skonfiguruj ustawienia migracji>>**. Możesz wybrać opcję **Tylko do** odczytu serwera źródłowego, aby ustawić źródło jako tylko do odczytu, ale należy zachować ostrożność, że jest to ustawienie na poziomie serwera. Jeśli ta opcja jest zaznaczona, ustawia cały serwer na tylko do odczytu, a nie tylko wybrane bazy danych.
    
    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłową bazę danych, Azure Database Migration Service domyślnie wybierze docelową bazę danych.
    ![Ekran Wybierania szczegółów bazy danych](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Chociaż w tym kroku można wybrać wiele baz danych, każde wystąpienie usługi Azure Database Migration Service obsługuje do 4 baz danych w przypadku migracji współbieżnej. Ponadto istnieje limit 10 wystąpień Azure Database Migration Service na subskrypcję na region. Jeśli na przykład masz 80 baz danych do migracji, możesz migrować 40 z nich do tego samego regionu jednocześnie, ale tylko wtedy, gdy utworzono 10 wystąpień Azure Database Migration Service.

4. Na **ekranie Konfigurowanie ustawień migracji** wybierz tabele, które mają być częścią migracji, a następnie wybierz pozycję **Dalej: Podsumowanie>>**. Jeśli tabele docelowe zawierają jakiekolwiek dane, nie są one wybierane domyślnie, ale można je jawnie wybrać i zostaną one obcięte przed rozpoczęciem migracji.

    ![Ekran Wybierania tabel](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Na **ekranie** Podsumowanie  w polu tekstowym Nazwa działania określ nazwę działania migracji i przejrzyj podsumowanie, aby upewnić się, że szczegóły źródła i celu są zgodne z wcześniej określonymi.

    ![Podsumowanie projektu migracji](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Wybierz **pozycję Rozpocznij migrację.** Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**. Po **rozpoczęciu** **migracji** tabeli stan zmieni się na Uruchomiony.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Stan działania — ukończono](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for MySQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for MySQL](known-issues-azure-mysql-online.md).
* Aby uzyskać informacje Azure Database Migration Service, zobacz artykuł [Co to jest Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje Azure Database for MySQL, zobacz artykuł [Co to jest Azure Database for MySQL?](../mysql/overview.md).
