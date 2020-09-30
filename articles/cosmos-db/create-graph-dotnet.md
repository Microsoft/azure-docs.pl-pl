---
title: Kompilowanie .NET Framework Azure Cosmos DB, aplikacji podstawowych przy użyciu interfejsu API Gremlin
description: Przykładowy kod programu .NET Framework/Core, którego można używać do nawiązywania połączeń z usługą Azure Cosmos DB i wykonywania w niej zapytań
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: jasonh
ms.custom: devx-track-dotnet
ms.openlocfilehash: 72b3f8a4e4a6da434ef20c8e505718ccd3e2deff
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578392"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Szybki Start: Tworzenie aplikacji .NET Framework lub Core przy użyciu konta interfejsu API Azure Cosmos DB Gremlin

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość i grafów. 

Ten przewodnik Szybki Start przedstawia sposób Azure Cosmos DB tworzenia konta [interfejsu API Gremlin](graph-introduction.md) , bazy danych i grafu (kontenera) przy użyciu Azure Portal. Następnie aplikacja konsoli utworzona za pomocą sterownika open source [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) zostanie skompilowana i uruchomiona.  

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Dodawanie wykresu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API języka Gremlin z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Następnie otwórz program Visual Studio i otwórz plik rozwiązania.

5. Przywróć pakiety NuGet w projekcie. Powinny one obejmować sterownik Gremlin.Net, a także pakiet Newtonsoft.Json.


6. Sterownik Gremlin.Net możesz także zainstalować ręcznie, za pomocą menedżera pakietów NuGet lub [narzędzia wiersza polecenia nuget](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku Program.cs.

* Ustaw parametry połączenia na podstawie utworzonego powyżej konta: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Polecenia Gremlin, które mają zostać wykonane, są wymienione w słowniku:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Utwórz nowe `GremlinServer` i `GremlinClient` obiekty połączenia, korzystając z parametrów podanych powyżej:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Wykonaj każde zapytanie Gremlin, używając `GremlinClient` obiektu z zadaniem asynchronicznym. Można odczytać zapytania Gremlin z słownika zdefiniowanego w poprzednim kroku i wykonać je. Później Pobierz wynik i odczytaj wartości, które są sformatowane jako słownik, używając `JsonSerializer` klasy z Newtonsoft.Jsw pakiecie:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do swojego konta bazy danych programu Graph. Na karcie **Przegląd** są wyświetlane dwa punkty końcowe: 
 
   **Identyfikator URI zestawu .NET SDK** — ta wartość jest używana podczas łączenia się z kontem grafu przy użyciu biblioteki Microsoft. Azure. graphs. 

   **Punkt końcowy języka Gremlin** — ta wartość jest używana podczas nawiązywania połączenia z kontem programu Graph przy użyciu biblioteki Gremlin.Net.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Kopiowanie punktu końcowego&quot;:::

   Aby uruchomić ten przykład, skopiuj wartość **punktu końcowego Gremlin** , Usuń numer portu na końcu, który jest identyfikatorem URI `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . Wartość punktu końcowego powinna wyglądać jak `testgraphacct.gremlin.cosmosdb.azure.com`

1. Następnie przejdź do karty **klucze** i skopiuj wartość **klucz podstawowy** z Azure Portal. 

1. Po skopiowaniu identyfikatora URI i klucza podstawowego konta Zapisz je w nowej zmiennej środowiskowej na maszynie lokalnej, na której działa aplikacja. Aby ustawić zmienną środowiskową, Otwórz okno wiersza polecenia i uruchom następujące polecenie. Pamiętaj, aby zastąpić <Your_Azure_Cosmos_account_URI> i <Your_Azure_Cosmos_account_PRIMARY_KEY> wartości.

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>&quot;
   ```

1. Otwórz plik *program.cs* i zaktualizuj zmienne &quot;Database i" Container "przy użyciu bazy danych i kontenera (która jest również nazwą grafu) utworzonymi powyżej.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Zapisz plik Program.cs. 

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. Aplikacja będzie wyświetlać polecenia zapytań języka Gremlin i ich wyniki w konsoli.

   W oknie konsoli będą widoczne wierzchołki i krawędzie dodawane do grafu. Po zakończeniu działania skryptu naciśnij klawisz ENTER dwa razy, aby zamknąć okno konsoli.

## <a name="browse-using-the-data-explorer"></a>Przeglądanie za pomocą Eksploratora danych

Teraz możesz wrócić do Eksploratora danych w witrynie Azure Portal, aby przeglądać nowe dane i wykonywać zapytania względem nich.

1. W Eksploratorze danych nowa baza danych jest wyświetlana w okienku Grafy. Rozwiń węzły bazy danych i kontenera, a następnie kliknij pozycję **Graf**.

2. Kliknij polecenie **Zastosuj filtr**, aby użyć domyślnego zapytania do wyświetlenia wszystkich wierzchołków grafu. Dane wygenerowane przez przykładową aplikację zostaną wyświetlone w okienku Grafy.

    Możesz powiększać i zmniejszać graf, rozszerzać obszar wyświetlania grafu, dodawać kolejne wierzchołki oraz przenosić wierzchołki na wyświetlanej powierzchni.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Kopiowanie punktu końcowego&quot;:::

   Aby uruchomić ten przykład, skopiuj wartość **punktu końcowego Gremlin** , Usuń numer portu na końcu, który jest identyfikatorem URI `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . Wartość punktu końcowego powinna wyglądać jak `testgraphacct.gremlin.cosmosdb.azure.com`

1. Następnie przejdź do karty **klucze** i skopiuj wartość **klucz podstawowy** z Azure Portal. 

1. Po skopiowaniu identyfikatora URI i klucza podstawowego konta Zapisz je w nowej zmiennej środowiskowej na maszynie lokalnej, na której działa aplikacja. Aby ustawić zmienną środowiskową, Otwórz okno wiersza polecenia i uruchom następujące polecenie. Pamiętaj, aby zastąpić <Your_Azure_Cosmos_account_URI> i <Your_Azure_Cosmos_account_PRIMARY_KEY> wartości.

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>&quot;
   ```

1. Otwórz plik *program.cs* i zaktualizuj zmienne &quot;Database i":::

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia grafu za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)

