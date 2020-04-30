---
title: Szybki Start — używanie środowiska Node. js do wykonywania zapytań z Azure Cosmos DB konta interfejsu API SQL
description: Jak używać środowiska Node. js do tworzenia aplikacji, która łączy się z Azure Cosmos DB konta interfejsu API SQL i wysyła zapytania do danych.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240313"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Szybki Start: Używanie środowiska Node. js do nawiązywania połączenia i wykonywania zapytań dotyczących danych z Azure Cosmos DB konta interfejsu API SQL

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejsu API SQL z Azure Portal i zarządzasz nim, a następnie korzystasz z aplikacji node. js sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="walkthrough-video"></a>Film instruktażowy

Obejrzyj ten film wideo, aby zapoznać się z pełnym przewodnikiem dotyczącym zawartości w tym artykule.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node. js 6.0.0 +](https://nodejs.org/).
- Usługi [git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>tworzenie konta usługi Azure Cosmos

W tym celu szybkiego startu możesz użyć opcji [wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) , aby utworzyć konto usługi Azure Cosmos.

1. Przejdź do strony [wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) .

1. Wybierz konto interfejsu API **SQL** i wybierz pozycję **Utwórz**. Zaloguj się przy użyciu konto Microsoft.

1. Po pomyślnym zalogowaniu Twoje konto usługi Azure Cosmos powinno być gotowe. Wybierz pozycję **Otwórz w Azure Portal** , aby otworzyć nowo utworzone konto.

Opcja "Wypróbuj Azure Cosmos DB bezpłatnie" nie wymaga subskrypcji platformy Azure i oferuje konto usługi Azure Cosmos przez ograniczony okres 30 dni. Jeśli chcesz korzystać z konta usługi Azure Cosmos przez dłuższy czas, należy [utworzyć konto](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) w ramach subskrypcji platformy Azure.

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz można użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener.

1. Wybierz pozycję **Eksplorator danych** > **nowy kontener**.

   Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo w celu wyświetlenia go.

   ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

   | Ustawienie           | Sugerowana wartość | Opis                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Identyfikator bazy danych**   | Zadania           | Wprowadź _Zadania_ jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
   | **Przepływność**    | 400             | Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.                                                                                                                                                                                                                                                    |
   | **Identyfikator kontenera**  | Items           | Wprowadź _elementy_ jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.                                                                                                                                                                                                                                                               |
   | **Klucz partycji** | /category       | W przykładzie opisanym w tym artykule jest stosowany _/Category_ jako klucz partycji.                                                                                                                                                                                                                                                                                                           |

   Oprócz powyższych ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe Zasady kluczy podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).

   Wybierz przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujmy aplikację Node. js z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych Azure Cosmos są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string).

Jeśli znasz poprzednią wersję zestawu SDK JavaScript języka SQL, możesz użyć, aby wyświetlić _kolekcje_ terminów i _dokument_. Ponieważ Azure Cosmos DB obsługuje [wiele modeli interfejsów API](introduction.md), [wersja 2.0 + zestawu JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) używa _kontenera_warunków ogólnych, który może być kolekcją, wykresem lub tabelą i _elementem_ do opisywania zawartości kontenera.

Cosmos DB zestawu SDK języka JavaScript nazywa się@azure/cosmos"" i można go zainstalować z npm...

```bash
npm install @azure/cosmos
```

Wszystkie poniższe fragmenty kodu pochodzą z pliku _app.js_.

- `CosmosClient` Zaimportowano z pakietu `@azure/cosmos` npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Nowy `CosmosClient` obiekt jest zainicjowany.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Wybierz bazę danych "Tasks" (zadania).

  ```javascript
  const database = client.database(databaseId);
  ```

- Wybierz kontener/kolekcję "Items".

  ```javascript
  const container = database.container(containerId);
  ```

- Zaznacz wszystkie elementy w kontenerze "Items" (elementy).

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Utwórz nowy element

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Aktualizowanie elementu

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Usuwanie elementu

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> W ramach metod "Update" i "Delete" element musi zostać wybrany z bazy danych przez wywołanie `container.item()`. Przesłane dwa parametry są identyfikatorem elementu i kluczem partycji elementu. W tym przypadku klucz partycjonowania jest wartością pola "Category".

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do Azure Portal, aby uzyskać szczegóły parametrów połączenia konta usługi Azure Cosmos. Skopiuj parametry połączenia do aplikacji, aby można było połączyć się z bazą danych.

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **klucze** w lewym okienku nawigacji, a następnie wybierz pozycję klucze **odczytu i zapisu**. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować identyfikator URI i klucz podstawowy do pliku _App. js_ w następnym kroku.

   ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-dotnet/keys.png)

2. W obszarze Otwórz plik _config. js_ .

3. Skopiuj wartość identyfikatora URI z portalu (przy użyciu przycisku kopiowania) i ustaw ją jako wartość klucza punktu końcowego w _pliku config. js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Następnie skopiuj wartość klucza podstawowego z portalu i ustaw ją na wartość `config.key` w _pliku config. js_. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Uruchom `npm install` w terminalu, aby zainstalować@azure/cosmospakiet npm

2. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

3. Dwa elementy, które zostały utworzone wcześniej w tym przewodniku Szybki Start, są wymienione na liście. Zostanie utworzony nowy element. Flaga "IsComplete" w tym elemencie jest aktualizowana do wartości "true", a następnie jest usuwana.

Możesz kontynuować eksperymentowanie z tą przykładową aplikacją lub wrócić do Eksplorator danych, modyfikować i pracę z danymi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji node. js. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
