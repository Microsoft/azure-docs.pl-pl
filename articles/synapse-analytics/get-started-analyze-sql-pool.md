---
title: 'Samouczek: wprowadzenie do analizowania danych za pomocą dedykowanych pul SQL'
description: W tym samouczku użyjesz przykładowych danych dotyczących taksówek w NYC, aby poznać możliwości analityczne puli SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536336"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analizowanie danych za pomocą dedykowanych pul SQL

W tym samouczku użyjesz danych dotyczących taksówek w Stanie Nowy Jork, aby poznać możliwości dedykowanej puli SQL.

## <a name="create-a-dedicated-sql-pool"></a>Tworzenie dedykowanej puli SQL

1. W Synapse Studio okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami SQL.**
1. Wybierz **pozycję Nowy**
1. W **przypadku nazwy puli SQL** wybierz pozycję **SQLPOOL1**
1. W **przypadku opcji Poziom wydajności** wybierz pozycję **DW100C**
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Dedykowana pula SQL będzie gotowa w ciągu kilku minut. 

Dedykowana pula SQL jest skojarzona z bazą danych SQL o nazwie **SQLPOOL1.**
1. Przejdź do obszaru **roboczego**  >  **danych.**
1. Powinna zostać wyświetlony baza danych o nazwie **SQLPOOL1.** Jeśli nie jest on wyświetlony, kliknij pozycję **Odśwież.**

Dedykowana pula SQL zużywa rozliczane zasoby, o ile jest aktywna. Pulę można wstrzymać później, aby obniżyć koszty.

> [!NOTE] 
> Podczas tworzenia nowej dedykowanej puli SQL (dawniej SQL DW) w obszarze roboczym zostanie otwarta dedykowana strona inicjowania obsługi puli SQL. Aprowizowanie odbywa się na serwerze logicznym SQL.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Ładowanie danych dotyczących taksówek w NYC do puli SQLPOOL1

1. W Synapse Studio przejdź do **centrum** Opracowywanie, kliknij przycisk, aby dodać nowy zasób, a następnie utwórz nowy skrypt **+** SQL.
1. Wybierz pulę "SQLPOOL1" (pula utworzona w [KROKU 1](./get-started-create-workspace.md) tego samouczka) na liście rozwijanej "Połącz z" nad skryptem.
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

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Kliknij przycisk Uruchom, aby wykonać skrypt.
1. Ten skrypt zakończy się w mniej niż 60 sekund. Ładuje 2 miliony wierszy danych dotyczących taksówek w Nowy Jorku do tabeli o nazwie **dbo. Podróż**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Eksplorowanie danych dotyczących taksówek w NYC w dedykowanej puli SQL

1. W Synapse Studio przejdź do **centrum** danych.
1. Przejdź do **sekcji SQLPOOL1 Tables (Tabele SQLPOOL1).**  >   
3. Kliknij prawym przyciskiem myszy **dbo. Tabela NYCTaxiTripSmall i** wybierz pozycję **New SQL Script** Select  >  **TOP 100 Rows (Nowy skrypt SQL) Wybierz pozycję TOP 100 Rows (100 najwyższych wierszy).**
4. Zaczekaj, aż zostanie utworzony i uruchomiony nowy skrypt SQL.
5. Zwróć uwagę, że w górnej części skryptu SQL Połącz **z** jest automatycznie ustawiana pula SQL o nazwie **SQLPOOL1.**
6. Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    To zapytanie pokazuje, jak łączna odległość podróży i średnia odległość podróży odnoszą się do liczby pasażerów.
1. W oknie wyników skryptu SQL zmień **widok** na **Wykres,** aby wyświetlić wizualizację wyników jako wykres liniowy.
    
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych na koncie usługi Azure Storage](get-started-analyze-storage.md)
