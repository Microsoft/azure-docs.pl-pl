---
title: 'Samouczek: ładowanie danych dotyczących taksówek w Nowym Jorku'
description: Samouczek używa Azure Portal i SQL Server Management Studio do ładowania danych taksówek w Nowym Jorku z obiektu blob platformy Azure na Synapse SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7ede40aba8e2d36e4262b4bc89a35f5d67079e0e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567512"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Samouczek: ładowanie zestawu danych New York Taxicab

W tym samouczku instrukcja [COPY jest używana do](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) ładowania zestawu danych New York Taxicab z Azure Blob Storage konta. W tym samouczku użyto witryny [Azure Portal](https://portal.azure.com) i programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS), aby wykonać następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie użytkownika wyznaczonego do ładowania danych
> * Tworzenie tabel dla przykładowego zestawu danych 
> * Ładowanie danych do magazynu danych za pomocą instrukcji COPY języka T-SQL
> * Wyświetlanie postępu ładowania danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).  

W tym samouczku przyjęto założenie, że utworzono już dedykowaną pulę SQL z następującego [samouczka:](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin).

## <a name="create-a-user-for-loading-data"></a>Tworzenie użytkownika do ładowania danych

Konto administratora serwera jest przeznaczone do wykonywania operacji zarządzania i nie jest odpowiednie do wykonywania zapytań względem danych użytkownika. Operacja ładowania danych bardzo obciąża pamięć. Maksymalne wartości pamięci są definiowane zgodnie ze skonfigurowanymi jednostkami [magazynu](what-is-a-data-warehouse-unit-dwu-cdwu.md) danych [i klasą](resource-classes-for-workload-management.md) zasobów.

Najlepszym rozwiązaniem jest utworzenie identyfikatora logowania i użytkownika, które są przeznaczone do ładowania danych. Następnie należy dodać użytkownika ładującego do [klasy zasobów](resource-classes-for-workload-management.md), która umożliwia odpowiednią maksymalną alokację pamięci.

Połącz się jako administrator serwera, aby można było tworzyć nazwy logowania i użytkowników. Wykonaj następujące czynności, aby utworzyć identyfikator logowania i użytkownika o nazwie **LoaderRC20**. Następnie przypisz tego użytkownika do klasy zasobów **staticrc20**.

1. W programie SSMS wybierz prawym przyciskiem myszy **pozycję master,** aby wyświetlić menu rozwijane, a następnie wybierz **pozycję Nowe zapytanie.** Otworzy się okno nowego zapytania.

    ![Nowe zapytanie w bazie danych master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. W oknie zapytania wprowadź następujące polecenia języka T-SQL, aby utworzyć identyfikator logowania i użytkownika o nazwie LoaderRC20, podając własne hasło zamiast hasła „a123STRONGpassword!”.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Wybierz pozycję **Wykonaj**.

4. Kliknij prawym przyciskiem myszy pozycję **mySampleDataWarehouse** i wybierz pozycję **Nowe zapytanie**. Zostanie otwarte okno nowego zapytania.  

    ![Nowe zapytanie dotyczące przykładowego magazynu danych](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Wprowadź następujące polecenia języka T-SQL, aby utworzyć użytkownika bazy danych o nazwie LoaderRC20 dla identyfikatora logowania LoaderRC20. Drugi wiersz przyznaje nowemu użytkownikowi uprawnienia kontrolne (CONTROL) do nowego magazynu danych.  Te uprawnienia dają użytkownikowi możliwości podobne do tych, które miałby jako właściciel bazy danych. Trzeci wiersz dodaje nowego użytkownika jako członka [klasy zasobów](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Wybierz pozycję **Wykonaj**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Nawiązywanie połączenia z serwerem jako użytkownik ładujący

Pierwszym krokiem do załadowania danych jest zalogowanie się jako użytkownik LoaderRC20.  

1. W Eksplorator obiektów wybierz menu rozwijane **Połącz,** a następnie wybierz pozycję **Aparat bazy danych**. Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem**.

    ![Nawiązywanie połączenia za pomocą nowego konta logowania](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Wprowadź w pełni kwalifikowaną nazwę serwera i jako nazwę logowania wprowadź identyfikator **LoaderRC20**.  Wprowadź hasło dla użytkownika LoaderRC20.

3. Wybierz pozycję **Połącz**.

4. Gdy połączenie będzie gotowe, w Eksploratorze obiektów zobaczysz dwa połączenia z serwerem. Jedno połączenie jako ServerAdmin i jedno połączenie jako MedRCLogin.

    ![Połączenie nawiązane pomyślnie](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Tworzenie tabel dla przykładowych danych

Wszystko jest gotowe do rozpoczęcia procesu ładowania danych do nowego magazynu danych. W tej części samouczka pokazano, jak załadować zestaw danych taksówek w Nowym Jorku z obiektu blob usługi Azure Storage za pomocą instrukcji COPY. Aby dowiedzieć się, jak w przyszłości pobrać dane do Azure Blob Storage lub załadować je bezpośrednio ze źródła, zobacz [omówienie ładowania](design-elt-data-loading.md).

Uruchom następujące skrypty SQL i określ informacje o danych, które chcesz załadować. Informacje te obejmują obecną lokalizację danych, format zawartości danych i definicję tabel dla danych.

1. W poprzedniej sekcji zalogowano się do magazynu danych jako użytkownik LoaderRC20. W programie SSMS kliknij prawym przyciskiem myszy połączenie użytkownika LoaderRC20, a następnie wybierz polecenie **Nowe zapytanie**.  Zostanie otwarte okno nowego zapytania.

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

W tej sekcji użyto [instrukcji COPY, aby załadować](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) przykładowe dane z Azure Storage Blob.  

> [!NOTE]
> W tym samouczku dane są ładowane bezpośrednio do tabeli końcowej. Zazwyczaj ładuje się do tabeli przejściowej dla obciążeń produkcyjnych. Gdy dane znajdują się w tabeli przejściowej, można wykonać wszelkie niezbędne przekształcenia. 

1. Uruchom następujące instrukcje, aby załadować dane:

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

2. Wyświetlaj dane podczas ładowania. Ładujesz kilka kb/s danych i kompresujesz je do wysoce wydajnych klastrowanych indeksów magazynu kolumn. Uruchom następujące zapytanie korzystające z dynamicznych widoków zarządzania (DMV), aby wyświetlić stan ładowania.

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

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz swój magazyn danych.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Gdy magazyn danych jest wstrzymany, widoczny jest przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom.**

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe ani magazynowe, wybierz pozycję **Usuń.**

4. Aby usunąć utworzony serwer, wybierz pozycję **mynewserver-20180430.database.windows.net** na poprzedniej ilustracji, a następnie wybierz pozycję **Usuń.**  Należy zachować ostrożność, ponieważ usunięcie serwera spowoduje usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz **grupę zasobów myResourceGroup,** a następnie wybierz **pozycję Usuń grupę zasobów.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia magazynu danych i tworzenia użytkownika wyznaczonego do ładowania danych. Za pomocą prostej instrukcji [COPY załadowaliśmy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples) dane do magazynu danych.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
>
> * Utworzenie magazynu danych w witrynie Azure Portal
> * Konfigurowanie reguły zapory na poziomie serwera w witrynie Azure Portal
> * Nawiązanie połączenia z magazynem danych za pomocą programu SSMS
> * Utworzenie użytkownika wyznaczonego do ładowania danych
> * Utworzono tabele dla przykładowych danych
> * Ładowanie danych do magazynu danych za pomocą instrukcji COPY języka T-SQL
> * Wyświetlenie postępu ładowania danych

Aby dowiedzieć się, jak przeprowadzić migrację istniejącej bazy danych do bazy danych, należy przejść do przeglądu Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Decyzje projektowe dotyczące migrowania istniejącej bazy danych do Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Aby uzyskać więcej przykładów i odwołań ładowania, zobacz następującą dokumentację:

- [Dokumentacja referencyjna instrukcji COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Przykłady copy dla każdej metody uwierzytelniania](./quickstart-bulk-load-copy-tsql-examples.md)
- [Copy quickstart for a single table (Szybki start: kopiowanie pojedynczej tabeli)](./quickstart-bulk-load-copy-tsql.md)
