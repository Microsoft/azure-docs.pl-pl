---
title: Wywoływanie ASP.NET internetowego interfejsu API chronionego przez platformę tożsamości firmy Microsoft
description: W tym przewodniku szybki start dowiesz się, jak wywoływać interfejs API sieci Web ASP.NET chroniony przez platformę tożsamości firmy Microsoft z aplikacji klasycznej systemu Windows. Klient WPF uwierzytelnia użytkownika, żąda tokenu dostępu i wywołuje internetowy interfejs API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 22bf7e85a48e0d138bfdbca82cf032287d982899
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339586"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Szybki Start: wywoływanie interfejsu API sieci Web ASP.NET chronionego przez platformę tożsamości firmy Microsoft

W tym przewodniku szybki start uwidaczniasz internetowy interfejs API i zabezpieczesz go tak, aby tylko uwierzytelniony użytkownik mógł uzyskać do niego dostęp. Ten przykład pokazuje, jak uwidocznić internetowy interfejs API ASP.NET, aby mógł akceptować tokeny wystawione przez konta osobiste (w tym outlook.com, live.com i inne), a także konta służbowe z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft.

Przykład zawiera również klienta aplikacji klasycznych systemu Windows (WPF), który pokazuje, jak można zażądać tokenu dostępu w celu uzyskania dostępu do internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są następujące elementy:

* Program Visual Studio 2017 lub 2019.  Pobierz [bezpłatnie program Visual Studio](https://www.visualstudio.com/downloads/).

* [Konto Microsoft](https://www.outlook.com) lub [Microsoft 365 programu dla deweloperów](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Pobierz lub Sklonuj ten przykład

Można sklonować ten przykład z powłoki lub wiersza polecenia:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Możesz też [pobrać przykład jako plik zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Rejestrowanie internetowego interfejsu API w portalu rejestracji aplikacji

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Wybierz dzierżawę usługi Azure AD, w której chcesz utworzyć aplikacje

Jeśli chcesz zarejestrować aplikacje ręcznie, musisz najpierw przeprowadzić następujące czynności:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto jest obecne w więcej niż jednej dzierżawie usługi Azure AD, wybierz swój profil w prawym górnym rogu menu w górnej części strony, a następnie **Przełącz katalog**.
   Zmień sesję portalu na żądaną dzierżawę usługi Azure AD.

### <a name="register-the-service-app-todolistservice"></a>Rejestrowanie aplikacji usługi (TodoListService)

1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
1. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Zmień **obsługiwane typy kont** na **konta w dowolnym katalogu organizacyjnym**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.

1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Będzie to konieczne, aby skonfigurować plik konfiguracyjny programu Visual Studio dla tego projektu ( `ClientId` w programie `TodoListService\Web.config` ).
1. Wybierz sekcję **Uwidacznianie interfejsu API** i:
   - Wybierz pozycję **Dodaj zakres**
   - Zaakceptuj proponowany identyfikator URI aplikacji (API://{clientId}), wybierając pozycję **Zapisz i Kontynuuj** .
   - Wprowadź następujące parametry:
     - Aby użyć **nazwy zakresu**`access_as_user`
     - Upewnij się, że wybrano opcję **Administratorzy i użytkownicy** , dla **kogo można wyrazić zgodę**
     - w polu **Nazwa wyświetlana nazwy wyświetlanej zgody administratora**`Access TodoListService as a user`
     - w polu **Opis zgody administratora**`Accesses the TodoListService web API as a user`
     - w polu **Nazwa wyświetlana nazwy wyświetlanej zgody użytkownika**`Access TodoListService as a user`
     - w polu **Opis wyrażania zgody użytkownika**`Accesses the TodoListService web API as a user`
     - Zachowaj **stan** jako **włączony**
     - Wybierz pozycję **Dodaj zakres**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Skonfiguruj projekt usługi tak, aby był zgodny z zarejestrowanym interfejsem API sieci Web

1. Otwórz rozwiązanie w programie Visual Studio, a następnie otwórz plik **Web.config** w katalogu głównym projektu **TodoListService** .
1. Zastąp wartość `ida:ClientId` PARAMETRU **identyfikatorem klienta (Identyfikator aplikacji)** z aplikacji, która została właśnie zarejestrowana w portalu rejestracji aplikacji.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Dodaj nowy zakres do app.config *TodoListClient*

1. Otwórz plik **app.config** znajdujący się w folderze głównym projektu **TodoListClient** , a następnie wklej **Identyfikator aplikacji** z aplikacji, która została właśnie zarejestrowana dla *TodoListService* w obszarze `TodoListServiceScope` parametru, zastępując ciąg `{Enter the Application ID of your TodoListService from the app registration portal}` .

   > Uwaga: Upewnij się, że używa następującego formatu:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(gdzie {TodoListService-Application-ID} jest identyfikatorem GUID reprezentującym identyfikator aplikacji dla TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Rejestrowanie aplikacji klienckiej (TodoListClient)

W tym kroku skonfigurujesz projekt *TodoListClient* , rejestrując nową aplikację w portalu rejestracji aplikacji. W przypadkach, gdy klient i serwer są uważane za *tę samą aplikację* , możesz również po prostu ponownie użyć tej samej aplikacji zarejestrowanej w kroku 2. Jeśli chcesz, aby użytkownicy mogli logować się za pomocą kont osobistych firmy Microsoft, jest potrzebna ta sama aplikacja

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Rejestrowanie aplikacji *TodoListClient* w *portalu rejestracji aplikacji*

1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
1. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `NativeClient-DotNet-TodoListClient`.
   - Zmień **obsługiwane typy kont** na **konta w dowolnym katalogu organizacyjnym**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie Przegląd aplikacji wybierz sekcję **Uwierzytelnianie**.
   - W sekcji **adresy URI przekierowania**  |  **sugerowane identyfikatory URI dla klientów publicznych (Mobile, Desktop)** zaznacz pole wyboru**https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Wybierz pozycję **Zapisz**.
1. Wybierz sekcję **uprawnienia interfejsu API**
   - Kliknij przycisk **Dodaj uprawnienia** , a następnie
   - Wybierz kartę **Moje interfejsy API** .
   - Na liście interfejsów API wybierz `AppModelv2-NativeClient-DotNet-TodoListService API` lub nazwę wprowadzoną dla internetowego interfejsu API.
   - Zaznacz uprawnienie **access_as_user** , jeśli nie zostało jeszcze zaznaczone. W razie potrzeby użyj pola wyszukiwania.
   - Wybierz przycisk **Dodaj uprawnienia**

### <a name="configure-your-todolistclient-project"></a>Konfigurowanie projektu *TodoListClient*

1. W *portalu rejestracji aplikacji*na stronie **Przegląd** skopiuj wartość **Identyfikator aplikacji (klienta).**
1. Otwórz plik **app.config** znajdujący się w folderze głównym projektu **TodoListClient** , a następnie wklej wartość w polu `ida:ClientId` wartość parametru.

## <a name="run-your-project"></a>Uruchamianie projektu

1. Naciśnij klawisz `<F5>` , aby uruchomić projekt. Należy otworzyć *TodoListClient* .
1. Wybierz pozycję **Zaloguj się** w prawym górnym rogu i zaloguj się przy użyciu tego samego użytkownika, który został użyty do zarejestrowania aplikacji lub użytkownika w tym samym katalogu.
1. W tym momencie, jeśli logujesz się po raz pierwszy, może zostać wyświetlony monit o zgodę na *TodoListService* internetowego interfejsu API.
1. Logowanie żąda również tokenu dostępu do zakresu *access_as_user* , aby uzyskać dostęp do *TodoListService* internetowego interfejsu API i manipulowania listą *czynności do wykonania* .

## <a name="pre-authorize-your-client-application"></a>Wstępna Autoryzacja aplikacji klienckiej

Jednym ze sposobów zezwalania użytkownikom z innych katalogów na dostęp do internetowego interfejsu API jest *wstępne autoryzowanie* aplikacji klienckich w celu uzyskania dostępu do internetowego interfejsu API przez dodanie identyfikatorów aplikacji z aplikacji klienckich na liście *wstępnie autoryzowanych* aplikacji dla internetowego interfejsu API. Przez dodanie wstępnie autoryzowanego klienta nie jest wymagane, aby użytkownik wyraził zgodę na korzystanie z internetowego interfejsu API. Wykonaj poniższe kroki, aby wstępnie autoryzować aplikację sieci Web::

1. Wróć do *portalu rejestracji aplikacji* i Otwórz właściwości **TodoListService**.
1. W sekcji **Uwidacznianie interfejsu API** kliknij pozycję **Dodaj aplikację kliencką** w sekcji *autoryzowane aplikacje klienckie* .
1. W polu *Identyfikator klienta* wklej identyfikator aplikacji `TodoListClient` aplikacji.
1. W sekcji *autoryzowane zakresy* wybierz zakres dla tego internetowego interfejsu API `api://<Application ID>/access_as_user` .
1. Naciśnij przycisk **Dodaj aplikację** w dolnej części strony.

## <a name="run-your-project"></a>Uruchamianie projektu

1. Naciśnij klawisz `<F5>` , aby uruchomić projekt. Należy otworzyć *TodoListClient* .
1. Wybierz pozycję **Zaloguj się** w prawym górnym rogu (lub wyczyść pamięć podręczną/Zaloguj się), a następnie zaloguj się przy użyciu osobistego konto Microsoft (live.com lub hotmail.com) lub konta służbowego.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Opcjonalne: Ogranicz dostęp do logowania do aplikacji

Domyślnie podczas pobierania tego przykładu kodu i konfigurowania aplikacji do korzystania z punktu końcowego Azure Active Directory v2 po powyższych krokach konta osobiste, takie jak outlook.com, live.com i inne, a także konta służbowe z dowolnej organizacji zintegrowanej z usługą Azure AD mogą żądać tokenów i uzyskiwać dostęp do internetowego interfejsu API.

Aby ograniczyć liczbę użytkowników, którzy mogą zalogować się do aplikacji, użyj jednej z opcji:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opcja 1: ograniczanie dostępu do jednej organizacji (pojedynczej dzierżawy)

Dostęp do logowania do aplikacji można ograniczyć tylko do kont użytkowników należących do pojedynczej dzierżawy usługi Azure AD — w tym *kont Gości* tej dzierżawy. Ten scenariusz jest typowy dla *aplikacji biznesowych*:

1. Otwórz plik **App_Start \Startup.auth** i zmień wartość punktu końcowego metadanych, który jest przesyłany do programu `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (można również użyć nazwy dzierżawcy, takiej jak `contoso.onmicrosoft.com` ).
2. W tym samym pliku Ustaw `ValidIssuer` Właściwość na, `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` a `ValidateIssuer` argument na `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opcja 2: Użyj niestandardowej metody do walidacji wystawców

Można zaimplementować metodę niestandardową w celu weryfikacji wystawców przy użyciu parametru **IssuerValidator** . Aby uzyskać więcej informacji o tym, jak używać tego parametru, Przeczytaj o [klasie TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat scenariusza chronionego interfejsu API sieci Web obsługiwanego przez platformę Microsoft Identity:
> [!div class="nextstepaction"]
> [Scenariusz chronionego interfejsu API sieci Web](scenario-protected-web-api-overview.md)
