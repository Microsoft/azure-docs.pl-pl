---
title: Azure Functions powiązania wyjściowe HTTP
description: Dowiedz się, jak zwracać odpowiedzi HTTP w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697446"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions powiązania wyjściowe HTTP

Użyj powiązania wyjściowego HTTP, aby odpowiedzieć na nadawcę żądania HTTP. To powiązanie wymaga wyzwalacza HTTP i pozwala na dostosowanie odpowiedzi skojarzonej z żądaniem wyzwalacza.

Domyślną wartością zwracaną dla funkcji wyzwalanej przez protokół HTTP jest:

- `HTTP 204 No Content` z pustą treścią w funkcjach 2. x i wyższych
- `HTTP 200 OK` z pustą treścią w funkcjach 1. x

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawione w *function.js* pliku. W przypadku bibliotek klas języka C# nie ma właściwości atrybutów odpowiadających tym *function.js* właściwości.

|Właściwość  |Opis  |
|---------|---------|
| **Wprowadź** |Musi być ustawiony na `http` . |
| **wskazywa** | Musi być ustawiony na `out` . |
| **Nazwij** | Nazwa zmiennej używana w kodzie funkcji dla odpowiedzi lub `$return` do użycia wartości zwracanej. |

## <a name="usage"></a>Użycie

Aby wysłać odpowiedź HTTP, użyj standardowych wzorców odpowiedzi języka. W skrypcie języka C# lub C# należy sprawić, aby funkcja zwracała typ `IActionResult` lub `Task<IActionResult>` . W języku C# atrybut wartości zwracanej nie jest wymagany.

Na przykład odpowiedzi, zobacz [przykład wyzwalacza](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Przykład host.jsna poniższym pliku zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersjach 2. x i więcej, zobacz [host.json Reference for Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać informacje na temat host.jsw funkcjach 1. x, zobacz [host.json Reference for Azure Functions 1. x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
| customHeaders|brak|Umożliwia ustawienie niestandardowych nagłówków w odpowiedzi HTTP. Poprzedni przykład dodaje `X-Content-Type-Options` nagłówek do odpowiedzi, aby uniknąć wykrywania typu zawartości. |
|dynamicThrottlesEnabled|oznacza<sup>\*</sup>|Po włączeniu tego ustawienia powoduje, że potok przetwarzania żądań okresowo sprawdza liczniki wydajności systemu, podobnie jak `connections/threads/processes/memory/cpu/etc` i jeśli którykolwiek z tych liczników korzysta ze standardowego górnego progu (80%), żądania zostaną odrzucone z `429 "Too Busy"` odpowiedzią, dopóki licznik (s) zwróci normalne poziomy.<br/><sup>\*</sup>Wartość domyślna w planie zużycia to `true` . Wartość domyślna w ramach dedykowanego planu to `false` .|
|HSTS|Niewłączony|Gdy `isEnabled` jest ustawiona na `true` , wymuszane jest [zachowanie zabezpieczeń HTTP Strict Transport (HSTS) programu .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) , zgodnie z definicją w [ `HstsOptions` klasie](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Powyższy przykład ustawia również [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) Właściwość na 10 dni. Obsługiwane właściwości `hsts` : <table><tr><th>Właściwość</th><th>Opis</th></tr><tr><td>excludedHosts</td><td>Tablica ciągów nazw hostów, dla których nie został dodany nagłówek HSTS.</td></tr><tr><td>includeSubDomains</td><td>Wartość logiczna wskazująca, czy jest włączony parametr includeSubDomain nagłówka Strict-Transport-Security.</td></tr><tr><td>Parametru</td><td>Ciąg definiujący maksymalny parametr wieku w nagłówku Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Wartość logiczna wskazująca, czy jest włączony parametr wstępnego ładowania nagłówka zabezpieczeń Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maksymalna liczba funkcji HTTP, które są wykonywane równolegle. Ta wartość umożliwia kontrolowanie współbieżności, co może ułatwić zarządzanie użyciem zasobów. Na przykład może istnieć funkcja HTTP, która używa dużej liczby zasobów systemowych (pamięć/procesor CPU/gniazda), co sprawia, że występuje problemy, gdy współbieżność jest zbyt wysoka. Może też istnieć funkcja, która wysyła żądania wychodzące do usługi innej firmy, a te wywołania muszą mieć ograniczoną szybkość. W takich przypadkach można w tym celu zastosować ograniczenie przepustowości. <br/><sup>*</sup>Wartością domyślną planu zużycia jest 100. Wartość domyślna dla planu dedykowanego jest niepowiązana ( `-1` ).|
|maxOutstandingRequests|200<sup>\*</sup>|Maksymalna liczba oczekujących żądań, które są przechowywane w danym momencie. Ten limit obejmuje żądania, które są umieszczane w kolejce, ale nie rozpoczęto wykonywania, a także w trakcie wykonywania. Wszystkie żądania przychodzące przez ten limit są odrzucane przez odpowiedź 429 "zbyt zajęta". Dzięki temu obiekty wywołujące mogą korzystać z strategii ponawiania prób, a także kontrolować maksymalne opóźnienia żądania. Tylko kontroluje kolejkowanie, które występuje w ścieżce wykonywania hosta skryptu. Inne kolejki, takie jak Kolejka żądań ASP.NET, nadal będą obowiązywać i nie mają wpływu na to ustawienie. <br/><sup>\*</sup>Wartością domyślną planu zużycia jest 200. Wartość domyślna dla planu dedykowanego jest niepowiązana ( `-1` ).|
|routePrefix|api|Prefiks trasy dotyczący wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji z żądania HTTP](./functions-bindings-http-webhook-trigger.md)