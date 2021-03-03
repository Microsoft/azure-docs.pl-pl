---
title: 'Szybki Start: wywoływanie Microsoft Graph z aplikacji klasycznej Node.js | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, w jaki sposób aplikacja klasyczna Node.js może zalogować użytkowników i uzyskać token dostępu w celu wywołania interfejsu API chronionego przez punkt końcowy platformy tożsamości firmy Microsoft
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653273"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Szybki Start: uzyskiwanie tokenu dostępu i wywoływanie interfejsu API Microsoft Graph z aplikacji dla komputerów stacjonarnych elektronów

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak aplikacja stacjonarna elektronów może zalogować użytkowników i uzyskać tokeny dostępu w celu wywołania interfejsu API Microsoft Graph.

Ten przewodnik Szybki Start używa [biblioteki uwierzytelniania firmy Microsoft dla Node.js (węzeł MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) z [przepływem kodu autoryzacji z PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Rejestrowanie i pobieranie przykładowej aplikacji
>
> Postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć pracę.
>
> #### <a name="step-1-register-the-application"></a>Krok 1. rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `msal-node-desktop` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wybierz pozycję **Dodaj**  >  **aplikacje mobilne i klasyczne**.
> 1. W sekcji **identyfikatory URI przekierowania** wprowadź wartość `msal://redirect` .
> 1. Wybierz pozycję **Konfiguruj**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w Azure Portal
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać adres URL odpowiedzi jako **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-windows-desktop/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-electron-sample-project"></a>Krok 2. Pobieranie przykładowego projektu elektronów

> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Krok 3. Konfigurowanie przykładowego projektu elektronu
>
> 1. Wyodrębnij plik zip do folderu lokalnego w pobliżu katalogu głównego dysku, na przykład *C:/Azure-Samples*.
> 1. Edytuj *. env* i Zastąp wartości pól `TENANT_ID` i `CLIENT_ID` następującym fragmentem kodu:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Gdzie:
>    - `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
>    - `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)**, przejdź do strony **Przegląd** aplikacji w witrynie Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

Należy zainstalować zależności tego przykładu raz:

```console
npm install
```

Następnie uruchom aplikację za pomocą wiersza polecenia lub konsoli programu:

```console
npm start
```

Powinieneś zobaczyć interfejs użytkownika aplikacji przy użyciu przycisku **Zaloguj** .

## <a name="about-the-code"></a>Informacje o kodzie

Poniżej omówiono niektóre ważne aspekty przykładowej aplikacji.

### <a name="msal-node"></a>Węzeł MSAL

[Węzeł MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Aby uzyskać więcej informacji na temat używania węzła MSAL z aplikacjami klasycznymi, zobacz [ten artykuł](scenario-desktop-overview.md).

Można zainstalować węzeł MSAL, uruchamiając następujące polecenie npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Można dodać odwołanie do węzła MSAL, dodając następujący kod:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Gdzie: |Opis |
> |---------|---------|
> | `clientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |
> | `authority`    | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. W chmurze publicznej jest to zwykle `https://login.microsoftonline.com/{tenant}`, gdzie {tenant} jest nazwą dzierżawy lub identyfikatorem dzierżawy.|

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

W pierwszym etapie przepływu kodu autoryzacji za pomocą PKCE Przygotuj i Wyślij żądanie kodu autoryzacji z odpowiednimi parametrami. Następnie w drugim etapie przepływu nasłuchuje odpowiedzi na kod autoryzacji. Po uzyskaniu kodu należy wymienić go, aby uzyskać token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Gdzie:| Opis |
> |---------|---------|
> | `authWindow` | Bieżące okno elektronów w procesie. |
> | `tokenRequest` | Zawiera żądane zakresy, takie jak `"User.Read"` dla Microsoft Graph lub `"api://<Application ID>/access_as_user"` niestandardowych interfejsów API sieci Web. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia aplikacji klasycznych dla komputerów stacjonarnych za pomocą węzła MSAL, zobacz Samouczek:

> [!div class="nextstepaction"]
> [Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph w aplikacji klasycznej](tutorial-v2-nodejs-desktop.md)