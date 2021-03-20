---
title: Uwidacznianie funkcji za pomocą usługi OpenAPI na platformie API Management Azure
description: Utwórz definicję interfejsu OpenAPI, która umożliwia innym aplikacjom i usługom wywołanie Twojej funkcji na platformie Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212912"
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

Funkcja oblicza następnie koszt naprawy i ilość przychodów, które może wykonać w 24-godzinnym okresie. Aby utworzyć funkcję wyzwalaną przez protokół HTTP w [Azure Portal](https://portal.azure.com):

1. W menu po lewej stronie aplikacji usługi Functions wybierz pozycję **funkcje**, a następnie wybierz pozycję **Dodaj** z górnego menu.

1. W oknie **Nowa funkcja** wybierz pozycję **wyzwalacz http**.

1. Dla **nowej funkcji** wprowadź `TurbineRepair` . 

1. Wybierz z listy rozwijanej **[poziom autoryzacji](functions-bindings-http-webhook-trigger.md#http-auth)** pozycję **Funkcja** , a następnie wybierz pozycję **Utwórz funkcję**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Utwórz funkcję HTTP dla OpenAPI":::

1. Wybierz pozycję **Code + test**, a następnie na liście rozwijanej wybierz pozycję **Run. CSX** . Zastąp zawartość pliku skryptu Run. CSX C# następującym kodem, a następnie wybierz pozycję **Zapisz**:

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

    Ten kod funkcji zwraca komunikat z `Yes` lub `No` , aby wskazać, czy Naprawa awaryjna jest opłacalna. Zwraca również szansę, że analiza bioreprezentuje, i koszt naprawy turbiny.

1. Aby przetestować funkcję, wybierz pozycję **test**, wybierz kartę **dane wejściowe** , wprowadź następujące dane wejściowe dla **treści**, a następnie wybierz pozycję **Uruchom**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Testowanie funkcji w witrynie Azure Portal":::

    Na karcie **dane wyjściowe** zwracany jest następujący wynik:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Funkcja określająca opłacalność naprawy awaryjnej jest już gotowa. Następnie utworzysz definicję OpenAPI dla aplikacji funkcji.

## <a name="generate-the-openapi-definition"></a>Generowanie definicji interfejsu OpenAPI

Aby wygenerować definicję OpenAPI:

1. Wybierz aplikację funkcji, wybierz **API Management** z menu po lewej stronie, a następnie wybierz pozycję **Utwórz nowy** w obszarze **API Management**.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Wybierz API Management":::


1. Użyj ustawień API Management, zgodnie z opisem w poniższej tabeli:

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Nazwa jest generowana na podstawie nazwy aplikacji funkcji. |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzony nowy zasób. |  
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Ten sam zasób, który jest używany przez aplikację funkcji, który powinien zostać ustawiony dla Ciebie. |
    | **Lokalizacja** | Zachodnie stany USA | Wybierz lokalizację zachodnie stany USA. |
    | **Nazwa organizacji** | Contoso | Nazwa organizacji używanej w portalu dla deweloperów i powiadomień e-mail. |
    | **Adres e-mail administratora** | Twój adres e-mail | Wiadomość e-mail, która otrzymała powiadomienia systemowe od API Management. |
    | **Warstwa cenowa** | Zużycie | Warstwa zużycia nie jest dostępna we wszystkich regionach. Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem API Management](https://azure.microsoft.com/pricing/details/api-management/) . |

    ![Tworzenie nowej usługi API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Wybierz pozycję **Utwórz**, aby utworzyć wystąpienie usługi API Management, co może potrwać kilka minut.

1. Po utworzeniu wystąpienia na platformie Azure włączana jest opcja **włącz Application Insights** na stronie. Wybierz go, aby wysłać dzienniki do tego samego miejsca, w którym znajduje się aplikacja funkcji, a następnie wybierz pozycję **Połącz interfejs API**.

1. Zostanie otwarty **Azure Functions importowania** z wyróżnioną funkcją **TurbineRepair** . Wybierz pozycję **Wybierz**, aby kontynuować.

    ![Importowanie Azure Functions do API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Na stronie **Utwórz z aplikacja funkcji** zaakceptuj wartości domyślne, a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Utwórz z aplikacja funkcji":::

    Platforma Azure tworzy interfejs API dla funkcji.

## <a name="test-the-api"></a>Testowanie interfejsu API

Przed użyciem definicji OpenAPI należy sprawdzić, czy interfejs API działa.

1. Na stronie aplikacja funkcji wybierz pozycję **API Management**, wybierz kartę **test** , a następnie wybierz pozycję **Opublikuj TurbineRepair**. 

1. Wprowadź następujący kod w **treści żądania**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Wybierz pozycję **Wyślij**, a następnie Wyświetl **odpowiedź HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Interfejs API funkcji testowych":::

## <a name="download-the-openapi-definition"></a>Pobierz definicję OpenAPI

Jeśli interfejs API działa zgodnie z oczekiwaniami, można pobrać definicję OpenAPI.

1. Wybierz pozycję **Pobierz definicję openapi** w górnej części strony.
   
   ![Pobieranie definicji interfejsu OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Zapisz pobrany plik JSON, a następnie otwórz go. Zapoznaj się z definicją.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

Funkcja API Management Integration została użyta do wygenerowania definicji OpenAPI funkcji. Teraz można edytować definicję w API Management w portalu. Możesz również [dowiedzieć się więcej na temat API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Edytuj definicję OpenAPI w API Management](../api-management/edit-api.md)
