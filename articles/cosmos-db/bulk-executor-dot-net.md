---
title: Użycie zbiorczego programu .NET Library w Azure Cosmos DB na potrzeby operacji importowania zbiorczego i aktualizacji
description: Zbiorcze importowanie i aktualizowanie Azure Cosmos DB dokumentów przy użyciu zbiorczej biblioteki programu .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b97d702fffa7e65e24b8367892599b7bde5b669
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092623"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Użycie zbiorczej biblioteki programu .NET do wykonywania operacji zbiorczych w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Ta zbiorcza Biblioteka wykonawcza opisana w tym artykule jest obsługiwana w przypadku aplikacji korzystających z wersji zestawu .NET SDK 2. x. W przypadku nowych aplikacji można korzystać z **pomocy technicznej zbiorczej** dostępnej bezpośrednio w [zestawie SDK .NET w wersji 3. x](tutorial-sql-api-dotnet-bulk-import.md) , która nie wymaga żadnej biblioteki zewnętrznej. 

> Jeśli obecnie korzystasz z biblioteki wykonawców zbiorczych i planujesz przeprowadzić migrację do obsługi zbiorczej w nowszym zestawie SDK, wykonaj kroki opisane w [przewodniku migracji](how-to-migrate-from-bulk-executor-library.md) , aby przeprowadzić migrację aplikacji.

Ten samouczek zawiera instrukcje dotyczące korzystania z biblioteki funkcji wykonawczej .NET operacji zbiorczych do importowania i aktualizowania dokumentów do kontenera usługi Azure Cosmos. Aby dowiedzieć się więcej o bibliotece modułu wykonawczego zbiorczego i o sposobie korzystania z dużej przepływności i magazynu, zobacz artykuł [Omówienie biblioteki wykonawców zbiorczych](bulk-executor-overview.md) . W tym samouczku zostanie wyświetlona Przykładowa aplikacja .NET, która zbiorczo importuje losowo wygenerowane dokumenty do kontenera usługi Azure Cosmos. Po zaimportowaniu pokazuje, jak można zbiorczo aktualizować zaimportowane dane, określając poprawki jako operacje do wykonania w określonych polach dokumentu.

Obecnie Biblioteka wykonawców zbiorczych jest obsługiwana przez Azure Cosmos DB tylko konta interfejsu API SQL i Gremlin. W tym artykule opisano sposób użycia zbiorczej biblioteki programu .NET z kontami interfejsu API SQL. Aby dowiedzieć się więcej o używaniu zbiorczej biblioteki programu .NET z kontami interfejsu API Gremlin, zobacz [wykonywanie operacji zbiorczych w interfejsie API usługi Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać [program Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niego. Upewnij się, że podczas instalacji programu Visual Studio włączono opcję "Programowanie na platformie Azure".

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Możesz bezpłatnie [Wypróbować Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można też użyć [emulatora Azure Cosmos DB](./local-emulator.md) z `https://localhost:8081` punktem końcowym. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticate-requests).

* Utwórz konto Azure Cosmos DB interfejsu API SQL, korzystając z procedury opisanej w sekcji [Tworzenie konta bazy danych](create-sql-api-dotnet.md#create-account) artykułu przewodnika Szybki Start platformy .NET.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem, pobierając przykładową aplikację platformy .NET z usługi GitHub. Ta aplikacja wykonuje operacje zbiorcze na danych przechowywanych na koncie usługi Azure Cosmos. Aby sklonować aplikację, Otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz go skopiować, a następnie uruchom następujące polecenie:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Sklonowane repozytorium zawiera dwa przykłady "BulkImportSample" i "BulkUpdateSample". Możesz otworzyć jedną z przykładowych aplikacji, zaktualizować parametry połączenia w pliku App.config przy użyciu parametrów połączenia konta Azure Cosmos DB, skompilować rozwiązanie i uruchomić je.

Aplikacja "BulkImportSample" generuje losowe dokumenty i zbiorczo importuje je do konta usługi Azure Cosmos. Aplikacja "BulkUpdateSample" zbiorczo aktualizuje zaimportowane dokumenty, określając poprawki jako operacje do wykonania w określonych polach dokumentu. W następnych sekcjach opisano kod w każdej z tych przykładowych aplikacji.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Zbiorcze importowanie danych do konta usługi Azure Cosmos

1. Przejdź do folderu "BulkImportSample" i Otwórz plik "BulkImportSample. sln".  

2. Parametry połączenia Azure Cosmos DB są pobierane z pliku App.config, jak pokazano w poniższym kodzie:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importer zbiorczy tworzy nową bazę danych i kontener z nazwą bazy danych, nazwą kontenera i wartościami przepływności określonymi w pliku App.config.

3. Następny obiekt DocumentClient jest inicjowany z bezpośrednim trybem połączenia TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Obiekt BulkExecutor jest inicjowany z wysoką wartością ponawiania dla żądań czasu oczekiwania i ograniczania. A następnie są ustawiane na 0, aby przekazać kontrolę przeciążenia do BulkExecutor przez jego okres istnienia.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Aplikacja wywołuje interfejs API BulkImportAsync. Biblioteka .NET oferuje dwa przeciążenia interfejsu API importowania zbiorczego — jeden, który akceptuje listę serializowanych dokumentów JSON, a drugi, który akceptuje listę deserializowanych dokumentów POCO. Aby dowiedzieć się więcej na temat definicji każdej z tych przeciążonych metod, zapoznaj się z [dokumentacją interfejsu API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Metoda BulkImportAsync akceptuje następujące parametry:**
   
   |**Parametr**  |**Opis** |
   |---------|---------|
   |enableUpsert    |   Flaga włączenia operacji upsert na dokumentach. Jeśli dokument o podanym IDENTYFIKATORze już istnieje, zostanie zaktualizowany. Domyślnie jest ustawiona wartość false.      |
   |disableAutomaticIdGeneration    |    Flaga wyłączenia automatycznej generacji identyfikatora. Domyślnie jest ustawiona wartość true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maksymalny stopień współbieżności dla zakresu kluczy partycji, ustawienie na wartość null spowoduje, że biblioteka użyje wartości domyślnej równej 20. |
   |maxInMemorySortingBatchSize     |  Maksymalna liczba dokumentów pobieranych z modułu wyliczającego dokumenty, które są przesyłane do wywołania interfejsu API na każdym etapie. W przypadku fazy sortowania w pamięci, która jest wykonywana przed importem zbiorczym, ustawienie tego parametru na wartość null spowoduje użycie przez bibliotekę domyślnej wartości minimalnej (Documents. Count, 1000000).       |
   |cancellationToken    |    Token anulowania, aby bezpiecznie opuścić operację importu zbiorczego.     |

   **Definicja obiektu odpowiedzi importu zbiorczego** Wynik wywołania interfejsu API importu zbiorczego zawiera następujące atrybuty:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |NumberOfDocumentsImported (Long)   |  Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z całkowitej liczby dokumentów dostarczonych do wywołania interfejsu API importu zbiorczego.       |
   |TotalRequestUnitsConsumed (Double)   |   Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API importu zbiorczego.      |
   |TotalTimeTaken (TimeSpan)    |   Łączny czas trwania wywołania interfejsu API importu zbiorczego w celu ukończenia wykonywania.      |
   |BadInputDocuments (lista \<object> )   |     Lista dokumentów z nieprawidłowym formatem, które nie zostały pomyślnie zaimportowane do wywołania interfejsu API importu zbiorczego. Popraw zwrócone dokumenty i ponów próbę importu. Dokumenty z błędami sformatowanymi zawierają dokumenty, których identyfikator nie jest ciągiem (wartość null lub dowolny inny typ danych jest uznawany za nieprawidłowy).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Zbiorcze aktualizowanie danych na koncie usługi Azure Cosmos

Istniejące dokumenty można aktualizować za pomocą interfejsu API BulkUpdateAsync. W tym przykładzie ustawisz `Name` pole na nową wartość i usuniesz `Description` pole z istniejących dokumentów. Pełny zestaw obsługiwanych operacji aktualizacji można znaleźć w [dokumentacji interfejsu API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

1. Przejdź do folderu "BulkUpdateSample" i Otwórz plik "BulkUpdateSample. sln".  

2. Zdefiniuj elementy aktualizacji wraz z odpowiednimi operacjami aktualizacji pola. W tym przykładzie zostanie użyta `SetUpdateOperation` Aktualizacja `Name` pola i `UnsetUpdateOperation` usunięcie `Description` pola ze wszystkich dokumentów. Można również wykonywać inne operacje, takie jak zwiększanie pola dokumentu przez określoną wartość, wypychanie określonych wartości do pola tablicy lub usuwanie określonej wartości z pola tablicy. Aby dowiedzieć się więcej o różnych metodach dostarczonych przez interfejs API aktualizacji zbiorczej, zapoznaj się z [dokumentacją interfejsu API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Aplikacja wywołuje interfejs API BulkUpdateAsync. Aby dowiedzieć się więcej na temat definicji metody BulkUpdateAsync, zapoznaj się z [dokumentacją interfejsu API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet&preserve-view=true).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Metoda BulkUpdateAsync akceptuje następujące parametry:**

   |**Parametr**  |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maksymalny stopień współbieżności na zakres kluczy partycji, ustawienie dla tego parametru wartości null spowoduje, że biblioteka będzie używać wartości domyślnej (20).   |
   |maxInMemorySortingBatchSize    |    Maksymalna liczba elementów aktualizacji pobranych z numeratora elementów aktualizacji została przeniesiona do wywołania interfejsu API na każdym etapie. Dla fazy sortowania w pamięci, która jest wykonywana przed aktualizacją zbiorczą, ustawienie dla tego parametru wartości null spowoduje użycie domyślnej wartości minimalnej (updateItems. Count, 1000000).     |
   | cancellationToken|Token anulowania, aby bezpiecznie opuścić operację aktualizacji zbiorczej. |

   **Definicja obiektu odpowiedzi aktualizacji zbiorczej** Wynik wywołania interfejsu API aktualizacji zbiorczej zawiera następujące atrybuty:

   |**Parametr**  |**Opis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (Long)    |   Liczba dokumentów, które zostały pomyślnie zaktualizowane z całkowitej liczby dokumentów dostarczonych do wywołania interfejsu API aktualizacji zbiorczej.      |
   |TotalRequestUnitsConsumed (Double)   |    Łączna liczba jednostek żądań (jednostek ru) zużytych przez wywołanie interfejsu API aktualizacji zbiorczej.    |
   |TotalTimeTaken (TimeSpan)   | Łączny czas trwania wywołania interfejsu API aktualizacji zbiorczej, aby ukończyć wykonywanie. |
    
## <a name="performance-tips"></a>Porady dotyczące wydajności 

W celu uzyskania lepszej wydajności podczas korzystania z biblioteki wykonawczej zbiorczego należy wziąć pod uwagę następujące kwestie:

* Aby uzyskać najlepszą wydajność, uruchom aplikację z maszyny wirtualnej platformy Azure, która znajduje się w tym samym regionie, co region zapisu konta usługi Azure Cosmos.  

* Zaleca się utworzenie wystąpienia pojedynczego `BulkExecutor` obiektu dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadającej określonemu kontenerowi usługi Azure Cosmos.  

* Ponieważ wykonywanie pojedynczego interfejsu API operacji zbiorczej zużywa duży fragment procesora CPU i sieci we/wy komputera klienckiego (dzieje się to przez duplikowanie wielu zadań wewnętrznie). Unikaj duplikowania wielu współbieżnych zadań w procesie aplikacji, które wykonują wywołania interfejsu API operacji zbiorczej. Jeśli pojedyncze wywołanie interfejsu API operacji zbiorczej działające na jednej maszynie wirtualnej nie jest w stanie korzystać z przepływności całego kontenera (jeśli przepływność kontenera > 1 000 000 RU/s), preferowane jest utworzenie oddzielnych maszyn wirtualnych do współbieżnego wykonywania wywołań interfejsu API operacji zbiorczych.  

* Upewnij się, że `InitializeAsync()` Metoda jest wywoływana po utworzeniu wystąpienia obiektu BulkExecutor w celu pobrania mapy partycji docelowego kontenera Cosmos.  

* W App.Config aplikacji upewnij się, że **gcServer** jest włączona w celu uzyskania lepszej wydajności
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteka emituje dane śledzenia, które mogą być zbierane w pliku dziennika lub w konsoli programu. Aby włączyć oba, Dodaj następujący kod do pliku App.Config aplikacji.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat szczegółów pakietu NuGet i informacji o wersji, zobacz [Szczegóły zestawu SDK programu Bulk](sql-api-sdk-bulk-executor-dot-net.md).