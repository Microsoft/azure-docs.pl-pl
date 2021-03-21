---
title: Azure Cosmos DB interfejs API tabel przy użyciu zestawu SDK .NET Standard
description: Dowiedz się, jak przechowywać dane strukturalne i wysyłać do nich zapytania przy użyciu konta Azure Cosmos DB interfejs API tabel
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: c641e24a498a6263d6a7c2325eed099b75a82caa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426437"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Rozpoczynanie pracy z interfejsem API tabel usługi Azure Cosmos DB oraz usługą Azure Table Storage przy użyciu zestawu SDK platformy .NET
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Za pomocą interfejs API tabel Azure Cosmos DB lub magazynu tabel platformy Azure można przechowywać strukturalne dane NoSQL w chmurze, zapewniając Magazyn kluczy/atrybutów ze schematem, który nie jest zaprojektowany. Ponieważ Azure Cosmos DB interfejs API tabel i magazyn tabel są mniej schematem, można łatwo dostosować dane w miarę rozwoju aplikacji. Za pomocą interfejs API tabel Azure Cosmos DB lub magazynu tabel można przechowywać elastyczne zestawy danych, takie jak dane użytkowników dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach lub inne typy metadanych, których wymaga Twoja usługa. 

W tym samouczku opisano przykład, w którym pokazano, jak używać [biblioteki tabel Microsoft Azure Cosmos DB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) z usługami Azure Cosmos DB interfejs API tabel i Azure Table Storage. Musisz użyć połączenia specyficznego dla usługi platformy Azure. Te scenariusze zostały omówione przy użyciu przykładów języka C#, które ilustrują sposób tworzenia tabel, wstawiania/aktualizowania danych, wykonywania zapytań dotyczących danych i usuwania tabel.

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia pracy z przykładem wymagane są następujące elementy:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Biblioteka tabel Microsoft Azure CosmosDB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) — Ta biblioteka jest obecnie dostępna dla .NET Standard i programu .NET Framework. 

* [Azure Cosmos DB interfejs API tabel konta](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Tworzenie projektu konsoli .NET

W programie Visual Studio Utwórz nową aplikację konsolową platformy .NET. Poniższe kroki pokazują, jak utworzyć aplikację konsolową w programie Visual Studio 2019. Biblioteki tabel Azure Cosmos DB można używać w dowolnym typie aplikacji platformy .NET, w tym w usłudze w chmurze platformy Azure lub aplikacji sieci Web oraz aplikacji klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. Wybierz pozycję **aplikacja konsoli (.NET Core)**, a następnie wybierz przycisk **dalej**.

1. W polu **Nazwa projektu** wprowadź nazwę aplikacji, na przykład **CosmosTableSamples**. (W razie konieczności można podać inną nazwę).

1. Wybierz przycisk **Utwórz**.

Wszystkie przykłady kodu z tego przykładu można dodać do metody Main () pliku **program. cs** aplikacji konsolowej.

## <a name="install-the-required-nuget-package"></a>Zainstaluj wymagany pakiet NuGet

Aby uzyskać pakiet NuGet, wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wyszukaj w trybie online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) pozycję, [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) i wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę tabel Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

1. W [Azure Portal](https://portal.azure.com/)przejdź do konta usługi Azure Cosmos lub konta Table Storage. 

1. Otwórz okienko **Parametry połączenia** lub **klucze dostępu** . Użyj przycisku kopiowania po prawej stronie okna, aby skopiować **PODSTAWOWE PARAMETRY POŁĄCZENIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Wyświetlanie i kopiowanie PODSTAWOWYCH PARAMETRÓW POŁĄCZENIA w okienku parametrów połączenia":::
   
1. Aby skonfigurować parametry połączenia, w programie Visual Studio kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**.

1. Wybierz pozycję **Dodaj** , a następnie pozycję **nowy element**. Utwórz nowy plik **Settings.jsna serwerze** z typem pliku jako plik **konfiguracji JSON języka TypeScript** . 

1. Zastąp kod w Settings.jsw pliku następującym kodem i przypisz podstawowe parametry połączenia:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i Dodaj klasę o nazwie **appSettings. cs**.

1. Dodaj następujący kod do pliku AppSettings. cs. Ten plik odczytuje parametry połączenia z Settings.jsw pliku i przypisuje je do parametru konfiguracji:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Analizowanie i weryfikowanie szczegółów połączenia

1. Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i Dodaj klasę o nazwie **Common. cs**. Napiszesz kod, aby zweryfikować szczegóły połączenia i utworzyć tabelę w tej klasie.

1. Zdefiniuj metodę `CreateStorageAccountFromConnectionString` , jak pokazano poniżej. Ta metoda analizuje szczegóły parametrów połączenia i sprawdza, czy nazwa konta i szczegóły klucza konta podane w pliku "Settings.json" są prawidłowe.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Tworzenie tabeli 

Klasa [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) umożliwia pobieranie tabel i jednostek przechowywanych w usłudze Table Storage. Ponieważ nie mamy żadnych tabel na koncie Cosmos DB interfejs API tabel, dodajmy `CreateTableAsync` metodę do klasy **Common. cs** , aby utworzyć tabelę:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Jeśli wystąpi błąd "wyjątek niedostępności usługi 503", istnieje możliwość, że wymagane porty dla trybu łączności są blokowane przez zaporę. Aby rozwiązać ten problem, należy otworzyć wymagane porty lub użyć łączności w trybie bramy, jak pokazano w poniższym kodzie:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Zdefiniuj jednostkę 

Jednostki mapują obiekty do obiektów C# przy użyciu klasy niestandardowej pochodnej od [klasy tableentity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki.

Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **Nowy folder** i nadaj jej nazwę **model**. W folderze model Dodaj klasę o nazwie **CustomerEntity. cs** i Dodaj do niej następujący kod.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Ten kod definiuje klasę jednostki używającą imienia klienta jako klucza wiersza i nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, ale użycie różnych kluczy partycji umożliwia większą skalowalność operacji równoległych. Jednostki, które mają być przechowywane w tabelach, muszą być obsługiwanego typu, na przykład muszą pochodzić od klasy [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Właściwości jednostki, które zamierzasz przechowywać w tabeli, muszą być publicznymi właściwościami typu i obsługiwać zarówno pobieranie, jak i ustawianie wartości. Ponadto typ jednostki musi ujawniać konstruktor bez parametrów.

## <a name="insert-or-merge-an-entity"></a>Wstawianie lub scalanie jednostki

Poniższy przykład kodu tworzy obiekt Entity i dodaje go do tabeli. Metoda InsertOrMerge w klasie [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) służy do wstawiania lub scalania jednostki. Metoda [CloudTable.ExecuteAsync](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) jest wywoływana, aby wykonać operację. 

Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i Dodaj klasę o nazwie **SamplesUtils. cs**. Ta klasa przechowuje wszystkie kody wymagane do wykonywania operacji CRUD na jednostkach. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Pobieranie jednostki z partycji

Jednostkę można pobrać z partycji za pomocą metody get w klasie [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . Poniższy przykład kodu Pobiera klucz wiersza klucza partycji, adres e-mail i numer telefonu jednostki klienta. Ten przykład drukuje również jednostki żądań zużyte do zapytania dla jednostki. Aby wykonać zapytanie dotyczące jednostki, Dodaj następujący kod do pliku **SamplesUtils. cs** :

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Usuwanie jednostki

Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta. Aby usunąć jednostkę, Dołącz następujący kod do pliku **SamplesUtils. cs** : 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Wykonywanie operacji CRUD na przykładowych danych

Po zdefiniowaniu metod tworzenia tabeli, wstawiania lub scalania jednostek należy uruchomić te metody na przykładowych danych. Aby to zrobić, kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i Dodaj klasę o nazwie **BasicSamples. cs** i Dodaj do niej następujący kod. Ten kod tworzy tabelę, dodaje do niej jednostki.

Jeśli nie chcesz usuwać jednostki i tabeli na końcu projektu, Dodaj komentarz do `await table.DeleteIfExistsAsync()` metod i w `SamplesUtils.DeleteEntityAsync(table, customer)` poniższym kodzie. Najlepiej jest skomentować te metody i zweryfikować dane przed usunięciem tabeli.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Poprzedni kod tworzy tabelę rozpoczynającą się od "demonstracji", a wygenerowany identyfikator GUID jest dołączany do nazwy tabeli. Następnie dodaje jednostkę klienta z imię i nazwisko jako "Harpa Waltera", a później aktualizuje numer telefonu tego użytkownika. 

W tym samouczku przedstawiono tworzenie kodu do wykonywania podstawowych operacji CRUD na danych przechowywanych na koncie interfejs API tabel. Można również wykonywać Zaawansowane operacje, takie jak — wsadowe Wstawianie danych, wykonywanie zapytań dotyczących wszystkich danych w obrębie partycji, wykonywanie zapytań względem zakresu danych w obrębie partycji, wyświetlanie listy tabel na koncie, których nazwy zaczynają się od określonego prefiksu. Możesz pobrać kompletną przykładową postać [Azure-Cosmos-Table-dotnet-Core-](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) Rozpoczynanie pracy z repozytorium GitHub. Klasa [AdvancedSamples. cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) ma więcej operacji, które można wykonywać na danych.  

## <a name="run-the-project"></a>Uruchamianie projektu

Z projektu **CosmosTableSamples**. Otwórz klasę o nazwie **program. cs** i Dodaj do niej następujący kod służący do wywoływania BasicSamples podczas działania projektu.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Teraz Skompiluj rozwiązanie i naciśnij klawisz F5, aby uruchomić projekt. Po uruchomieniu projektu w wierszu polecenia zostaną wyświetlone następujące dane wyjściowe:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Dane wyjściowe z wiersza polecenia":::

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że nie można odnaleźć Settings.jsw pliku podczas uruchamiania projektu, można go rozwiązać, dodając następujący wpis XML do ustawień projektu. Kliknij prawym przyciskiem myszy pozycję CosmosTableSamples, wybierz pozycję Edytuj CosmosTableSamples. csproj i Dodaj następujący element: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Teraz możesz zalogować się do Azure Portal i sprawdzić, czy dane istnieją w tabeli. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Wyniki w portalu":::

## <a name="next-steps"></a>Następne kroki

Teraz możesz przechodzić do następnego samouczka i dowiedzieć się, jak migrować dane do konta interfejs API tabel Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Migrowanie danych do usługi Azure Comsos DB interfejs API tabel](../cosmos-db/table-import.md)