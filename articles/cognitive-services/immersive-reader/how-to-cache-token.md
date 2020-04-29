---
title: Buforowanie tokenu uwierzytelniania
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób buforowania tokenu uwierzytelniania.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946167"
---
# <a name="how-to-cache-the-authentication-token"></a>Jak buforować token uwierzytelniania

W tym artykule pokazano, jak buforować token uwierzytelniania w celu poprawy wydajności aplikacji.

## <a name="using-aspnet"></a>Korzystanie z ASP.NET

Zaimportuj pakiet NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** , który jest używany do uzyskiwania tokenu. Następnie użyj poniższego kodu `AuthenticationResult`, aby uzyskać, korzystając z wartości uwierzytelniania uzyskanych podczas [tworzenia zasobu czytnika immersyjny](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult` Obiekt ma `AccessToken` właściwość, która jest rzeczywistym tokenem, który będzie używany podczas uruchamiania czytnika immersyjny przy użyciu zestawu SDK. Ma również `ExpiresOn` właściwość, która oznacza, że token wygaśnie. Przed uruchomieniem czytnika immersyjny można sprawdzić, czy token wygasł i uzyskać nowy token tylko wtedy, gdy jego ważność wygasła.

## <a name="using-nodejs"></a>Przy użyciu środowiska Node. JS

Dodaj pakiet [**żądania**](https://www.npmjs.com/package/request) npm do projektu. Użyj poniższego kodu, aby uzyskać token, używając wartości uwierzytelniania uzyskanych podczas [tworzenia zasobu czytnika immersyjny](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on` Właściwość to data i godzina wygaśnięcia tokenu wyrażona jako liczba sekund od 1 stycznia 1970 czasu UTC. Użyj tej wartości, aby określić, czy token wygasł przed podjęciem próby uzyskania nowego.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)