---
title: Wywoływanie ASP.NET internetowego interfejsu API chronionego przez platformę tożsamości firmy Microsoft
description: W tym przewodniku Szybki start dowiedz się, jak wywołać ASP.NET internetowy interfejs API chroniony przez platformę tożsamości firmy Microsoft z aplikacji Pulpitu systemu Windows (WPF). Klient WPF uwierzytelnia użytkownika, żąda tokenu dostępu i wywołuje internetowy interfejs API.
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
ms.openlocfilehash: 084083a704a007e6675234883c62350d1d9a0849
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536152"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Szybki start: wywoływanie ASP.NET interfejsu API sieci Web chronionego przez platformę tożsamości firmy Microsoft

W tym przewodniku Szybki start można udostępnić internetowy interfejs API i chronić go, tak aby tylko uwierzytelniony użytkownik może uzyskać do niego dostęp. W tym przykładzie pokazano, jak udostępnić ASP.NET interfejsu API sieci web, dzięki czemu można akceptować tokeny wystawione przez konta osobiste (w tym outlook.com, live.com i inne), a także konta służbowe i szkolne z dowolnej firmy lub organizacji, która zintegrowała się z platformą tożsamości firmy Microsoft.

Przykład zawiera również klienta aplikacji pulpitu systemu Windows (WPF), który pokazuje, jak można zażądać tokenu dostępu, aby uzyskać dostęp do internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten przykład, należy wykonać następujące czynności:

* Visual Studio 2017 lub 2019.  Pobierz [program Visual Studio za darmo](https://www.visualstudio.com/downloads/).

* [Konto Microsoft](https://www.outlook.com) lub [program deweloperski usługi Office 365](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Pobierz lub sklonuj ten przykład

Możesz sklonować ten przykład z powłoki lub wiersza polecenia:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Możesz też [pobrać próbkę jako plik ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Zarejestruj swój internetowy interfejs API w portalu rejestracji aplikacji

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Wybierz dzierżawę usługi Azure AD, w której chcesz utworzyć aplikacje

Jeśli chcesz zarejestrować aplikacje ręcznie, w pierwszym kroku musisz:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawie usługi Azure AD, wybierz swój profil w prawym górnym rogu w menu u góry strony, a następnie **przełącz katalog**.
   Zmień sesję portalu na żądaną dzierżawę usługi Azure AD.

### <a name="register-the-service-app-todolistservice"></a>Zarejestruj aplikację usługi (TodoListService)

1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Wybierz **pozycję Nowa rejestracja**.
1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Zmień **obsługiwane typy kont** na Konta w dowolnym katalogu **organizacyjnym**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.

1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później. Będzie potrzebny do skonfigurowania pliku konfiguracyjnego programu`ClientId` `TodoListService\Web.config`Visual Studio dla tego projektu ( w ).
1. Wybierz **sekcję Uwidacznij interfejs API** i:
   - Wybierz **pozycję Dodaj zakres**
   - zaakceptuj proponowany identyfikator URI identyfikatora aplikacji (api://{clientId}), wybierając **pozycję Zapisz i Kontynuuj**
   - Wprowadź następujące parametry:
     - dla **używania nazwy zakresu**`access_as_user`
     - Upewnij się, że wybrano opcję **Administratorzy i użytkownicy** dla **Kto może wyrazić zgodę**
     - w **typ nazwy wyświetlanej zgody administratora**`Access TodoListService as a user`
     - w **typie opisu zgody administratora**`Accesses the TodoListService web API as a user`
     - w typ **nazwy wyświetlanej zgody użytkownika**`Access TodoListService as a user`
     - w **typie opisu zgody użytkownika**`Accesses the TodoListService web API as a user`
     - Zachowaj **stan** jako **włączony**
     - Wybierz **pozycję Dodaj zakres**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Konfigurowanie projektu usługi w celu dopasowania do zarejestrowanego interfejsu API sieci Web

1. Otwórz rozwiązanie w programie Visual Studio, a następnie otwórz plik **Web.config** w katalogu głównym projektu **TodoListService.**
1. Zastąp `ida:ClientId` wartość parametru **identyfikatorem klienta (identyfikatorem aplikacji)** z aplikacji zarejestrowanej właśnie w portalu rejestracji aplikacji.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Dodaj nowy zakres do app.config *TodoListClient*

1. Otwórz plik **app.config** znajdujący się w folderze głównym projektu **TodoListClient,** a następnie wklej **identyfikator** `TodoListServiceScope` aplikacji z aplikacji, którą właśnie zarejestrowałeś dla swojego `{Enter the Application ID of your TodoListService from the app registration portal}` *parametru TodoListService,* zastępując ciąg .

   > Uwaga: Upewnij się, że używa następującego formatu:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(gdzie {TodoListService-Application-ID} jest identyfikatorem GUID reprezentującym identyfikator aplikacji dla usługi TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Zarejestruj aplikację kliencką (TodoListClient)

W tym kroku można skonfigurować projekt *TodoListClient,* rejestrując nową aplikację w portalu rejestracji aplikacji. W przypadkach, gdy klient i serwer są uważane za *tę samą aplikację* można również po prostu ponownie użyć tej samej aplikacji zarejestrowanej w "Kroku 2.". Korzystanie z tej samej aplikacji jest potrzebne, jeśli chcesz, aby użytkownicy logują się za pomocą kont osobistych Microsoft

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Zarejestruj aplikację *TodoListClient* w *portalu rejestracji aplikacji*

1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Wybierz **pozycję Nowa rejestracja**.
1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `NativeClient-DotNet-TodoListClient`.
   - Zmień **obsługiwane typy kont** na Konta w dowolnym katalogu **organizacyjnym**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie Przegląd aplikacji wybierz sekcję **Uwierzytelnianie**.
   - W sekcji **Redirect URIs** | **Suggested Redirect Redirect URI dla klientów publicznych (mobilnych, stacjonarnych)****https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Wybierz pozycję **Zapisz**.
1. Wybierz sekcję **Uprawnienia interfejsu API**
   - Kliknij przycisk **Dodaj uprawnienie,** a następnie
   - Wybierz kartę **Moje interfejsy API.**
   - Na liście interfejsów API wybierz `AppModelv2-NativeClient-DotNet-TodoListService API`program lub nazwę wprowadzona dla internetowego interfejsu API.
   - Sprawdź uprawnienie **access_as_user,** jeśli nie jest jeszcze zaznaczone. W razie potrzeby użyj pola wyszukiwania.
   - Wybierz przycisk **Dodaj uprawnienia**

### <a name="configure-your-todolistclient-project"></a>Konfigurowanie projektu *TodoListClient*

1. W *portalu rejestracji aplikacji*na stronie Przegląd skopiuj wartość **Overview** **identyfikatora aplikacji (klienta)**
1. Otwórz plik **app.config** znajdujący się w folderze głównym projektu **TodoListClient,** a następnie wklej wartość w wartości parametru `ida:ClientId`

## <a name="run-your-project"></a>Uruchamianie projektu

1. Naciśnij, `<F5>` aby uruchomić projekt. Twój *TodoListClient* powinien zostać otwarty.
1. Wybierz **pozycję Zaloguj się w** prawym górnym rogu i zaloguj się z tym samym użytkownikiem, którego użyto do zarejestrowania aplikacji, lub użytkownikiem w tym samym katalogu.
1. W tym momencie, jeśli logujesz się po raz pierwszy, może zostać wyświetlony monit o wyrażenie zgody na interfejs api sieci Web *TodoListService.*
1. Logowania również zażądać tokenu dostępu do zakresu *access_as_user,* aby uzyskać dostęp do interfejsu API sieci Web *TodoListService* i *manipulować* listy zadań do wykonania.

## <a name="pre-authorize-your-client-application"></a>Pre-authorize aplikacji klienckiej

Jednym ze sposobów umożliwienia użytkownikom z innych katalogów dostępu do internetowego interfejsu API jest *wstępne autoryzowanie* aplikacji klienckich w celu uzyskania dostępu do internetowego interfejsu API przez dodanie identyfikatorów aplikacji z aplikacji klienckich na liście *wstępnie autoryzowanych* aplikacji dla internetowego interfejsu API. Dodając wstępnie autoryzowanego klienta, użytkownik nie będzie wymagał zgody użytkownika na korzystanie z internetowego interfejsu API. Wykonaj poniższe czynności, aby wstępnie autoryzować aplikację sieci Web::

1. Wróć do *portalu rejestracji aplikacji* i otwórz właściwości usługi **TodoListService**.
1. W sekcji **Uwidacznianie interfejsu API** kliknij pozycję **Dodaj aplikację kliencką** w sekcji *Autoryzowane aplikacje klienckie.*
1. W polu *Identyfikator klienta* wklej identyfikator `TodoListClient` aplikacji.
1. W sekcji *Autoryzowane zakresy* wybierz zakres tego `api://<Application ID>/access_as_user`internetowego interfejsu API .
1. Naciśnij przycisk **Dodaj aplikację** u dołu strony.

## <a name="run-your-project"></a>Uruchamianie projektu

1. Naciśnij, `<F5>` aby uruchomić projekt. Twój *TodoListClient* powinien zostać otwarty.
1. Wybierz **pozycję Zaloguj się w** prawym górnym rogu (lub Wyczyść pamięć podręczną/zaloguj się), a następnie zaloguj się przy użyciu osobistego konta Microsoft (live.com lub hotmail.com) lub konta służbowego lub szkolnego.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Opcjonalnie: ograniczanie dostępu do logowania do aplikacji

Domyślnie po pobraniu tego przykładu kodu i skonfigurowaniu aplikacji do używania punktu końcowego usługi Azure Active Directory w wersji 2 zgodnie z poprzednimi krokami, zarówno konta osobiste — takie jak outlook.com, live.com i inne — a także konta służbowe lub szkolne z dowolnej organizacji zintegrowanych z usługą Azure AD mogą żądać tokenów i uzyskiwać dostęp do internetowego interfejsu API.

Aby ograniczyć możliwość logowania się do aplikacji, użyj jednej z opcji:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opcja 1: Ograniczanie dostępu do jednej organizacji (z jedną dzierżawą)

Można ograniczyć dostęp do logowania dla aplikacji tylko do kont użytkowników, które znajdują się w jednej dzierżawie usługi Azure AD — w tym *kont gości* tej dzierżawy. Ten scenariusz jest wspólny dla *aplikacji biznesowych:*

1. Otwórz plik **App_Start\Startup.Auth** i zmień wartość punktu końcowego metadanych, który `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` jest przekazywany do (można `contoso.onmicrosoft.com`również użyć nazwy dzierżawy, takiej jak ).
2. W tym samym pliku `ValidIssuer` ustaw `TokenValidationParameters` właściwość na do `"https://sts.windows.net/{Tenant ID}/"` i argument na `ValidateIssuer` `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opcja 2: Użyj metody niestandardowej, aby zweryfikować wystawców

Można zaimplementować metodę niestandardową do sprawdzania poprawności wystawców przy użyciu **parametru IssuerValidator.** Aby uzyskać więcej informacji na temat używania tego parametru, przeczytaj o [Klasie TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o scenariuszu chronionego interfejsu API sieci Web, który obsługuje platforma tożsamości firmy Microsoft:
> [!div class="nextstepaction"]
> [Scenariusz chronionego interfejsu API sieci Web](scenario-protected-web-api-overview.md)
