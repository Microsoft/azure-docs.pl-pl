---
title: Zapytania bazy danych podzielonej na fragmenty
description: Uruchom zapytania w fragmentów za pomocą biblioteki klienta Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3843b7dff0f987fa53336056cdcb82af00336a7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442721"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Wykonywanie zapytań wielofragmentuowych przy użyciu narzędzi Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Omówienie

Za pomocą [narzędzi Elastic Database](elastic-scale-introduction.md)można tworzyć rozwiązania bazy danych podzielonej na fragmenty. **Zapytania fragmentu** są używane do zadań, takich jak zbieranie danych/raportowanie, które wymagają uruchomienia zapytania, które rozciąga się na kilka fragmentów. (W przeciwieństwie do [routingu zależnego od danych](elastic-scale-data-dependent-routing.md), które wykonuje wszystkie prace na jednym fragmentu).

1. Pobierz **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) przy użyciu **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) lub metody **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Zobacz [konstruowanie elementu ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) i [pobieranie RangeShardMap lub ListShardMap](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Utwórz obiekt **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Utwórz element **MultiShardStatement lub MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Ustaw **Właściwość CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) na polecenie T-SQL.
5. Wykonaj polecenie, wywołując metodę **ExecuteQueryAsync lub ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Wyniki można wyświetlić przy użyciu klasy **MultiShardResultSet lub MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Przykład

Poniższy kod ilustruje użycie zapytań wielofragmentuowych przy użyciu danego **ShardMap** o nazwie *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Kluczową różnicą jest konstrukcja połączeń wielofragmentuowych. Gdy element **SqlConnection** działa na pojedynczej bazie danych, **MultiShardConnection** pobiera ***kolekcję fragmentów*** jako dane wejściowe. Wypełnij kolekcję fragmentów z mapy fragmentu. Zapytanie jest następnie wykonywane w kolekcji fragmentów przy użyciu semantyki **All Union** do złożenia pojedynczego ogólnego wyniku. Opcjonalnie nazwę fragmentu, z którego pochodzi wiersz, można dodać do danych wyjściowych przy użyciu właściwości **ExecutionOptions** polecenia.

Zwróć uwagę na wywołanie metody **myShardMap. GetShards ()**. Ta metoda pobiera wszystkie fragmentów z mapy fragmentu i zapewnia łatwy sposób uruchamiania zapytania dla wszystkich odpowiednich baz danych. Kolekcję fragmentów dla zapytania o wiele fragmentu można zwiększyć, wykonując zapytanie LINQ w kolekcji zwróconej przez wywołanie **myShardMap. GetShards ()**. W połączeniu z zasadą częściowe wyniki bieżąca możliwość w kwerendach fragmentu została zaprojektowana tak, aby była dobrze gotowa do setek fragmentów.

Ograniczenie związane z wykonywaniem zapytań obejmujących wiele fragmentuów jest obecnie brakiem weryfikacji dla fragmentów i podfragmentów, które są wysyłane do zapytania. Podczas gdy Routing zależny od danych weryfikuje, że dana fragmentu jest częścią mapy fragmentu w czasie wykonywania zapytania, zapytania wielofragmentuowe nie wykonują tego sprawdzenia. Może to prowadzić do zapytań obejmujących wiele fragmentu uruchomionych w bazach danych, które zostały usunięte z mapy fragmentu.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Zapytania fragmentu i operacje dzielenia i scalania

Zapytania fragmentu nie sprawdzają, czy podfragmentów w bazie danych, której dotyczy zapytanie, uczestniczy w trwających operacjach dzielenia i scalania. (Zobacz [skalowanie przy użyciu narzędzia Elastic Database Split-Merge](elastic-scale-overview-split-and-merge.md)). Może to prowadzić do niespójności, w których wiersze z tego samego podfragmentu są wyświetlane dla wielu baz danych w tym samym zapytaniu wielofragmentunym. Należy pamiętać o tych ograniczeniach i rozważyć opróżnianie trwających operacji Split-Scale i zmian mapy fragmentu podczas wykonywania zapytań wielofragmentuowych.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]