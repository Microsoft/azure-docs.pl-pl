---
title: Szybki Start — używanie języka Java do tworzenia bazy danych dokumentów przy użyciu Azure Cosmos DB
description: Ten przewodnik Szybki Start przedstawia przykładowy kod Java, którego można użyć do nawiązania połączenia z interfejsem API SQL Azure Cosmos DB i wykonywania na nich zapytań
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090413"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki Start: Tworzenie aplikacji Java do zarządzania Azure Cosmos DB danych interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejsu API SQL i zarządzasz nim z Azure Portal i używasz aplikacji Java sklonowanej z usługi GitHub. Najpierw utwórz konto Azure Cosmos DB interfejsu API SQL przy użyciu Azure Portal, a następnie Utwórz aplikację Java przy użyciu zestawu SQL Java SDK, a następnie Dodaj zasoby do konta Cosmos DB przy użyciu aplikacji Java. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

> [!IMPORTANT]  
> Ten przewodnik Szybki Start dotyczy tylko Azure Cosmos DB Java SDK v4. Aby uzyskać więcej informacji, zapoznaj się z informacjami o [wersji](sql-api-sdk-java-v4.md)Azure Cosmos DB Java SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB przewodniku Azure Cosmos DB [dotyczącym](performance-tips-java-sdk-v4-sql.md) [rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) z zestawem Java SDK 4. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Zestaw Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wskaż `JAVA_HOME` zmienną środowiskową do folderu, w którym zainstalowano JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). W systemie Ubuntu Uruchom polecenie, `apt-get install maven` Aby zainstalować Maven.
- Usługi [git](https://www.git-scm.com/downloads). W systemie Ubuntu Uruchom polecenie, `sudo apt-get install git` Aby zainstalować usługę git.

## <a name="introductory-notes"></a>Uwagi wprowadzające

*Struktura konta Cosmos DB.* Niezależnie od interfejsu API lub języka programowania, *konto* Cosmos DB zawiera zero lub więcej *baz danych* , *baza danych* (DB) zawiera zero lub więcej *kontenerów* , a *kontener* zawiera zero lub więcej elementów, jak pokazano na poniższym diagramie:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Jednostki kont usługi Azure Cosmos" border="false":::

Więcej informacji na temat baz danych, kontenerów i elementów można znaleźć [tutaj.](account-databases-containers-items.md) Niektóre ważne właściwości są definiowane na poziomie kontenera, między nimi *przepływność* i *klucz partycji* . 

Zainicjowana przepływność jest mierzona w jednostkach żądania ( *jednostek ru* ), które mają cenę pieniężną i są istotnym czynnikiem w kosztu eksploatacyjnym konta. Zainicjowaną przepływność można wybrać w poziomie szczegółowości poszczególnych kontenerów lub stopnia szczegółowości poszczególnych baz danych, jednak Specyfikacja przepływności na poziomie kontenera jest zazwyczaj preferowana. Więcej informacji o aprowizacji przepływności można znaleźć [tutaj.](set-throughput.md)

Gdy elementy są wstawiane do kontenera Cosmos DB, baza danych rozszerza się w poziomie, dodając więcej magazynu i obliczeń do obsługi żądań. Pojemność magazynu i mocy obliczeniowej są dodawane w odrębnych jednostkach nazywanych *partycjami* , a użytkownik musi wybrać jedno pole w dokumentach, aby być kluczem partycji, który mapuje każdy dokument na partycję. Zarządzanie takimi partycjami polega na tym, że każda partycja ma przypisany surowy wycink z zakresu wartości klucza partycji; w związku z tym zaleca się wybranie klucza partycji, który jest relatywnie losowy lub równomiernie dystrybuowany. W przeciwnym razie niektóre partycje będą widzieć znacznie więcej żądań ( *gorąca partycja* ), a inne partycje zobaczą znacznie mniejszą liczbę żądań ( *zimna partycja* ) i należy to uniknąć. Więcej informacji na temat partycjonowania można znaleźć [tutaj](partitioning-overview.md).

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

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Zarządzanie zasobami bazy danych za pomocą synchronicznego (synchronizacji) interfejsu API

* Inicjowanie klienta `CosmosClient`. `CosmosClient`Zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos Database. Ten klient jest używany do konfigurowania i wykonywania żądań dotyczących usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` Tworzenie.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` Tworzenie.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Tworzenie elementu przy użyciu `createItem` metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Odczyty punktów są wykonywane przy użyciu `readItem` metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Zapytania SQL w formacie JSON są wykonywane przy użyciu `queryItems` metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Asynchroniczny interfejs API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Zarządzanie zasobami bazy danych przy użyciu asynchronicznego (asynchronicznego) interfejsu API

* Asynchroniczne wywołania interfejsu API zwracają natychmiast, bez oczekiwania na odpowiedź z serwera. W związku z tym następujące fragmenty kodu pokazują właściwe wzorce projektowe do realizacji wszystkich powyższych zadań zarządzania przy użyciu asynchronicznego interfejsu API.

* Inicjowanie klienta `CosmosAsyncClient`. `CosmosAsyncClient`Zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos Database. Ten klient służy do konfigurowania i wykonywania żądań asynchronicznych dotyczących usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` Tworzenie.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` Tworzenie.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Podobnie jak w przypadku interfejsu API synchronizacji, Tworzenie elementu jest realizowane przy użyciu `createItem` metody. Ten przykład pokazuje, jak skutecznie wystawiać wiele żądań asynchronicznych `createItem` , subskrybując ponownie aktywny strumień, który wystawia żądania i drukuje powiadomienia. Ponieważ ten prosty przykład działa w celu ukończenia i zakończenia, `CountDownLatch` wystąpienia są używane do upewnienia się, że program nie zostanie zakończony podczas tworzenia elementu. **Odpowiednie asynchroniczne metody programowania nie zablokują wywołań asynchronicznych — w przypadku realistycznych żądań użycia generowane są z głównej pętli (), która jest wykonywana w nieskończoność, eliminując konieczność zablokowania wywołań asynchronicznych.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Podobnie jak w przypadku interfejsu API synchronizacji, odczyty punktów są wykonywane przy użyciu `readItem` metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Podobnie jak w przypadku interfejsu API synchronizacji zapytania SQL za pośrednictwem JSON są wykonywane przy użyciu `queryItems` metody.

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

3. W oknie terminalu usługi git Użyj następującego polecenia, aby uruchomić aplikację Java (Zastąp SYNCASYNCMODE with `sync` lub `async` , w zależności od tego, który przykładowy kod chcesz uruchomić, zastąp YOUR_COSMOS_DB_HOSTNAME wartością URI z cudzysłowem z portalu i zastąp YOUR_COSMOS_DB_MASTER_KEY z wytoczonym kluczem podstawowym z portalu)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Okno terminala wyświetli powiadomienie o utworzeniu bazy danych FamilyDB. 
    
4. Aplikacja tworzy bazę danych o nazwie `AzureSampleFamilyDB`
5. Aplikacja tworzy kontener o nazwie `FamilyContainer`
6. Aplikacja przeprowadzi odczyty punktów przy użyciu identyfikatorów obiektów i wartości klucza partycji (czyli lastName w naszym przykładzie). 
7. Aplikacja będzie wysyłać zapytania o elementy, aby pobrać wszystkie rodziny o nazwisku ("Andersen", "Wakefield", "Johnsonem")

7. Aplikacja nie usuwa utworzonych zasobów. Przełącz się do portalu, aby [wyczyścić zasoby](#clean-up-resources).  z poziomu konta, aby nie spowodować naliczenia opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta interfejsu API SQL Azure Cosmos DB, tworzenia bazy danych dokumentów i kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji Java w celu programistycznego wykonywania tych samych czynności. Teraz możesz zaimportować dodatkowe dane do konta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
