---
title: 'Szybki Start: wywoływanie Microsoft Graph z aplikacji konsolowej Node.js | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który pokazuje, jak Aplikacja konsolowa Node.js może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft przy użyciu własnej tożsamości aplikacji
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653749"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji konsolowej Node.js przy użyciu tożsamości aplikacji

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak Aplikacja konsolowa Node.js może uzyskać token dostępu przy użyciu tożsamości aplikacji w celu wywołania interfejsu API Microsoft Graph i wyświetlenia [listy użytkowników](/graph/api/user-list) w katalogu. Przykład kodu demonstruje, jak zadanie nienadzorowane lub usługa systemu Windows mogą być uruchamiane przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika.

Ten przewodnik Szybki Start używa [biblioteki uwierzytelniania firmy Microsoft dla Node.js (węzeł MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) z [przydzieleniem poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Rejestrowanie i pobieranie przykładowej aplikacji
>
> Postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć pracę.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Krok 1. rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `msal-node-cli` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**.
> 1. W obszarze wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**, wprowadź nazwę, a następnie wybierz pozycję **Dodaj**. Zapisz wartość klucza tajnego w bezpiecznej lokalizacji do użycia w późniejszym kroku.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. Wybierz **Microsoft Graph**.
> 1. Wybierz pozycję **Uprawnienia aplikacji**.
> 1. W obszarze węzeł **użytkownika** wybierz pozycję **użytkownik. odczyt. wszystkie**, a następnie wybierz pozycję **Dodaj uprawnienia**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Pobieranie i Konfigurowanie przykładowej aplikacji
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w Azure Portal
> Aby działał przykładowy kod z tego przewodnika Szybki start, musisz utworzyć klucz tajny klienta i dodać uprawnienie aplikacji **User.Read.All** interfejsu API programu Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-netcore-daemon/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Krok 2. Pobieranie przykładowego projektu Node.js

> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Krok 3. Konfigurowanie przykładowego projektu Node.js
>
> 1. Wyodrębnij plik zip do folderu lokalnego w pobliżu katalogu głównego dysku, na przykład *C:/Azure-Samples*.
> 1. Edytuj *. env* i Zastąp wartości pól `TENANT_ID` , `CLIENT_ID` i `CLIENT_SECRET` następującym fragmentem kodu:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Gdzie:
>    - `Enter_the_Application_Id_Here` -jest **identyfikatorem aplikacji (klienta)** , która została zarejestrowana wcześniej. Znajdź ten identyfikator w okienku **Przegląd** rejestracji aplikacji w Azure Portal.
>    - `Enter_the_Tenant_Id_Here` -Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy** (na przykład contoso.Microsoft.com).  Znajdź te wartości w okienku **Przegląd** rejestracji aplikacji w Azure Portal.
>    - `Enter_the_Client_Secret_Here` -Zamień tę wartość na utworzony wcześniej klucz tajny klienta. Aby wygenerować nowy klucz, użyj **certyfikatów & wpisów tajnych** w ustawieniach rejestracji aplikacji w Azure Portal.
>
> > [!WARNING]
> > Każdy tajny tekst w kodzie źródłowym stanowi zwiększone ryzyko dla bezpieczeństwa. W tym artykule użyto tylko elementu tajnego w postaci zwykłego tekstu dla uproszczenia. Użyj [poświadczeń certyfikatu](active-directory-certificate-credentials.md) zamiast wpisów tajnych klienta w poufnych aplikacjach klienckich, zwłaszcza tych, które zamierzasz wdrożyć w środowisku produkcyjnym.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3. zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, otrzymasz komunikat o błędzie *HTTP 403 — Dostęp zabroniony* : `Insufficient privileges to complete the operation` . Ten błąd występuje, ponieważ każde *uprawnienie tylko do aplikacji* wymaga **zgody administratora**: Administrator globalny katalogu musi wyrazić zgodę na swoją aplikację. Wybierz jedną z poniższych opcji, w zależności od roli:

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, przejdź do strony **uprawnienia interfejsu API** w Azure Portal rejestracji aplikacji i wybierz pozycję **Udziel zgody administratora dla {Nazwa dzierżawy}** (gdzie {nazwa dzierżawy} to nazwa katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony **Uprawnienia interfejsu API** i wybierz pozycję **Wyraź zgodę administratora dla katalogu wpisz_tutaj_nazwę_dzierżawy**
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś użytkownikiem standardowym Twojej dzierżawy, musisz poprosił administratora globalnego o przyznanie **zgody administratora** dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Gdzie:
>> * `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Zlokalizuj folder główny przykładu (gdzie `package.json` znajduje się) w wierszu polecenia lub konsoli programu. Należy zainstalować zależności tego przykładu raz:

```console
npm install
```

Następnie uruchom aplikację za pomocą wiersza polecenia lub konsoli programu:

```console
node . --op getUsers
```

Powinno zostać wyświetlone dane wyjściowe konsoli część fragmentu kodu JSON reprezentująca listę użytkowników w katalogu usługi Azure AD.

## <a name="about-the-code"></a>Informacje o kodzie

Poniżej omówiono niektóre ważne aspekty przykładowej aplikacji.

### <a name="msal-node"></a>Węzeł MSAL

[Węzeł MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Zgodnie z opisem, ten przewodnik Szybki Start żąda tokenów według uprawnień aplikacji (przy użyciu tożsamości własnej aplikacji) zamiast uprawnień delegowanych. Przepływ uwierzytelniania używany w tym przypadku jest znany jako [przepływ poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md). Aby uzyskać więcej informacji na temat korzystania z węzła MSAL z aplikacjami demonów, zobacz [Scenariusz: aplikacja demona](scenario-daemon-overview.md).

 Można zainstalować węzeł MSAL, uruchamiając następujące polecenie npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```javascript
const msal = require('@azure/msal-node');
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Gdzie: |Opis |
> |---------|---------|
> | `clientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |
> | `authority`    | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. W chmurze publicznej jest to zwykle `https://login.microsoftonline.com/{tenant}`, gdzie {tenant} jest nazwą dzierżawy lub identyfikatorem dzierżawy.|
> | `clientSecret` | Klucz tajny klienta utworzony dla aplikacji w witrynie Azure Portal. |

Więcej informacji można znaleźć w [dokumentacji dotyczącej metody `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, należy użyć metody `acquireTokenByClientCredential`:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Gdzie:| Opis |
> |---------|---------|
> | `tokenRequest` | Zawiera żądane zakresy. W przypadku klientów poufnych format powinien być podobny do `{Application ID URI}/.default`, aby wskazać, że żądane zakresy są zdefiniowane statycznie w obiekcie aplikacji ustawionym w witrynie Azure Portal (w przypadku programu Microsoft Graph element `{Application ID URI}` wskazuje na adres `https://graph.microsoft.com`). W przypadku niestandardowych interfejsów API sieci Web `{Application ID URI}` jest zdefiniowany w sekcji **UWIDACZNIANIE interfejsu API** w rejestracji aplikacji w witrynie Azure Portal. |
> | `tokenResponse` | Odpowiedź zawiera token dostępu dla żądanych zakresów. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia aplikacji demona/Aplikacja konsolowa przy użyciu węzła MSAL, zobacz Samouczek:

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje interfejsy API sieci Web](tutorial-v2-nodejs-console.md)