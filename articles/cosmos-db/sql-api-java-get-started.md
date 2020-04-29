---
title: 'Samouczek NoSQL: zestaw Java SDK interfejsu SQL API dla usługi Azure Cosmos DB'
description: Samouczek NoSQL, który pokazuje tworzenie bazy danych w trybie online i aplikacji konsolowej Java przy użyciu interfejsu API SQL dla usługi Azure Cosmos DB. Usługa Azure SQL jest bazą danych NoSQL dla formatu JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 9f4757bca79476a1e59f5f18a94753c1ea06cf9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80985222"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Samouczek NoSQL: tworzenie aplikacji konsolowej w języku Java dla interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Witamy w samouczku NoSQL dla zestawu Java SDK interfejsu API SQL dla usługi Azure Cosmos DB! W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

Zostaną opisane:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Konfigurowanie rozwiązania Visual Studio
* Tworzenie bazy danych w trybie online
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Usługi [git](https://git-scm.com/downloads).
* [Zestaw Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Klonowanie projektu GitHub](#GitClone). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do kroku [Klonowanie projektu GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-clone-the-github-project"></a><a id="GitClone"></a>Krok 2. Klonowanie projektu GitHub
Możesz rozpocząć od sklonowania repozytorium GitHub na potrzeby pracy z tematem [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started) (Rozpoczynanie pracy z usługą Azure Cosmos DB i językiem Java). Na przykład z katalogu lokalnego uruchom następujące polecenie, aby lokalnie pobrać przykładowy projekt.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Katalog zawiera plik `pom.xml` dla projektu i folder `src` zawierający kod źródłowy języka Java łącznie z plikiem `Program.java`, który pokazuje sposób wykonywania prostych operacji za pomocą usługi Azure Cosmos DB, takich jak tworzenie dokumentów oraz wykonywanie zapytań o dane w ramach kolekcji. Plik `pom.xml` zawiera zależność dla [zestawu Java SDK usługi Azure Cosmos DB w Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB
Następnie wróć do [Azure Portal](https://portal.azure.com) , aby pobrać punkt końcowy i podstawowy klucz główny. Klucz podstawowy i punkt końcowy usługi Azure Cosmos DB są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji.

W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**. Skopiuj identyfikator URI z portalu i wklej go w miejsce `https://FILLME.documents.azure.com` w pliku Program.java. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Zrzut ekranu przedstawiający Azure Portal używany przez samouczek NoSQL do tworzenia aplikacji konsolowej Java. Przedstawia konto usługi Azure Cosmos DB z wyróżnionym AKTYWNYM centrum, przyciskiem KLUCZE wyróżnionym w bloku konta usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI, KLUCZ PODSTAWOWY i KLUCZ POMOCNICZY wyróżnionymi w bloku Klucze][keys]

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych
Własną [bazę danych](databases-containers-items.md#azure-cosmos-databases) usługi Azure Cosmos DB można utworzyć za pomocą metody [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) klasy **DocumentClient**. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a name="step-5-create-a-collection"></a><a id="CreateColl"></a>Krok 5. Tworzenie kolekcji
> [!WARNING]
> Metoda **createCollection** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma wpływ na cenę. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Kolekcję można utworzyć za pomocą metody [createCollection ](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos containers can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a name="step-6-create-json-documents"></a><a id="CreateDoc"></a>Krok 6. Tworzenie dokumentów JSON
Dokument można utworzyć za pomocą metody [ondocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) klasy **DocumentClient** . Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć [narzędzia migracji danych](import-data.md) usługi Azure Cosmos DB, aby zaimportować dane do bazy danych.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram pokazujący hierarchiczną relację między kontem, bazą danych w trybie online, kolekcją i dokumentami używanymi przez samouczek NoSQL do tworzenia aplikacji konsolowej Java](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](how-to-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji.  Następujący przykładowy kod przedstawia sposób wykonania zapytania względem dokumentów w usłudze Azure Cosmos DB przy użyciu składni SQL za pomocą metody [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a name="step-8-replace-json-document"></a><a id="ReplaceDocument"></a>Krok 8. Zastępowanie dokumentu JSON
Usługa Azure Cosmos DB obsługuje aktualizowanie dokumentów JSON za pomocą metody [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a name="step-9-delete-json-document"></a><a id="DeleteDocument"></a>Krok 9. Usuwanie dokumentu JSON
Podobnie usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON za pomocą metody [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych
Usunięcie utworzonej bazy danych usunie bazę danych i wszystkie zasoby podrzędne (kolekcje, dokumenty itd.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a name="step-11-run-your-java-console-application-all-together"></a><a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej Java!
Aby uruchomić aplikację z poziomu konsoli, przejdź do folderu projektu i przeprowadź kompilację za pomocą narzędzia Maven:
    
    mvn package

Uruchomienie polecenia `mvn package` spowoduje pobranie najnowszej biblioteki Azure Cosmos DB z narzędzia Maven i utworzenie pliku `GetStarted-0.0.1-SNAPSHOT.jar`. Następnie można uruchomić aplikację za pomocą następującego polecenia:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Gratulacje! Pomyślnie ukończono ten samouczek NoSQL i utworzono działającą aplikację konsolową Java!

## <a name="next-steps"></a>Następne kroki
* Czy chcesz zapoznać się z samouczkiem aplikacji internetowej w języku Java? Zobacz [Build a web application with Java using Azure Cosmos DB](sql-api-java-application.md) (Tworzenie aplikacji internetowej w języku Java przy użyciu usługi Azure Cosmos DB).
* Dowiedz się, jak [monitorować konto Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
