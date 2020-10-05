---
title: Tworzenie i używanie tabel zewnętrznych w programie SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji dowiesz się, jak tworzyć tabele zewnętrzne i korzystać z nich na żądanie SQL (wersja zapoznawcza).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 182deba959144f6a3992bb41243f29023bad5e5c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289331"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Tworzenie i używanie tabel zewnętrznych w programie SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tej sekcji dowiesz się, jak tworzyć [tabele zewnętrzne](develop-tables-external-tables.md) i korzystać z nich na żądanie SQL (wersja zapoznawcza). Tabele zewnętrzne są przydatne, gdy chcesz kontrolować dostęp do danych zewnętrznych w programie SQL na żądanie, a jeśli chcesz używać narzędzi, takich jak Power BI, w połączeniu z SQL na żądanie. Tabele zewnętrzne mogą uzyskać dostęp do dwóch typów magazynu:
- Magazyn publiczny, w którym użytkownicy uzyskują dostęp do publicznych plików magazynu.
- Chroniony magazyn, w którym użytkownicy uzyskują dostęp do plików magazynu przy użyciu poświadczeń SAS, tożsamości usługi Azure AD lub zarządzanej tożsamości obszaru roboczego Synapse.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest utworzenie bazy danych, w której zostaną utworzone tabele. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie następujących obiektów, które są używane w tym przykładzie:
- POŚWIADCZENIA w zakresie bazy danych `sqlondemand` , które umożliwiają dostęp do `https://sqlondemandstorage.blob.core.windows.net` konta usługi Azure Storage chronionego przez SAS.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- ZEWNĘTRZNE źródło danych `sqlondemanddemo` , które odwołuje się do demonstracyjnego konta magazynu chronionego za pomocą klucza SAS i zewnętrznego źródła danych `YellowTaxi` , które odwołuje się do publicznie dostępnego konta usługi Azure Storage w lokalizacji `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Formaty plików `QuotedCSVWithHeaderFormat` i `ParquetFormat` opisujące typy plików CSV i Parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Zapytania w tym artykule będą wykonywane w przykładowej bazie danych i używają tych obiektów. 

## <a name="create-an-external-table-on-protected-data"></a>Tworzenie tabeli zewnętrznej dla chronionych danych

Możesz tworzyć tabele zewnętrzne, które uzyskują dostęp do danych na koncie usługi Azure Storage, które umożliwiają dostęp użytkownikom przy użyciu tożsamości usługi Azure AD lub klucza SAS. Tabele zewnętrzne można tworzyć w ten sam sposób, w jaki tworzysz regularne SQL Server tabele zewnętrzne. 

Następujące zapytanie tworzy tabelę zewnętrzną, która odczytuje plik *population.csv* z SynapseSQL demonstracyjnego konta usługi Azure Storage, do którego odwołuje się `sqlondemanddemo` Źródło danych i jest chroniony przy użyciu poświadczeń o zakresie bazy danych o nazwie `sqlondemand` . 

Poświadczenia w zakresie źródła danych i bazy danych są tworzone w [skrypcie Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Tworzenie tabeli zewnętrznej na danych publicznych

Można utworzyć tabele zewnętrzne, które odczytają dane z plików umieszczonych w publicznie dostępnej usłudze Azure Storage. Ten [skrypt instalacji](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) spowoduje utworzenie definicji publicznego zewnętrznego źródła danych i pliku Parquet, który jest używany w następującej kwerendzie:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Korzystanie z tabeli zewnętrznej

W zapytaniach można używać [tabel zewnętrznych](develop-tables-external-tables.md) w taki sam sposób, w jaki są one używane w kwerendach SQL Server.

Poniższe zapytanie ilustruje tę wartość przy użyciu tabeli zewnętrznej *wypełniania* utworzonej w poprzedniej sekcji. Zwraca nazwy kraju/regionu z populacją w 2019 w kolejności malejącej.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu przechowywania wyników zapytania do magazynu, zapoznaj się z [wynikami zapytania Store](../sql/create-external-table-as-select.md) w artykule magazyn.
