---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033457"
---
## <a name="authentication"></a>Uwierzytelnianie

Każde żądanie wymaga nagłówka autoryzacji. W tej tabeli przedstawiono nagłówki, które są obsługiwane dla każdej usługi:

| Obsługiwane nagłówki autoryzacji | Zamiana mowy na tekst | Zamiana tekstu na mowę |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Tak | Tak |
| Autoryzacja: okaziciela | Tak | Tak |

W przypadku korzystania z `Ocp-Apim-Subscription-Key` nagłówka wymagane jest tylko podanie klucza subskrypcji. Na przykład:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

W przypadku korzystania z `Authorization: Bearer` nagłówka wymagane jest żądanie żądania do `issueToken` punktu końcowego. W ramach tego żądania użytkownik wymienia swój klucz subskrypcji dla tokenu dostępu, który jest ważny przez 10 minut. W kilku następnych sekcjach dowiesz się, jak uzyskać token i użyć tokenu.

### <a name="how-to-get-an-access-token"></a>Jak uzyskać token dostępu

Aby uzyskać token dostępu, musisz wykonać żądanie do `issueToken` punktu końcowego przy użyciu `Ocp-Apim-Subscription-Key` klucza subskrypcji i.

`issueToken`Punkt końcowy ma następujący format:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Zamień na `<REGION_IDENTIFIER>` Identyfikator pasujący do regionu subskrypcji z tej tabeli:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Te przykłady umożliwiają utworzenie żądania tokenu dostępu.

#### <a name="http-sample"></a>Przykład HTTP

Ten przykład to proste żądanie HTTP do pobrania tokenu. Zamień `YOUR_SUBSCRIPTION_KEY` na klucz subskrypcji usługi rozpoznawania mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, Zastąp `Host` nagłówek nazwą hosta regionu.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi zawiera token dostępu w formacie tokenu sieci Web JSON (JWT).

#### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

Ten przykład to prosty skrypt programu PowerShell umożliwiający uzyskanie tokenu dostępu. Zamień `YOUR_SUBSCRIPTION_KEY` na klucz subskrypcji usługi rozpoznawania mowy. Upewnij się, że jest używany prawidłowy punkt końcowy dla regionu zgodnego z subskrypcją. Ten przykład jest obecnie ustawiony na zachodnie stany USA.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>zazwinięcie próbki

Zwinięcie to narzędzie wiersza polecenia dostępne w systemie Linux (i w podsystemie Windows dla systemu Linux). W tym zaleceniu pokazano, jak uzyskać token dostępu. Zamień `YOUR_SUBSCRIPTION_KEY` na klucz subskrypcji usługi rozpoznawania mowy. Upewnij się, że jest używany prawidłowy punkt końcowy dla regionu zgodnego z subskrypcją. Ten przykład jest obecnie ustawiony na zachodnie stany USA.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Przykład w języku C#

Ta klasa języka C# ilustruje sposób uzyskiwania tokenu dostępu. Przekaż swój klucz subskrypcji usługi rozpoznawania mowy podczas tworzenia wystąpienia klasy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, Zmień wartość tak, `FetchTokenUri` aby była zgodna z regionem subskrypcji.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Przykład języka Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Jak używać tokenu dostępu

Token dostępu powinien zostać wysłany do usługi jako `Authorization: Bearer <TOKEN>` nagłówek. Każdy token dostępu jest ważny przez 10 minut. W dowolnym momencie możesz uzyskać nowy token, jednak aby zminimalizować obciążenie sieci i opóźnienia, zalecamy użycie tego samego tokenu przez dziewięć minut.

Oto przykładowe żądanie HTTP do interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
