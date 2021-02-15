---
title: 'Szybki Start: wywoływanie interfejsu API sieci Web ASP.NET, który jest chroniony przez platformę tożsamości firmy Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak wywołać interfejs API sieci Web ASP.NET, który jest chroniony przez platformę tożsamości firmy Microsoft z aplikacji klasycznej systemu Windows (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 9dc787c57ef3ab84d9a2b90cfe059594b0a31d08
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102516"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Szybki Start: wywoływanie interfejsu API sieci Web ASP.NET chronionego przez platformę tożsamości firmy Microsoft

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak chronić internetowy interfejs API ASP.NET przez ograniczenie dostępu do zasobów tylko do autoryzowanych kont. Przykład obsługuje autoryzację osobistych kont Microsoft i kont w dowolnej organizacji Azure Active Directory (Azure AD).

W tym artykule jest również stosowana aplikacja Windows Presentation Foundation (WPF) do zademonstrowania, w jaki sposób można zażądać tokenu dostępu w celu uzyskania dostępu do internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Program Visual Studio 2017 lub 2019. Pobierz [bezpłatnie program Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Klonowanie lub pobieranie przykładu

Możesz uzyskać przykład na jeden z dwóch sposobów:

* Klonuj ją z poziomu powłoki lub wiersza polecenia:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Pobierz go jako plik zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Rejestrowanie internetowego interfejsu API

W tej sekcji rejestrujesz internetowy interfejs API w **rejestracje aplikacji** w Azure Portal.

### <a name="choose-your-azure-ad-tenant"></a>Wybieranie dzierżawy usługi Azure AD

Aby ręcznie zarejestrować aplikacje, wybierz dzierżawę usługi Azure Active Directory (Azure AD), w której chcesz utworzyć aplikacje.

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, którego chcesz użyć.

### <a name="register-the-todolistservice-app"></a>Rejestrowanie aplikacji TodoListService

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji, na przykład `AppModelv2-NativeClient-DotNet-TodoListService` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. W przypadku **obsługiwanych typów kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** , a następnie zarejestruj ją w celu późniejszego użycia. Będzie to konieczne, aby skonfigurować plik konfiguracyjny programu Visual Studio dla tego projektu (czyli `ClientId` w pliku *TodoListService\Web.config* ).
1. W obszarze **Zarządzaj** wybierz opcję **Uwidocznij interfejs API**  >  **Dodaj zakres**. Zaakceptuj proponowany identyfikator URI aplikacji ( `api://{clientId}` ), wybierając pozycję **Zapisz i Kontynuuj**, a następnie wprowadź następujące informacje:

    1. W obszarze **Nazwa zakresu** wprowadź `access_as_user` .
    1. Dla **osób, które mogą wyrazić zgodę**, upewnij się, że wybrano opcję **Administratorzy i użytkownicy** .
    1. W polu **Nazwa wyświetlana zgody administratora** wprowadź wartość `Access TodoListService as a user` .
    1. W polu **Opis zgody administratora** wprowadź wartość `Accesses the TodoListService web API as a user` .
    1. W polu **Nazwa wyświetlana zgody użytkownika** wprowadź wartość `Access TodoListService as a user` .
    1. W polu **Opis zgody użytkownika** wprowadź wartość `Accesses the TodoListService web API as a user` .
    1. W przypadku **stanu** pozostaw **włączone**.
1. Wybierz pozycję **Dodaj zakres**.

### <a name="configure-the-service-project"></a>Konfigurowanie projektu usługi

Skonfiguruj projekt usługi tak, aby był zgodny z zarejestrowanym interfejsem API sieci Web, wykonując następujące czynności:

1. Otwórz rozwiązanie w programie Visual Studio, a następnie otwórz plik *Web.config* w obszarze głównym projektu TodoListService.

1. Zastąp wartość `ida:ClientId` parametru wartością identyfikator klienta (Identyfikator aplikacji) z aplikacji właśnie zarejestrowanej w portalu **rejestracje aplikacji** .

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Dodaj nowy zakres do pliku app.config

Aby dodać nowy zakres do pliku TodoListClient *app.config* , wykonaj następujące czynności:

1. W folderze głównym projektu TodoListClient Otwórz plik *app.config* .

1. Wklej identyfikator aplikacji z aplikacji, która została właśnie zarejestrowana dla projektu TodoListService w `TodoListServiceScope` parametrze, zastępując `{Enter the Application ID of your TodoListService from the app registration portal}` ciąg.

  > [!NOTE]
  > Upewnij się, że identyfikator aplikacji używa następującego formatu: `api://{TodoListService-Application-ID}/access_as_user` (gdzie `{TodoListService-Application-ID}` to identyfikator GUID reprezentujący identyfikator aplikacji dla aplikacji TodoListService).

## <a name="register-the-todolistclient-client-app"></a>Rejestrowanie aplikacji klienckiej TodoListClient

W tej sekcji zarejestrujesz aplikację TodoListClient w **rejestracje aplikacji** w Azure Portal, a następnie skonfigurujesz kod w projekcie TodoListClient. Jeśli klient i serwer są uważane za tę *samą aplikację*, można ponownie użyć aplikacji zarejestrowanej w kroku 2. Użyj tej samej aplikacji, jeśli chcesz, aby użytkownicy mogli logować się za pomocą konta osobistego firmy Microsoft.

### <a name="register-the-app"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację TodoListClient, wykonaj następujące czynności:

1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) portalu.
1. Wybierz pozycję **Nowa rejestracja**.
1. Po otwarciu **strony Zarejestruj aplikację** wprowadź informacje o rejestracji aplikacji:

    1. W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji (na przykład **NativeClient-dotnet-TodoListClient**).
    1. W przypadku **obsługiwanych typów kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
    1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.

   > [!NOTE]
   > W pliku *app.config* projektu TodoListClient wartość domyślna `ida:Tenant` to `common` . Możliwe wartości są następujące:
   > - `common`: Możesz zalogować się przy użyciu konta służbowego lub konta osobistego firmy Microsoft (ponieważ w kroku 3B zostały wybrane **konta w dowolnym katalogu organizacyjnym** ).
   > - `organizations`: Możesz zalogować się przy użyciu konta służbowego.
   > - `consumers`: Możesz zalogować się tylko przy użyciu konta osobistego firmy Microsoft.

1. Na stronie **Przegląd** aplikacji wybierz pozycję **uwierzytelnianie**, a następnie wykonaj następujące czynności:

    1. W obszarze **Konfiguracja platformy** wybierz przycisk **Dodaj platformę** .
    1. W przypadku **aplikacji mobilnych i klasycznych** wybierz **aplikacje mobilne i klasyczne**.
    1. W obszarze **identyfikatory URI przekierowania** zaznacz **https://login.microsoftonline.com/common/oauth2/nativeclient** pole wyboru.
    1. Wybierz pozycję **Konfiguruj**.

1. Wybierz pozycję **uprawnienia interfejsu API**, a następnie wykonaj następujące czynności:

    1. Wybierz przycisk **Dodaj uprawnienia**.
    1. Wybierz kartę **Moje interfejsy API** .
    1. Na liście interfejsów API wybierz pozycję **AppModelv2-NativeClient-dotnet-TODOLISTSERVICE API** lub nazwę wprowadzoną dla internetowego interfejsu API.
    1. Zaznacz pole wyboru **access_as_user** uprawnienie, jeśli nie zostało jeszcze wybrane. W razie potrzeby użyj pola wyszukiwania.
    1. Wybierz przycisk **Dodaj uprawnienia** .

### <a name="configure-your-project"></a>Konfigurowanie projektu

Aby skonfigurować projekt TodoListClient, wykonaj następujące czynności:

1. Na stronie **Przegląd** w portalu **rejestracje aplikacji** skopiuj wartość **Identyfikator aplikacji (klienta)**.

1. W folderze głównym projektu TodoListClient Otwórz plik *app.config* , a następnie wklej wartość Identyfikator aplikacji w `ida:ClientId` parametrze.

## <a name="run-your-todolistclient-project"></a>Uruchamianie projektu TodoListClient

Aby uruchomić projekt TodoListClient, wykonaj następujące czynności:

1. Naciśnij klawisz F5, aby otworzyć projekt TodoListClient. Strona projektu powinna zostać otwarta.

1. W prawym górnym rogu wybierz pozycję **Zaloguj**, a następnie zaloguj się przy użyciu tych samych poświadczeń, które zostały użyte do zarejestrowania aplikacji, lub Zaloguj się jako użytkownik w tym samym katalogu.

   Jeśli logujesz się po raz pierwszy, może zostać wyświetlony monit o zgodę na interfejs API sieci Web TodoListService.

   Aby ułatwić dostęp do interfejsu API sieci Web TodoListService i manipulowania listą *czynności do wykonania* , logowanie również żąda tokenu dostępu do zakresu *access_as_user* .

## <a name="pre-authorize-your-client-application"></a>Wstępna Autoryzacja aplikacji klienckiej

Jednym ze sposobów zezwalania użytkownikom z innych katalogów na dostęp do internetowego interfejsu API jest wstępne autoryzowanie aplikacji klienckiej w celu uzyskania dostępu do internetowego interfejsu API. W tym celu należy dodać identyfikator aplikacji z aplikacji klienckiej do listy wstępnie autoryzowanych aplikacji dla internetowego interfejsu API. Dodając wstępnie autoryzowanego klienta, zezwolisz użytkownikom na dostęp do internetowego interfejsu API bez konieczności udzielania zgody. Aby wstępnie autoryzować aplikację kliencką, wykonaj następujące czynności:

1. W portalu **rejestracje aplikacji** Otwórz właściwości aplikacji TodoListService.
1. W sekcji **Uwidacznianie interfejsu API** w obszarze **autoryzowane aplikacje klienckie** wybierz pozycję **Dodaj aplikację kliencką**.
1. W polu **Identyfikator klienta** wklej identyfikator aplikacji aplikacji TodoListClient.
1. W sekcji **autoryzowane zakresy** wybierz zakres dla internetowego `api://<Application ID>/access_as_user` interfejsu API.
1. Wybierz pozycję **Dodaj aplikację**.

### <a name="run-your-project"></a>Uruchamianie projektu

1. Naciśnij klawisz F5, aby uruchomić projekt. Aplikacja TodoListClient powinna zostać otwarta.
1. W prawym górnym rogu wybierz pozycję **Zaloguj**, a następnie zaloguj się przy użyciu osobistego konto Microsoft, takiego jak live.com lub hotmail.com, lub konta służbowego.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Opcjonalne: Ogranicz dostęp do logowania do określonych użytkowników

Domyślnie po wykonaniu powyższych kroków wszystkie konta osobiste, takie jak outlook.com lub live.com, lub konta służbowe z organizacji zintegrowanych z usługą Azure AD mogą żądać tokenów i uzyskiwać dostęp do internetowego interfejsu API.

Aby określić, kto może logować się do aplikacji, użyj jednej z następujących opcji:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Opcja 1. ograniczanie dostępu do jednej organizacji (pojedynczej dzierżawy)

Możesz ograniczyć dostęp logowania do aplikacji do kont użytkowników należących do pojedynczej dzierżawy usługi Azure AD, w tym *kont Gości* tej dzierżawy. Ten scenariusz jest typowy dla *aplikacji biznesowych*.

1. Otwórz plik *App_Start \Startup.auth* , a następnie zmień wartość punktu końcowego metadanych, który jest przesyłany do `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . Możesz również użyć nazwy dzierżawcy, takiej jak `contoso.onmicrosoft.com` .
2. W tym samym pliku Ustaw `ValidIssuer` Właściwość na `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` , a następnie ustaw `ValidateIssuer` argument na `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opcja 2: Użyj niestandardowej metody do walidacji wystawców

Można zaimplementować metodę niestandardową w celu weryfikacji wystawców przy użyciu `IssuerValidator` parametru. Aby uzyskać więcej informacji o tym parametrze, zobacz [Klasa TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat scenariusza chronionego interfejsu API sieci Web obsługiwanego przez platformę Microsoft Identity:
> [!div class="nextstepaction"]
> [Scenariusz chronionego interfejsu API sieci Web](scenario-protected-web-api-overview.md)
