---
title: Samouczek aplikacji jednostronicowej angular — Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak aplikacje Angular SPA mogą wywoływać interfejs API, który wymaga tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 84d7be64e7157c35639fec19cb28fe787fe29b19
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533891"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Samouczek: Zaloguj się do użytkowników i zadzwoń do interfejsu API programu Microsoft Graph z aplikacji jednostronicowej angular

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre aspekty tej funkcji mogą ulec zmianie przed ogólną dostępnością (GA).

W tym samouczku pokazano, jak kątowa aplikacja jednostronicowa (SPA) może:
- Zaloguj się na konta osobiste, konta służbowe lub konta szkolne.
- Uzyskaj token dostępu.
- Wywołanie interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają tokenów dostępu z *punktu końcowego platformy tożsamości firmy Microsoft*.

>[!NOTE]
>W tym samouczku opisano sposób tworzenia nowego angular SPA przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Jeśli chcesz pobrać przykładową aplikację, zobacz [przewodnik Szybki start](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Jak działa przykładowa aplikacja

![Diagram przedstawiający, jak działa przykładowa aplikacja generowana w tym samouczku](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona w tym samouczku umożliwia angular SPA do kwerendy microsoft Graph API lub interfejsu API sieci web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. Biblioteka MSAL dla kątów jest otoką podstawowej biblioteki MSAL.js. Umożliwia aplikacjom Angular (6+) uwierzytelnienie użytkowników korporacyjnych przy użyciu usługi Microsoft Azure Active Directory, użytkowników kont Microsoft i użytkowników tożsamości społecznościowych (takich jak Facebook, Google i LinkedIn). Biblioteka umożliwia również aplikacjom uzyskanie dostępu do usług w chmurze firmy Microsoft lub programu Microsoft Graph.

W tym scenariuszu po zalogowaniu się użytkownika token dostępu jest wymagany i dodawany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Pozyskiwanie i odnawianie tokenów są obsługiwane przez msal.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Biblioteki

W tym samouczku użyto następującej biblioteki:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka uwierzytelniania firmy Microsoft dla otoki kątowej javascript|

> [!NOTE]
> *Msal.js* jest przeznaczony dla punktu końcowego platformy tożsamości firmy Microsoft, który umożliwia konta osobiste, konta służbowe i konta szkolne logowanie się i nabywanie tokenów. Punkt końcowy platformy tożsamości firmy Microsoft ma [pewne ograniczenia.](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)
> Aby zrozumieć różnice między punktami końcowymi w wersji 1.0 i v2.0, zobacz [przewodnik porównawczy punktów końcowych](../azuread-dev/azure-ad-endpoint-comparison.md).

Kod źródłowy biblioteki MSAL.js można znaleźć w repozytorium [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) w usłudze GitHub.

<!--end-collapse-->


## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten samouczek, potrzebujesz:

* Lokalny serwer sieci Web, taki jak [Node.js](https://nodejs.org/en/download/). Instrukcje w tym samouczku są oparte na node.js.
* Zintegrowane środowisko programistyczne (IDE), takie jak [Visual Studio Code](https://code.visualstudio.com/download), do edycji plików projektu.

## <a name="create-your-project"></a>Tworzenie projektu

Generowanie nowej aplikacji kątowej przy użyciu następujących poleceń npm:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Postępuj zgodnie z [instrukcjami, aby zarejestrować aplikację jednostronicową](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) w witrynie Azure portal.

Na stronie **Przegląd** aplikacji rejestracji zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.

Zarejestruj wartość **URI przekierowania** jako **http://localhost:4200/** i włącz niejawne ustawienia dotacji.

## <a name="configure-the-application"></a>Konfigurowanie aplikacji

1. W folderze *src/app* edytuj *plik app.module.ts* i dodaj `MSALModule` do `imports` stałej: `isIE`

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

    Zastąp następujące wartości:

    |Nazwa wartości|Informacje|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na **stronie Przegląd** rejestracji aplikacji jest to wartość **identyfikatora aplikacji (klienta).** |
    |Enter_the_Cloud_Instance_Id_Here|Jest to wystąpienie chmury platformy Azure. W przypadku głównej lub globalnej chmury platformy Azure wprowadź plik **https://login.microsoftonline.com**. Zobacz też: Chmury narodowe [.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)|
    |Enter_the_Tenant_Info_Here| Ustaw jedną z następujących opcji: Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym,* zastąp tę wartość identyfikatorem katalogu (dzierżawcy) lub nazwą dzierżawy (na przykład **contoso.microsoft.com**). Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym,* zastąp tę wartość **organizacjami**. Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft,* zastąp tę wartość **wspólną**. Aby ograniczyć obsługę tylko do *osobistych kont Microsoft,* zastąp tę wartość **konsumentami.** |
    |Enter_the_Redirect_Uri_Here|Zamień na **http://localhost:4200**.|

    Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md).

2. U góry tego samego pliku dodaj następującą instrukcję importu:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Dodaj następujące instrukcje importu `src/app/app.component.ts`do góry:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Logowanie użytkownika

Dodaj następujący kod, aby `AppComponent` zalogować się do użytkownika:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

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
> Zalecamy `loginRedirect` używanie dla użytkowników programu Internet Explorer.

## <a name="acquire-a-token"></a>Uzyskiwanie tokenu

### <a name="angular-interceptor"></a>Kątowy przechwytujący

MSAL Angular `Interceptor` zapewnia klasę, która automatycznie uzyskuje tokeny dla wychodzących żądań, które używają klienta kątowego `http` do znanych chronionych zasobów.

Najpierw dołącz `Interceptor` klasę jako dostawcę do aplikacji:

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

Następnie należy przedstawić mapę chronionych `protectedResourceMap` zasobów, aby `consentScopes`uwzględnić `MsalModule.forRoot()` te zakresy w:

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

Na koniec pobierz profil użytkownika z żądaniem HTTP:

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
MSAL używa trzech metod pozyskiwania `acquireTokenRedirect`tokenów: , `acquireTokenPopup`, i `acquireTokenSilent`. Jednak zaleca się `MsalInterceptor` używanie klasy zamiast dla aplikacji kątowych, jak pokazano w poprzedniej sekcji.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje nabycia tokenów i odnawianie bez interakcji z użytkownikiem. Po `loginRedirect` wykonaniu `loginPopup` metody lub po raz `acquireTokenSilent` pierwszy, jest często używany do uzyskiwania tokenów używanych do uzyskiwania dostępu do chronionych zasobów w późniejszych wywołaniach. Wywołania żądania lub odnowienia tokenów są wykonywane dyskretnie.

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

W tym `scopes` kodzie zawiera zakresy żądane do zwrócenia w tokenie dostępu dla interfejsu API.

Przykład:

* `["user.read"]`dla programu Microsoft Graph
* `["<Application ID URL>/scope"]`dla niestandardowych interfejsów API `api://<Application ID>/access_as_user`sieci Web (tj. )

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Czasami użytkownik musi wchodzić w interakcje z punktem końcowym platformy tożsamości firmy Microsoft. Przykład:

* Użytkownicy mogą być zmuszeni do ponownego wprowadzenia swoich poświadczeń, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, na które użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Zalecany wzorzec dla `acquireTokenSilent` większości aplikacji jest najpierw wywołać, a następnie złapać wyjątek, a następnie wywołać `acquireTokenPopup` (lub `acquireTokenRedirect`), aby uruchomić żądanie interaktywne.

Wywołanie `acquireTokenPopup` powoduje wyświetlenie okna logowania wyskakującym okienkiem. Alternatywnie `acquireTokenRedirect` przekierowuje użytkowników do punktu końcowego platformy tożsamości firmy Microsoft. W tym oknie użytkownicy muszą potwierdzić swoje poświadczenia, wyrazić zgodę na wymagany zasób lub ukończyć uwierzytelnianie dwuskładnikowe.

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
> Ten przewodnik Szybki `loginRedirect` start `acquireTokenRedirect` korzysta z programu Microsoft Internet Explorer i z powodu [znanego problemu](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związanego z obsługą wyskakujących okienek przez program Internet Explorer.

## <a name="log-out"></a>Wyloguj się

Dodaj następujący kod, aby wylogować użytkownika:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika
Na przykład jak dodać interfejs użytkownika przy użyciu biblioteki składników Materiał kątowy, zobacz [przykładową aplikację](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>testowanie kodu

1.  Uruchom serwer sieci web, aby nasłuchiwać portu, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

    ```bash
    npm install
    npm start
    ```
1. W przeglądarce **http://localhost:4200** wprowadź **http://localhost:{port}** lub , gdzie *port* jest portem, na którym nasłuchuje serwer www.


### <a name="provide-consent-for-application-access"></a>Udzielanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu się do aplikacji zostanie wyświetlony monit o przyznanie jej dostępu do profilu i zezwolenie mu na zalogowanie się:

![Okno "Wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="add-scopes-and-delegated-permissions"></a>Dodawanie zakresów i uprawnień delegowanych

Interfejs API programu Microsoft Graph wymaga, aby zakres *user.read* odczytywany był w profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API programu Microsoft Graph, a także niestandardowe interfejsy API serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API programu Microsoft Graph wymaga zakresu *Calendars.Read* w celu wyświetlenia listy kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, dodaj *calendars.Read* delegowane uprawnienia do informacji o rejestracji aplikacji. Następnie dodaj *zakres Calendars.Read* `acquireTokenSilent` do wywołania.

>[!NOTE]
>Użytkownik może zostać poproszony o dodatkowe zgody w miarę zwiększania liczby zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (nie zalecane), można użyć *clientId* jako zakres w wywołaniach do uzyskania tokenów.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Następnie dowiedz się, jak zalogować się do użytkownika i uzyskać tokeny w samouczku Angular:

> [!div class="nextstepaction"]
> [Samouczek kątowy](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
