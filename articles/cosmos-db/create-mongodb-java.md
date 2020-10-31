---
title: 'Szybki Start: Tworzenie aplikacji sieci Web przy użyciu interfejsu API Azure Cosmos DB dla Mongo DB i zestawu Java SDK'
description: Dowiedz się, jak utworzyć przykładowy kod Java, którego można użyć do nawiązania połączenia z interfejsem API Azure Cosmos DB i wykonywania zapytań przy użyciu interfejsu MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2dcd72f9231726d5a2f473dbbfa4d8fd81e64b97
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098983"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Szybki Start: Tworzenie aplikacji konsolowej przy użyciu języka Java i interfejsu API MongoDB w Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

W tym przewodniku szybki start utworzysz Azure Cosmos DB konta interfejsu API MongoDB z Azure Portal i zarządzasz nim, a następnie dodasz dane przy użyciu aplikacji Java SDK sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z parametrami połączenia `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Zestaw Java Development Kit (JDK) w wersji 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 
- [Maven](https://maven.apache.org/download.cgi). Lub uruchom `apt-get install maven` , aby zainstalować Maven.
- Usługi [git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

Nadaj nazwę **nowej bazie danych, a** Nowa kolekcja **Coll** .

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Następnie otwórz kod w ulubionym edytorze. 

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku *program. Java* .

Ta Aplikacja konsolowa używa [sterownika Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

* Inicjowanie klienta DocumentClient.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Tworzenie nowej bazy danych i kolekcji.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Wstawianie dokumentów za pomocą metody `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Wykonywanie zapytań za pomocą metody `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. Na koncie usługi Azure Cosmos DB wybierz pozycję **Szybki Start** , wybierz pozycję **Java** , a następnie skopiuj parametry połączenia do Schowka.

2. Otwórz plik *program. Java* , Zastąp argument konstruktorem MongoClientURI parametrami połączenia. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. Uruchom polecenie `mvn package` w terminalu, aby zainstalować wymagane moduły npm.

2. Uruchom polecenie `mvn exec:java -D exec.mainClass=GetStarted.Program` w terminalu, aby uruchomić aplikację Java.

Teraz można używać [Korzystanie narzędzia robomongo](mongodb-robomongo.md)  /  [Studio 3T](mongodb-mongochef.md) do wykonywania zapytań, modyfikowania i pracy z nowymi danymi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia Azure Cosmos DB interfejsu API dla konta usługi Mongo DB, dodawania bazy danych i kontenera przy użyciu Eksplorator danych i dodawania danych przy użyciu aplikacji konsolowej Java. Teraz możesz zaimportować dodatkowe dane do swojej bazy danych usługi Cosmos. 

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)