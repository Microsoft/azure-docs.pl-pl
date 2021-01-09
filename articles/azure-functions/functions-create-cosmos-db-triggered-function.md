---
title: Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB
description: Utwórz za pomocą usługi Azure Functions funkcję niewymagającą użycia serwera wywoływaną w przypadku dodania danych do bazy danych w usłudze Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 621773a84db99dbacfaa163f77189974ba102163
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034819"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB

Dowiedz się, jak utworzyć funkcję wyzwalaną w przypadku dodania lub zmiany danych w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji o usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB: Serverless database computing using Azure Functions](../cosmos-db/serverless-computing-database.md) (Azure Cosmos DB: przetwarzanie danych w bazie danych bez użycia serwera dzięki usłudze Azure Functions).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Kod Azure Cosmos DB":::

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Do utworzenia wyzwalacza konieczne jest posiadanie konta usługi Azure Cosmos DB, które używa interfejsu API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-a-function-app-in-azure"></a>Tworzenie aplikacji funkcji na platformie Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Tworzenie wyzwalacza usługi Azure Cosmos DB

1. W aplikacji funkcji wybierz pozycję **funkcje** w menu po lewej stronie, a następnie wybierz pozycję **Dodaj** z górnego menu. 

1. Na stronie **Nowa funkcja** wprowadź `cosmos` w polu wyszukiwania, a następnie wybierz szablon **wyzwalacza Azure Cosmos DB** .

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Strona funkcje w Azure Portal":::


1. Skonfiguruj nowy wyzwalacz przy użyciu ustawień określonych w poniższej tabeli:

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nowa funkcja** | Zaakceptuj nazwę domyślną | Nazwa funkcji. |
    | **Połączenie konta usługi Cosmos DB** | Zaakceptuj domyślną nową nazwę | Wybierz pozycję **Nowy**, utworzone wcześniej **konto bazy danych** , a następnie kliknij przycisk **OK**. Ta akcja powoduje utworzenie ustawienia aplikacji dla połączenia z kontem. To ustawienie jest używane przez powiązanie do nawiązywania połączenia z bazą danych. |
    | **Nazwa bazy danych** | Zadania | Nazwa bazy danych, która zawiera kolekcję do monitorowania. |
    | **Nazwa kolekcji** | Elementy | Nazwa kolekcji do monitorowania. |
    | **Nazwa kolekcji dzierżaw** | leases | Nazwa kolekcji do przechowywania dzierżaw. |
    | **Utwórz kolekcję dzierżaw, jeśli nie istnieje** | Tak | Sprawdza obecność kolekcji dzierżaw i automatycznie tworzy ją. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB":::

1. Wybierz pozycję **Utwórz funkcję**. 

    Platforma Azure tworzy funkcję wyzwalacza Cosmos DB.

1. Aby wyświetlić kod funkcji oparty na szablonie, wybierz pozycję **Code + test**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Szablon funkcji usługi Azure Cosmos DB w języku C#":::

    Ten szablon funkcji zapisuje w dziennikach liczbę dokumentów oraz identyfikator pierwszego dokumentu.

Następnie nawiąż połączenie z kontem Azure Cosmos DB i Utwórz `Items` kontener w `Tasks` bazie danych.

## <a name="create-the-items-container"></a>Tworzenie kontenera elementów

1. Otwórz drugie wystąpienie witryny [Azure Portal](https://portal.azure.com) w nowej karcie przeglądarki.

1. Rozwiń pasek ikon po lewej stronie portalu, wpisz ciąg `cosmos` w polu wyszukiwania, a następnie wybierz pozycję **Azure Cosmos DB**.

    ![Wyszukiwanie usługi Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Wybierz konto usługi Azure Cosmos DB, a następnie wybierz pozycję **Eksplorator danych**. 

1. W obszarze **interfejs API SQL** wybierz pozycję baza danych **zadania** i wybierz pozycję **nowy kontener**.

    ![Tworzenie kontenera](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. W obszarze **Dodaj kontener** Użyj ustawień przedstawionych w tabeli poniżej obrazu. 

    ![Definiowanie kontenera zadań](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Ustawienie|Sugerowana wartość|Opis |
    | ---|---|--- |
    | **Identyfikator bazy danych** | Zadania |Nazwa nowej bazy danych. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji. |
    | **Identyfikator kontenera** | Elementy | Nazwa nowego kontenera. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji.  |
    | **[Klucz partycji](../cosmos-db/partitioning-overview.md)** | /category|Klucz partycji służący do równomiernego dystrybuowania danych do każdej partycji. Wybór poprawnego klucza partycji jest istotny podczas tworzenia kontenera wykonywania. | 
    | **Przepływność** |400 RU| Użyj wartości domyślnej. Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę. |    

1. Kliknij przycisk **OK** , aby utworzyć kontener elementów. Utworzenie kontenera może chwilę potrwać.

Gdy kontener określony w powiązaniu funkcji istnieje, można przetestować funkcję, dodając elementy do tego nowego kontenera.

## <a name="test-the-function"></a>Testowanie funkcji

1. Rozwiń kontener nowe **elementy** w obszarze Eksplorator danych, wybierz pozycję **elementy**, a następnie wybierz pozycję **nowy element**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Tworzenie elementu w kontenerze elementów":::

1. Zastąp zawartość nowego elementu następującą zawartością, a następnie wybierz pozycję **Zapisz**.

    ```yaml
    {
        "id": "task1",
        "category": "general",
        "description": "some task"
    }
    ```

1. Przejdź do pierwszej karty przeglądarki, zawierającej funkcję w portalu. Rozwiń dzienniki funkcji i sprawdź, czy nowy dokument spowodował wyzwolenie funkcji. Zobaczysz, że wartość identyfikatora dokumentu `task1` jest zapisana w dziennikach. 

    ![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Opcjonalnie) Wróć do dokumentu, wprowadź zmianę i kliknij pozycję **Aktualizuj**. Następnie wróć do dzienników funkcji i sprawdź, czy aktualizacja również spowodowała wyzwolenie funkcji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję uruchamianą w przypadku dodania lub zmodyfikowania dokumentu w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji na temat wyzwalaczy bazy danych Azure Cosmos DB, zobacz [Azure Cosmos DB bindings for Azure Functions](functions-bindings-cosmosdb.md) (Powiązania bazy danych Azure Cosmos DB na potrzeby usługi Azure Functions).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
