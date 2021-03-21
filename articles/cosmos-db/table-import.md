---
title: Migrowanie istniejących danych do konta interfejsu API tabel w usłudze Azure Cosmos DB
description: Dowiedz się, jak migrować lub importować dane lokalne lub w chmurze do konta usługi Azure interfejs API tabel w programie Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096083"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrowanie danych na konto interfejsu API tabel w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ten samouczek zawiera instrukcje dotyczące importowania danych do użycia z [interfejs API tabel](table-introduction.md)Azure Cosmos DB. Jeśli masz dane przechowywane w usłudze Azure Table Storage, możesz zaimportować je do interfejsu API tabel w usłudze Azure Cosmos DB za pomocą narzędzia do migracji danych lub narzędzia AzCopy. Jeśli masz dane przechowywane na koncie interfejsu API tabel usługi Azure Cosmos DB w wersji zapoznawczej, musisz użyć narzędzia do migracji danych, aby przenieść dane. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Importowanie danych za pomocą narzędzia do migracji danych
> * Importowanie danych za pomocą narzędzia AzCopy
> * Migracja z interfejsu API tabel w wersji zapoznawczej do interfejsu API tabel 

## <a name="prerequisites"></a>Wymagania wstępne

* **Zwiększ przepływność:** Czas trwania migracji danych zależy od ilości skonfigurowanej przepływności dla danego kontenera lub zestawu kontenerów. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Aby uzyskać więcej informacji na temat zwiększania przepływności w witrynie Azure Portal, zobacz Performance levels and pricing tiers in Azure Cosmos DB (Poziomy wydajności i warstwy cenowe w usłudze Azure Cosmos DB).

* **Utworzenie zasobów usługi Azure Cosmos DB:** przed rozpoczęciem migracji danych utwórz wstępnie wszystkie tabele z poziomu witryny Azure Portal. W przypadku migracji do konta usługi Azure Cosmos DB, która ma przepływność poziomu bazy danych, pamiętaj o podaniu klucza partycji podczas tworzenia tabel usługi Azure Cosmos DB.

## <a name="data-migration-tool"></a>Narzędzie do migracji danych

Narzędzie wiersza polecenia usługi Azure Cosmos DB do migracji danych (dt.exe) umożliwia importowanie istniejących danych z usługi Azure Table Storage na konto ogólnie dostępnej wersji interfejsu API tabel oraz migrowanie danych z konta wersji zapoznawczej interfejsu API tabel na konto ogólnie dostępnej wersji interfejsu API tabel. Inne źródła nie są obecnie obsługiwane. Narzędzie do migracji danych z interfejsem użytkownika (dtui.exe) nie jest obecnie obsługiwane w przypadku kont interfejsu API tabel. 

Aby przeprowadzić migrację danych tabeli, wykonaj następujące czynności:

1. Pobierz narzędzie do migracji danych z witryny [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Uruchom narzędzie `dt.exe` przy użyciu argumentów wiersza polecenia odpowiednich do scenariusza. Narzędzie `dt.exe` przyjmuje polecenia w następującym formacie:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Obsługiwane opcje dla tego polecenia to:

* **/ErrorLog:** Obowiązkowe. Nazwa pliku CSV do przekierowania niepowodzeń transferu danych
* **/OverwriteErrorLog:** Obowiązkowe. Zastąp plik dziennika błędów
* **/ProgressUpdateInterval:** Opcjonalne, wartość domyślna to 00:00:01. Przedział czasu, aby odświeżyć postęp transferu danych na ekranie
* **/ErrorDetails:** Opcjonalne, wartość domyślna to None. Określa, że należy wyświetlić szczegółowe informacje o błędzie dla następujących błędów: brak, krytyczne, wszystkie
* **/EnableCosmosTableLog:** Obowiązkowe. Przekieruj dziennik do konta tabeli Cosmos. Jeśli ta wartość jest ustawiona, domyślnie używane są parametry połączenia z kontem docelowym, chyba że podano również/CosmosTableLogConnectionString. Jest to przydatne w przypadku jednoczesnego uruchamiania wielu wystąpień DT.
* **/CosmosTableLogConnectionString:** Obowiązkowe. Parametry połączenia, aby skierować dziennik do zdalnego konta tabeli Cosmos.

### <a name="command-line-source-settings"></a>Ustawienia źródła w wierszu polecenia

Podczas definiowania usługi Azure Table Storage lub interfejsu API tabel w wersji zapoznawczej jako źródła migracji użyj następujących opcji źródła.

* **/s: Azure** : Odczytuje dane z usługi Azure Table Storage
* **/s.ConnectionString:** Parametry połączenia dla punktu końcowego tabeli. Można go pobrać z Azure Portal
* **/s.LocationMode:** Opcjonalne, wartość domyślna to PrimaryOnly. Określa tryb lokalizacji, który ma być używany podczas nawiązywania połączenia z usługą Azure Table Storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.Table:** Nazwa tabeli platformy Azure
* **/s.InternalFields:** Ustaw wartość wszystkie dla migracji tabeli, ponieważ RowKey i PartitionKey są wymagane do zaimportowania.
* **/s.Filter:** Obowiązkowe. Ciąg filtru do zastosowania
* **/s.projection:** Obowiązkowe. Lista kolumn do wybrania

Aby pobrać parametry połączenia źródłowego podczas importowania z usługi Azure Table Storage, Otwórz Azure Portal a następnie kliknij pozycję  >    >  **klucze dostępu** konta konta magazynu, a następnie użyj przycisku kopiowania, aby skopiować **Parametry połączenia**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Zrzut ekranu pokazujący konta magazynu > opcje > kluczy dostępu i podświetl przycisk Kopiuj.":::

Aby pobrać parametry połączenia źródłowego podczas importowania z konta Azure Cosmos DB interfejs API tabel (wersja zapoznawcza), Otwórz Azure Portal, kliknij pozycję **Azure Cosmos DB**  >    >  **Parametry połączenia** z kontem i użyj przycisku kopiowania, aby skopiować **Parametry połączenia**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Zrzut ekranu przedstawiający opcje źródła dla bazy danych HBase":::

[Przykład polecenia dla usługi Azure Table Storage](#azure-table-storage)

[Przykład polecenia dla wersji zapoznawczej interfejsu API tabel usługi Azure Cosmos DB](#table-api-preview)

### <a name="command-line-target-settings"></a>Ustawienia miejsca docelowego w wierszu polecenia

Podczas definiowania interfejsu API tabel usługi Azure Cosmos DB jako miejsca docelowego migracji użyj następujących opcji miejsca docelowego.

* **/t: TableAPIBulk:** Przekazuje dane do tabeli usługi Azure CosmosDB w partiach
* **/t.ConnectionString:** Parametry połączenia dla punktu końcowego tabeli
* **/t.TableName:** Określa nazwę tabeli do zapisu
* **/t.Overwrite:** Opcjonalne, wartość domyślna to false. Określa, czy istniejące wartości powinny być zastępowane
* **/t.MaxInputBufferSize:** Opcjonalne, wartość domyślna to 1 GB. Przybliżone oszacowanie bajtów wejściowych do buforu przed opróżnieniem danych do ujścia
* **/t.Throughput:** Opcjonalne, jeśli nie zostanie określony, wartości domyślne usługi. Określa przepływność do skonfigurowania dla tabeli
* **/t.maxBatchSize:** Opcjonalne, wartość domyślna to 2 MB. Określ rozmiar wsadu w bajtach

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Przykładowe polecenie: źródłem jest usługa Azure Table Storage

Oto przykład wiersza polecenia przedstawiający sposób importowania danych z usługi Azure Table Storage do interfejsu API tabel:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Przykładowe polecenie: źródłem jest wersja zapoznawcza interfejsu API tabel usługi Azure Cosmos DB

Oto przykład wiersza polecenia przedstawiający sposób importowania danych z wersji zapoznawczej interfejsu API tabel do wersji ogólnie dostępnej interfejsu API tabel:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrowanie danych przy użyciu narzędzia AzCopy

Inną opcją migracji danych z usługi Azure Table Storage do interfejsu API tabel usługi Azure Cosmos DB jest użycie narzędzia wiersza polecenia AzCopy. Aby użyć narzędzia AzCopy, należy najpierw wyeksportować dane zgodnie z opisem w sekcji [Export data from Table Storage (Eksportowanie danych z usługi Table Storage)](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage), a następnie zaimportować dane do usługi Azure Cosmos DB zgodnie z opisem w sekcji [Azure Cosmos DB Table API (Interfejs API tabel usługi Azure Cosmos DB)](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Podczas importowania danych do usługi Azure Cosmos DB skorzystaj z poniższego przykładu. Zwróć uwagę, że dla parametru /Dest jest używana wartość „cosmosdb”, nie „core”.

Przykładowe polecenie importu:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migracja z interfejsu API tabel w wersji zapoznawczej do interfejsu API tabel

> [!WARNING]
> Jeśli chcesz od razu skorzystać z możliwości, jakie dają tabele w wersji ogólnie dostępnej, przeprowadź migrację istniejących tabel w wersji zapoznawczej zgodnie z instrukcjami przedstawionymi w tej sekcji. W przeciwnym razie dla obecnych klientów korzystających z wersji zapoznawczej będzie w nadchodzących tygodniach prowadzona migracja automatyczna. Należy zwrócić uwagę, że tabele z wersji zapoznawczej po migracji automatycznej będą objęte pewnymi ograniczenia, które nie będą dotyczyć nowo utworzonych tabel.

Interfejs API tabel jest teraz ogólnie dostępny. Istnieją różnice między wersją zapoznawczą a wersją ogólnie dostępną tabel, dotyczące zarówno kodu działającego w chmurze, jak i kodu działającego po stronie klienta. Dlatego nie zalecamy używania klienta SDK w wersji zapoznawczej z kontem interfejsu API tabel w wersji ogólnie dostępnej i na odwrót. Klienci korzystający z wersji zapoznawczej interfejsu API tabel, którzy chcą nadal korzystać z istniejących tabel, ale w środowisku produkcyjnym, muszą przeprowadzić migrację ze środowiska wersji zapoznawczej do środowiska ogólnie dostępnego lub zaczekać na migrację automatyczną. Jeśli zaczekasz na migrację automatyczną, otrzymasz powiadomienie na temat ograniczeń dotyczących zmigrowanych tabel. Po migracji będziesz mieć możliwość tworzenia na obecnym koncie nowych tabel bez ograniczeń (ograniczenia dotyczą tylko zmigrowanych tabel).

Aby przeprowadzić migrację z wersji zapoznawczej interfejsu API tabel do wersji ogólnie dostępnej interfejsu API tabel:

1. Utwórz nowe konto usługi Azure Cosmos DB i wybierz typ interfejsu API „Tabela platformy Azure”, zgodnie z opisem w sekcji [Tworzenie konta bazy danych](create-table-dotnet.md#create-a-database-account).

2. Zmień ustawienia klientów tak, aby korzystały z ogólnie dostępnej wersji [zestawów SDK interfejsu API tabel](table-sdk-dotnet.md).

3. Przeprowadź migrację danych klientów z tabel w wersji zapoznawczej do wersji ogólnie dostępnej za pomocą narzędzia do migracji danych. Instrukcje dotyczące używania narzędzia do migracji danych do tego celu znajdziesz w sekcji [Narzędzie do migracji danych](#data-migration-tool). 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie danych za pomocą narzędzia do migracji danych
> * Importowanie danych za pomocą narzędzia AzCopy
> * Migracja z interfejsu API tabel w wersji zapoznawczej do interfejsu API tabel

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak wykonywać zapytania dotyczące danych za pomocą interfejsu API tabel usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonywać zapytania dotyczące danych?](../cosmos-db/tutorial-query-table.md)