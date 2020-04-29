---
title: Uwidacznianie funkcji za pomocą usługi OpenAPI na platformie API Management Azure
description: Utwórz definicję interfejsu OpenAPI, która umożliwia innym aplikacjom i usługom wywołanie Twojej funkcji na platformie Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77210212"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Tworzenie definicji OpenAPI dla interfejsu API bezserwerowego przy użyciu usługi Azure API Management

Interfejsy API REST są często opisane przy użyciu definicji OpenAPI. Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API.

W ramach tego samouczka utworzysz funkcję, która pozwoli określić, czy awaryjna naprawa turbiny wiatrowej jest opłacalna. Następnie utworzysz definicję OpenAPI dla aplikacji funkcji przy użyciu [usługi Azure API Management](../api-management/api-management-key-concepts.md) , aby można było wywołać funkcję z innych aplikacji i usług.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie funkcji na platformie Azure
> * Generowanie definicji OpenAPI przy użyciu usługi Azure API Management
> * Testowanie definicji przez wywołanie funkcji
> * Pobierz definicję OpenAPI

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Tworzenie funkcji

W tym samouczku jest stosowana funkcja wyzwalana przez protokół HTTP, która przyjmuje dwa parametry:

* Szacowany czas trwania naprawy turbiny w godzinach.
* Pojemność turbiny w kilowatach. 

Następnie funkcja oblicza koszt naprawy oraz przychody, które mogą zostać uzyskane dzięki turbinie w ciągu 24 godzin. Aby utworzyć funkcję wyzwalaną przez protokół HTTP w [Azure Portal](https://portal.azure.com):

1. Rozwiń swoją aplikację funkcji i wybierz **+** przycisk obok pozycji **funkcje**. Wybierz pozycję **w portalu** > **Kontynuuj**.

1. Wybierz pozycję **więcej szablonów...**, a następnie wybierz pozycję **Zakończ i Wyświetl szablony**

1. Wybierz pozycję wyzwalacz HTTP, `TurbineRepair` wpisz **nazwę** `Function` funkcji, wybierz pozycję **[poziom uwierzytelniania](functions-bindings-http-webhook-trigger.md#http-auth)**, a następnie wybierz pozycję **Utwórz**.  

    ![Utwórz funkcję HTTP dla OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Zastąp zawartość pliku skryptu Run. CSX C# następującym kodem, a następnie wybierz pozycję **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Ten kod funkcji zwraca komunikat `Yes` lub `No` wskazujący, czy awaryjna naprawa turbiny jest opłacalna, a także wartość ewentualnego przychodu, który można uzyskać dzięki turbinie, oraz koszt naprawy turbiny.

1. Aby przetestować funkcję, kliknij przycisk **Testuj** po prawej stronie, aby rozwinąć kartę test. Wprowadź następującą wartość dla **treści żądania**, a następnie kliknij przycisk **Uruchom**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testowanie funkcji w witrynie Azure Portal](media/functions-openapi-definition/test-function.png)

    W treści odpowiedzi jest zwracana poniższa wartość.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Funkcja określająca opłacalność naprawy awaryjnej jest już gotowa. Następnie utworzysz definicję OpenAPI dla aplikacji funkcji.

## <a name="generate-the-openapi-definition"></a>Generowanie definicji interfejsu OpenAPI

Teraz możesz przystąpić do generowania definicji interfejsu OpenAPI.

1. Wybierz aplikację funkcji, a następnie w obszarze **funkcje platformy**wybierz **API Management** a następnie wybierz pozycję **Utwórz nowy** w obszarze **API Management**.

    ![Wybierz API Management w funkcjach platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Użyj ustawień API Management określonych w tabeli poniżej obrazu.

    ![Tworzenie nowej usługi API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Nazwa jest generowana na podstawie nazwy aplikacji funkcji. |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzony nowy zasób. |  
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Ten sam zasób, który jest używany przez aplikację funkcji, który powinien zostać ustawiony dla Ciebie. |
    | **Lokalizacja** | Zachodnie stany USA | Wybierz lokalizację zachodnie stany USA. |
    | **Nazwa organizacji** | Contoso | Nazwa organizacji używanej w portalu dla deweloperów i powiadomień e-mail. |
    | **Adres e-mail administratora** | Twój adres e-mail | Wiadomość e-mail, która otrzymała powiadomienia systemowe od API Management. |
    | **Warstwa cenowa** | Użycie (wersja zapoznawcza) | Warstwa zużycia jest w wersji zapoznawczej i nie jest dostępna we wszystkich regionach. Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem API Management](https://azure.microsoft.com/pricing/details/api-management/) . |

1. Wybierz pozycję **Utwórz**, aby utworzyć wystąpienie usługi API Management, co może potrwać kilka minut.

1. Wybierz pozycję **włącz Application Insights** , aby wysyłać dzienniki do tego samego miejsca, w którym znajduje się aplikacja funkcji, a następnie zaakceptuj pozostałe wartości domyślne i wybierz pozycję **interfejs API łączenia**.

1. Zostanie otwarty **Azure Functions importowania** z wyróżnioną funkcją **TurbineRepair** . Wybierz pozycję **Wybierz**, aby kontynuować.

    ![Importowanie Azure Functions do API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Na stronie **Tworzenie na podstawie aplikacja funkcji** zaakceptuj wartości domyślne i wybierz pozycję **Utwórz** .

    ![Utwórz z aplikacja funkcji](media/functions-openapi-definition/create-function-openapi.png)

Interfejs API jest teraz tworzony dla funkcji.

## <a name="test-the-api"></a>Testowanie interfejsu API

Przed użyciem definicji OpenAPI należy sprawdzić, czy interfejs API działa.

1. Na karcie **testowanie** funkcji wybierz pozycję **Księguj** .

1. Wprowadź wartości **godzin** i **pojemności**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Kliknij przycisk **Wyślij**, a następnie Wyświetl odpowiedź HTTP.

    ![Interfejs API funkcji testowych](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Pobierz definicję OpenAPI

Jeśli interfejs API działa zgodnie z oczekiwaniami, można pobrać definicję OpenAPI.

1. Wybierz pozycję **Pobierz definicję openapi** w górnej części strony.
   
   ![Pobieranie definicji interfejsu OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Otwórz pobrany plik JSON i zapoznaj się z definicją.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

Funkcja API Management Integration została użyta do wygenerowania definicji OpenAPI funkcji. Teraz można edytować definicję w API Management w portalu. Możesz również [dowiedzieć się więcej na temat API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Edytuj definicję OpenAPI w API Management](../api-management/edit-api.md)
