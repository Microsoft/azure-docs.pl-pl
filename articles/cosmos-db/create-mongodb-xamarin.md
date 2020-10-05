---
title: Tworzenie aplikacji platformy Xamarin przy użyciu interfejsu API .NET i Azure Cosmos DB dla usługi MongoDB
description: Przykładowy kod Xamarin, którego można używać do nawiązywania połączeń z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB i wysyłania do niego zapytań
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/16/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d5e8643abf41a488dd5f9b8cbc39c3f1bac1c0a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89005041"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Szybki Start: Tworzenie aplikacji Xamarin. Forms przy użyciu zestawu .NET SDK i interfejsu API Azure Cosmos DB dla MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość i grafów.

Ten przewodnik Szybki start przedstawia tworzenie [konta Cosmos skonfigurowanego za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md), bazy danych dokumentów i kolekcji przy użyciu witryny Azure Portal. Następnie utworzysz aplikację platformy Xamarin.Forms z listą zadań do wykonania za pomocą [sterownika .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Wymagania wstępne dotyczące uruchamiania przykładowej aplikacji

Aby uruchomić przykład, musisz mieć program [Visual Studio](https://www.visualstudio.com/downloads/) lub [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i ważne konto usługi Azure CosmosDB.

Jeśli nie masz jeszcze programu Visual Studio, Pobierz [program Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/) z pakietem roboczym **opracowywanie aplikacji mobilnych za pomocą platformy .NET** zainstalowanej z instalatorem.

Jeśli wolisz pracować na komputerze Mac, pobierz program [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i uruchom instalację.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Przykład opisany w tym artykule jest zgodny ze sterownikiem bazy danych MongoDB.Driver w wersji 2.6.1.

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

Najpierw pobierz przykładową aplikację z usługi GitHub. Implementuje ona aplikację listy zadań za pomocą modelu magazynu dokumentów bazy danych MongoDB.



# <a name="windows"></a>[Windows](#tab/windows)

1. W systemie Windows otwórz wiersz polecenia lub na komputerze Mac otwórz Terminal, Utwórz nowy folder o nazwie git-Samples, a następnie zamknij okno.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Jeśli nie chcesz używać narzędzia Git, możesz również [pobrać projekt jako plik zip](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string).

Wszystkie poniższe fragmenty kodu pochodzą z klasy `MongoService`, którą można znaleźć w następującej lokalizacji: src/TaskList.Core/Services/MongoService.cs.

* Inicjowanie klienta Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Pobieranie odwołania do bazy danych i kolekcji. Jeśli baza danych i kolekcja jeszcze nie istnieją, zestaw .NET SDK bazy danych MongoDB automatycznie je utworzy.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Pobieranie wszystkich dokumentów jako listy.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Wysyłanie zapytań dotyczących określonych dokumentów.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Tworzenie zadania i wstawianie go do kolekcji.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Aktualizowanie zadania w kolekcji.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Usuwanie zadania z kolekcji.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. W witrynie [Azure Portal](https://portal.azure.com/), korzystając ze swojego konta usługi Azure Cosmos DB, kliknij na lewym panelu nawigacyjnym pozycję **Parametry połączenia**, a następnie pozycję **Klucze odczytu i zapisu**. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować podstawowe parametry połączenia w kolejnych krokach.

2. Otwórz plik **APIKeys.cs** w katalogu **Helpers** projektu **TaskList.Core**.

3. Skopiuj **podstawowe parametry połączenia** z portalu (używając przycisku kopiowania) i wprowadź je jako wartość pola **ConnectionString** w pliku **APIKeys.cs**.

4. Usuń `&replicaSet=globaldb` z parametrów połączenia. Jeśli ta wartość nie zostanie usunięta z ciągu zapytania, zostanie wyświetlony komunikat o błędzie środowiska uruchomieniowego.

> [!IMPORTANT]
> Należy usunąć `&replicaSet=globaldb` parę klucz/wartość z ciągu zapytania ciągu połączenia w celu uniknięcia błędu czasu wykonywania.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB.

## <a name="run-the-app"></a>Uruchamianie aplikacji

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. W programie Visual Studio kliknij poszczególne projekty prawym przyciskiem myszy w **Eksploratorze rozwiązań** i kliknij polecenie **Zarządzaj pakietami NuGet**.
2. Kliknij polecenie **Przywróć wszystkie pakiety NuGet**.
3. Kliknij prawym przyciskiem myszy projekt **TaskList.Android** i wybierz polecenie **Ustaw jako projekt startowy**.
4. Naciśnij klawisz F5, aby rozpocząć debugowanie aplikacji.
5. Jeśli chcesz uruchamiać aplikację w systemie iOS, najpierw konieczne będzie połączenie maszyny z komputerem Mac (tutaj znajdziesz [instrukcje](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Kliknij prawym przyciskiem myszy projekt **TaskList.iOS** i wybierz polecenie **Ustaw jako projekt startowy**.
7. Naciśnij klawisz F5, aby rozpocząć debugowanie aplikacji.

### <a name="visual-studio-for-mac"></a>Visual Studio dla komputerów Mac

1. Na liście rozwijanej platform wybierz pozycję TaskList.iOS lub TaskList.Android, w zależności od platformy, na której chcesz uruchamiać aplikację.
2. Naciśnij klawisze Cmd + Enter, aby rozpocząć debugowanie aplikacji.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB i uruchamiania aplikacji Xamarin.Forms za pomocą interfejsu API dla usługi MongoDB. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Import danych do usługi Azure Cosmos DB skonfigurowanej za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-migrate.md)
