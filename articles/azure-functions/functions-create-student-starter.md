---
title: Tworzenie funkcji przy użyciu platformy Azure for Students Starter
description: Dowiedz się, jak utworzyć funkcję platformy Azure z poziomu subskrypcji startowej platformy Azure dla uczniów
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756462"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Tworzenie funkcji przy użyciu platformy Azure for Students Starter

W tym samouczku utworzymy funkcję HTTP "Hello World" w ramach subskrypcji platformy Azure for Students Starter. Zawarto również informacje o tym, co jest dostępne w Azure Functions w tym typie subskrypcji.

*Platforma Microsoft Azure for Students Starter* ułatwia rozpoczęcie pracy z produktami platformy Azure, które są potrzebne do samotworzenia w chmurze. [Więcej informacji na temat tej oferty znajdziesz tutaj.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji sieci Web. [Dowiedz się więcej o funkcjach tutaj.](./functions-overview.md)

## <a name="create-a-function"></a>Tworzenie funkcji

 W tym temacie dowiesz się, jak używać funkcji do tworzenia protokołu HTTP wyzwalanego przez funkcję "Hello World" w Azure Portal.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

1. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu Azure Portal. Następnie wybierz pozycję **obliczeniowe** > **aplikacja funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
   | **[App Service plan/lokalizacja](./functions-scale.md)** | Nowa | Plan hostingu kontrolujący region, w którym wdrożono aplikację funkcji, oraz gęstość zasobów. Wiele aplikacji funkcji wdrożonych w ramach tego samego planu będzie korzystać z tego samego pojedynczego bezpłatnego wystąpienia. Jest to ograniczenie planu początkowego studenta. W [tym miejscu objaśniono](./functions-scale.md) pełne opcje hostingu.|
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled (Włączony) | Application Insights jest używany do przechowywania i analizowania dzienników aplikacji funkcji. Jest ona domyślnie włączona w przypadku wybrania lokalizacji, która obsługuje Application Insights. Application Insights można włączyć dla dowolnej funkcji, ręcznie wybierając w pobliżu region do wdrożenia Application Insights. Bez Application Insights będziesz mieć możliwość wyświetlania dzienników przesyłania strumieniowego na żywo.

3. Wybierz pozycję **App Service plan/lokalizacja** powyżej, aby wybrać inną lokalizację

4. Wybierz pozycję **Utwórz nowy** , a następnie nadaj Zaplanuj unikatową nazwę.

5. Wybierz lokalizację znajdującą się najbliżej siebie. [Tutaj znajdziesz pełną mapę regionów świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-student-starter/function-app-create-notification.png)

8. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

1. Rozwiń nową **+** aplikację funkcji, a następnie wybierz przycisk obok pozycji **funkcje**, wybierz pozycję **w portalu**, a następnie wybierz pozycję **Kontynuuj**.

    ![Szybkie rozpoczynanie pracy z usługą Functions — wybieranie platformy.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Wybierz opcję **Element WebHook + interfejs API**, a następnie wybierz opcję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Obsługiwane funkcje platformy Azure dla uczniów Starter

Na platformie Azure for Students Starter masz dostęp do większości funkcji środowiska uruchomieniowego Azure Functions, a poniżej przedstawiono kilka ograniczeń klucza:

* Wyzwalacz HTTP jest jedynym obsługiwanym typem wyzwalacza.
    * Wszystkie powiązania danych wejściowych i danych wyjściowych są obsługiwane. [Zapoznaj się z pełną listą tutaj.](functions-triggers-bindings.md)
* Obsługiwane języki: 
    * C# (.NET Core 2)
    * JavaScript (Node. js 8 & 10)
    * F # (.NET Core 2)
    * [Zobacz języki obsługiwane w wyższych planach tutaj](supported-languages.md)
* System Windows jest jedynym obsługiwanym systemem operacyjnym.
* Skalowanie jest ograniczone do [jednego wystąpienia warstwy Bezpłatna](https://azure.microsoft.com/pricing/details/app-service/windows/) uruchomionego przez maksymalnie 60 minut każdego dnia. Serverlessly skalowanie od 0 do 1 wystąpienia jest automatycznie, ponieważ odbierany jest ruch HTTP, ale nie będzie więcej.
* Obsługiwane są tylko [wersje 2. x i nowsze](functions-versions.md) środowiska uruchomieniowego Functions.
* Wszystkie narzędzia deweloperskie są obsługiwane w przypadku funkcji edycji i publikowania. Obejmuje to VS Code, Visual Studio, interfejs wiersza polecenia platformy Azure i Azure Portal. Jeśli chcesz użyć innych elementów niż Portal, musisz najpierw utworzyć aplikację w portalu, a następnie wybrać tę aplikację jako cel wdrożenia w preferowanym narzędziu.

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. Teraz możesz eksplorować narzędzia lokalne, inne języki, monitorowanie i integracje.

 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Przewodnik dla deweloperów Azure Functions JavaScript](./functions-reference-node.md)
 * [Użyj Azure Functions, aby nawiązać połączenie z Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Dowiedz się więcej o Azure Functions powiązaniach http](./functions-bindings-http-webhook.md).
 * [Monitoruj Azure Functions](./functions-monitoring.md)
