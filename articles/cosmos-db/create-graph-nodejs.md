---
title: Tworzenie aplikacji Node.js w usłudze Azure Cosmos DB za pomocą interfejsu API języka Gremlin
description: Przykładowy kod Node.js, którego można używać do nawiązywania połączeń z usługą Azure Cosmos DB i wykonywania w niej zapytań
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360143"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Szybki Start: Tworzenie aplikacji Node.js przy użyciu konta interfejsu API usługi Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

W tym przewodniku szybki start utworzysz konto interfejsu API Azure Cosmos DB Gremlin (Graph) z Azure Portal i zarządzasz nim, a następnie dodasz dane przy użyciu aplikacji Node.js sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Node.js 0.10.29 +](https://nodejs.org/).
- Usługi [git](https://git-scm.com/downloads).

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Otwórz plik rozwiązania w programie Visual Studio. 

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku *app.js*.

Ta Aplikacja konsolowa używa sterownika [Gremlin Node.js](https://www.npmjs.com/package/gremlin) Open Source.

* Tworzenie klienta języka Gremlin.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  Konfiguracje są wszystkie w *config.js*, które edytujemy w [poniższej sekcji](#update-your-connection-string).

* W celu wykonywania różnych operacji języka Gremlin zdefiniowano serie funkcji. To jest jedna z nich:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Każda funkcja wykonuje metodę `client.execute` z parametrem ciągu zapytania języka Gremlin. Oto przykład wykonywania metody `g.V().count()`:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* Na końcu pliku są następnie wywoływane wszystkie metody. Powoduje wykonywanie ich jedna po drugiej:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

1. Otwórz plik *config.js* . 

2. W *config.js* Wypełnij `config.endpoint` klucz wartością **Endpoint Gremlin** na stronie **Przegląd** konta Cosmos DB w Azure Portal. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Wyświetlanie i kopiowanie klucza dostępu w Azure Portal, Strona przegląd":::

3. W *config.js* Wypełnij wartość config. PrimaryKey wartością **klucza podstawowego** ze strony **klucze** konta Cosmos DB w Azure Portal. 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Blok kluczy Azure Portal":::

4. Wprowadź nazwę bazy danych i nazwę grafu (kontenera) dla wartości parametrów config.database i config.collection. 

Oto przykład tego, jak ukończony plik *config.js* powinien wyglądać następująco:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. Otwórz okno terminalu i Zmień (za pośrednictwem `cd` polecenia) do katalogu instalacyjnego *package.jsw* pliku, który znajduje się w projekcie.

2. Uruchom polecenie `npm install`, aby zainstalować wymagane moduły npm, w tym `gremlin`.

3. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

## <a name="browse-with-data-explorer"></a>Przeglądanie w Eksploratorze danych

Teraz możesz wrócić do Eksploratora danych w witrynie Azure Portal, aby wyświetlać nowe dane grafu, wykonywać o nie zapytania, modyfikować je i pracować z nimi.

W Eksploratorze danych nowa baza danych jest wyświetlana w okienku **Grafy**. Rozwiń bazę danych i kontener, a następnie wybierz pozycję **Graf**.

Dane generowane przez aplikację przykładową będą wyświetlane w kolejnym okienku na karcie **Graf**, gdy wybierzesz przycisk **Zastosuj filtr**.

Spróbuj uzupełnić wartość `g.V()` ciągiem `.has('firstName', 'Thomas')`, aby przetestować filtr. Pamiętaj, że w wartości jest uwzględniana wielkość liter.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia wykresu przy użyciu Eksplorator danych i uruchamiania aplikacji Node.js w celu dodania danych do grafu. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)
