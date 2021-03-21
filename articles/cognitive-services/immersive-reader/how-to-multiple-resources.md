---
title: Integrowanie wielu zasobów czytników immersyjny
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację Node.js, która uruchamia czytnik immersyjny przy użyciu wielu zasobów czytnika.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620170"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Integrowanie wielu zasobów czytników immersyjny

W [przeglądzie](./overview.md)zawarto informacje na temat tego, co to jest czytnik immersyjny i w jaki sposób implementuje sprawdzone techniki w celu zwiększenia czytelności dla osób uczące się, nowych czytelników i studentów z różnicami w nauce. W samouczku [Szybki Start](./quickstarts/client-libraries.md)pokazano, jak używać czytnika immersyjny z pojedynczym zasobem. W tym samouczku opisano sposób integrowania wielu zasobów czytnika immersyjny w tej samej aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wielu zasobów czytnika immersyjny w istniejącej grupie zasobów
> * Uruchamianie czytnika immersyjny przy użyciu wielu zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

* Postępuj zgodnie z [przewodnikiem Szybki Start](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) , aby utworzyć aplikację sieci Web, która uruchamia czytnik immersyjny z NodeJS. Ten przewodnik Szybki Start umożliwia skonfigurowanie pojedynczego zasobu czytnika immersyjny. Na tym samouczku utworzymy wszystko.

## <a name="create-the-immersive-reader-resources"></a>Tworzenie zasobów czytnika immersyjny

Postępuj zgodnie z [tymi instrukcjami](./how-to-create-immersive-reader.md) , aby utworzyć każdy zasób czytnika immersyjny. Skrypt **Create-ImmersiveReaderResource** ma `ResourceName` Parametry, `ResourceSubdomain` i `ResourceLocation` . Te powinny być unikatowe dla każdego tworzonego zasobu. Pozostałe parametry powinny być takie same jak użyte podczas konfigurowania pierwszego zasobu czytnika immersyjny. W ten sposób każdy zasób może być połączony z tą samą grupą zasobów platformy Azure i aplikacją usługi Azure AD.

W poniższym przykładzie pokazano, jak utworzyć dwa zasoby, jeden w zachodnich i drugi na wschodnim Wschodzie. Zwróć uwagę na unikatowe wartości parametrów `ResourceName` , `ResourceSubdomain` i `ResourceLocation` .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Dodaj zasoby do konfiguracji środowiska

W samouczku szybki start utworzono plik konfiguracji środowiska zawierający `TenantId` `ClientId` Parametry,, `ClientSecret` i `Subdomain` . Ponieważ wszystkie zasoby korzystają z tej samej aplikacji usługi Azure AD, można użyć tych samych wartości dla `TenantId` , `ClientId` i `ClientSecret` . Jedyną zmianą, którą należy wykonać, jest wyświetlenie listy każdej poddomeny dla każdego zasobu.

Nowy plik __ENV__ powinien teraz wyglądać podobnie do poniższego:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Nie należy zatwierdzić tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne.

Następnie zmodyfikujemy plik  _routes\index.js_ , który został utworzony w celu obsługi naszych wielu zasobów. Zastąp jego zawartość następującym kodem.

Jak wcześniej, ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token uwierzytelniania usługi Azure AD przy użyciu hasła nazwy głównej usługi. Tym razem umożliwia użytkownikowi określenie lokalizacji zasobu i przekazanie jej jako parametru zapytania. Następnie zwraca obiekt zawierający token i odpowiednią poddomenę.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Punkt końcowy interfejsu API **getimmersivereaderlaunchparams** powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego samouczka.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

1. Otwórz _views\index.Pug_ i Zastąp jego zawartość następującym kodem. Ten kod wypełnia stronę z niepewną przykładową zawartością i dodaje dwa przyciski, które uruchamiają czytnik immersyjny. Jeden do uruchamiania czytnika immersyjny dla zasobu wschodniego i drugiego dla zasobu Zachodnie.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }

        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    ```

3. Nasza aplikacja sieci Web jest teraz gotowa. Uruchom aplikację, uruchamiając:

    ```bash
    npm start
    ```

4. Otwórz przeglądarkę i przejdź do `http://localhost:3000` . Na stronie powinna zostać wyświetlona powyższa zawartość. Kliknij przycisk czytnika w trybie **immersyjny** **lub, aby uruchomić czytnik immersyjny** przy użyciu odpowiednich zasobów.

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)