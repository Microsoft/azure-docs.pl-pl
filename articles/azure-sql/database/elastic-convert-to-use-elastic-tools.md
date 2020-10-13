---
title: Migrowanie istniejących baz danych w celu skalowania w poziomie
description: Konwertowanie baz danych podzielonej na fragmenty na korzystanie z narzędzi Elastic Database przez utworzenie Menedżera mapy fragmentu
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 95afd0b9117caca4b531c453417217d8a8de7c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443469"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrowanie istniejących baz danych w celu skalowania w poziomie
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Łatwo Zarządzaj istniejącymi bazami danych podzielonej na fragmenty skalowanymi w poziomie przy użyciu narzędzi (takich jak [Elastic Database Biblioteka klienta](elastic-database-client-library.md)). Najpierw przekonwertuj istniejący zestaw baz danych, aby użyć [Menedżera mapy fragmentu](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Omówienie

Aby przeprowadzić migrację istniejącej bazy danych podzielonej na fragmenty:

1. Przygotuj [bazę danych Menedżera map fragmentu](elastic-scale-shard-map-management.md).
2. Utwórz mapę fragmentu.
3. Przygotuj poszczególne fragmentów.  
4. Dodaj mapowania do mapy fragmentu.

Te techniki można zaimplementować przy użyciu [biblioteki klienta .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)lub skryptów programu PowerShell dostępnych w [skryptach narzędzi Azure SQL Database Elastic Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). W przykładach użyto skryptów programu PowerShell.

Aby uzyskać więcej informacji na temat ShardMapManager, zobacz [fragmentu Map Management](elastic-scale-shard-map-management.md). Aby zapoznać się z omówieniem narzędzi Elastic Database, zobacz [Omówienie funkcji usługi Elastic Database](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Przygotowywanie bazy danych Menedżera map fragmentu

Menedżer mapy fragmentu jest specjalną bazą danych, która zawiera dane służące do zarządzania skalowanymi bazami danych. Możesz użyć istniejącej bazy danych lub utworzyć nową. Baza danych działająca jako Menedżer mapy fragmentu nie powinna być taka sama jak fragmentu. Skrypt programu PowerShell nie tworzy bazy danych.

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1. Tworzenie Menedżera mapy fragmentu

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Aby pobrać Menedżera mapy fragmentu

Po utworzeniu można pobrać Menedżera mapy fragmentu z tym poleceniem cmdlet. Ten krok jest konieczny za każdym razem, gdy trzeba użyć obiektu ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Krok 2. Tworzenie mapy fragmentu

Wybierz typ mapy fragmentu do utworzenia. Wybór zależy od architektury bazy danych:

1. Pojedyncze dzierżawy na bazę danych (w przypadku warunków, zobacz [słownik](elastic-scale-glossary.md)).
2. Wiele dzierżawców na bazę danych (dwa typy):
   1. Mapowanie listy
   2. Mapowanie zakresu

W przypadku modelu z jedną dzierżawą Utwórz mapę **listy mapowania** fragmentu. Model o pojedynczej dzierżawie przypisuje jedną bazę danych na dzierżawcę. Jest to skuteczny model dla deweloperów SaaS, który upraszcza zarządzanie.

![Mapowanie listy][1]

Model wielu dzierżawców przypisuje kilka dzierżawców do pojedynczej bazy danych (i można dystrybuować grupy dzierżawców w wielu bazach danych). Użyj tego modelu, gdy oczekuje się, że każda dzierżawa będzie miała małe potrzeby w zakresie danych. W tym modelu Przypisz zakres dzierżawców do bazy danych przy użyciu **mapowania zakresu**.

![Mapowanie zakresu][2]

Lub można zaimplementować model bazy danych z wieloma dzierżawcami przy użyciu *mapowania listy* , aby przypisać wiele dzierżawców do pojedynczej bazy danych. Na przykład DB1 jest używany do przechowywania informacji o IDENTYFIKATORze dzierżawy 1 i 5, a w programie DB2 są przechowywane dane dla dzierżawców 7 i dzierżawców 10.

![Wiele dzierżawców w pojedynczej bazie danych][3]

**Na podstawie wybranego wyboru wybierz jedną z następujących opcji:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opcja 1. Tworzenie mapy fragmentu na potrzeby mapowania list

Utwórz mapę fragmentu za pomocą obiektu ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opcja 2. Tworzenie mapy fragmentu dla mapowania zakresu

Aby użyć tego wzorca mapowania, wartości identyfikatora dzierżawy muszą być ciągłymi zakresami i dopuszczają przerwy w zakresach przez pominięcie zakresu podczas tworzenia baz danych.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opcja 3: mapowania listy dla pojedynczej bazy danych

Skonfigurowanie tego wzorca wymaga również utworzenia mapy listy, jak pokazano w kroku 2, opcja 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3. przygotowanie poszczególnych fragmentów

Dodaj każdy fragmentu (baza danych) do Menedżera mapy fragmentu. Przygotowuje to pojedyncze bazy danych do przechowywania informacji dotyczących mapowania. Wykonaj tę metodę dla każdego fragmentuu.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Krok 4. Dodawanie mapowań

Dodawanie mapowań zależy od rodzaju utworzonej mapy fragmentu. Jeśli utworzono mapę list, należy dodać mapowania listy. Jeśli utworzono mapę zakresu, należy dodać mapowania zakresu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opcja 1. Mapowanie danych dla mapowania listy

Mapuj dane przez dodanie mapowania listy dla każdej dzierżawy.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opcja 2: mapowanie danych dla mapowania zakresu

Dodaj mapowania zakresu dla wszystkich zakresów identyfikatora dzierżawy — skojarzenia bazy danych:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Krok 4. zamapuj dane dla wielu dzierżawców w pojedynczej bazie danych

Dla każdej dzierżawy Uruchom Add-ListMapping (opcja 1).

## <a name="checking-the-mappings"></a>Sprawdzanie mapowań

Do informacji o istniejących fragmentów i skojarzonych z nimi mapowań można wykonać zapytania przy użyciu następujących poleceń:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Podsumowanie

Po zakończeniu instalacji można rozpocząć korzystanie z biblioteki klienta Elastic Database. Można również użyć kwerendy routingu i [fragmentu](elastic-scale-multishard-querying.md) [opartego na danych](elastic-scale-data-dependent-routing.md) .

## <a name="next-steps"></a>Następne kroki

Pobierz skrypty programu PowerShell ze [skryptów narzędzi bazy danych Azure SQL Database-Elastic Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Narzędzia są również w serwisie GitHub: [Azure/Elastic-DB-Tools](https://github.com/Azure/elastic-db-tools).

Użyj narzędzia Split-Merge do przenoszenia danych do modelu z wieloma dzierżawcami lub z niego do modelu z jednym dzierżawcą. Zobacz [dzielenie narzędzia do scalania](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](saas-tenancy-app-design-patterns.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

## <a name="questions-and-feature-requests"></a>Pytania i żądania funkcji

Pytania można uzyskać, korzystając z [&stronie](https://docs.microsoft.com/answers/topics/azure-sql-database.html) pytań i odpowiedzi dla SQL Database i dla żądań funkcji, Dodaj je do [forum SQL Database opinii](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png
