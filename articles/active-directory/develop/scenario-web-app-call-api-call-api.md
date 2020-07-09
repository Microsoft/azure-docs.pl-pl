---
title: Wywoływanie interfejsu API sieci Web z aplikacji sieci Web — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (wywołując chroniony internetowy interfejs API)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 12802ab6dcfbbe5a1c5576ab672ead864dd0b4ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82559877"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: wywoływanie interfejsu API sieci Web

Teraz, gdy masz token, możesz wywołać chroniony internetowy interfejs API.

## <a name="call-a-protected-web-api"></a>Wywoływanie chronionego internetowego interfejsu API

Wywoływanie chronionego internetowego interfejsu API zależy od języka i struktury wyboru:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Oto uproszczony kod dla akcji `HomeController` . Ten kod pobiera token do wywołania Microsoft Graph. Dodano kod, aby pokazać, jak wywołać Microsoft Graph jako interfejs API REST. Adres URL Microsoft Graph interfejsu API jest dostępny w appsettings.jspliku i odczytywany w zmiennej o nazwie `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

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
> Większość interfejsów API sieci Web platformy Azure udostępnia zestaw SDK, który upraszcza wywoływanie interfejsu API. Jest to również prawdziwe Microsoft Graph. W następnym artykule dowiesz się, gdzie znaleźć samouczek, który ilustruje użycie interfejsu API.

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

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-web-app-call-api-production.md)
