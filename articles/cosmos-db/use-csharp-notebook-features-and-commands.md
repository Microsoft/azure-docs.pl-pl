---
title: Korzystanie z wbudowanych poleceń i funkcji notesu w Azure Cosmos DB notesach C# (wersja zapoznawcza)
description: Dowiedz się, jak używać wbudowanych poleceń i funkcji, aby wykonywać typowe operacje przy użyciu wbudowanych notesów języka C# Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340485"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Korzystanie z wbudowanych poleceń i funkcji notesu w Azure Cosmos DB notesach C# (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano, jak używać wbudowanych poleceń i funkcji notesu do wykonywania typowych operacji w notesach C#.

## <a name="install-a-new-nuget-package"></a>Zainstaluj nowy pakiet NuGet
Po włączeniu obsługi notesu dla kont usługi Azure Cosmos można otworzyć nowy Notes i zainstalować pakiet.

W nowej komórce kodu Wstaw i uruchom następujący kod, zastępując ``PackageToBeInstalled`` go odpowiednim pakietem NuGet i ``optionalVersion`` z określoną wersją pakietu w razie potrzeby. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

W tej samej komórce można zainstalować wiele pakietów NuGet. Pakiety będą dostępne do użycia z dowolnego notesu w obszarze roboczym konta usługi Azure Cosmos. 

Obecnie obszar roboczy notesów C# nie obsługuje cyklicznego rozpoznawania pakietów NuGet. Jeśli pakiet NuGet ma zależności od innych pakietów NuGet, które nie są obecnie zainstalowane, należy jawnie odwoływać się do nich wraz z pakietem nadrzędnym.

> [!TIP]
> Jeśli Notes wymaga pakietu niestandardowego, zalecamy dodanie komórki do notesu w celu zainstalowania pakietu i utworzenia pierwszej komórki. Zmniejsza to prawdopodobieństwo konfliktów z innymi pakietami, które domyślnie Azure Cosmos DB ładowane. Można również łatwo ponownie zainstalować pakiety po [zresetowaniu obszaru roboczego](#reset-notebooks-workspace), co spowoduje usunięcie wszystkich pakietów. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Korzystanie z wbudowanego zestawu SDK Azure Cosmos DB .NET
Wersja 3 [zestawu Azure Cosmos DB .NET SDK dla interfejsu API SQL](https://github.com/Azure/azure-cosmos-dotnet-v3) jest zainstalowana i dołączona do środowiska notesu dla konta usługi Azure Cosmos.

Utwórz wystąpienie programu, ``CosmosClient`` Aby uruchomić dowolną operację zestawu SDK. 

Przykład:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Aby dowiedzieć się więcej, zobacz [przykłady zestawu SDK dla platformy .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> Wbudowany zestaw SDK Azure Cosmos DB .NET jest obsługiwany tylko w przypadku kont interfejsu API SQL (Core). W przypadku innych interfejsów API konieczne będzie [zainstalowanie odpowiedniego sterownika .NET](#install-a-new-nuget-package) ODPOWIADAJĄCego interfejsowi API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Ustawianie opcji niestandardowych przy użyciu ``CosmosClientOptions``
Aby uzyskać większą elastyczność, można ustawić właściwość niestandardową ``CosmosClientOptions`` i przekazać ją do ``CosmosClient`` wystąpienia. Tej właściwości można użyć do:

- W polu Nazwa aplikacji ustaw sufiks User-Agent, aby uwzględnić go w każdym żądaniu.
- Ustaw preferowany region, który ma być używany podczas wykonywania operacji w usłudze.
- Ustaw niestandardowe zasady ponawiania, aby obsługiwać żądania z ograniczoną szybkością.

Zobacz artykuł [Dokumentacja interfejsu API CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) dla wszystkich obsługiwanych ustawień. Oto przykład, który pokazuje, jak ustawić `cosmosClientOptions` Właściwość:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Dostęp do punktu końcowego konta i zmiennych klucza podstawowego
Możesz uzyskać dostęp do wbudowanego punktu końcowego i klucza konta usługi Azure Cosmos, w którym znajduje się Twój Notes.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key``Zmienne i ``Cosmos.Endpoint`` są stosowane tylko w przypadku interfejsu API SQL. W przypadku innych interfejsów API Znajdź punkt końcowy i klucz w bloku **Parametry połączenia** lub **klucze** na koncie usługi Azure Cosmos.  

## <a name="print-console-output-in-c-code"></a>Drukuj dane wyjściowe konsoli w kodzie C#
W kodzie C# można użyć składni Display. AsMarkdown () z [interpolacją ciągu](/dotnet/csharp/language-reference/tokens/interpolated) do drukowania danych wyjściowych konsoli, które będą wyświetlane po uruchomieniu komórki. 

Przykład: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Składnia Console. WriteLine () nie jest obecnie obsługiwana w notesach C#. Użyj Display. AsMarkdown, aby wydrukować dane wyjściowe konsoli z programu. 

## <a name="use-built-in-nteract-data-explorer"></a>Korzystanie z wbudowanego Eksploratora danych nteract
Można użyć wbudowanego [Eksploratora danych nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) do filtrowania i wizualizacji kolekcji elementów. W komórce należy umieścić zmienną, którą chcesz wizualizować w ostatnim wierszu, która jest automatycznie wyświetlana w nteract podczas uruchamiania komórki.

Na przykład w *GetingStarted_Csharp. ipynb* można wydrukować zmienną z wynikiem, a ``telemetryEvents`` . Pełny przykład można znaleźć w [notesie GettingStarted_Csharp. ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) . 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Komórka zapytania CSharp":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Eksplorator danych nteract":::

## <a name="use-built-in-dictionary-viewer"></a>Korzystanie z wbudowanej przeglądarki słowników
Aby wyświetlić zmienną, można użyć wbudowanej przeglądarki słowników. W komórce należy umieścić zmienną, którą chcesz wizualizować w ostatnim wierszu, która zostanie automatycznie wyświetlona, gdy komórka zostanie uruchomiona.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Wbudowana przeglądarka słowników":::

## <a name="upload-json-items-to-a-container"></a>Przekazywanie elementów JSON do kontenera
Możesz użyć ``%%upload`` polecenia Magic, aby przekazać dane z pliku JSON do określonego kontenera usługi Azure Cosmos. Aby przekazać elementy, użyj następującego polecenia:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Zastąp wartości ``{database_id}`` i ``{container_id}`` nazwą bazy danych i kontenera na koncie usługi Azure Cosmos. 
- Zamień ``{url_location_of_file}`` na lokalizację pliku JSON. Plik musi być tablicą prawidłowych obiektów JSON i powinien być dostępny za pośrednictwem publicznego Internetu.

Przykład:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Statystyki wyjściowe umożliwiają obliczenie obowiązujących jednostek RU/s używanych do przekazywania elementów. Na przykład jeśli 25 000 jednostek ru zostały zużyte ponad 38 sekund, obowiązująca wartość RU/s to 25 000 jednostek ru/38 s = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Uruchom inny Notes w bieżącym notesie 
Możesz użyć ``%%run`` polecenia Magic, aby uruchomić inny Notes w obszarze roboczym z bieżącego notesu. Użyj składni:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Zamień ``{notebookName}`` na nazwę notesu, który chcesz uruchomić. Notes musi znajdować się w bieżącym obszarze roboczym "Moje notesy". 

## <a name="reset-notebooks-workspace"></a>Resetuj obszar roboczy notesów
Aby zresetować obszar roboczy notesy do ustawień domyślnych, wybierz pozycję **Zresetuj obszar roboczy** na pasku poleceń. Spowoduje to usunięcie wszystkich zainstalowanych pakietów niestandardowych i ponowne uruchomienie serwera Jupyter. Nie wpłynie to na Twoje notesy, pliki i zasoby platformy Azure Cosmos.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Resetuj obszar roboczy notesów":::

## <a name="next-steps"></a>Następne kroki

- Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
- Dowiedz się więcej o [interfejsie API programu Azure Cosmos DB .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)
