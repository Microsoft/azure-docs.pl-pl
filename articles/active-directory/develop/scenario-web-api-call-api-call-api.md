---
title: Interfejs API sieci Web, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038547"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API

Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API. Zwykle są wywoływane podrzędne interfejsy API z kontrolera lub stron internetowego interfejsu API.

## <a name="controller-code"></a>Kod kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Gdy korzystasz z *Microsoft. Identity. Web*, masz trzy scenariusze użycia:

- [Opcja 1: Wywołaj Microsoft Graph z zestawem SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Opcja 2: wywoływanie podrzędnego interfejsu API sieci Web z klasą pomocnika](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opcja 3: wywoływanie podrzędnego interfejsu API sieci Web bez klasy pomocnika](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opcja 1: Wywołaj Microsoft Graph z zestawem SDK

W tym scenariuszu został dodany `.AddMicrosoftGraph()` w programie *Start. cs* zgodnie z [konfiguracją kodu](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), a można bezpośrednio wstrzyknąć do tego `GraphServiceClient` kontrolera lub konstruktora stronicowego w celu użycia w akcjach. Poniższa przykładowa strona Razor wyświetla zdjęcie zalogowanego użytkownika.

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

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Opcja 2: wywoływanie podrzędnego interfejsu API sieci Web z klasą pomocnika

W tym scenariuszu dodano `.AddDownstreamWebApi()` plik *Start. cs* zgodnie z [konfiguracją kodu](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)i można bezpośrednio wstrzyknąć `IDownstreamWebApi` usługę w kontrolerze lub Konstruktorze stron i użyć jej w akcjach:

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

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Opcja 3: wywoływanie podrzędnego interfejsu API sieci Web bez klasy pomocnika

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
Przykład pokazujący ten przepływ przy użyciu języka MSAL Python jest dostępny w [MS-Identity-Python-](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of)in-of.

---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-web-api-call-api-production.md).
