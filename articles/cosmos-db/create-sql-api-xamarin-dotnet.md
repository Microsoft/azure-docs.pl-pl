---
title: 'Azure Cosmos DB: tworzenie aplikacji z listą zadań do wykonania za pomocą oprogramowania Xamarin'
description: Przykładowy kod Xamarin, którego można używać do nawiązywania połączeń z usługą Azure Cosmos DB i wykonywania w niej zapytań
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a940f4bb519332e147577e4a9172406c401d152
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365743"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Szybki start: tworzenie aplikacji z todo za pomocą platformy Xamarin przy użyciu Azure Cosmos DB interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość i grafów.

> [!NOTE]
> Przykładowy kod dla całej kanonicznej przykładowej aplikacji platformy Xamarin z wieloma ofertami platformy Azure, w tym usługą CosmosDB, można znaleźć w witrynie GitHub [tutaj](https://github.com/xamarinhq/app-geocontacts). Ta aplikacja demonstruje wyświetlanie geograficznie rozproszonych kontaktów i umożliwianie tym kontaktom zaktualizowanie ich lokalizacji.

Ten przewodnik Szybki start przedstawia, jak utworzyć konto interfejsu API SQL usługi Azure Cosmos DB, bazę danych dokumentów i kontener przy użyciu witryny Azure Portal. Następnie za pomocą platformy [Xamarin.Forms](/xamarin/) i wzorca architektury MVVM skompilowana na podstawie interfejsu API SQL platformy [.NET](sql-api-sdk-dotnet.md) i platformy [Xamarin](/xamarin/) zostanie skompilowana i wdrożona aplikacja mobilna z listą [zadań do zadań do zatwierdzeń.](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Aplikacji Xamarin z listą zadań do wykonania działająca w systemie iOS":::

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli program jesteś w systemie Windows i nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać bezpłatny program  [Visual Studio Community Edition 2019](https://www.visualstudio.com/downloads/)i korzystać z tego programu. Podczas instalacji programu Visual Studio upewnij się, że włączono pakiety robocze **Programowanie na platformie Azure** i **Opracowywanie aplikacji mobilnych za pomocą środowiska .NET**.

Jeśli używasz komputera Mac, możesz pobrać **bezpłatny program** [Visual Studio dla komputerów Mac](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu Xamarin SQL API z repozytorium GitHub, przejrzymy kod, uzyskamy klucze interfejsu API i uruchomimy aplikację. Zobaczysz, jak łatwo jest pracować programowo z danymi.

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. W Visual Studio otwórz plik **C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Uzyskiwanie kluczy interfejsu API

Wróć do witryny Azure Portal, aby uzyskać informacje o kluczach interfejsu API i skopiować je do aplikacji.

1. W witrynie [Azure Portal](https://portal.azure.com/), korzystając ze swojego konta interfejsu API SQL usługi Azure Cosmos DB, kliknij na lewym panelu nawigacyjnym pozycję **Klucze**, a następnie pozycję **Klucze odczytu i zapisu**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz identyfikator URI i klucz podstawowy do pliku APIKeys.cs.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze":::

2. W Visual Studio otwórz **toDoItems.Core/Helpers/APIKeys.cs.**

3. W Azure Portal za pomocą przycisku kopiowania skopiuj wartość **URI** i nadaj jej wartość zmiennej `CosmosEndpointUrl` w pliku APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure portal]";
    ```

4. W Azure Portal przy użyciu przycisku kopiowania skopiuj wartość **KLUCZ PODSTAWOWY** i nadaj jej wartość w `Cosmos Auth Key` pliku APIKeys.cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

To rozwiązanie przedstawia sposób tworzenia aplikacji z listą zadań do wykonania przy użyciu interfejsu API SQL usługi Azure Cosmos DB i platformy Xamarin.Forms. Aplikacja ma dwie karty. Na pierwszej z nich jest widok listy zadań, które nie zostały jeszcze wykonane. Na drugiej karcie są wyświetlane zadania, które zostały już wykonane. Oprócz wyświetlania na pierwszej karcie zadań, które nie zostały jeszcze wykonane, można również dodawać nowe zadania do wykonania, edytować istniejące zadania i oznaczać zadania jako wykonane.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Kopiowanie danych json i klikanie pozycji Zapisz w Eksploratorze danych w witrynie Azure Portal":::

Kod w rozwiązaniu ToDoItems zawiera następujące elementy:

* **ToDoItems.Core**
   * jest to projekt .NET Standard zawierający projekt platformy Xamarin.Forms i współużytkowany kod logiki aplikacji obsługujący zadania do wykonania w usłudze Azure Cosmos DB.
* **ToDoItems.Android**
  * ten projekt zawiera aplikację dla systemu Android.
* **ToDoItems.iOS**
  * ten projekt zawiera aplikację dla systemu iOS.

Teraz dokonamy szybkiego przeglądu sposobu komunikowania się aplikacji z usługą Azure Cosmos DB.

* Wymagane jest dodanie pakietu NuGet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) do wszystkich projektów.
* Klasa `ToDoItem` w **folderze ToDoItems.Core/Models** modeluje dokumenty w utworzonym powyżej **kontenerze** Items. Należy pamiętać, że w nazwach właściwości jest rozróżniana wielkość liter.
* Klasa `CosmosDBService` w **folderze ToDoItems.Core/Services** hermetyzuje komunikację z Azure Cosmos DB.
* Klasa `CosmosDBService` zawiera zmienną typu `DocumentClient`. Polecenie `DocumentClient` służy do konfigurowania i wykonywania żądań względem Azure Cosmos DB i jest wykonywane:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Podczas wykonywania zapytania o dokumenty w kontenerze `DocumentClient.CreateDocumentQuery<T>` używana jest metoda , jak podano tutaj w funkcji `CosmosDBService.GetToDoItems` :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    Przyjmuje `CreateDocumentQuery<T>` on URI, który wskazuje kontener utworzony w poprzedniej sekcji. Można również określić operatory LINQ, na przykład klauzulę `Where`. W tym przypadku zwracane są tylko te zadania, które nie zostały jeszcze wykonane.

    Funkcja `CreateDocumentQuery<T>` jest wykonywana synchronicznie i zwraca obiekt `IQueryable<T>`. Jednak metoda `AsDocumentQuery` konwertuje obiekt `IQueryable<T>` na obiekt , który może być wykonywany `IDocumentQuery<T>` asynchronicznie. Dzięki temu wątek interfejsu użytkownika nie jest blokowany w aplikacjach mobilnych.

    Funkcja pobiera stronę wyników z Azure Cosmos DB, która sprawdzi, czy zostaną zwrócone `IDocumentQuery<T>.ExecuteNextAsync<T>` `HasMoreResults` dodatkowe wyniki.

> [!TIP]
> Kilka funkcji, które działają na kontenerach i dokumentach usługi Azure Cosmos, bierze jako parametr URI, który określa adres kontenera lub dokumentu. Ten identyfikator URI jest tworzony przy użyciu klasy `URIFactory`. Za pomocą tej klasy można tworzyć wszystkie interfejsy URI dla baz danych, kontenerów i dokumentów.

* Funkcja `ComsmosDBService.InsertToDoItem` pokazuje, jak wstawić nowy dokument:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Zostanie określony adres URI elementu, a także element, który ma zostać wstawiony.

* Funkcja `CosmosDBService.UpdateToDoItem` pokazuje, jak zastąpić istniejący dokument nowym:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    W tym miejscu nowy identyfikator URI jest potrzebny do unikatowego identyfikowania dokumentu do zastąpienia. Jest on uzyskiwany przy użyciu funkcji i przekazywaniu do niego nazw baz danych i kontenerów oraz `UriFactory.CreateDocumentUri` identyfikatora dokumentu.

    Funkcja `DocumentClient.ReplaceDocumentAsync` zastępuje dokument identyfikowany przez identyfikator URI dokumentem określonym jako parametr.

* Usuwanie elementu jest demonstrowane za pomocą `CosmosDBService.DeleteToDoItem` funkcji :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Ponownie zanotuj utworzony i przekazany do funkcji `DocumentClient.DeleteDocumentAsync` unikatowy identyfikator URI dokumentu.

## <a name="run-the-app"></a>Uruchamianie aplikacji

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB.

W poniższych krokach zademonstrowano sposób uruchamiania aplikacji przy użyciu debugera programu Visual Studio dla komputerów Mac.

> [!NOTE]
> Sposób użycia aplikacji w wersji dla systemu Android jest dokładnie taki sam. Ewentualne różnice zostaną przestawione w poniższych krokach. Jeśli wolisz debugować tę aplikację w programie Visual Studio w systemie Windows, [tutaj można znaleźć dokumentację dla systemu iOS](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin), a [tutaj dla systemu Android](/xamarin/android/deploy-test/debugging/).

1. Najpierw wybierz platformę docelową, klikając wyróżnioną listę rozwijaną i wybierając pozycję ToDoItems.iOS dla systemu iOS lub pozycję ToDoItems.Android dla systemu Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Wybieranie platformy do debugowania w programie Visual Studio dla komputerów Mac":::

2. Aby rozpocząć debugowanie aplikacji, naciśnij klawisze cmd+Enter lub kliknij przycisk odtwarzania.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Uruchamianie na potrzeby debugowania w programie Visual Studio dla komputerów Mac":::

3. Po zakończeniu uruchamiania symulatora systemu iOS lub emulatora systemu Android aplikacja wyświetli dwie karty w dolnej części ekranu dla systemu iOS i w górnej części ekranu dla systemu Android. Pierwszy pokazuje zadania do zadania, które nie zostały ukończone, a drugi przedstawia zadania do zadania do zadania, które zostały ukończone.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Ekran z uruchomioną aplikacją z listą zadań do wykonania":::

4. Aby oznaczyć zadanie jako wykonane w systemie iOS, przesuń zadanie w lewą stronę i naciśnij przycisk **Complete** (Wykonane). Aby oznaczyć zadanie jako wykonane w systemie Android, naciśnij je i przytrzymaj, a następnie naciśnij przycisk Complete (Wykonane).

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Oznaczanie zadania jako wykonane":::

5. Aby edytować zadanie do wykonania, naciśnij je. Zostanie wyświetlony nowy ekran umożliwiający wprowadzenie nowych wartości. Po naciśnięciu przycisku zapisywania zmiany zostaną utrwalone w usłudze Azure Cosmos DB.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Edytowanie zadania do wykonania":::

6. Aby dodać zadanie do wykonania, naciśnij przycisk **Add** (Dodaj) w prawym górnym rogu ekranu głównego. Zostanie wyświetlona nowa pusta strona edycji.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Dodawanie zadania do wykonania":::

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Cosmos, tworzenia kontenera przy użyciu usługi Eksplorator danych oraz kompilowania i wdrażania aplikacji platformy Xamarin. Teraz możesz zaimportować więcej danych do konta usługi Azure Cosmos.

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
