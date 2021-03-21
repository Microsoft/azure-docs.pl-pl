---
title: Wywoływanie interfejsu API sieci Web z aplikacji sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (wywołując chroniony internetowy interfejs API)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753295"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: wywoływanie interfejsu API sieci Web

Teraz, gdy masz token, możesz wywołać chroniony internetowy interfejs API. Zazwyczaj wywoływany jest podrzędny interfejs API z kontrolera lub stron aplikacji sieci Web.

## <a name="call-a-protected-web-api"></a>Wywoływanie chronionego internetowego interfejsu API

Wywoływanie chronionego internetowego interfejsu API zależy od języka i struktury wyboru:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Gdy korzystasz z *Microsoft. Identity. Web*, masz trzy opcje użycia na potrzeby wywoływania interfejsu API:

- [Opcja 1: Wywołaj Microsoft Graph z zestawem SDK Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [Opcja 2: wywoływanie podrzędnego interfejsu API sieci Web z klasą pomocnika](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opcja 3: wywoływanie podrzędnego interfejsu API sieci Web bez klasy pomocnika](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opcja 1: Wywołaj Microsoft Graph z zestawem SDK

Chcesz wywołać Microsoft Graph. W tym scenariuszu został dodany `AddMicrosoftGraph` w programie *Start. cs* zgodnie z [konfiguracją kodu](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph), a można bezpośrednio wstrzyknąć do tego `GraphServiceClient` kontrolera lub konstruktora stronicowego w celu użycia w akcjach. Poniższa przykładowa strona Razor wyświetla zdjęcie zalogowanego użytkownika.

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

Chcesz wywołać internetowy interfejs API inny niż Microsoft Graph. W takim przypadku został dodany `AddDownstreamWebApi` w programie *Start. cs* zgodnie z [konfiguracją kodu](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)i można bezpośrednio wstrzyknąć `IDownstreamWebApi` usługę do kontrolera lub konstruktora stron i użyć jej w akcjach:

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
}
```

`CallWebApiForUserAsync`Ma także jednoznacznie wpisane typy zastąpień, które umożliwiają bezpośrednie odbieranie obiektu. Na przykład następująca metoda odbiera `Todo` wystąpienie, które jest silnie wpisaną reprezentacją JSON zwróconego przez internetowy interfejs API.

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

Użytkownik zdecydował się uzyskać token ręcznie przy użyciu `ITokenAcquisition` usługi, a teraz musisz użyć tokenu. W takim przypadku Poniższy kod kontynuuje przykładowy kod wyświetlany w [aplikacji sieci Web, która wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-app-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów aplikacji sieci Web.

Po uzyskaniu tokenu Użyj go jako tokenu okaziciela do wywołania podrzędnego interfejsu API, w tym przypadku Microsoft Graph.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Tej samej zasady można użyć do wywołania dowolnego internetowego interfejsu API.
>
> Większość interfejsów API sieci Web platformy Azure udostępnia zestaw SDK, który upraszcza wywoływanie interfejsu API, tak jak w przypadku Microsoft Graph. Zobacz, na przykład, [Utwórz aplikację sieci Web, która autoryzuje dostęp do usługi BLOB Storage w usłudze Azure AD](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , na przykład aplikację sieci Web używającą Microsoft. Identity. Web i korzystającą z zestawu SDK usługi Azure Storage.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-web-app-call-api-production.md).