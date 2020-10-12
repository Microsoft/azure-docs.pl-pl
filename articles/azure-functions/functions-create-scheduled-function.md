---
title: Tworzenie funkcji uruchamianej zgodnie z harmonogramem na platformie Azure
description: Dowiedz się, jak utworzyć na platformie Azure funkcję uruchamianą zgodnie z określonym harmonogramem.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9e542c493c02174364072f91d092f05ad9ec69cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973091"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Tworzenie funkcji wyzwalanej czasomierzem na platformie Azure

Dowiedz się, jak za pomocą Azure Functions utworzyć funkcję [bezserwerową](https://azure.microsoft.com/solutions/serverless/) , która jest uruchamiana na podstawie zdefiniowanego harmonogramu.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Twoja nowa aplikacja funkcji jest gotowa do użycia. Następnie utworzysz funkcję w nowej aplikacji funkcji.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Tworzenie funkcji wyzwalanej czasomierzem

1. W aplikacji funkcji wybierz pozycję **funkcje**, a następnie wybierz pozycję **+ Dodaj** . 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

1. Wybierz szablon **wyzwalacza czasomierza** . 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

1. Skonfiguruj nowy wyzwalacz przy użyciu ustawień określonych w tabeli poniżej obrazu, a następnie wybierz pozycję **Utwórz funkcję**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::
    
    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa** | Domyślne | Określa nazwę funkcji wyzwalanej czasomierzem. |
    | **Zaplanuj** | 0 \* /1 \* \* \*\* | Składające się z 6 pól [wyrażenie CRON](functions-bindings-timer.md#ncrontab-expressions) planujące uruchamianie funkcji co minutę. |

## <a name="test-the-function"></a>Testowanie funkcji

1. W funkcji wybierz pozycję **Code + test** i rozwiń dzienniki.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

1. Sprawdź wykonywanie, wyświetlając informacje zapisane w dziennikach.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

Teraz możesz zmienić harmonogram funkcji tak, aby była uruchamiana co godzinę, a nie co minutę.

## <a name="update-the-timer-schedule"></a>Aktualizowanie harmonogramu czasomierza

1. W funkcji wybierz pozycję **integracja**. Tutaj definiujesz powiązania danych wejściowych i wyjściowych dla funkcji, a także ustawisz harmonogram. 

1. Wybierz **czasomierz (Timer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

1. Zaktualizuj wartość **harmonogramu** do `0 0 */1 * * *` , a następnie wybierz pozycję **Zapisz**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Pomyślnie utworzona aplikacja funkcji." border="true":::

Teraz masz funkcję, która jest uruchamiana co godzinę, w ciągu godziny.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję, która jest uruchamiana na podstawie harmonogramu. Aby uzyskać więcej informacji na temat wyzwalaczy czasomierza, zobacz [Planowanie wykonywania kodu za pomocą usługi Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
