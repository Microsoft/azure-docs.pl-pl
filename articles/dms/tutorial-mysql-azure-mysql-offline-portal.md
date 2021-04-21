---
title: 'Samouczek: migrowanie programu MySQL do trybu Azure Database for MySQL offline przy użyciu usługi DMS'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację offline z lokalnego programu MySQL do usługi Azure Database for MySQL przy użyciu Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819730"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Samouczek: migrowanie programu MySQL do trybu Azure Database for MySQL offline przy użyciu usługi DMS

Możesz użyć Azure Database Migration Service, aby przeprowadzić jednokrotną pełną migrację bazy danych w lokalnym wystąpieniu programu MySQL w celu Azure Database for MySQL [z](../mysql/index.yml) możliwością szybkiego migrowania danych. W tym samouczku zmigrujemy przykładową bazę danych z lokalnego wystąpienia programu MySQL 5.7 do programu Azure Database for MySQL (wersja 5.7) przy użyciu działania migracji w trybie offline w programie Azure Database Migration Service. Chociaż w artykułach przyjęto założenie, że źródłem jest wystąpienie bazy danych MySQL i obiektem docelowym Azure Database for MySQL, można go użyć do migracji z jednego serwera Azure Database for MySQL do innego tylko przez zmianę nazwy i poświadczeń serwera źródłowego. Obsługiwana jest również migracja z serwerów MySQL w niższej wersji (wersja 5.6 lub nowsza) do wyższych wersji.

> [!IMPORTANT]
> W przypadku migracji online możesz użyć tej nowej oferty wraz z [replikacją typu data-in.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Alternatywnie możesz użyć narzędzi typu open source, takich jak [MyDumper/MyLoader,](https://centminmod.com/mydumper.html) z replikacją typu data-in na użytek migracji online. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Migrowanie schematu bazy danych przy użyciu narzędzia mysqldump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free)
* Mieć lokalną bazę danych MySQL w wersji 5.7. Jeśli nie, pobierz i zainstaluj [program MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7.
* [Utwórz wystąpienie usługi Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Zapoznaj się z artykułem [Use MySQL Workbench to](../mysql/connect-workbench.md) connect and query data (Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu aplikacji Workbench. Wersja Azure Database for MySQL powinna być równa lub wyższa niż wersja lokalnego programu MySQL. Na przykład system MySQL 5.7 można migrować do wersji Azure Database for MySQL 5.7 lub uaktualnić do wersji 8. 
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
* Otwórz zaporę systemu Windows, aby zezwolić na połączenia z usługi Virtual Network dla programu Azure Database Migration Service na dostęp do źródłowego serwera MySQL Server, który domyślnie jest portem TCP 3306.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby zezwolić na połączenia z usługi Virtual Network dla programu Azure Database Migration Service na dostęp do źródłowych baz danych na potrzeby migracji.
* Utwórz regułę [](../azure-sql/database/firewall-configure.md) zapory na [](../mysql/howto-manage-vnet-using-portal.md) poziomie serwera lub skonfiguruj punkty końcowe usługi sieci wirtualnej dla docelowych Azure Database for MySQL, aby umożliwić Virtual Network dostępu Azure Database Migration Service do docelowych baz danych.
* Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:

    ```
    SELECT @@version;
    ```

* Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* Użytkownik musi mieć uprawnienia do odczytu danych w źródłowej bazie danych.

## <a name="migrate-database-schema"></a>Migrowanie schematu bazy danych

Aby przenieść wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy i procedury składowane, musimy wyodrębnić schemat ze źródłowej bazy danych i zastosować go do docelowej bazy danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem `--no-data`. Do tego celu potrzebna jest maszyna, która może łączyć się zarówno ze źródłową bazą danych MySQL, jak i docelową bazą Azure Database for MySQL.

Aby wyeksportować schemat przy użyciu narzędzia mysqldump, uruchom następujące polecenie:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Na przykład:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Aby zaimportować schemat do Azure Database for MySQL docelowego, uruchom następujące polecenie:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Na przykład:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Jeśli schemat zawiera klucze obce, równoległe ładowanie danych podczas migracji będzie obsługiwane przez zadanie migracji. Podczas migracji schematu nie trzeba popuszczać kluczy obcych.

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

Rejestrację dostawcy zasobów należy wykonać w każdej subskrypcji platformy Azure tylko raz. Bez rejestracji nie będzie można utworzyć wystąpienia usługi **Azure Database Migration Service**.

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów.**

3. Wyszukaj migrację, a następnie po prawej stronie witryny **Microsoft.DataMigration** wybierz **pozycję Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Tworzenie Database Migration Service danych

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz warstwę cenową i przejdź do ekranu sieci. Funkcja migracji offline jest dostępna w warstwie cenowej Standardowa i Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie Azure Database Migration Service ustawień podstawowych](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Wybierz istniejącą sieć wirtualną z listy lub podaj nazwę nowej sieci wirtualnej, która ma zostać utworzona. Przejdź do ekranu przeglądu i tworzenia. Opcjonalnie możesz dodać tagi do usługi przy użyciu ekranu tagów.

    Sieć wirtualna zapewnia Azure Database Migration Service dostęp do źródła i SQL Server docelowego Azure SQL Database wystąpienia.

    ![Konfigurowanie Azure Database Migration Service sieci](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł Create [a virtual network using](../virtual-network/quick-create-portal.md)the Azure Portal (Tworzenie sieci wirtualnej przy użyciu Azure Portal).

6. Przejrzyj konfiguracje i wybierz **pozycję Utwórz,** aby utworzyć usługę.
    
    ![Azure Database Migration Service tworzenia](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.  

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Wybierz wystąpienie usługi migracji z wyników wyszukiwania, a następnie wybierz pozycję + **Nowy projekt migracji.**
    
    ![Tworzenie nowego projektu migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Na  ekranie Nowy projekt migracji określ nazwę projektu, w polu wyboru Typ serwera źródłowego  wybierz pozycję **MySQL,** w polu wyboru Typ serwera docelowego wybierz pozycję **Azure Database for MySQL,** a następnie w polu **Wyboru** typ działania Migracja wybierz pozycję Wersja zapoznawcza migracji **\[ danych. \]**  Wybierz pozycję **Utwórz i uruchom działanie**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternatywnie możesz wybrać pozycję **Utwórz tylko projekt,** aby utworzyć projekt migracji teraz i wykonać migrację później.

## <a name="configure-migration-project"></a>Konfigurowanie projektu migracji

1. Na **ekranie Wybierz źródło** określ szczegóły połączenia dla źródłowego wystąpienia mySQL, a następnie wybierz pozycję **Dalej: Wybierz nazwę>>**

    ![Ekran dodawania szczegółów źródła](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. Na **ekranie Wybieranie obiektu** docelowego określ szczegóły połączenia dla wystąpienia Azure Database for MySQL docelowego, a następnie wybierz pozycję **Dalej: Wybierz bazy danych>>**

    ![Ekran dodawania szczegółów docelowych](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Na **ekranie Wybieranie baz** danych zamapuj źródłową i docelową bazę danych na migrację, a następnie wybierz pozycję **Dalej:** Skonfiguruj ustawienia migracji>>. Możesz wybrać opcję **Tylko do** odczytu serwera źródłowego, aby ustawić źródło jako tylko do odczytu, ale należy zachować ostrożność, że jest to ustawienie na poziomie serwera. Jeśli ta opcja jest zaznaczona, ustawia cały serwer na tylko do odczytu, a nie tylko wybrane bazy danych.
    
    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłową bazę danych, Azure Database Migration Service domyślnie wybierze docelową bazę danych.
    ![Ekran Wybierania szczegółów bazy danych](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > W tym kroku można wybrać wiele baz danych, ale istnieją ograniczenia dotyczące ich ilu baz danych można migrować w ten sposób, ponieważ każda baza danych współużytkuje zasoby obliczeniowe. W przypadku domyślnej konfiguracji wersji Premium SKU każde zadanie migracji spróbuje przeprowadzić równoległą migrację dwóch tabel. Te tabele mogą pochodzić z dowolnej z wybranych baz danych. Jeśli nie jest to wystarczająco szybkie, możesz podzielić działania migracji bazy danych na różne zadania migracji i skalować je w wielu usługach. Ponadto istnieje limit 10 wystąpień Azure Database Migration Service na subskrypcję na region.
    > Aby uzyskać bardziej szczegółową kontrolę nad przepływnością migracji i przetwarzaniem równoległym, zapoznaj się z artykułem [PowerShell: Run offline migration from MySQL database to Azure Database for MySQL using DMS](./migrate-mysql-to-azure-mysql-powershell.md) (Program PowerShell: uruchamianie migracji w trybie offline z bazy danych MySQL do usługi Azure Database for MySQL dms)

4. Na **ekranie Konfigurowanie ustawień migracji** wybierz tabele, które mają być częścią migracji, a następnie wybierz pozycję **Dalej: Podsumowanie>>**. Jeśli tabele docelowe zawierają jakiekolwiek dane, nie są one wybierane domyślnie, ale można je jawnie wybrać i zostaną one obcięte przed rozpoczęciem migracji.

    ![Ekran Wybierania tabel](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Na **ekranie** Podsumowanie  w polu tekstowym Nazwa działania określ nazwę działania migracji i przejrzyj podsumowanie, aby upewnić się, że szczegóły źródła i celu są zgodne z wcześniej określonymi.

    ![Podsumowanie projektu migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Wybierz **pozycję Rozpocznij migrację.** Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**. Po **rozpoczęciu** **migracji** tabeli stan zmieni się na Uruchomiony.
 
     ![Uruchamianie migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybierz pozycję **Odśwież,** aby zaktualizować ekran i wyświetlić postęp na temat liczby ukończonych tabel. 

2. Możesz kliknąć nazwę bazy danych na ekranie działania, aby wyświetlić stan każdej tabeli podczas migrowania. Wybierz **pozycję Odśwież,** aby zaktualizować ekran. 

     ![Monitorowanie migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Kończenie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

    ![Kończenie migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Działania po migracji

Migracja migracji w trybie offline jest procesem zależnym od aplikacji, który jest poza zakresem tego dokumentu, ale zalecane są następujące działania po migracji:

1. Utwórz identyfikatory logowania, role i uprawnienia zgodnie z wymaganiami aplikacji.
2. Utwórz ponownie wszystkie wyzwalacze w docelowej bazie danych wyodrębnione w kroku przed migracją.
3. Wykonaj testowanie sanity aplikacji względem docelowej bazy danych, aby certyfikować migrację. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal używać usługi Database Migration Service, możesz usunąć usługę, wykonać następujące czynności:

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Lokalizowanie wszystkich wystąpień usługi DMS](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Wybierz wystąpienie usługi migracji z wyników wyszukiwania, a następnie wybierz **pozycję Usuń usługę**.
    
    ![Usuwanie usługi migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. W oknie dialogowym potwierdzenia wpisz nazwę usługi w polu tekstowym WPISZ NAZWĘ **USŁUGI MIGRACJI BAZY** DANYCH i wybierz pozycję **Usuń.**

    ![Potwierdzanie usunięcia usługi migracji](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach podczas przeprowadzania migracji przy użyciu usługi DMS, zobacz artykuł Typowe problemy [— Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Aby rozwiązać problemy z łącznością ze źródłową bazą danych podczas korzystania z usługi DMS, zobacz artykuł Issues connecting source databases (Problemy z [łączeniu źródłowych baz danych).](./known-issues-troubleshooting-dms-source-connectivity.md)
* Aby uzyskać informacje Azure Database Migration Service, zobacz artykuł [Co to jest Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje Azure Database for MySQL, zobacz artykuł [Co to jest Azure Database for MySQL?](../mysql/overview.md).
* Aby uzyskać wskazówki dotyczące korzystania z usługi DMS za pośrednictwem programu PowerShell, zobacz artykuł [PowerShell: Run offline migration from MySQL database to Azure Database for MySQL using DMS](./migrate-mysql-to-azure-mysql-powershell.md) (Program PowerShell: uruchamianie migracji w trybie offline z bazy danych MySQL do usługi AZURE DATABASE FOR MYSQL przy użyciu usługi DMS)