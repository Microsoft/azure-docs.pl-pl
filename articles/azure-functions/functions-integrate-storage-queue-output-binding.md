---
title: Dodawanie komunikatów do kolejki usługi Azure Storage przy użyciu funkcji
description: Za pomocą usługi Azure Functions utwórz bezserwerową funkcję wywoływaną za pomocą żądania HTTP i służącą do tworzenia komunikatu w kolejce usługi Azure Storage.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674205"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Dodawanie komunikatów do kolejki usługi Azure Storage przy użyciu funkcji

W usłudze Azure Functions powiązania danych wejściowych i wyjściowych zapewniają deklaratywną metodę udostępniania danych z usług zewnętrznych na potrzeby kodu. W tym przewodniku Szybki start użyjesz powiązania danych wyjściowych do utworzenia komunikatów w kolejce w momencie wyzwolenia funkcji przez żądanie HTTP. Kontener usługi Azure Storage jest używany do wyświetlania komunikatów w kolejce tworzonych przez funkcję.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Postępuj zgodnie z instrukcjami znajdującymi się w kroku [Tworzenie pierwszej funkcji w witrynie Azure Portal](./functions-get-started.md) i nie wykonuj kroku **Oczyszczanie zasobów**. W tym przewodniku Szybki start tworzona jest aplikacja funkcji i funkcja używana w tym miejscu.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Dodawanie powiązania danych wyjściowych

W tej sekcji użyjesz interfejsu użytkownika portalu w celu dodania powiązania danych wyjściowych usługi Queue Storage do wcześniej utworzonej funkcji. To powiązanie umożliwia zapisanie minimalnej ilości kodu w celu utworzenia komunikatu w kolejce. Nie będzie konieczne pisanie kodu na potrzeby zadań, takich jak otwieranie połączenia z magazynem, tworzenie kolejki lub pobieranie odwołania do kolejki. Te zadania zostaną wykonane za pomocą środowiska uruchomieniowego usługi Azure Functions i powiązania danych wyjściowych kolejki.

1. W witrynie Azure Portal otwórz stronę aplikacji funkcji na potrzeby aplikacji funkcji utworzonej w kroku [Tworzenie pierwszej funkcji w witrynie Azure Portal](./functions-get-started.md). Aby otworzyć stronę, Wyszukaj i wybierz **aplikacja funkcji**. Następnie wybierz aplikację funkcji.

1. Wybierz aplikację funkcji, a następnie wybierz funkcję utworzoną w poprzednim przewodniku Szybki Start.

1. Wybierz pozycję **integracja**, a następnie wybierz pozycję **+ Dodaj dane wyjściowe**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Utwórz powiązanie danych wyjściowych dla funkcji." border="true":::

1. Wybierz typ powiązania **queue storage platformy Azure** i Dodaj ustawienia określone w tabeli, która następuje po tym zrzucie ekranu: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Dodaj powiązanie danych wyjściowych kolejki magazynu do funkcji w witrynie Azure Portal." border="true":::
    
    | Ustawienie      |  Sugerowana wartość   | Opis                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa parametru komunikatu** | outputQueueItem | Nazwa parametru powiązania danych wyjściowych. | 
    | **Nazwa kolejki**   | outqueue  | Nazwa kolejki, z którą zostanie nawiązane połączenie na koncie magazynu. |
    | **Połączenie konta magazynu** | AzureWebJobsStorage | Możesz skorzystać z połączenia konta magazynu już używanego przez aplikację funkcji lub utworzyć nowe.  |

1. Wybierz **przycisk OK** , aby dodać powiązanie.

Po zdefiniowaniu powiązania danych wyjściowych musisz zaktualizować kod, tak aby stosować powiązanie do dodawania komunikatów do kolejki.  

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

W tej sekcji dodasz kod służący do zapisywania komunikatu do kolejki wyjściowej. Komunikat zawiera wartość przekazywaną do wyzwalacza HTTP w ciągu zapytania. Jeśli na przykład ciąg zapytania zawiera wartość `name=Azure`, komunikat w kolejce będzie następujący: *Nazwa przekazana do funkcji: Azure*.

1. W funkcji wybierz pozycję **Code + test** , aby wyświetlić kod funkcji w edytorze.

1. Zaktualizuj kod funkcji w zależności od języka funkcji:

    # <a name="c"></a>[S\#](#tab/csharp)

    Dodaj parametr **outputQueueItem** do sygnatury metody, jak pokazano w poniższym przykładzie.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    W treści funkcji tuż przed instrukcją `return` dodaj kod używający parametru w celu utworzenia komunikatu w kolejce.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Dodaj kod używający powiązania danych wyjściowych w ramach obiektu `context.bindings` w celu utworzenia komunikatu w kolejce. Dodaj ten kod przed instrukcją `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

## <a name="test-the-function"></a>Testowanie funkcji

1. Po zapisaniu zmian w kodzie wybierz pozycję **Testuj**.
1. Upewnij się, że test pasuje do poniższego obrazu, i wybierz pozycję **Uruchom**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Przetestuj powiązanie magazynu kolejki w Azure Portal." border="true":::

    Zwróć uwagę na to, że **treść żądania** zawiera `name` wartość *Azure*. Ta wartość jest wyświetlana w komunikacie w kolejce tworzonym po wywołaniu funkcji.
    
    Alternatywą do wyboru w tym miejscu polecenia **Uruchom** może być wywołanie funkcji, wprowadzając adres URL w przeglądarce i określając wartość `name` w ciągu zapytania. Metoda zakładająca użycie przeglądarki jest przedstawiona w [poprzednim przewodniku Szybkie start](./functions-get-started.md).

1. Sprawdź dzienniki, aby upewnić się, że funkcja zakończyła się pomyślnie. 

Środowisko uruchomieniowe funkcji utworzy nową kolejkę o nazwie **outqueue** w koncie magazynu przy pierwszym użyciu powiązania danych wyjściowych. Użyj konta magazynu, aby sprawdzić, czy kolejka i komunikat zostały utworzone.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Znajdź konto magazynu połączone z usługą AzureWebJobsStorage


1. Przejdź do aplikacji funkcji i wybierz pozycję **Konfiguracja**.

1. W obszarze **Ustawienia aplikacji** wybierz pozycję **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Zrzut ekranu przedstawia stronę konfiguracji z wybraną pozycją AzureWebJobsStorage." border="true":::

1. Znajdź i zanotuj nazwę konta.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Znajdź konto magazynu połączone z usługą AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W grupie zasobów dla aplikacji funkcji wybierz konto magazynu używane w ramach tego przewodnika Szybki Start.

1. W obszarze **Usługa kolejki** wybierz pozycję **Queues** (kolejki) i wybierz kolejkę o nazwie **requeue**. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

1. Ponownie uruchom funkcję. W kolejce pojawi się nowy komunikat.  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dodano powiązanie danych wyjściowych do istniejącej funkcji. Aby uzyskać więcej informacji na temat tworzenia powiązań z kolejką magazynu, zobacz [Powiązania kolejki magazynu w usłudze Azure Functions](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
