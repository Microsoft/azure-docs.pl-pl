---
title: Tworzenie funkcji na platformie Azure wyzwalanej przez komunikaty kolejki
description: Użyj Azure Functions, aby utworzyć bezserwerową funkcję wywoływaną przez komunikaty przesłane do kolejki na platformie Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90981576"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Tworzenie funkcji wyzwalanej przez usługę Azure Queue Storage

Dowiedz się, jak utworzyć funkcję, która jest wyzwalana w momencie przesłania komunikatów do kolejki usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="Pomyślnie utworzono aplikację funkcji.." border="true":::

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Tworzenie funkcji wyzwalanej przez kolejkę

1. Wybierz pozycję **funkcje**, a następnie wybierz pozycję **+ Dodaj** , aby dodać nową funkcję.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Wybierz szablon funkcji w Azure Portal." border="true":::

1. Wybierz szablon **wyzwalacza usługi Azure queue storage** .

1. Użyj ustawień w sposób określony w tabeli znajdującej się poniżej obrazu.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Nazwa i konfiguracja funkcji wyzwalanej przez magazyn kolejek." border="true":::


    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa** | Unikatowa w obrębie aplikacji funkcji | Nazwa funkcji wyzwalanej przez kolejkę. |
    | **Nazwa kolejki**   | myqueue-items    | Nazwa kolejki, z którą zostanie nawiązane połączenie na koncie magazynu. |
    | **Połączenie konta magazynu** | AzureWebJobsStorage | Możesz skorzystać z połączenia konta magazynu już używanego przez aplikację funkcji lub utworzyć nowe.  |    

1. Wybierz pozycję **Utwórz funkcję** , aby utworzyć funkcję.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Utwórz funkcję wyzwalaną przez magazyn kolejek." border="true":::

Następnie nawiąż połączenie z kontem usługi Azure Storage i Utwórz kolejkę magazynu **elementów webqueue** .

## <a name="create-the-queue"></a>Tworzenie kolejki

1. W funkcji na stronie **Przegląd** wybierz grupę zasobów.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Wybierz grupę zasobów Azure Portal." border="true":::

1. Znajdź i wybierz konto magazynu grupy zasobów.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Uzyskaj dostęp do konta magazynu." border="true":::

1. Wybierz **kolejno pozycje kolejki**, a następnie pozycję **+ Queue**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Dodaj kolejkę do konta magazynu w Azure Portal." border="true":::

1. W polu **Nazwa** wpisz `myqueue-items` , a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Nazwij kontener magazynu kolejki." border="true":::

Teraz, gdy masz już kolejkę magazynu, możesz przetestować funkcję, dodając komunikat do kolejki.

## <a name="test-the-function"></a>Testowanie funkcji

1. Wróć do witryny Azure Portal, przejdź do swoich funkcji, rozwiń pozycję **Dzienniki** w dolnej części strony i upewnij się, że strumieniowe przesyłanie dzienników nie jest wstrzymane.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Rozwiń dziennik w Azure Portal." border="true":::

1. W osobnym oknie przeglądarki przejdź do grupy zasobów w Azure Portal i wybierz konto magazynu.

1. Wybierz pozycję **kolejki**, a następnie wybierz kontener **elementy z kolejki** .

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Przejdź do kolejki elementu webqueue-Items w Azure Portal." border="true":::

1. Wybierz pozycję **Dodaj komunikat** i wpisz "Hello World!" w **wiadomości tekstowej**. Wybierz przycisk **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Zrzut ekranu pokazuje wybrany przycisk Dodaj komunikat, a pole tekst komunikatu wyróżnione." border="true":::

1. Poczekaj kilka sekund, a następnie wróć do dzienników funkcji i sprawdź, czy nowy komunikat został odczytany z kolejki.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Wyświetlanie komunikatu w dziennikach." border="true":::

1. Wróć do kolejki magazynu, wybierz pozycję **Odśwież** i sprawdź, czy komunikat został przetworzony i czy nie jest już w kolejce.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję, która jest uruchamiana w momencie dodania komunikatu do kolejki magazynu. Aby uzyskać więcej informacji na temat wyzwalaczy usługi Queue Storage, zobacz [Powiązania usługi Queue Storage w usłudze Azure Functions](functions-bindings-storage-queue.md).

Teraz, gdy masz już utworzoną swoją pierwszą funkcję, dodajmy do funkcji powiązanie danych wyjściowych, które zapisuje komunikat z powrotem do innej kolejki.

> [!div class="nextstepaction"]
> [Dodawanie komunikatów do kolejki usługi Azure Storage przy użyciu funkcji](functions-integrate-storage-queue-output-binding.md)
