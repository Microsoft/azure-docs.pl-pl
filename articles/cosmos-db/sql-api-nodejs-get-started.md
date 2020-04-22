---
title: Samouczek środowiska Node.js dotyczący interfejsu API SQL dla usługi Azure Cosmos DB
description: Samouczek środowiska Node.js, w którym przedstawiono, jak nawiązać połączenie z bazą danych Azure Cosmos DB i wykonywać względem niej zapytania za pomocą interfejsu API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 212dd243842a8bdacc8a77241f456795ef508d9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731692"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Samouczek: Tworzenie aplikacji konsoli Node.js za pomocą zestawu JavaScript SDK do zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako deweloper być może masz aplikacje, które używają danych dokumentów NoSQL. Konto interfejsu API SQL w usłudze Azure Cosmos DB umożliwia przechowywanie tych danych dokumentów i uzyskiwanie do nich dostępu. W tym samouczku pokazano, jak utworzyć aplikację konsolową Node.js na potrzeby tworzenia zasobów usługi Azure Cosmos DB i wykonywania zapytań względem nich.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
> * Konfigurowanie aplikacji
> * Tworzenie bazy danych
> * Tworzenie kontenera
> * Dodawanie elementów do kontenera
> * Wykonywanie podstawowych operacji względem elementów, kontenera i bazy danych

## <a name="prerequisites"></a>Wymagania wstępne 

Upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Platforma [Node.js](https://nodejs.org/) w wersji 6.0.0 lub nowszej.

## <a name="create-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie aplikacji Node.js](#SetupNode). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie aplikacji Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Konfigurowanie aplikacji Node.js

Przed rozpoczęciem pisania kodu w celu utworzenia aplikacji możesz utworzyć platformę dla swojej aplikacji. Wykonaj następujące kroki, aby skonfigurować aplikację Node.js zawierającą kod platformy:

1. Otwórz swój ulubiony terminal.
2. Zlokalizuj folder lub katalog, w którym chcesz zapisać aplikację Node.js.
3. Utwórz puste pliki JavaScript za pomocą następujących poleceń:

   * W systemie Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * W systemie Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Utwórz i zainicjuj plik `package.json`. Użyj następującego polecenia:
   * ```npm init -y```

5. Zainstaluj moduł @azure/cosmos za pomocą menedżera npm. Użyj następującego polecenia:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Ustawianie konfiguracji aplikacji

Gdy aplikacja już istnieje, musisz się upewnić, że może ona komunikować się z usługą Azure Cosmos DB. Aktualizując kilka ustawień konfiguracji, jak pokazano w poniższych krokach, możesz skonfigurować aplikację tak, aby komunikowała się z usługą Azure Cosmos DB:

1. Otwórz plik *config.js* w ulubionym edytorze tekstu.

1. Skopiuj i wklej następujący fragment kodu do pliku *config.js* i ustaw właściwości `endpoint` i `key` identyfikator URI punktu końcowego bazy danych Usługi Azure Cosmos DB i klucz podstawowy. Baza danych, nazwy kontenerów są ustawione na **Zadania** i **Elementy**. Klucz partycji, którego użyjesz dla tej aplikacji, to **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Szczegóły punktu końcowego i klucza można znaleźć w okienku **Klucze** [w witrynie Azure portal](https://portal.azure.com).

   ![Zrzut ekranu przedstawiający pobieranie kluczy z witryny Azure Portal][keys]

JavaScript SDK używa ogólnego *kontenera* terminów i *elementu*. Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. W poprzednim fragmentie kodu `module.exports = config;` kod jest używany do eksportowania obiektu konfiguracyjnego, dzięki czemu można odwoływać się do niego w pliku *app.js.*

## <a name="create-a-database-and-a-container"></a>Tworzenie bazy danych i kontenera

1. Otwórz plik *databaseContext.js* w ulubionym edytorze tekstu.

1. Skopiuj i wklej następujący kod do *pliku databaseContext.js.* Ten kod definiuje funkcję, która tworzy "Zadania", "Elementy" bazy danych i kontenera, jeśli nie istnieją one jeszcze na koncie usługi Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach. Tworzenie bazy danych przy użyciu `createIfNotExists` funkcji lub utworzyć **databases** klasy. Kontener składa się z elementów, które w przypadku interfejsu API SQL są dokumentami JSON. Utworzyć kontener przy użyciu `createIfNotExists` funkcji lub utworzyć z **Containers** klasy. Po utworzeniu kontenera można przechowywać i wysyłać zapytania do danych.

   > [!WARNING]
   > Utworzenie kontenera ma implikacje cenowe. Odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/), aby wiedzieć, czego oczekiwać.

## <a name="import-the-configuration"></a>Importowanie konfiguracji

1. Otwórz plik *app.js* w ulubionym edytorze tekstu.

1. Skopiuj i wklej `@azure/cosmos` poniższy kod, aby zaimportować moduł, konfigurację i bazę danychContext zdefiniowany w poprzednich krokach. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Łączenie się z kontem usługi Azure Cosmos

W pliku *app.js* skopiuj i wklej następujący kod, aby użyć wcześniej zapisanego punktu końcowego i klucza do utworzenia nowego obiektu CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> W przypadku nawiązywania połączenia z **emulatorem bazy danych usługi Cosmos**, wyłącz weryfikację TLS dla procesu węzła:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Teraz, gdy masz kod służący do zainicjowania klienta usługi Azure Cosmos DB, przyjrzyjmy się sposobowi pracy z zasobami usługi Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Elementy kwerendy

Usługa Azure Cosmos DB obsługuje zaawansowane zapytania dotyczące elementów JSON przechowywanych w każdym kontenerze. Poniższy przykładowy kod przedstawia kwerendę, którą można uruchomić względem elementów w kontenerze. Można zbadać elementy przy użyciu funkcji `Items` kwerendy klasy. Dodaj następujący kod do pliku *app.js,* aby zbadać elementy z konta usługi Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Tworzenie elementu

Element można utworzyć przy użyciu funkcji `Items` tworzenia klasy. Gdy korzystasz z interfejsu API SQL, elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. W tym samouczku utworzysz nowy element w bazie danych zadań.

1. W pliku app.js zdefiniuj definicję elementu:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Dodaj następujący kod, aby utworzyć poprzednio zdefiniowany element:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aktualizowanie elementu

Usługa Azure Cosmos DB obsługuje zastępowanie zawartości elementów. Skopiuj i wklej następujący kod do pliku *app.js.* Ten kod pobiera element z kontenera i *aktualizuje isComplete* pole true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Usuwanie elementu

Usługa Azure Cosmos DB obsługuje usuwanie elementów JSON. Poniższy kod pokazuje, jak uzyskać element według jego identyfikatora i usunąć go. Skopiuj i wklej następujący kod do pliku *app.js:*

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Uruchamianie aplikacji Node.js

Cały kod powinien wyglądać następująco:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

W terminalu znajdź swój plik ```app.js```, a następnie uruchom polecenie:

```bash 
node app.js
```

Powinny zostać wyświetlone dane wyjściowe aplikacji rozpoczynania pracy. Dane wyjściowe powinny odpowiadać tekstowi przykładu poniżej.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka środowiska Node.js

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać kod, możesz uzyskać go w serwisie [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Do uruchomienia rozwiązania z wprowadzeniem, które zawiera cały kod znajdujący się w tym artykule, będą potrzebne następujące elementy:

* [Konto usługi Azure Cosmos DB][create-account].
* Rozwiązanie [z wprowadzeniem](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) dostępne w witrynie GitHub.

Zainstaluj zależności projektu za pomocą menedżera npm. Użyj następującego polecenia:

* ```npm install``` 

Następnie w ```config.js``` pliku zaktualizuj wartości config.endpoint i config.key zgodnie z opisem w [kroku 3: Ustaw konfiguracje aplikacji](#Config).  

W terminalu znajdź swój plik ```app.js```, a następnie uruchom następujące polecenie:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos DB oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów używaną w przypadku konta usługi Azure Cosmos DB, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie konta usługi Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
