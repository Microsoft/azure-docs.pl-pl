---
title: Interfejs API sieci Web, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b1582af2bbd97579852ead0d4462f80f3a50fe6a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257150"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API

Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API. Zwykle są wywoływane podrzędne interfejsy API z kontrolera lub stron internetowego interfejsu API.

## <a name="controller-code"></a>Kod kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Gdy korzystasz z *Microsoft. Identity. Web*, masz trzy scenariusze użycia:

- [Microsoft Graph wywołania](#call-microsoft-graph)
- [Wywoływanie internetowego interfejsu API innego niż Microsoft Graph](#call-web-api-other-than-microsoft-graph)
- [Uzyskaj token ręcznie](#acquire-a-token-manually)

#### <a name="call-microsoft-graph"></a>Microsoft Graph wywołania

W tym scenariuszu dodano program `.AddMicrosoftGraph()` *Startup.cs* zgodnie z [konfiguracją kodu](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)i można bezpośrednio wstrzyknąć do `GraphServiceClient` kontrolera lub konstruktora stronicowego do użycia w akcjach. Poniższa przykładowa strona Razor wyświetla zdjęcie zalogowanego użytkownika.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="call-web-api-other-than-microsoft-graph"></a>Wywoływanie internetowego interfejsu API innego niż Microsoft Graph

W tym scenariuszu dodano program `.AddDownstreamWebApi()` *Startup.cs* zgodnie z [konfiguracją kodu](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)i można bezpośrednio wstrzyknąć `IDownstreamWebApi` usługę do kontrolera lub konstruktora stron i używać jej w akcjach:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

`CallWebApiForUserAsync`Metoda ma także jednoznacznie wpisane typy zastąpień, które umożliwiają bezpośrednie odbieranie obiektu. Na przykład następująca metoda otrzymała `Todo` wystąpienie, które jest silnie wpisaną reprezentacją JSON zwróconego przez internetowy interfejs API.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="acquire-a-token-manually"></a>Uzyskaj token ręcznie

Jeśli użytkownik zdecydował się uzyskać token ręcznie przy użyciu `ITokenAcquisition` usługi, należy użyć tokenu. W takim przypadku Poniższy kod kontynuuje przykładowy kod pokazywany w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

 Po uzyskaniu tokenu Użyj go jako tokenu okaziciela, aby wywołać podrzędny interfejs API.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API programu MS Graph.

Po uzyskaniu tokenu Użyj go jako tokenu okaziciela, aby wywołać podrzędny interfejs API.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Przykład pokazujący ten przepływ za pomocą MSAL Python nie jest jeszcze dostępny.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego](scenario-web-api-call-api-production.md)
