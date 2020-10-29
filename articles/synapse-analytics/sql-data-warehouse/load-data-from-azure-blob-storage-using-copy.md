---
title: 'Samouczek: ładowanie danych z Nowego Jorku Taxicab'
description: Samouczek używa Azure Portal i SQL Server Management Studio do załadowania Taxicab danych z obiektu blob platformy Azure dla Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb5984ba5d5764ee2ffa3f28e2d95612c14f7e27
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025939"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Samouczek: Załaduj zestaw danych Taxicab Nowego Jorku

Ten samouczek używa [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) do ładowania zestawu danych Taxicab New York z konta usługi Azure Blob Storage. W tym samouczku użyto witryny [Azure Portal](https://portal.azure.com) i programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS), aby wykonać następujące czynności:

> [!div class="checklist"]
>
> * Utwórz pulę SQL w Azure Portal
> * Konfigurowanie reguły zapory na poziomie serwera w witrynie Azure Portal
> * Nawiązywanie połączenia z magazynem danych za pomocą programu SSMS
> * Tworzenie użytkownika wyznaczonego do ładowania danych
> * Tworzenie tabel dla przykładowego zestawu danych 
> * Ładowanie danych do magazynu danych za pomocą instrukcji COPY języka T-SQL
> * Wyświetlanie postępu ładowania danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych

Zostanie utworzona Pula SQL ze zdefiniowanym zestawem [zasobów obliczeniowych](memory-concurrency-limits.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i w [logicznym serwerze SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Wykonaj następujące kroki, aby utworzyć pustą bazę danych.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** .

2. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure Synapse Analytics** w obszarze **proponowane** na **nowej** stronie.

    ![Zrzut ekranu przedstawia SQL Data Warehouse wybrane z baz danych w Azure Portal.](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Wypełnij formularz, używając poniższych informacji:

   | Ustawienie            | Sugerowana wartość       | Opis                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Nazwij**            | mySampleDataWarehouse | Prawidłowe nazwy baz danych znajdują się w temacie [identyfikatory baz danych](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
   | **Subskrypcja**   | Twoja subskrypcja     | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup       | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |
   | **Wybierz źródło**  | Pusta baza danych        | Określa, że ma zostać utworzona pusta baza danych. Pamiętaj, że magazyn danych jest jednym z typów bazy danych. |

    ![Zrzut ekranu przedstawia okienko SQL Data Warehouse, w którym można wprowadzić te wartości.](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Wybierz pozycję **Serwer** , aby utworzyć i skonfigurować nowy serwer dla nowej bazy danych. Wypełnij **formularz nowego serwera** , używając następujących informacji:

    | Ustawienie                | Sugerowana wartość          | Opis                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Nazwa serwera**        | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |
    | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa           | Prawidłowe nazwy logowania można znaleźć w temacie [identyfikatory baz danych](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Hasło**           | Dowolne prawidłowe hasło       | Hasło musi mieć co najmniej osiem znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
    | **Lokalizacja**           | Dowolna prawidłowa lokalizacja       | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

    ![Utwórz serwer](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Wybierz pozycję **Wybierz** .

6. Wybierz pozycję **poziom wydajności** , aby określić, czy magazyn danych to Gen1, czy Gen2, oraz liczbę jednostek magazynu danych.

7. Na potrzeby tego samouczka wybierz pozycję SQL Pool **Gen2** . Suwak jest domyślnie ustawiony na **DW1000c** .  Spróbuj przesunąć go w górę i w dół, aby zobaczyć, jak działa.

    ![konfigurowanie wydajności](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Wybierz przycisk **Zastosuj** .
9. W bloku aprowizacji wybierz **Sortowanie** dla pustej bazy danych. Na potrzeby tego samouczka użyj wartości domyślnej. Aby uzyskać więcej informacji na temat sortowań, zobacz [Sortowania](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

10. Po ukończeniu formularza wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną bazy danych. Aprowizacja zajmuje kilka minut.

11. Na pasku narzędzi wybierz pozycję **Powiadomienia** , aby monitorować proces wdrażania.
  
     ![Zrzut ekranu przedstawia Azure Portal z otwartym okienkiem powiadomień z wdrożeniem w toku.](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Zapora na poziomie serwera, która uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem lub dowolnymi bazami danych na serwerze. Aby umożliwić łączność, możesz dodać reguły zezwalające na połączenia dla konkretnych adresów IP.  Wykonaj następujące kroki, aby utworzyć [regułę zapory na poziomie serwera](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) dla Twojego adresu IP klienta.

> [!NOTE]
> Usługa Azure Synapse Analytics komunikuje się przez port 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.

1. Po zakończeniu wdrażania wybierz pozycję **bazy danych SQL** w menu po lewej stronie, a następnie wybierz pozycję **mySampleDatabase** na stronach **bazy danych SQL** . Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20180430.Database.Windows.NET** ) i opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera w celu nawiązania połączenia z serwerem i jego bazami danych w kolejnych przewodnikach Szybki start. Następnie wybierz nazwę serwera, aby otworzyć ustawienia serwera.

    ![znajdowanie nazwy serwera](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Wybierz nazwę serwera, aby otworzyć ustawienia serwera.

    ![ustawienia serwera](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Wybierz pozycję **Pokaż ustawienia zapory** . Zostanie otwarta strona **Ustawienia zapory** dla serwera.

    ![reguła zapory serwera](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

6. Wybierz pozycję **Zapisz** . Reguła zapory na poziomie serwera jest tworzona dla bieżącego adresu IP otwierającego port 1433 na serwerze.

7. Wybierz przycisk **OK** , a następnie zamknij stronę **Ustawienia zapory** .

Teraz można nawiązać połączenie z serwerem i jego magazynami danych przy użyciu tego adresu IP. Połączenie działa z programu SQL Server Management Studio lub dowolnego innego narzędzia. Przy łączeniu się używaj wcześniej utworzonego konta administratora serwera.  

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Wybierz pozycję **wyłączone** na tej stronie, a następnie wybierz pozycję **Zapisz** , aby wyłączyć zaporę dla wszystkich usług platformy Azure.

## <a name="get-the-fully-qualified-server-name"></a>Uzyskiwanie w pełni kwalifikowanej nazwy serwera

Uzyskaj w pełni kwalifikowaną nazwę serwera dla serwera w Azure Portal. Nazwa ta będzie używana później przy nawiązywaniu połączenia z serwerem.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Azure Synapse Analytics** z menu po lewej stronie, a następnie wybierz swoją bazę danych ze strony **Analiza usługi Azure Synapse** .
3. W okienku **Essentials** na stronie bazy danych w witrynie Azure Portal zlokalizuj i skopiuj **nazwę serwera** . W tym przykładzie w pełni kwalifikowana nazwa to mynewserver-20180430.database.windows.net.

    ![informacje o połączeniu](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Nawiąż połączenie z serwerem jako administrator serwera

Ta sekcja używa [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) do nawiązywania połączenia z serwerem.

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

    | Ustawienie        | Sugerowana wartość                            | Opis                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Typ serwera    | Aparat bazy danych                            | Ta wartość jest wymagana                                       |
    | Nazwa serwera    | W pełni kwalifikowana nazwa serwera            | Nazwa powinna wyglądać następująco: **mynewserver-20180430.Database.Windows.NET** . |
    | Uwierzytelnianie | Uwierzytelnianie programu SQL Server                  | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
    | Zaloguj się          | Konto administratora serwera                   | To konto określono podczas tworzenia serwera. |
    | Hasło       | Hasło konta administratora serwera | To hasło określono podczas tworzenia serwera. |

    ![łączenie z serwerem](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Wybierz pozycję **Połącz** . W programie SSMS zostanie otwarte okno Eksplorator obiektów.

4. W Eksploratorze obiektów rozwiń pozycję **Bazy danych** . Następnie rozwiń węzły **Systemowe bazy danych** i **master** , aby wyświetlić obiekty w bazie danych master.  Rozwiń węzeł **mySampleDatabase** , aby wyświetlić obiekty w nowej bazie danych.

    ![obiekty bazy danych](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Tworzenie użytkownika do ładowania danych

Konto administratora serwera jest przeznaczone do wykonywania operacji zarządzania i nie jest odpowiednie do wykonywania zapytań względem danych użytkownika. Operacja ładowania danych bardzo obciąża pamięć. Maksymalne wartości pamięci są definiowane zgodnie z konfiguracją [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) i [klasą zasobów](resource-classes-for-workload-management.md) .

Najlepszym rozwiązaniem jest utworzenie identyfikatora logowania i użytkownika, które są przeznaczone do ładowania danych. Następnie należy dodać użytkownika ładującego do [klasy zasobów](resource-classes-for-workload-management.md), która umożliwia odpowiednią maksymalną alokację pamięci.

Obecnie łączysz się jako administrator serwera, dlatego możesz tworzyć identyfikatory logowania i użytkowników. Wykonaj następujące czynności, aby utworzyć identyfikator logowania i użytkownika o nazwie **LoaderRC20** . Następnie przypisz tego użytkownika do klasy zasobów **staticrc20** .

1. W programie SSMS kliknij prawym przyciskiem myszy pozycję **Master** , aby wyświetlić menu rozwijane, a następnie wybierz pozycję **nowe zapytanie** . Otworzy się okno nowego zapytania.

    ![Nowe zapytanie w bazie danych master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. W oknie zapytania wprowadź następujące polecenia języka T-SQL, aby utworzyć identyfikator logowania i użytkownika o nazwie LoaderRC20, podając własne hasło zamiast hasła „a123STRONGpassword!”.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Wybierz pozycję **Wykonaj** .

4. Kliknij prawym przyciskiem myszy pozycję **mySampleDataWarehouse** i wybierz pozycję **Nowe zapytanie** . Zostanie otwarte okno nowego zapytania.  

    ![Nowe zapytanie dotyczące przykładowego magazynu danych](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Wprowadź następujące polecenia języka T-SQL, aby utworzyć użytkownika bazy danych o nazwie LoaderRC20 dla identyfikatora logowania LoaderRC20. Drugi wiersz przyznaje nowemu użytkownikowi uprawnienia kontrolne (CONTROL) do nowego magazynu danych.  Te uprawnienia dają użytkownikowi możliwości podobne do tych, które miałby jako właściciel bazy danych. Trzeci wiersz dodaje nowego użytkownika jako członka [klasy zasobów](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Wybierz pozycję **Wykonaj** .

## <a name="connect-to-the-server-as-the-loading-user"></a>Nawiązywanie połączenia z serwerem jako użytkownik ładujący

Pierwszym krokiem do załadowania danych jest zalogowanie się jako użytkownik LoaderRC20.  

1. W Eksplorator obiektów wybierz menu rozwijane **Połącz** i wybierz pozycję **aparat bazy danych** . Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem** .

    ![Nawiązywanie połączenia za pomocą nowego konta logowania](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Wprowadź w pełni kwalifikowaną nazwę serwera i jako nazwę logowania wprowadź identyfikator **LoaderRC20** .  Wprowadź hasło dla użytkownika LoaderRC20.

3. Wybierz pozycję **Połącz** .

4. Gdy połączenie będzie gotowe, w Eksploratorze obiektów zobaczysz dwa połączenia z serwerem. Jedno połączenie jako ServerAdmin i jedno połączenie jako MedRCLogin.

    ![Połączenie nawiązane pomyślnie](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Tworzenie tabel dla przykładowych danych

Wszystko jest gotowe do rozpoczęcia procesu ładowania danych do nowego magazynu danych. W tej części samouczka pokazano, jak użyć instrukcji COPY do załadowania zestawu danych cab w Nowym Jorku z obiektu BLOB usługi Azure Storage. Aby dowiedzieć się, jak pobrać dane do usługi Azure Blob Storage lub załadować je bezpośrednio ze źródła, zobacz [Omówienie ładowania](design-elt-data-loading.md).

Uruchom następujące skrypty SQL i podaj informacje o danych, które chcesz załadować. Informacje te obejmują obecną lokalizację danych, format zawartości danych i definicję tabel dla danych.

1. W poprzedniej sekcji zalogowano się do magazynu danych jako użytkownik LoaderRC20. W programie SSMS kliknij prawym przyciskiem myszy połączenie użytkownika LoaderRC20, a następnie wybierz polecenie **Nowe zapytanie** .  Zostanie otwarte okno nowego zapytania.

    ![Okno nowego zapytania ładującego](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Porównaj to okno zapytania z poprzednią ilustracją.  Sprawdź, czy okno nowego zapytania zostało uruchomione dla użytkownika LoaderRC20 i czy zapytania są wykonywane względem bazy danych MySampleDataWarehouse. Wszystkie kroki ładowania wykonaj w tym oknie zapytania.

7. Uruchom następujące instrukcje języka T-SQL, aby utworzyć tabele:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Ładowanie danych do magazynu danych

Ta sekcja używa [instrukcji Copy do ładowania](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) przykładowych danych z Azure Storage BLOB.  

> [!NOTE]
> W tym samouczku dane są ładowane bezpośrednio do tabeli końcowej. Zwykle można ładować do tabeli przemieszczania dla obciążeń produkcyjnych. Gdy dane znajdują się w tabeli przejściowej, można wykonać wszelkie niezbędne przekształcenia. 

1. Aby załadować dane, uruchom następujące instrukcje:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Wyświetlaj dane podczas ładowania. Ładujesz kilka gigabajtów danych i skompresujesz ją na wysoce wydajne klastrowane indeksy magazynu kolumn. Uruchom następujące zapytanie korzystające z dynamicznych widoków zarządzania (DMV), aby wyświetlić stan ładowania.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Wyświetl wszystkie zapytania systemowe.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Korzystaj z danych poprawnie załadowanych do magazynu danych.

    ![Wyświetlanie załadowanych tabel](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za zasoby obliczeniowe i dane załadowane do magazynu danych. Są one rozliczane osobno.

* Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Dzięki wstrzymaniu obliczeń opłaty będą naliczane tylko za przechowywanie danych. Obliczenia możesz wznowić w dowolnym momencie, gdy będziesz chcieć pracować z danymi.
* Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz magazyn danych.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy magazyn danych jest wstrzymany, widoczny jest przycisk **Uruchom** .  Aby wznowić obliczenia, wybierz pozycję **Uruchom** .

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń** .

4. Aby usunąć utworzony serwer, wybierz pozycję **mynewserver-20180430.Database.Windows.NET** na poprzednim obrazie, a następnie wybierz pozycję **Usuń** .  Należy zachować ostrożność, ponieważ usunięcie serwera spowoduje usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz pozycję Moja **zasobów** , a następnie wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia magazynu danych i tworzenia użytkownika wyznaczonego do ładowania danych. Użyto prostej [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) do załadowania danych do magazynu danych.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
>
> * Utworzenie magazynu danych w witrynie Azure Portal
> * Konfigurowanie reguły zapory na poziomie serwera w witrynie Azure Portal
> * Nawiązanie połączenia z magazynem danych za pomocą programu SSMS
> * Utworzenie użytkownika wyznaczonego do ładowania danych
> * Utworzono tabele dla przykładowych danych
> * Użyto instrukcji COPY T-SQL do załadowania danych do magazynu danych
> * Wyświetlenie postępu ładowania danych

Przejdź do omówienia opracowywania, aby dowiedzieć się, jak przeprowadzić migrację istniejącej bazy danych do usługi Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Podejmowanie decyzji projektowych dotyczących migracji istniejącej bazy danych do usługi Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Aby uzyskać więcej przykładów i odwołań, zapoznaj się z następującą dokumentacją:

- [Kopiuj dokumentację referencyjną instrukcji](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Kopiuj przykłady dla każdej metody uwierzytelniania](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Kopiuj Przewodnik Szybki Start dla pojedynczej tabeli](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
