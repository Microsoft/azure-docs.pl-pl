---
title: Przechowywanie danych niestrukturalnych przy użyciu Azure Cosmos DB i funkcji
description: Przechowywanie danych niestrukturalnych przy użyciu usług Azure Functions i Cosmos DB
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91661163"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Przechowywanie danych niestrukturalnych przy użyciu usług Azure Functions i Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to doskonały sposób przechowywania danych bez struktury i JSON. Dzięki połączeniu usług Azure Functions i Cosmos DB przechowywanie danych staje się szybkie i proste oraz wymaga znacznie krótszego kodu niż w przypadku przechowywania danych w relacyjnej bazie danych.

> [!NOTE]
> W tej chwili wyzwalacz usługi Azure Cosmos DB, powiązania danych wejściowych i powiązania danych wyjściowych współpracują tylko z kontami interfejsu API SQL i interfejsu API programu Graph.

W usłudze Azure Functions powiązania danych wejściowych i wyjściowych zapewniają deklaratywną metodę łączenia z danymi usług zewnętrznych z funkcji. W tym artykule opisano, jak zaktualizować istniejącą funkcję w celu dodania powiązania danych wyjściowych, które zapisuje dane bez struktury w dokumencie usługi Azure Cosmos DB.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Do utworzenia powiązania danych wyjściowych konieczne jest posiadanie konta usługi Azure Cosmos DB, które używa interfejsu API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

1. W Azure Portal przejdź do i wybierz wcześniej utworzoną aplikację funkcji.

1. Wybierz pozycję **funkcje**, a następnie wybierz funkcję HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::

1. Wybierz pozycję **integracja** i **+ Dodaj dane wyjściowe**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::

1. Użyj ustawień **tworzenia danych wyjściowych** , jak określono w tabeli:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Typ powiązania** | Azure Cosmos DB | Nazwa typu powiązania do wybrania, aby utworzyć powiązanie danych wyjściowych do Azure Cosmos DB. |
    | **Nazwa parametru dokumentu** | taskDocument | Nazwa, która odwołuje się do obiektu Cosmos DB w kodzie. |
    | **Nazwa bazy danych** | taskDatabase | Nazwa bazy danych do zapisywania dokumentów. |
    | **Nazwa kolekcji** | Kolekcji taskcollection | Nazwa bazy kolekcji bazy danych. |
    | **W przypadku wartości true tworzy bazę danych i kolekcję usługi Cosmos DB** | Tak | Kolekcja jeszcze nie istnieje, więc należy ją utworzyć. |
    | **Połączenie konta usługi Cosmos DB** | Nowe ustawienie | Wybierz pozycję **Nowy**, a następnie wybierz **konto Azure Cosmos DB** i **konto bazy danych** utworzone wcześniej, a następnie wybierz przycisk **OK**. Spowoduje to utworzenie ustawienia aplikacji na potrzeby połączenia konta. To ustawienie jest używane przez powiązanie do nawiązywania połączenia z bazą danych. |

1. Wybierz **przycisk OK** , aby utworzyć powiązanie.

## <a name="update-the-function-code"></a>Aktualizacja kodu funkcji

Zastąp istniejący kod funkcji w następującym kodem w wybranym języku:

# <a name="c"></a>[C#](#tab/csharp)

Zastąp istniejącą funkcję w języku C# następującym kodem:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zastąp istniejącą funkcję w języku JavaScript następującym kodem:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Ten przykładowy kod odczytuje ciągi zapytań żądania HTTP i przypisuje je do pól w obiekcie `taskDocument`. Powiązanie `taskDocument` wysyła dane obiektu z tego parametru powiązania do przechowywania w powiązanej bazie danych dokumentów. Baza danych zostanie utworzona przy pierwszym uruchomieniu funkcji.

## <a name="test-the-function-and-database"></a>Testowanie funkcji i bazy danych

1. Wybierz **test/Uruchom**. W obszarze **zapytanie**wybierz pozycję **+ Dodaj parametr** i Dodaj następujące parametry do ciągu zapytania:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::


1. Wybierz pozycję **Uruchom** i sprawdź, czy jest zwracany stan 200.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::


1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::

1. Wybierz konto Azure Cosmos DB, a następnie wybierz pozycję  **Eksplorator danych**.

1. Rozwiń węzły **taskcollection** , zaznacz nowy dokument i upewnij się, że dokument zawiera wartości ciągu zapytania wraz z dodatkowymi metadanymi.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Wybierz funkcję http w Azure Portal." border="true":::

Powiązanie zostało pomyślnie dodane do wyzwalacza HTTP w celu zapisania danych bez struktury w bazie danych Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia powiązań z bazą danych usługi Cosmos DB, zobacz temat [Powiązania bazy danych usługi Cosmos DB w usłudze Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
