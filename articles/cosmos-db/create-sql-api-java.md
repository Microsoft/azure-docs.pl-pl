---
title: Szybki start — tworzenie bazy danych dokumentów przy użyciu języka Java przy użyciu języka Azure Cosmos DB
description: Ten przewodnik Szybki start przedstawia przykładowy kod Java, który umożliwia nawiązywanie połączeń z interfejsem API SQL i wykonywanie zapytań Azure Cosmos DB API SQL
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: e1f81ddba717dc2a9df02fda82ef74b52da8fd82
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366049"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki start: tworzenie aplikacji Java do zarządzania danymi interfejsu API SQL Azure Cosmos DB SQL
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

W tym przewodniku Szybki start utworzysz konto interfejsu API SQL Azure Cosmos DB i zarządzasz Azure Portal, a także za pomocą aplikacji Java sklonowanej z usługi GitHub. Najpierw należy utworzyć konto interfejsu API SQL usługi Azure Cosmos DB przy użyciu usługi Azure Portal, następnie utworzyć aplikację Java przy użyciu zestawu SDK języka Java języka SQL, a następnie dodać zasoby do konta Cosmos DB przy użyciu aplikacji Java. Azure Cosmos DB to wielo modelowa usługa bazy danych, która umożliwia szybkie tworzenie dokumentów, tabel, klucz-wartość i grafowych baz danych z możliwościami globalnej dystrybucji i skalowania w poziomie.

> [!IMPORTANT]  
> Ten przewodnik Szybki start jest Azure Cosmos DB zestawu Java SDK w wersji 4. Aby uzyskać więcej [informacji,](performance-tips-java-sdk-v4-sql.md)zobacz Azure Cosmos DB informacje o wersji zestawu Java SDK w wersji [4,](sql-api-sdk-java-v4.md)repozytorium [Maven,](https://mvnrepository.com/artifact/com.azure/azure-cosmos)porady dotyczące wydajności zestawu Azure Cosmos DB JAVA SDK w wersji 4 oraz przewodnik rozwiązywania problemów z zestawem AZURE COSMOS DB Java SDK w wersji [4.](troubleshoot-java-sdk-v4-sql.md) Jeśli obecnie używasz starszej wersji niż wersja 4, zobacz przewodnik Migrowanie do wersji 4 zestawu [Java SDK 4](migrate-java-v4-sdk.md) Azure Cosmos DB, aby uzyskać pomoc w uaktualnianiu do wersji 4.
>

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Możesz [też Azure Cosmos DB bezpłatnie bez](https://azure.microsoft.com/try/cosmosdb/) subskrypcji platformy Azure. Możesz również użyć emulatora [Azure Cosmos DB z](https://aka.ms/cosmosdb-emulator) URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Zestaw Java Development Kit (JDK) 8.](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) W zmiennej `JAVA_HOME` środowiskowej wskaż folder, w którym zainstalowano plik JDK.
- Archiwum [binarne maven.](https://maven.apache.org/download.cgi) W systemie Ubuntu uruchom program , `apt-get install maven` aby zainstalować program Maven.
- [Git](https://www.git-scm.com/downloads). W systemie Ubuntu uruchom program , `sudo apt-get install git` aby zainstalować program Git.

## <a name="introductory-notes"></a>Uwagi wprowadzające

*Struktura konta Cosmos DB konta.* Niezależnie od interfejsu API lub języka  programowania konto usługi Cosmos DB zawiera zero lub więcej baz *danych,* baza danych *(DB)* zawiera zero lub więcej kontenerów,  *a* kontener zawiera zero lub więcej elementów, jak pokazano na poniższym diagramie:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Jednostki konta usługi Azure Cosmos" border="false":::

Więcej informacji na temat baz danych, kontenerów i elementów można uzyskać [tutaj.](account-databases-containers-items.md) Na poziomie kontenera zdefiniowano kilka ważnych właściwości, między innymi aprowizowanej *przepływności* i *klucza partycji.* 

Aprowizowana przepływność jest mierzona w jednostkach żądania *(JEDNOSTKI* ŻĄDAŃ), które mają cenę pieniężną i są znaczącym czynnikiem określającym koszt operacyjny konta. Aprowizowana przepływność można wybrać z poziomem szczegółowości dla 1 kontenera lub z poziomem szczegółowości dla bazy danych, jednak zwykle preferowana jest specyfikacja przepływności na poziomie kontenera. Więcej informacji na temat aprowizowania przepływności można uzyskać [tutaj.](set-throughput.md)

Gdy elementy są wstawiane do kontenera Cosmos DB, baza danych zwiększa się w poziomie, dodając więcej magazynu i zasobów obliczeniowych do obsługi żądań. Pojemność magazynu i mocy obliczeniowej są dodawane w jednostkach dyskretnych nazywanych partycjami i należy wybrać jedno pole w dokumentach jako klucz partycji, który mapuje każdy dokument na partycję. Sposób zarządzania partycjami to przypisanie każdej partycji w przybliżeniu równego wycinka z zakresu wartości klucza partycji; Dlatego zaleca się wybranie klucza partycji, który jest względnie losowy lub równomiernie rozproszony. W przeciwnym razie w niektórych partycjachbędzie znacznie więcej żądań (gorąca partycja), podczas gdy w innych partycjach będzie znacznie mniej żądań *(partycja* zimna), a tego należy unikać. Więcej informacji na temat partycjonowania można uzyskać [tutaj.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów należy utworzyć konto interfejsu API SQL za pomocą usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujmy aplikację interfejsu API SQL z serwisu GitHub, ustawmy parametry połączenia i uruchommy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 


# <a name="sync-api"></a>[Interfejs API synchronizacji](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Zarządzanie zasobami bazy danych przy użyciu interfejsu API synchronicznego (synchronizacji)

* Inicjowanie klienta `CosmosClient`. Zapewnia logiczną reprezentację usługi bazy danych Azure Cosmos po `CosmosClient` stronie klienta. Ten klient jest używany do konfigurowania i wykonywania żądań dotyczących usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Tworzenie elementu przy użyciu `createItem` metody .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Odczyty punktu są wykonywane przy użyciu `readItem` metody .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Zapytania SQL w języku JSON są wykonywane przy użyciu `queryItems` metody .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Asynchroniczny interfejs API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Zarządzanie zasobami bazy danych przy użyciu asynchronicznego interfejsu API

* Asynchroniczne wywołania interfejsu API są zwracane natychmiast, bez oczekiwania na odpowiedź z serwera. W związku z tym poniższe fragmenty kodu pokazują prawidłowe wzorce projektowe służące do wykonywania wszystkich poprzednich zadań zarządzania przy użyciu asynchronicznego interfejsu API.

* Inicjowanie klienta `CosmosAsyncClient`. Zapewnia logiczną reprezentację usługi bazy danych Azure Cosmos po `CosmosAsyncClient` stronie klienta. Ten klient służy do konfigurowania i wykonywania żądań asynchronicznych względem usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Podobnie jak w przypadku interfejsu API synchronizacji, tworzenie elementu jest realizowane przy użyciu `createItem` metody . W tym przykładzie pokazano, jak efektywnie wystawiać wiele żądań asynchronicznych przez subskrybowanie strumienia reaktywnego, który wystawia żądania i `createItem` wyświetla powiadomienia. Ponieważ ten prosty przykład jest uruchamiany do ukończenia i zakończenia, wystąpienia są używane w celu zapewnienia, że program nie zakończy `CountDownLatch` się podczas tworzenia elementu. **Właściwą praktyką programowania asynchronicznego nie jest blokowanie wywołań asynchronicznych — w realistycznych przypadkach użycia żądania są generowane z pętli main(), która jest wykonywana przez czas nieokreślony, eliminując konieczność zatrzaśkania wywołań asynchronicznych.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Podobnie jak w przypadku interfejsu API synchronizacji odczyty punktów są wykonywane przy użyciu `readItem` metody .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Podobnie jak w przypadku interfejsu API synchronizacji zapytania SQL w danych JSON są wykonywane przy użyciu `queryItems` metody .

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. W oknie terminala usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Java.

    ```bash
    mvn package
    ```

3. W oknie terminalu usługi Git użyj następującego polecenia, aby uruchomić aplikację Java (zastąp element SYNCASYNCMODE kodem lub w zależności od tego, który przykładowy kod chcesz uruchomić, zastąp wartość YOUR_COSMOS_DB_HOSTNAME wartością w cudzysłowym URI z portalu i zastąp wartość YOUR_COSMOS_DB_MASTER_KEY cudzysłowym kluczem podstawowym z `sync` `async` portalu)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Okno terminala wyświetli powiadomienie o utworzeniu bazy danych FamilyDB. 
    
4. Aplikacja tworzy bazę danych o nazwie `AzureSampleFamilyDB`
5. Aplikacja tworzy kontener o nazwie `FamilyContainer`
6. Aplikacja będzie wykonywać odczyty punktów przy użyciu identyfikatorów obiektów i wartości klucza partycji (w naszym przykładzie jest to lastName). 
7. Aplikacja będzie odpytywać elementy w celu pobrania wszystkich rodzin z nazwiskiem w elementach ("Andersen", "Wakefield", "Gdy")

7. Aplikacja nie usuwa utworzonych zasobów. Przełącz się do portalu, aby [wyczyścić zasoby](#clean-up-resources).  z poziomu konta, aby nie spowodować naliczenia opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wiesz już, jak utworzyć konto interfejsu API SQL usługi Azure Cosmos DB, utworzyć bazę danych dokumentów i kontener przy użyciu interfejsu Eksplorator danych oraz uruchomić aplikację Java, aby zrobić to samo programowo. Teraz możesz zaimportować dodatkowe dane na swoje Azure Cosmos DB konto. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
