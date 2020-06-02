---
title: Czytnik immersyjny — Biblioteka kliencka Node. js — Szybki Start
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i dodasz funkcję interfejsu API czytnika immersyjny.
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.openlocfilehash: 2092ccbedd95ee13ac4ad929f052afd1d85729ec
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268721"
---
[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności.

W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i zintegrujesz czytnik immersyjny przy użyciu biblioteki klienta czytnika immersyjny. Pełny przykład pracy tego przewodnika Szybki Start jest dostępny [tutaj](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](../../how-to-create-immersive-reader.md) , aby rozpocząć konfigurację. Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [Node. js](https://nodejs.org/) i [przędza](https://yarnpkg.com)
* IDE, takie jak [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Tworzenie aplikacji sieci Web w języku Node. js za pomocą języka Express

Utwórz aplikację sieci Web Node. js za pomocą `express-generator` Narzędzia.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Zainstaluj zależności przędzy i Dodaj zależności `request` oraz `dotenv` , które będą używane w dalszej części przewodnika Szybki Start.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania

### <a name="configure-authentication-values"></a>Konfigurowanie wartości uwierzytelniania

Utwórz nowy plik o nazwie _ENV_ w folderze głównym projektu. Wklej do niego następujący kod, dostarczając wartości podanych podczas tworzenia zasobu czytnika immersyjny.
Nie należy zawierać cudzysłowów ani znaków "{" i "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Nie należy zatwierdzić tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne.

Następnie otwórz plik _App. js_ i Dodaj następujący na początku pliku. Spowoduje to załadowanie właściwości zdefiniowanych w pliku ENV jako zmiennych środowiskowych do węzła.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Aktualizowanie routera w celu uzyskania tokenu
Otwórz plik _routes\index.js_ i Zastąp automatycznie wygenerowany kod następującym kodem.

Ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token uwierzytelniania usługi Azure AD przy użyciu hasła nazwy głównej usługi. Pobiera również poddomenę. Następnie zwraca obiekt zawierający token i poddomenę.

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
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Punkt końcowy interfejsu API **GetTokenAndSubdomain** powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego przewodnika Szybki Start.

## <a name="add-sample-content"></a>Dodaj przykładową zawartość

Teraz dodamy przykładową zawartość do tej aplikacji sieci Web. Otwórz _views\index.Pug_ i Zastąp automatycznie wygenerowany kod tym przykładem:

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
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

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
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
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

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
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


Zwróć uwagę, że cały tekst ma atrybut **lang** , który opisuje Języki tekstu. Ten atrybut pomaga czytnikom immersyjny zapewnić odpowiednie funkcje języka i gramatyki.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Nasza aplikacja sieci Web jest teraz gotowa. Uruchom aplikację, uruchamiając:

```bash
npm start
```

Otwórz przeglądarkę i przejdź do _http://localhost:3000_ . Powinien zostać wyświetlony następujący ekran:

![Przykładowa aplikacja](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Uruchom czytnik immersyjny

Po kliknięciu przycisku "czytnik immersyjny" zobaczysz czytnik immersyjny uruchamiany z zawartością na stronie.

![Czytnik immersyjny](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../../reference.md)