---
title: 'Szybki Start: ładowanie danych zbiorczo przy użyciu jednej instrukcji języka T-SQL'
description: Ładowanie zbiorcze danych za pomocą instrukcji COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83b5804888379316b855c36f803f646cec102d9e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024589"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Szybki Start: ładowanie danych przy użyciu instrukcji COPY

W tym przewodniku szybki start załadujesz dane do dedykowanej puli SQL przy użyciu prostej i elastycznej [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) do pozyskiwania danych o wysokiej przepływności. Instrukcja COPY jest zalecanym narzędziem do ładowania, ponieważ pozwala bezproblemowo i elastycznie ładować dane przez zapewnienie funkcjonalności:

- Zezwalaj niższym uprzywilejowanym użytkownikom na ładowanie bez konieczności stosowania ścisłych kontroli w magazynie danych
- Wykorzystanie tylko jednej instrukcji języka T-SQL bez konieczności tworzenia dodatkowych obiektów bazy danych
- Wykorzystanie bardziej precyzyjnego modelu uprawnień bez uwidaczniania kluczy konta magazynu za pomocą sygnatur dostępu współdzielonego (SAS)
- Określ inne konto magazynu dla lokalizacji ERRORFILE (REJECTED_ROW_LOCATION)
- Dostosuj wartości domyślne dla każdej kolumny docelowej i Określ pola danych źródłowych do załadowania do określonych kolumn docelowych
- Określ niestandardowy terminator wiersza dla plików CSV
- Ciąg ucieczki, pole i ograniczniki wierszy dla plików CSV
- Wykorzystanie SQL Server formatach dat dla plików CSV
- Określanie symboli wieloznacznych i wielu plików w ścieżce lokalizacji magazynu

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już dedykowaną pulę SQL. Jeśli dedykowana Pula SQL nie została utworzona, Skorzystaj z przewodnika Szybki Start dotyczącego [tworzenia i łączenia z portalem](create-data-warehouse-portal.md) .

## <a name="set-up-the-required-permissions"></a>Skonfiguruj wymagane uprawnienia

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

W tym przykładzie będziemy ładować dane z nowego zestawu danych programu York. Załadujemy tabelę o nazwie rejs, która reprezentuje podróże w jednym roku. Uruchom następujące kroki, aby utworzyć tabelę:

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

## <a name="run-the-copy-statement"></a>Uruchom instrukcję COPY

Uruchom następującą instrukcję COPY, która spowoduje załadowanie danych z konta usługi Azure Blob Storage do tabeli rejsów.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorowanie obciążenia

Sprawdź, czy obciążenie jest wykonywane przez okresowe uruchamianie następującej kwerendy:

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

- Aby uzyskać najlepsze rozwiązania dotyczące ładowania danych, zobacz [najlepsze rozwiązania dotyczące ładowania danych](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Aby uzyskać informacje na temat sposobu zarządzania zasobami na potrzeby ładowania danych, zobacz [izolacja obciążenia](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
