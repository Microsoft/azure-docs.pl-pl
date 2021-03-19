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
ms.date: 03/18/2020
ms.openlocfilehash: f03fa84c02c4b3894efe069289b0ecbb9e90dfdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654632"
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

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
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
    
    > [!NOTE]
    > Obszar roboczy z obsługą dedykowanej puli SQL (dawniej SQL DW) można zidentyfikować za pomocą etykietki narzędzia w centrum danych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych na koncie usługi Azure Storage](get-started-analyze-storage.md)
