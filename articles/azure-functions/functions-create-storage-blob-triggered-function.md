---
title: Tworzenie funkcji na platformie Azure wyzwalanej przez usługę BLOB Storage
description: Użyj Azure Functions, aby utworzyć funkcję bezserwerową, która jest wywoływana przez elementy dodane do kontenera magazynu obiektów BLOB.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83123126"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Tworzenie funkcji na platformie Azure wyzwalanej przez usługę BLOB Storage

Dowiedz się, jak utworzyć funkcję wyzwalaną, gdy pliki są przekazywane do kontenera magazynu obiektów blob lub zaktualizowane.

## <a name="prerequisites"></a>Wymagania wstępne

+ Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Twoja nowa aplikacja funkcji została pomyślnie utworzona.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Tworzenie funkcji wyzwalanej przez magazyn obiektów blob platformy Azure

1. Wybierz pozycję **funkcje**, a następnie wybierz pozycję **+ Dodaj** , aby dodać nową funkcję.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Wybierz szablon funkcji w Azure Portal." border="true":::

1. Wybierz szablon **wyzwalacza usługi Azure Blob Storage** .

1. Użyj ustawień w sposób określony w tabeli znajdującej się poniżej obrazu.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Nazwa i konfiguracja funkcji wyzwalanej przez magazyn obiektów BLOB." border="true":::

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nowa funkcja** | Unikatowa w obrębie aplikacji funkcji | Nazwa funkcji wyzwalanej przez obiekt blob. |
    | **Ścieżka**   | samples-workitems/{nazwa}    | Lokalizacja w monitorowanym magazynie obiektów Blob. Nazwa pliku obiektu Blob jest przekazywana w powiązaniu jako parametr _nazwa_.  |
    | **Połączenie konta magazynu** | AzureWebJobsStorage | Możesz skorzystać z połączenia konta magazynu już używanego przez aplikację funkcji lub utworzyć nowe.  |

1. Wybierz pozycję **Utwórz funkcję** , aby utworzyć funkcję.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Tworzenie funkcji wyzwalanej przez magazyn obiektów Blob." border="true":::

Następnie utwórz kontener **Samples-elementy robocze** .

## <a name="create-the-container"></a>Tworzenie kontenera

1. W funkcji na stronie **Przegląd** wybierz grupę zasobów.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Wybierz grupę zasobów Azure Portal." border="true":::

1. Znajdź i wybierz konto magazynu grupy zasobów.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Uzyskaj dostęp do konta magazynu." border="true":::

1. Wybierz **kontenery**, a następnie wybierz pozycję **+ kontener**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Dodaj kontener do konta magazynu w Azure Portal." border="true":::

1. W polu **Nazwa** wpisz `samples-workitems` , a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Nadaj nazwę kontenerowi magazynu." border="true":::

Teraz, gdy masz już kontener obiektów Blob, możesz przetestować funkcję, przekazując plik do kontenera.

## <a name="test-the-function"></a>Testowanie funkcji

1. Wróć do witryny Azure Portal, przejdź do swoich funkcji, rozwiń pozycję **Dzienniki** w dolnej części strony i upewnij się, że strumieniowe przesyłanie dzienników nie jest wstrzymane.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Rozwiń dziennik w Azure Portal." border="true":::

1. W osobnym oknie przeglądarki przejdź do grupy zasobów w Azure Portal i wybierz konto magazynu.

1. Wybierz pozycję **kontenery**, a następnie wybierz kontener **przykłady — elementy robocze** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Przejdź do kontenerów przykłady — elementy robocze w Azure Portal." border="true":::

1. Wybierz pozycję **Przekaż**, a następnie wybierz ikonę folderu, aby wybrać plik do przekazania.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Przekazywanie pliku do kontenera obiektów Blob." border="true":::

1. Przejdź do pliku na komputerze lokalnym, na przykład pliku obrazu, wybierz plik. Wybierz pozycję **Otwórz** , a następnie **Przekaż**.

1. Wróć do dzienników funkcji i sprawdź, czy obiekt Blob został odczytany.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Wyświetlanie komunikatu w dziennikach." border="true":::

    >[!NOTE]
    > Gdy aplikacja funkcji zostanie uruchomiona w domyślnym planie Zużycie, może wystąpić nawet kilkuminutowe opóźnienie między dodaniem lub zaktualizowaniem obiektu Blob a wyzwoleniem funkcji. Jeśli zależy Ci na małych opóźnieniach w funkcjach wyzwalanych przez obiekty Blob, rozważ uruchomienie aplikacji funkcji w planie usługi App Service.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję, która jest uruchamiana w momencie dodania obiektu Blob do magazynu obiektów Blob lub zaktualizowania obiektu Blob w magazynie. Aby uzyskać więcej informacji na temat wyzwalaczy magazynu obiektów Blob, zobacz [Powiązania magazynu obiektów Blob w usłudze Azure Functions](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
