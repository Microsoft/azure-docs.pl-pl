---
title: Tworzenie funkcji przy użyciu platformy Azure for Students Starter
description: Dowiedz się, jak utworzyć funkcję platformy Azure z poziomu subskrypcji startowej platformy Azure dla uczniów
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182533"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Tworzenie funkcji przy użyciu platformy Azure for Students Starter

W tym samouczku utworzysz funkcję HTTP "Hello World" w ramach subskrypcji platformy Azure for Students Starter. Zawarto również informacje o tym, co jest dostępne w Azure Functions w tym typie subskrypcji.

*Platforma Microsoft Azure for Students Starter* ułatwia rozpoczęcie pracy z produktami platformy Azure, które są potrzebne do samotworzenia w chmurze. [Więcej informacji na temat tej oferty znajdziesz tutaj.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji sieci Web. [Dowiedz się więcej o funkcjach tutaj.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Tworzenie funkcji

 W tym artykule dowiesz się, jak za pomocą Azure Functions utworzyć w Azure Portal funkcję wyzwalacza HTTP "Hello World".

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalacza HTTP

1. W menu po lewej stronie okna **funkcje** wybierz pozycję **funkcje**, a następnie wybierz pozycję **Dodaj** z górnego menu. 
 
1. W oknie **Nowa funkcja** wybierz pozycję **wyzwalacz http**.

    ![Wybierz funkcję wyzwalacza HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. W oknie **Nowa funkcja** Zaakceptuj nazwę domyślną **nowej funkcji** lub wprowadź nową nazwę. 

1. Z listy rozwijanej **poziom autoryzacji** wybierz pozycję **anonimowe** , a następnie wybierz pozycję **Utwórz funkcję**.

    Platforma Azure tworzy funkcję wyzwalacza HTTP. Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji wyzwalacza HTTP wybierz pozycję **Kod + test** z menu po lewej stronie, a następnie wybierz pozycję **Pobierz adres URL funkcji** z górnego menu.

    ![Wybierz adres URL funkcji Get](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. W oknie dialogowym **pobieranie adresu URL funkcji** wybierz opcję **domyślne** z listy rozwijanej, a następnie wybierz ikonę **Kopiuj do schowka** . 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `?name=<your_name>` na końcu tego adresu URL i naciśnij klawisz ENTER, aby uruchomić żądanie. 

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

1. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia, Wróć do strony **Code + test** w portalu i rozwiń strzałkę **dzienniki** w dolnej części strony.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Obsługiwane funkcje platformy Azure dla uczniów Starter

Na platformie Azure for Students Starter masz dostęp do większości funkcji środowiska uruchomieniowego Azure Functions, a poniżej przedstawiono kilka ograniczeń klucza:

* Wyzwalacz HTTP jest jedynym obsługiwanym typem wyzwalacza.
    * Wszystkie powiązania danych wejściowych i danych wyjściowych są obsługiwane. [Zapoznaj się z pełną listą tutaj.](functions-triggers-bindings.md)
* Obsługiwane języki: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [Zobacz języki obsługiwane w wyższych planach tutaj](supported-languages.md)
* System Windows jest jedynym obsługiwanym systemem operacyjnym.
* Skalowanie jest ograniczone do [jednego wystąpienia warstwy Bezpłatna](https://azure.microsoft.com/pricing/details/app-service/windows/) uruchomionego przez maksymalnie 60 minut każdego dnia. Będziesz serverlessly skalę od 0 do 1 wystąpienia automatycznie, ponieważ odbierany jest ruch HTTP, ale nie będzie więcej.
* Obsługiwane są tylko [wersje 2. x i nowsze](functions-versions.md) środowiska uruchomieniowego Functions.
* Wszystkie narzędzia deweloperskie są obsługiwane w przypadku funkcji edycji i publikowania. Obejmuje to VS Code, Visual Studio, interfejs wiersza polecenia platformy Azure i Azure Portal. Jeśli chcesz użyć innych elementów niż Portal, musisz najpierw utworzyć aplikację w portalu, a następnie wybrać tę aplikację jako cel wdrożenia w preferowanym narzędziu.

## <a name="next-steps"></a>Następne kroki

Tworzenie aplikacji funkcji z prostą funkcją wyzwalacza HTTP zakończyło się pomyślnie. Następnie możesz eksplorować narzędzia lokalne, inne języki, monitorowanie i integracje.

 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Przewodnik dla deweloperów Azure Functions JavaScript](./functions-reference-node.md)
 * [Użyj Azure Functions, aby nawiązać połączenie z Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Dowiedz się więcej o Azure Functions powiązaniach http](./functions-bindings-http-webhook.md).
 * [Monitoruj Azure Functions](./functions-monitoring.md)