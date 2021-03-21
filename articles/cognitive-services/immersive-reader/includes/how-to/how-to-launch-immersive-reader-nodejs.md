---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620194"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](../../how-to-create-immersive-reader.md) , aby rozpocząć konfigurację.  Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [Node.js](https://nodejs.org/) i [przędza](https://yarnpkg.com)
* IDE, takie jak [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Tworzenie aplikacji sieci Web Node.js za pomocą programu Express

Utwórz Node.js aplikację sieci Web za pomocą `express-generator` Narzędzia.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Zainstaluj zależności przędzy i Dodaj zależności `request` i `dotenv` , które będą używane w dalszej części samouczka.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Następnie napisz interfejs API zaplecza, aby pobrać token uwierzytelniania usługi Azure AD.

Wymagane są pewne wartości z kroku wymagań wstępnych konfiguracji uwierzytelniania usługi Azure AD powyżej dla tej części. Odwołaj się do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Po uzyskaniu tych wartości Utwórz nowy plik o nazwie _ENV_ i wklej do niego następujący kod, dostarczając wartości właściwości niestandardowych z powyższych. Nie należy zawierać cudzysłowów ani znaków "{" i "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Nie należy zatwierdzić tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne.

Następnie otwórz _app.js_ i Dodaj następujący na początku pliku. Spowoduje to załadowanie właściwości zdefiniowanych w pliku ENV jako zmiennych środowiskowych do węzła.

```javascript
require('dotenv').config();
```

Otwórz plik _routes\index.js_ i Zastąp jego zawartość następującym kodem.

Ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token uwierzytelniania usługi Azure AD przy użyciu hasła nazwy głównej usługi. Pobiera również poddomenę. Następnie zwraca obiekt zawierający token i poddomenę.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
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
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Punkt końcowy interfejsu API **getimmersivereaderlaunchparams** powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego samouczka.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

1. Otwórz _views\layout.Pug_ i Dodaj następujący kod pod `head` tagiem przed `body` tagiem. `script`Tagi te ładują [zestaw SDK programu immersyjny](https://github.com/microsoft/immersive-reader-sdk) i jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otwórz _views\index.Pug_ i Zastąp jego zawartość następującym kodem. Ten kod wypełnia stronę z niepewną przykładową zawartością i dodaje przycisk, który uruchamia czytnik immersyjny.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Nasza aplikacja sieci Web jest teraz gotowa. Uruchom aplikację, uruchamiając:

    ```bash
    npm start
    ```

4. Otwórz przeglądarkę i przejdź do _http://localhost:3000_ . Na stronie powinna zostać wyświetlona powyższa zawartość. Kliknij przycisk **czytnika immersyjny** , aby uruchomić czytnik immersyjny z zawartością.

## <a name="specify-the-language-of-your-content"></a>Określ język zawartości

Czytnik immersyjny obsługuje wiele różnych języków. Język zawartości można określić, wykonując poniższe kroki.

1. Otwórz _views\index.Pug_ i Dodaj następujący kod poniżej `p(id=content)` tagu, który został dodany w poprzednim kroku. Ten kod dodaje zawartość Hiszpańska zawartości do strony.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. W kodzie JavaScript Dodaj następujące polecenie powyżej wywołania do `ImmersiveReader.launchAsync` . Ten kod przekazuje zawartość Hiszpańska do czytnika immersyjny.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Przejdź do _http://localhost:3000_ ponownie. Na stronie powinien zostać wyświetlony tekst hiszpański, a po kliknięciu **czytnika immersyjny** zostanie on wyświetlony również w czytniku immersyjny.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Określ język interfejsu czytnika immersyjny

Domyślnie język interfejsu czytnika immersyjny jest zgodny z ustawieniami języka przeglądarki. Można również określić język interfejsu czytnika immersyjny z poniższym kodem.

1. W _views\index.Pug_ Zastąp wywołanie `ImmersiveReader.launchAsync(token, subdomain, content)` poniższym kodem.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Przejdź do _http://localhost:3000_ . Po uruchomieniu czytnika immersyjny interfejs zostanie wyświetlony w języku francuskim.

## <a name="launch-the-immersive-reader-with-math-content"></a>Uruchamianie czytnika immersyjny z zawartością matematyczną

Możesz dołączyć zawartość matematyczną w czytniku immersyjny przy użyciu [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Zmodyfikuj _views\index.Pug_ w taki sposób, aby zawierał następujący kod powyżej wywołania `ImmersiveReader.launchAsync` :

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Przejdź do _http://localhost:3000_ . Gdy uruchamiasz czytnik immersyjny i przewijasz do dołu, zobaczysz formułę matematyczną.

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../../reference.md)
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
