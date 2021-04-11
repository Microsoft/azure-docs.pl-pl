---
title: Migrowanie istniejących danych do konta interfejs API tabel w programie Azure Cosmos DB
description: Dowiedz się, jak migrować lub importować dane lokalne lub w chmurze do konta usługi Azure interfejs API tabel w programie Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443342"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrowanie danych do konta interfejs API tabel Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ten samouczek zawiera instrukcje dotyczące importowania danych do użycia z [interfejs API tabel](table-introduction.md)Azure Cosmos DB. Jeśli masz dane przechowywane w usłudze Azure Table Storage, możesz użyć narzędzia do migracji danych lub AzCopy do zaimportowania danych do interfejs API tabel Azure Cosmos DB. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Importowanie danych za pomocą narzędzia do migracji danych
> * Importowanie danych za pomocą narzędzia AzCopy

## <a name="prerequisites"></a>Wymagania wstępne

* **Zwiększ przepływność:** Czas trwania migracji danych zależy od ilości skonfigurowanej przepływności dla danego kontenera lub zestawu kontenerów. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty.

* **Tworzenie zasobów Azure Cosmos DB:** Przed rozpoczęciem migrowania danych Utwórz wszystkie tabele z Azure Portal. W przypadku migrowania do konta Azure Cosmos DB, które ma przepływność na poziomie bazy danych, należy podać klucz partycji podczas tworzenia tabel Azure Cosmos DB.

## <a name="data-migration-tool"></a>Narzędzie do migracji danych

Możesz użyć narzędzia do migracji danych w wierszu polecenia (dt.exe) w Azure Cosmos DB, aby zaimportować istniejące dane Table Storage platformy Azure do konta interfejs API tabel. 

Aby przeprowadzić migrację danych tabeli:

1. Pobierz narzędzie do migracji danych z witryny [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Uruchamiaj za `dt.exe` pomocą argumentów wiersza polecenia dla danego scenariusza. Narzędzie `dt.exe` przyjmuje polecenia w następującym formacie:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Obsługiwane opcje dla tego polecenia to:

* **/ErrorLog:** Obowiązkowe. Nazwa pliku CSV do przekierowania niepowodzeń transferu danych.
* **/OverwriteErrorLog:** Obowiązkowe. Zastąp plik dziennika błędów.
* **/ProgressUpdateInterval:** Opcjonalne, wartość domyślna to `00:00:01` . Przedział czasu, aby odświeżyć postęp transferu danych na ekranie.
* **/ErrorDetails:** Opcjonalne, wartość domyślna to `None` . Określa, że należy wyświetlić szczegółowe informacje o błędzie dla następujących błędów: `None` , `Critical` , lub `All` .
* **/EnableCosmosTableLog:** Obowiązkowe. Przekieruj dziennik do konta tabeli Azure Cosmos DB. Jeśli ta wartość jest ustawiona, domyślnie używane są parametry połączenia z kontem docelowym, chyba że `/CosmosTableLogConnectionString` jest również podany. Jest to przydatne w przypadku jednoczesnego uruchamiania wielu wystąpień tego narzędzia.
* **/CosmosTableLogConnectionString:** Obowiązkowe. Parametry połączenia służące do skierowania dziennika do zdalnego konta tabeli Azure Cosmos DB.

### <a name="command-line-source-settings"></a>Ustawienia źródła w wierszu polecenia

Podczas definiowania Table Storage platformy Azure jako źródła migracji należy użyć następujących opcji źródła.

* **/s: Azure** : Odczytuje dane z Table Storage.
* **/s.ConnectionString:** Parametry połączenia dla punktu końcowego tabeli. Możesz pobrać ten z Azure Portal.
* **/s.LocationMode:** Opcjonalne, wartość domyślna to `PrimaryOnly` . Określa tryb lokalizacji, który ma być używany podczas nawiązywania połączenia z Table Storage: `PrimaryOnly` , `PrimaryThenSecondary` , `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.Table:** Nazwa tabeli platformy Azure.
* **/s.InternalFields:** Ustaw na `All` dla migracji tabeli, ponieważ `RowKey` `PartitionKey` są one wymagane do zaimportowania.
* **/s.Filter:** Obowiązkowe. Ciąg filtru, który ma zostać zastosowany.
* **/s.projection:** Obowiązkowe. Lista kolumn do wybrania,

Aby pobrać parametry połączenia źródłowego podczas importowania z Table Storage, należy otworzyć Azure Portal. Wybierz pozycję  >    >  **klucze dostępu do** konta kont magazynu i skopiuj **Parametry połączenia**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Zrzut ekranu pokazujący konta magazynu > opcje > kluczy dostępu i Podświetl ikonę kopiowania.":::

### <a name="command-line-target-settings"></a>Ustawienia miejsca docelowego w wierszu polecenia

Użyj następujących opcji docelowych podczas definiowania Azure Cosmos DB interfejs API tabel jako celu migracji.

* **/t: TableAPIBulk:** Przekazuje dane do interfejs API tabel Azure Cosmos DB w partiach.
* **/t.ConnectionString:** Parametry połączenia dla punktu końcowego tabeli.
* **/t.TableName:** Określa nazwę tabeli, do której mają zostać zapisane dane.
* **/t.Overwrite:** Opcjonalne, wartość domyślna to `false` . Określa, czy istniejące wartości powinny być zastępowane.
* **/t.MaxInputBufferSize:** Opcjonalne, wartość domyślna to `1GB` . Przybliżone oszacowanie bajtów wejściowych do buforu przed opróżnieniem danych do ujścia.
* **/t.Throughput:** Opcjonalne, jeśli nie zostanie określony, wartości domyślne usługi. Określa przepływność do skonfigurowania dla tabeli.
* **/t.maxBatchSize:** Opcjonalne, wartość domyślna to `2MB` . Określ rozmiar wsadu w bajtach.

### <a name="sample-command-source-is-table-storage"></a>Przykładowe polecenie: Źródło jest Table Storage

Oto przykład wiersza polecenia pokazujący sposób importowania z Table Storage do interfejs API tabel:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrowanie danych przy użyciu narzędzia AzCopy

Za pomocą narzędzia wiersza polecenia AzCopy można także przeprowadzić migrację danych z Table Storage do Azure Cosmos DB interfejs API tabel. Aby użyć AzCopy, najpierw wyeksportuj dane zgodnie z opisem w temacie [Eksportowanie danych z Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Następnie należy zaimportować dane do Azure Cosmos DB zgodnie z opisem w [Azure Cosmos DB interfejs API tabel](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Zapoznaj się z poniższym przykładem podczas importowania do Azure Cosmos DB. Należy zauważyć `/Dest` , że wartość używa `cosmosdb` , nie `core` .

Przykładowe polecenie importu:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wykonywać zapytania dotyczące danych przy użyciu interfejs API tabel Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonywać zapytania dotyczące danych?](../cosmos-db/tutorial-query-table.md)




