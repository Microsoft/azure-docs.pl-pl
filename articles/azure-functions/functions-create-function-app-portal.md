---
title: Tworzenie pierwszej funkcji w witrynie Azure Portal
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 336e531f4ec64141770fc26d7e6eea9ebfedf922
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517156"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Tworzenie pierwszej funkcji w witrynie Azure Portal

Azure Functions umożliwia uruchamianie kodu w środowisku bez serwera bez konieczności wcześniejszego tworzenia maszyny wirtualnej ani publikowania aplikacji internetowej. Z tego artykułu dowiesz się, jak za pomocą Azure Functions utworzyć funkcję wyzwalacza HTTP "hello world" w Azure Portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Zamiast tego zalecamy lokalne opracowywanie [funkcji](functions-develop-local.md) i publikowanie ich w aplikacji funkcji na platformie Azure.  
Aby rozpocząć pracę z wybranym lokalnym środowiskiem projektowym i językiem, użyj jednego z następujących linków:

| Visual Studio Code | Terminal/wiersz polecenia | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Wprowadzenie do języka C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Wprowadzenie do języka Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Wprowadzenie do języka JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Wprowadzenie do programu PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Wprowadzenie do języka Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Wprowadzenie do języka C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Wprowadzenie do języka Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Wprowadzenie do języka JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Wprowadzenie do programu PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Wprowadzenie do języka Python](./create-first-function-cli-python.md) | [Wprowadzenie do języka C #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie utwórz funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalacza HTTP

1. W menu po lewej stronie okna **Funkcje** wybierz pozycję **Funkcje,** a następnie wybierz **pozycję Dodaj** z górnego menu. 
 
1. W **oknie Dodawanie funkcji** wybierz szablon **Wyzwalacz** HTTP.

    ![Wybieranie funkcji wyzwalacza HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. W **obszarze Szczegóły szablonu** użyj dla opcji Nowa funkcja wybierz pozycję Anonimowe z listy rozwijanej Poziom autoryzacji, a następnie wybierz pozycję `HttpExample`  **Dodaj**.  **[](functions-bindings-http-webhook-trigger.md#authorization-keys)**

    Platforma Azure tworzy funkcję wyzwalacza HTTP. Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji wyzwalacza HTTP wybierz pozycję **Kod i test** z menu po lewej stronie, a następnie wybierz pozycję Pobierz adres **URL** funkcji z górnego menu.

    ![Wybierz pozycję Pobierz adres URL funkcji](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. W **oknie dialogowym Pobierz** adres URL funkcji wybierz pozycję **domyślne** z listy rozwijanej, a następnie wybierz ikonę Kopiuj **do schowka.** 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania na końcu tego adresu URL i `?name=<your_name>` naciśnij klawisz Enter, aby uruchomić żądanie. W przeglądarce powinien zostać wyświetlony komunikat odpowiedzi, który zwraca wartość ciągu zapytania. 

    Jeśli adres URL żądania zawiera [klucz](functions-bindings-http-webhook-trigger.md#authorization-keys) dostępu ( ), oznacza to, że podczas tworzenia funkcji należy wybrać pozycję Funkcja zamiast poziomu dostępu `?code=...` Anonimowe.   W tym przypadku należy zamiast tego dołączyć `&name=<your_name>` .

1. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia, wróć do strony Kod **i testowanie** w portalu i rozwiń strzałkę **Dzienniki** w dolnej części strony. Ponownie wywołaj funkcję, aby wyświetlić dane wyjściowe śledzenia zapisane w dziennikach. 

    :::image type="content" source="media/functions-create-first-azure-function/function-view-logs.png" alt-text="Przeglądarka dzienników usługi Functions w Azure Portal":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
