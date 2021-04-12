---
title: 'Samouczek: Rozpoczynanie analizy danych za pomocą dedykowanych pul SQL'
description: W tym samouczku poznasz przykładowe dane z NYC taksówkami, aby poznać możliwości analityczne puli SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 4588eee721a58a7e4f3366d0d325b48de0f56ae5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259816"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analizowanie danych za pomocą dedykowanych pul SQL

W tym samouczku użyjemy danych z NYC taksówkami, aby poznać możliwości dedykowanej puli SQL.

## <a name="create-a-dedicated-sql-pool"></a>Tworzenie dedykowanej puli SQL

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami SQL**.
1. Wybierz pozycję **Nowy**
1. W obszarze **Nazwa puli SQL** wybierz pozycję **SQLPOOL1**
1. Dla opcji **poziom wydajności** wybierz **DW100C**
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Dedykowana Pula SQL będzie gotowa w ciągu kilku minut. 

Dedykowana Pula SQL jest skojarzona z bazą danych SQL, która jest również nazywana **SQLPOOL1**.
1. Przejdź do   >  **obszaru roboczego** dane.
1. Powinna zostać wyświetlona baza danych o nazwie **SQLPOOL1**. Jeśli nie widzisz go, kliknij przycisk **Odśwież**.

Dedykowana Pula SQL zużywa zasoby do rozliczenia, o ile jest ona aktywna. Pulę można wstrzymać później, aby zmniejszyć koszty.

> [!NOTE] 
> Podczas tworzenia nowej dedykowanej puli SQL (dawniej SQL DW) w obszarze roboczym zostanie otwarta strona dedykowana obsługa administracyjna puli SQL. Inicjowanie obsługi administracyjnej odbywa się na logicznym serwerze SQL Server.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Załaduj dane z NYC taksówki do SQLPOOL1

1. W programie Synapse Studio przejdź do centrum **opracowywania** , kliknij **+** przycisk, aby dodać nowy zasób, a następnie utwórz nowy skrypt SQL.
1. Wybierz pulę "SQLPOOL1" (Pula utworzona w [kroku 1](./get-started-create-workspace.md) tego samouczka) na liście rozwijanej "Połącz z" powyżej skryptu.
1. Wprowadź następujący kod:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Kliknij przycisk Uruchom, aby wykonać skrypt.
1. Ten skrypt zakończy się w mniej niż 60 sekund. Ładuje on 2 000 000 wierszy danych z NYC taksówki do tabeli o nazwie **dbo. Podróż**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Eksplorowanie danych z NYC taksówki w dedykowanej puli SQL

1. W programie Synapse Studio przejdź do centrum **danych** .
1. Przejdź do   >  **tabel** SQLPOOL1. 
3. Kliknij prawym przyciskiem myszy obiekt **dbo. Tabelę podróży** i wybierz pozycję **Nowy skrypt SQL**  >  **Wybierz pierwsze 100 wierszy**.
4. Poczekaj, aż zostanie utworzony i uruchomiony nowy skrypt SQL.
5. Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie **SQLPOOL1**.
6. Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby osób.
1. W oknie wyników skryptu SQL Zmień **Widok** na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy.
    
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych na koncie usługi Azure Storage](get-started-analyze-storage.md)
