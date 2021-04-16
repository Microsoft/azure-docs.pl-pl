---
title: 'Szybki start: zbiorcze ładowanie danych przy użyciu pojedynczej instrukcji języka T-SQL'
description: Zbiorcze ładowanie danych przy użyciu instrukcji COPY
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2b41342ab7d267c37b8e68fdbcaa9d570034ac17
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568441"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Szybki start: zbiorcze ładowanie danych przy użyciu instrukcji COPY

W tym przewodniku Szybki start załadujesz zbiorczo dane do dedykowanej puli SQL przy użyciu prostej i elastycznej instrukcji [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) do pozyskiwania danych o wysokiej przepływności. Instrukcja COPY jest zalecanym narzędziem do ładowania, ponieważ umożliwia bezproblemowe i elastyczne ładowanie danych dzięki udostępnianiu funkcji:

- Zezwalaj użytkownikom z niższymi uprawnieniami na ładowanie bez konieczności ścisłego kontrolowania uprawnień w magazynie danych
- Korzystanie tylko z jednej instrukcji języka T-SQL bez konieczności tworzenia dodatkowych obiektów bazy danych
- Korzystanie z modelu uprawnień o wzorcu finer bez ujawniania kluczy konta magazynu przy użyciu sygnatur dostępu współdzielonego (SAS)
- Określ inne konto magazynu dla lokalizacji ERRORFILE (REJECTED_ROW_LOCATION)
- Dostosowywanie wartości domyślnych dla każdej kolumny docelowej i określanie pól danych źródłowych do załadowania do określonych kolumn docelowych
- Określanie niestandardowego terminatora wierszy dla plików CSV
- Ograniczniki ciągu ucieczki, pola i wiersza dla plików CSV
- Korzystanie SQL Server formatów daty dla plików CSV
- Określanie symboli wieloznacznych i wielu plików w ścieżce lokalizacji magazynu

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start założono, że masz już dedykowaną pulę SQL. Jeśli nie utworzono dedykowanej puli SQL, skorzystaj z przewodnika Szybki start Tworzenie i łączenie [—](create-data-warehouse-portal.md) portal.

## <a name="set-up-the-required-permissions"></a>Konfigurowanie wymaganych uprawnień

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Tworzenie tabeli docelowej

W tym przykładzie będziemy ładować dane z zestawu danych nowojorskich taksówek. Załadujmy tabelę o nazwie Trip (Podróż), która reprezentuje przejazdy taksówką w ciągu jednego roku. Uruchom następujące kroki, aby utworzyć tabelę:

```sql
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
```

## <a name="run-the-copy-statement"></a>Uruchamianie instrukcji COPY

Uruchom następującą instrukcje COPY, która załaduje dane z konta usługi Azure Blob Storage do tabeli Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorowanie obciążenia

Sprawdź, czy obciążenie postępuje, okresowo uruchamiając następujące zapytanie:

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
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać najlepsze rozwiązania dotyczące ładowania danych, zobacz [Best Practices for Loading Data](./guidance-for-loading-data.md)(Najlepsze rozwiązania dotyczące ładowania danych).
- Aby uzyskać informacje na temat zarządzania zasobami ładowania danych, zobacz [Izolacja obciążenia](./quickstart-configure-workload-isolation-tsql.md).