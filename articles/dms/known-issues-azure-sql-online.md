---
title: 'Znane problemy: migracje online do SQL Database'
titleSuffix: Azure Database Migration Service
description: Informacje o znanych problemach/ograniczeniach migracji z migracją online w celu Azure SQL Database korzystania z Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962471"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Znane problemy/ograniczenia migracji z migracją online do Azure SQL Database

Znane problemy i ograniczenia związane z migracją online z SQL Server do Azure SQL Database zostały opisane poniżej.

> [!IMPORTANT]
> W przypadku migracji w trybie online SQL Server do Azure SQL Database migracja SQL_variant typów danych nie jest obsługiwana.

### <a name="migration-of-temporal-tables-not-supported"></a>Migracja tabel danych czasowych nie jest obsługiwana

**Objaw**

Jeśli źródłowa baza danych składa się z co najmniej jednej tabeli czasowej, migracja bazy danych nie powiedzie się w trakcie operacji "pełne ładowanie danych" i może zostać wyświetlony następujący komunikat:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Przykład błędów tabeli danych czasowych](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Obejście**

Skorzystaj z poniższych instrukcji.

1. Znajdź tabele danych czasowych w schemacie źródłowym przy użyciu poniższego zapytania.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Wyklucz te tabele z bloku **Konfigurowanie ustawień migracji** , w którym można określić tabele do migracji.

3. Uruchom ponownie działanie migracji.

**Zasoby**

Aby uzyskać więcej informacji, zobacz [tabele](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)danych czasowych artykułu.

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migracja tabel zawiera jedną lub więcej kolumn z typem danych hierarchyid

**Objaw**

Podczas operacji "pełnego ładowania danych" może zostać wyświetlony wyjątek SQL z sugestią "ntext jest niezgodna z hierarchyid":

![przykład hierarchyid błędów](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Obejście**

Skorzystaj z poniższych instrukcji.

1. Znajdź tabele użytkownika zawierające kolumny z typem danych hierarchyid przy użyciu poniższego zapytania.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Wyklucz te tabele z bloku **Konfigurowanie ustawień migracji** , w którym można określić tabele do migracji.

3. Uruchom ponownie działanie migracji.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Błędy migracji z różnymi naruszeniami integralności z aktywnymi wyzwalaczami w schemacie podczas "pełnego ładowania danych" lub "przyrostowej synchronizacji danych"

**Obejście**

Skorzystaj z poniższych instrukcji.

1. Znajdź wyzwalacze, które są obecnie aktywne w źródłowej bazie danych, przy użyciu poniższego zapytania:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Wyłącz Wyzwalacze w źródłowej bazie danych, korzystając z kroków opisanych w artykule [wyłączanie wyzwalacza (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Uruchom ponownie działanie migracji.

### <a name="support-for-lob-data-types"></a>Obsługa typów danych LOB

**Objaw**

Jeśli długość kolumny dużego obiektu (LOB) jest większa niż 32 KB, dane mogą zostać obcięte w miejscu docelowym. Możesz sprawdzić długość kolumny LOB przy użyciu poniższego zapytania:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Obejście**

Jeśli masz kolumnę LOB o rozmiarze większym niż 32 KB, skontaktuj się z zespołem inżynieryjnym na stronie [zadawanie migracji usługi Azure Database](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemy z kolumnami znaczników czasu

**Objaw**

Azure Database Migration Service nie migruje źródłowej wartości sygnatury czasowej; Zamiast tego Azure Database Migration Service generuje nową wartość sygnatury czasowej w tabeli docelowej.

**Obejście**

Jeśli potrzebujesz Azure Database Migration Service do migrowania dokładnej wartości sygnatury czasowej przechowywanej w tabeli źródłowej, skontaktuj się z zespołem inżynieryjnym w temacie [zadawanie migracji usługi Azure Database](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Błędy migracji danych nie zapewniają dodatkowych informacji w bloku stanu szczegółowym bazy danych

**Objaw**

Po przeniesieniu błędów migracji w widoku stanu szczegółów baz danych, wybranie linku **błędy migracji danych** na górnej Wstążce może nie dostarczyć dodatkowych informacji specyficznych dla błędów migracji.

![Przykłady błędów migracji danych](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Obejście**

Aby uzyskać szczegółowe informacje o niepowodzeniu, wykonaj następujące czynności.

1. Zamknij blok szczegółowy stan bazy danych, aby wyświetlić ekran działania migracji.

     ![ekran działania migracji](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Wybierz pozycję **Zobacz szczegóły błędu** , aby wyświetlić określone komunikaty o błędach, które ułatwiają rozwiązywanie problemów z migracją.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Typ danych geografii nie jest obsługiwany w migracji SQLDB online

**Objaw**

Migracja kończy się niepowodzeniem z komunikatem o błędzie zawierającym następujący tekst:

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**Obejście**

Chociaż Azure Database Migration Service obsługuje typ danych Geografia dla migracji w trybie offline do Azure SQL Database, w przypadku migracji w trybie online typ danych geografii nie jest obsługiwany. Spróbuj użyć alternatywnych metod, aby zmienić typ danych w lokalizacji źródłowej na obsługiwany, przed podjęciem próby użycia Azure Database Migration Service do migracji w trybie online tej bazy danych.

### <a name="supported-editions"></a>Obsługiwane wersje

**Objaw**

Migracja kończy się niepowodzeniem z komunikatem o błędzie zawierającym następujący tekst:

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**Obejście**

Obsługa migracji w trybie online do Azure SQL Database przy użyciu Azure Database Migration Service rozszerza się tylko na wersje Enterprise, standard i developer. Przed rozpoczęciem procesu migracji upewnij się, że używasz obsługiwanej wersji.