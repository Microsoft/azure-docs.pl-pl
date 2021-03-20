---
title: 'Szybki Start: interfejs API Cassandra z platformą .NET Core — Azure Cosmos DB'
description: W tym przewodniku szybki start pokazano, jak za pomocą interfejs API Cassandra Azure Cosmos DB utworzyć aplikację profilu przy użyciu Azure Portal i platformy .NET Core
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93099984"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Szybki Start: Tworzenie aplikacji Cassandra za pomocą platformy .NET Core i Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

W tym przewodniku szybki start pokazano, jak używać platformy .NET Core i [interfejs API Cassandra](cassandra-introduction.md) Azure Cosmos DB do kompilowania aplikacji profilu przez klonowanie przykładu z usługi GitHub. W tym przewodniku Szybki start pokazano również sposób tworzenia konta usługi Azure Cosmos DB przy użyciu witryny internetowej Azure Portal.

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu globalnego rozproszenia i możliwości skalowania w poziomie w usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, tabel, par klucz/wartość i grafowe. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Potrzebne są też następujące elementy: 
* Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Zainstaluj usługę [Git](https://www.git-scm.com/), aby sklonować przykład.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację interfejsu API bazy danych Cassandra z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia. Utwórz nowy folder o nazwie `git-samples`. Następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Następnie otwórz plik rozwiązania CassandraQuickStartSample w programie Visual Studio. 

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. Wszystkie fragmenty kodu pochodzą z `Program.cs` pliku w `async Task ProcessAsync()` metodzie, który został zainstalowany w `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` folderze. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string).

* Zainicjuj sesję przez nawiązanie połączenia z punktem końcowym klastra Cassandra. Interfejs API bazy danych Cassandra w usłudze Azure Cosmos DB obsługuje tylko protokół TLS 1.2. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Usuń istniejące miejsce, jeśli już istnieje.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Utwórz nową przestrzeń kluczy.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Utwórz nową tabelę.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Wstaw jednostki użytkowników przez użycie obiektu IMapper z nową sesją łączącą się z przestrzenią kluczy uprofile.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Wyślij zapytanie w celu pobrania informacji o wszystkich użytkownikach.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Wyślij zapytanie w celu pobrania informacji o jednym użytkowniku.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Informacje o parametrach połączenia umożliwiają aplikacji komunikowanie się z hostowaną bazą danych.

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Parametry połączenia**.

1. Użyj :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: przycisku po prawej stronie ekranu, aby skopiować wartość UserName.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Wyświetlanie i kopiowanie klucza dostępu w witrynie Portal Azure, strona Parametry połączenia":::

1. W programie Visual Studio Otwórz plik program. cs. 

1. Wklej wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<PROVIDE>` w wierszu 13.

    Wiersz 13 pliku Program.cs powinien teraz wyglądać podobnie do: 

    `private const string UserName = "cosmos-db-quickstart";`

    Możesz także wkleić tę samą wartość `<PROVIDE>` w wierszu 15 w polu wartość punktu kontaktu:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Wróć do portalu i skopiuj wartość HASŁO. Wklej wartość HASŁO z portalu do lokalizacji `<PROVIDE>` w wierszu 14.

    Wiersz 14 pliku Program.cs powinien teraz wyglądać podobnie do: 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Wróć do portalu i skopiuj wartość PUNKT KONTAKTOWY. Wklej wartość punkt KONTAKTowy z portalu `<PROVIDE>` do w wierszu 16.

    Wiersz 16 programu program. cs powinien teraz wyglądać podobnie do 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Zapisz plik Program.cs.
    
## <a name="run-the-net-core-app"></a>Uruchamianie aplikacji .NET Core

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.

2. W wierszu polecenia użyj poniższego polecenia, aby zainstalować pakiet NuGet sterownika platformy .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Naciśnij klawisze CTRL+F5, aby uruchomić aplikację. Aplikacja zostanie wyświetlona w oknie konsoli. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="Wyświetlanie i weryfikowanie danych wyjściowych":::

    Naciśnij klawisze CTRL+C, aby zatrzymać wykonywanie programu i zamknąć okno konsoli. 
    
4. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Wyświetlanie danych w Eksploratorze danych":::

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kontenera za pomocą Eksploratora danych i uruchamiania aplikacji internetowej. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)
