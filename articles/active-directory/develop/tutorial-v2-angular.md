---
title: 'Samouczek: Tworzenie aplikacji kątowej korzystającej z platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację jednostronicową (SPA), która używa platformy tożsamości firmy Microsoft do logowania użytkowników i uzyskiwania tokenu dostępu w celu wywołania interfejsu API Microsoft Graph w ich imieniu.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756152"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph ze kątowej aplikacji jednostronicowej

W tym samouczku utworzysz aplikację jednostronicową (SPA), która loguje się do użytkowników i wywołuje interfejs API Microsoft Graph.

W tym samouczku:

> [!div class="checklist"]
> * Utwórz projekt kątowy za pomocą `npm`
> * Zarejestruj aplikację w Azure Portal
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Dodawanie kodu do wywołania interfejsu API Microsoft Graph
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/) na potrzeby uruchamiania lokalnego serwera sieci Web.
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor do modyfikowania plików projektu.

## <a name="how-the-sample-app-works"></a>Jak działa Przykładowa aplikacja

![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej w tym samouczku](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

Przykładowa aplikacja utworzona w tym samouczku umożliwia pojedynczemu SPAu Wysyłanie zapytań do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny wystawione przez platformę tożsamości firmy Microsoft. Używa biblioteki Microsoft Authentication Library (MSAL) dla kątowego i otoki podstawowej biblioteki MSAL.js. MSAL kątowy umożliwia stosowanie skośnie 6 + aplikacji w celu uwierzytelniania użytkowników w przedsiębiorstwach za pomocą Azure Active Directory (Azure AD), a także użytkowników z kontami Microsoft i tożsamościami społecznościowymi, takimi jak Facebook, Google i LinkedIn. Biblioteka umożliwia również aplikacjom uzyskiwanie dostępu do usług w chmurze firmy Microsoft i Microsoft Graph.

W tym scenariuszu po zalogowaniu się użytkownika token dostępu zostanie zaproszony i dodany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Pozyskiwanie i odnawianie tokenów jest obsługiwane przez MSAL.

### <a name="libraries"></a>Biblioteki

W tym samouczku jest stosowana następująca Biblioteka:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka uwierzytelniania firmy Microsoft dla otoki języka JavaScript|

Kod źródłowy biblioteki MSAL.js można znaleźć w repozytorium [AzureAD/Microsoft-Authentication-Library-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) w witrynie GitHub.

## <a name="create-your-project"></a>Tworzenie projektu

Generuj nową aplikację kątową przy użyciu następujących poleceń npm:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Postępuj zgodnie z [instrukcjami, aby zarejestrować aplikację jednostronicową](./scenario-spa-app-registration.md) w Azure Portal.

Na stronie **Przegląd** aplikacji w swojej rejestracji Zanotuj wartość **identyfikatora aplikacji (klienta)** na potrzeby późniejszego użycia.

Zarejestruj wartość **identyfikatora URI przekierowania** jako **http://localhost:4200/** i Włącz ustawienia niejawnego przydzielenia.

## <a name="configure-the-application"></a>Konfigurowanie aplikacji

1. W folderze *src/App* Edytuj *aplikację App. module. TS* i Dodaj do niej, a także `MSALModule` `imports` `isIE` stałą:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Zamień te wartości:

    |Nazwa wartości|Informacje|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na stronie **Przegląd** rejestracji aplikacji jest to wartość **identyfikatora aplikacji (klienta)** . |
    |Enter_the_Cloud_Instance_Id_Here|Jest to wystąpienie chmury platformy Azure. W głównej lub globalnej chmurze platformy Azure wprowadź wartość **https://login.microsoftonline.com** . W przypadku chmur narodowych (na przykład Chin), zobacz [chmury narodowe](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Ustaw jedną z następujących opcji: Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zastąp tę wartość identyfikatorem katalogu (dzierżawy) lub nazwą dzierżawy (na przykład **contoso.Microsoft.com**). Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**. Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**. |
    |Enter_the_Redirect_Uri_Here|Zamień na **http://localhost:4200** .|

    Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md).

2. W górnej części tego samego pliku Dodaj następującą instrukcję import:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Dodaj następujące instrukcje importu na początku `src/app/app.component.ts` :

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Zaloguj użytkownika

Dodaj następujący kod, aby `AppComponent` zalogować użytkownika:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Zalecamy używanie `loginRedirect` dla użytkowników programu Internet Explorer.

## <a name="acquire-a-token"></a>Uzyskiwanie tokenu

### <a name="angular-interceptor"></a>Interceptor kątowy

MSAL kątowy udostępnia `Interceptor` klasę, która automatycznie uzyskuje tokeny dla żądań wychodzących, które korzystają z klienta kątowego `http` do znanych chronionych zasobów.

Najpierw należy dołączyć `Interceptor` klasę jako dostawcę do aplikacji:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Następnie podaj mapę chronionych zasobów `MsalModule.forRoot()` jako `protectedResourceMap` i Dołącz te zakresy do programu `consentScopes` . Adresy URL, które podano w `protectedResourceMap` kolekcji, są rozróżniane wielkości liter.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Na koniec Pobierz profil użytkownika przy użyciu żądania HTTP:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL używa trzech metod do uzyskiwania tokenów: `acquireTokenRedirect` , `acquireTokenPopup` , i `acquireTokenSilent` . Zaleca się jednak używanie `MsalInterceptor` klasy zamiast aplikacji kątowych, jak pokazano w poprzedniej sekcji.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`acquireTokenSilent`Metoda obsługuje pozyskiwanie i odnawianie tokenów bez interakcji z użytkownikiem. Gdy `loginRedirect` lub `loginPopup` Metoda jest wykonywana po raz pierwszy, `acquireTokenSilent` jest często używana do uzyskiwania tokenów używanych do uzyskiwania dostępu do chronionych zasobów w późniejszych wywołaniach. Wywołania żądania lub odnowienia tokenów są wykonywane dyskretnie.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

W tym kodzie `scopes` zawiera zakresy wymagane do zwrócenia w tokenie dostępu dla interfejsu API.

Na przykład:

* `["user.read"]` dla Microsoft Graph
* `["<Application ID URL>/scope"]` w przypadku niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Czasami potrzebujesz, aby użytkownik mógł korzystać z platformy tożsamości firmy Microsoft. Na przykład:

* Może być konieczne ponowne wprowadzenie poświadczeń przez użytkowników, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, do których użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Zalecany wzorzec dla większości aplikacji jest najpierw wywoływany `acquireTokenSilent` , a następnie przechwytywany jest wyjątek, a następnie wywoływanie `acquireTokenPopup` (lub `acquireTokenRedirect` ) w celu uruchomienia interakcyjnego żądania.

Wywoływanie `acquireTokenPopup` wyników w oknie podręcznym logowania. Alternatywnie `acquireTokenRedirect` przekierowuje użytkowników do platformy tożsamości firmy Microsoft. W tym oknie Użytkownicy muszą potwierdzić swoje poświadczenia, wyrazić zgodę na wymagane zasoby lub ukończyć uwierzytelnianie dwuskładnikowe.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Ten przewodnik Szybki Start `loginRedirect` używa `acquireTokenRedirect` metod i programu Microsoft Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą wyskakujących okienek przez Internet Explorer.

## <a name="log-out"></a>Wyloguj

Dodaj następujący kod, aby wylogować użytkownika:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika
Aby zapoznać się z przykładem dodawania interfejsu użytkownika przy użyciu biblioteki składników kątowych, zobacz [przykładową aplikację](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Testowanie kodu

1.  Uruchom serwer sieci Web w celu nasłuchiwania na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

    ```bash
    npm install
    npm start
    ```
1. W przeglądarce wpisz **http://localhost:4200** lub **http://localhost:{port}** , gdzie *port* to port, na którym nasłuchuje serwer sieci Web.


### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Po pierwszym zalogowaniu się do aplikacji zostanie wyświetlony monit o udzielenie dostępu do Twojego profilu i umożliwienie mu zalogowania się użytkownika:

![Okno "wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Dodawanie zakresów i delegowanych uprawnień

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga zakresu *Calendars. Read* , aby wyświetlić kalendarze użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj *kalendarze. Odczytaj* delegowane uprawnienia do informacji rejestracyjnych aplikacji. Następnie Dodaj do wywołania zakres *Calendars. Read* . `acquireTokenSilent`

>[!NOTE]
>Użytkownik może zostać poproszony o dodatkowe przesłanie w miarę zwiększania liczby zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), można użyć *clientId* jako zakresu w wywołaniach w celu uzyskania tokenów.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Zajrzyj do aplikacji jednostronicowej (SPA) na platformie tożsamości firmy Microsoft w naszej serii artykułów wieloczęściowych.

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja jednostronicowa](scenario-spa-overview.md)
