---
title: Szybki start — używanie Node.js do wykonywania zapytań z Azure Cosmos DB interfejsu API SQL
description: Jak za pomocą Node.js utworzyć aplikację, która łączy się z kontem Azure Cosmos DB interfejsu API SQL i wykonuje zapytania o dane.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: ead4004813cf6415dfa1c7da3d308d93ea49a773
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365913"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Szybki start: nawiązywanie połączeń Node.js danych z konta interfejsu API SQL Azure Cosmos DB wykonywanie zapytań
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start utworzysz konto interfejsu API SQL Azure Cosmos DB i zarządzasz nimi z usługi Azure Portal oraz za pomocą aplikacji Node.js sklonowanej z usługi GitHub. Azure Cosmos DB to wielo modelowa usługa bazy danych, która umożliwia szybkie tworzenie dokumentów, tabel, tabel, klucz-wartość i grafowych baz danych z możliwościami globalnej dystrybucji i skalowania w poziomie.

## <a name="walkthrough-video"></a>Wideo z przewodnikami

Obejrzyj to wideo, aby uzyskać pełny przewodnik po zawartości tego artykułu.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Możesz [też Azure Cosmos DB bezpłatnie bez](https://azure.microsoft.com/try/cosmosdb/) subskrypcji platformy Azure. Możesz również użyć emulatora [Azure Cosmos DB z](https://aka.ms/cosmosdb-emulator) URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>tworzenie konta usługi Azure Cosmos

Na potrzeby tego przewodnika Szybki start możesz użyć bezpłatnej opcji [Azure Cosmos DB,](https://azure.microsoft.com/try/cosmosdb/) aby utworzyć konto usługi Azure Cosmos.

1. Przejdź do strony [wypróbuj Azure Cosmos DB bezpłatnie.](https://azure.microsoft.com/try/cosmosdb/)

1. Wybierz konto **interfejsu API SQL** i wybierz pozycję **Utwórz.** Zaloguj się przy użyciu konto Microsoft.

1. Po pomyślnym zalogowaniu twoje konto usługi Azure Cosmos powinno być gotowe. Wybierz **pozycję Otwórz w Azure Portal,** aby otworzyć nowo utworzone konto.

Opcja "wypróbuj Azure Cosmos DB bezpłatnie" nie wymaga subskrypcji platformy Azure i oferuje konto usługi Azure Cosmos przez ograniczony okres 30 dni. Jeśli chcesz używać konta usługi Azure Cosmos przez dłuższy czas, zamiast tego należy utworzyć konto [w](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) ramach subskrypcji platformy Azure.

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz możesz użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener.

1. Wybierz **Eksplorator danych**  >  **nowy kontener.**

   Obszar **Dodaj** kontener jest wyświetlany po prawej stronie. Aby go wyświetlić, może być konieczne przewinięcie w prawo.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera":::

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

   | Ustawienie           | Sugerowana wartość | Opis                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Identyfikator bazy danych**   | Zadania           | Wprowadź _Zadania_ jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Zaznacz opcję **Aprowizuj** przepływność bazy danych, aby udostępnić przepływność aprowizowana w bazie danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w oszczędzaniu kosztów. |
   | **Przepływność**    | 400             | Pozostaw przepływność na poziomie 400 jednostek żądania na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę.                                                                                                                                                                                                                                                    |
   | **Identyfikator kontenera**  | Elementy           | Wprowadź _Items_ jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.                                                                                                                                                                                                                                                               |
   | **Klucz partycji** | /category       | W przykładzie opisanym w tym artykule _użyto /category_ jako klucza partycji.                                                                                                                                                                                                                                                                                                           |

   Oprócz powyższych ustawień możesz opcjonalnie dodać unikatowe **klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc zasady unikatowych kluczy podczas tworzenia kontenera, zapewniasz unikatowość co najmniej jednej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).

   Wybierz przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujmy aplikację Node.js z usługi GitHub, ustawimy parametrów połączenia i uruchommy ją.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych usługi Azure Cosmos są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string).

Jeśli znasz poprzednią wersję zestawu SDK języka JavaScript SQL, możesz użyć terminów kolekcja _i_ _dokument_. Ponieważ usługa Azure Cosmos DB obsługuje wiele modeli interfejsu [API,](introduction.md)wersja [2.0 lub](https://www.npmjs.com/package/@azure/cosmos) nowsza zestawu SDK języka  JavaScript używa ogólnych terminów _kontener_, który może być kolekcją, grafem lub tabelą oraz elementem do opisywania zawartości kontenera.

Zestaw SDK Cosmos DB JavaScript ma nazwę " @azure/cosmos " i można go zainstalować z narzędzia npm...

```bash
npm install @azure/cosmos
```

Wszystkie poniższe fragmenty kodu pochodzą z pliku _app.js_.

- Wartość `CosmosClient` jest importowana z `@azure/cosmos` pakietu npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Zostanie `CosmosClient` zainicjowany nowy obiekt.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Wybierz bazę danych "Zadania".

  ```javascript
  const database = client.database(databaseId);
  ```

- Wybierz kontener/kolekcję "Items".

  ```javascript
  const container = database.container(containerId);
  ```

- Zaznacz wszystkie elementy w kontenerze "Items".

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
> W metodach "update" i "delete" element musi zostać wybrany z bazy danych przez wywołanie metody `container.item()` . Dwa przekazane parametry to identyfikator elementu i klucz partycji elementu. W tym przypadku klucz parzystego jest wartością pola "category".

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać szczegóły parametrów połączenia konta usługi Azure Cosmos. Skopiuj wartości parametrów połączenia do aplikacji, aby połączyć się z bazą danych.

1. Na koncie Azure Cosmos DB w witrynie [Azure Portal](https://portal.azure.com/)wybierz  pozycję Klucze w lewym panelu nawigacyjnym, a następnie wybierz pozycję **Klucze odczytu i zapisu.** Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować adres URI i klucz podstawowy do pliku _app.js_ w następnym kroku.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze":::

2. Na stronie Otwórz _config.js_ plik.

3. Skopiuj wartość URI z portalu (przy użyciu przycisku kopiowania) i nadaj jej wartość klucza punktu końcowego w _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Następnie skopiuj wartość KLUCZ PODSTAWOWY z portalu i nadaj jej wartość `config.key` w pliku _config.js_. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Uruchom `npm install` w terminalu, aby zainstalować @azure/cosmos pakiet npm ""

2. Uruchom polecenie `node app.js` w terminalu, aby uruchomić aplikację Node.

3. Zostaną wyświetlone dwa elementy utworzone wcześniej w tym przewodniku Szybki start. Zostanie utworzony nowy element. Flaga "isComplete" dla tego elementu jest aktualizowana do wartości "true", a następnie element jest usuwany.

Możesz kontynuować eksperymentowanie z tą przykładową aplikacją lub wrócić do Eksplorator danych, modyfikować i pracować z danymi.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kontenera przy użyciu Eksplorator danych i uruchamiania Node.js aplikacji. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [importowanie danych do usługi azure cosmos db](import-data.md)
