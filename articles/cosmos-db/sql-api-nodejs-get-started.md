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
ms.custom: devx-track-js
ms.openlocfilehash: b1e0f8c301d40ff10dbf977731d457a31b096328
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478001"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Samouczek: Tworzenie aplikacji konsolowej Node.js przy użyciu zestawu SDK dla języka JavaScript na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Java (asynchroniczny)](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako deweloper być może masz aplikacje, które używają danych dokumentów NoSQL. Konto interfejsu API SQL w usłudze Azure Cosmos DB umożliwia przechowywanie tych danych dokumentów i uzyskiwanie do nich dostępu. W tym samouczku pokazano, jak utworzyć aplikację konsolową Node.js na potrzeby tworzenia zasobów usługi Azure Cosmos DB i wykonywania zapytań względem nich.

Ten samouczek obejmuje następujące kroki:

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
3. Utwórz puste pliki JavaScript przy użyciu następujących poleceń:

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

1. Skopiuj i wklej poniższy fragment kodu do pliku *config.js* i ustaw właściwości `endpoint` oraz `key` identyfikator URI punktu końcowego Azure Cosmos DB i klucz podstawowy. Baza danych programu, nazwy kontenerów są ustawiane na **zadania** i **elementy**. Klucz partycji, który będzie używany dla tej aplikacji, to **/Category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Punkt końcowy i szczegóły klucza można znaleźć w okienku **klucze** [Azure Portal](https://portal.azure.com).

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Zrzut ekranu przedstawiający pobieranie kluczy z witryny Azure Portal":::

Zestaw SDK języka JavaScript używa *kontenera* warunków ogólnych i *elementu*. Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. W poprzednim fragmencie kodu `module.exports = config;` kod jest używany do eksportowania obiektu konfiguracji, aby można było odwołać się do niego w pliku *app.js* .

## <a name="create-a-database-and-a-container"></a>Tworzenie bazy danych i kontenera

1. Otwórz plik *databaseContext.js* w ulubionym edytorze tekstu.

1. Skopiuj i wklej następujący kod do pliku *databaseContext.js* . Ten kod definiuje funkcję, która tworzy bazę danych "Tasks", "Items" i kontener, jeśli jeszcze nie istnieją na koncie usługi Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach. Bazę danych można utworzyć za pomocą `createIfNotExists` lub funkcji Create klasy **Databases** . Kontener składa się z elementów, które w przypadku interfejsu API SQL są dokumentami JSON. Kontener można utworzyć przy użyciu albo `createIfNotExists` funkcji tworzenia z klasy **Containers** . Po utworzeniu kontenera można przechowywać dane i wysyłać do nich zapytania.

   > [!WARNING]
   > Utworzenie kontenera ma implikacje cenowe. Odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/), aby wiedzieć, czego oczekiwać.

## <a name="import-the-configuration"></a>Importowanie konfiguracji

1. Otwórz plik *app.js* w ulubionym edytorze tekstu.

1. Skopiuj i wklej poniższy kod, aby zaimportować `@azure/cosmos` moduł, konfigurację i databaseContext, które zostały zdefiniowane w poprzednich krokach. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Nawiązywanie połączenia z kontem usługi Azure Cosmos

W pliku *app.js* skopiuj i wklej następujący kod, aby użyć wcześniej zapisanego punktu końcowego i klucza w celu utworzenia nowego obiektu CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> W przypadku nawiązywania połączenia z **emulatorem Cosmos DB**Wyłącz weryfikację protokołu TLS dla procesu węzła:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Teraz, gdy masz kod służący do zainicjowania klienta usługi Azure Cosmos DB, przyjrzyjmy się sposobowi pracy z zasobami usługi Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Elementy zapytań

Azure Cosmos DB obsługuje zaawansowane zapytania względem elementów JSON przechowywanych w każdym kontenerze. Następujący przykładowy kod przedstawia zapytanie, które można uruchomić względem elementów w kontenerze. Można wysyłać zapytania do elementów przy użyciu funkcji zapytania `Items` klasy. Dodaj następujący kod do pliku *app.js* , aby wykonać zapytanie o elementy z konta usługi Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Tworzenie elementu

Element można utworzyć za pomocą funkcji tworzenia `Items` klasy. Gdy korzystasz z interfejsu API SQL, elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. W tym samouczku utworzysz nowy element w bazie danych zadań.

1. W pliku app.js Zdefiniuj definicję elementu:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Dodaj następujący kod, aby utworzyć wcześniej zdefiniowany element:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aktualizowanie elementu

Azure Cosmos DB obsługuje Zastępowanie zawartości elementów. Skopiuj i wklej następujący kod w celu *app.js* pliku. Ten kod pobiera element z kontenera i aktualizuje pole *IsComplete* do wartości true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Usuwanie elementu

Usługa Azure Cosmos DB obsługuje usuwanie elementów JSON. Poniższy kod przedstawia sposób pobierania elementu według jego identyfikatora i usuwania go. Skopiuj i wklej następujący kod do pliku *app.js* :

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

Następnie w ```config.js``` pliku zaktualizuj wartości config. Endpoint i config. Key zgodnie z opisem w [sekcji Krok 3. Ustawianie konfiguracji aplikacji](#Config).  

W terminalu znajdź swój plik ```app.js```, a następnie uruchom następujące polecenie:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos DB oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów używaną w przypadku konta usługi Azure Cosmos DB, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie konta usługi Azure Cosmos DB](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account