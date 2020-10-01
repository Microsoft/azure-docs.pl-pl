---
title: 'Samouczek: Rozpoczynanie analizy danych za pomocą dedykowanych pul SQL'
description: W tym samouczku poznasz przykładowe dane z NYC taksówkami, aby poznać możliwości analityczne puli SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 11c11d3e43f15b569a74c282b15a95525cea7e7c
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620039"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analizowanie danych za pomocą dedykowanych pul SQL

Usługa Azure Synapse Analytics umożliwia analizowanie danych za pomocą dedykowanej puli SQL. W tym samouczku użyjemy danych z NYC taksówkami, aby poznać możliwości dedicted puli SQL.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>Załaduj dane z NYC taksówki do SQLDB1

1. W programie Synapse Studio przejdź do centrum **opracowywania** , a następnie utwórz nowy skrypt SQL.
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
1. Wykonanie tego skryptu zajmie około 1 minutę. Ładuje on 2 000 000 wierszy danych z NYC taksówki do tabeli o nazwie **dbo. Podróż**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Eksplorowanie danych z NYC taksówki w dedykowanej puli SQL

1. W programie Synapse Studio przejdź do centrum **danych** .
1. Przejdź do **SQLDB1**  >  **tabel**SQLDB1. Zobaczysz kilka załadowanych tabel.
1. Kliknij prawym przyciskiem myszy obiekt **dbo. Tabelę podróży** i wybierz pozycję **Nowy skrypt SQL**  >  **Wybierz pierwsze 100 wierszy**.
1. Poczekaj, aż zostanie utworzony i uruchomiony nowy skrypt SQL.
1. Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie **SQLDB1**.
1. Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby osób.
1. W oknie wyników skryptu SQL Zmień **Widok** na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu platformy Spark](get-started-analyze-spark.md)

