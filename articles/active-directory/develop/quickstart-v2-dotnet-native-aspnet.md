---
title: Wywoływanie interfejsu API sieci Web ASP.NET chronionego przez platformę tożsamości firmy Microsoft
description: W tym przewodniku szybki start dowiesz się, jak wywołać interfejs API sieci Web ASP.NET chroniony przez platformę tożsamości firmy Microsoft z poziomu aplikacji klasycznej systemu Windows (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: e1b76c9b6a442e3be23ddd54c926b13601287d7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91354942"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Szybki Start: wywoływanie interfejsu API sieci Web ASP.NET chronionego przez platformę tożsamości firmy Microsoft

W tym przewodniku szybki start uwidaczniasz internetowy interfejs API i zabezpieczesz go tak, aby tylko uwierzytelnieni użytkownicy mieli do niego dostęp. W tym artykule pokazano, jak uwidocznić internetowy interfejs API ASP.NET, aby mógł akceptować tokeny wystawiane przez konta osobiste, takie jak outlook.com lub live.com, oraz konta służbowe z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft.

W tym artykule jest również stosowana aplikacja Windows Presentation Foundation (WPF) do zademonstrowania, w jaki sposób można zażądać tokenu dostępu w celu uzyskania dostępu do internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić przykładowy kod w tym artykule, potrzebne są:

* Program Visual Studio 2017 lub 2019.  Pobierz [bezpłatnie program Visual Studio](https://www.visualstudio.com/downloads/).
* [Konto Microsoft](https://www.outlook.com) lub [Microsoft 365 programu dla deweloperów](/office/developer-program/office-365-developer-program).

## <a name="clone-or-download-the-sample"></a>Klonowanie lub pobieranie przykładu

Możesz uzyskać przykład na jeden z dwóch sposobów:  

* Klonuj ją z poziomu powłoki lub wiersza polecenia:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```  
* [Pobierz go jako plik zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Rejestrowanie internetowego interfejsu API

W tej sekcji rejestrujesz internetowy interfejs API w portalu **rejestracje aplikacji** .

### <a name="choose-your-azure-ad-tenant"></a>Wybieranie dzierżawy usługi Azure AD

Aby ręcznie zarejestrować aplikacje, wybierz dzierżawę usługi Azure Active Directory (Azure AD), w której chcesz utworzyć aplikacje.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą konta służbowego lub konto Microsoft prywatnego.

1. Jeśli Twoje konto jest obecne w więcej niż jednej dzierżawie usługi Azure AD, wybierz swój profil w prawym górnym rogu, a następnie wybierz pozycję **Przełącz katalog**.
1. Zmień sesję portalu na dzierżawę usługi Azure AD, której chcesz użyć.

### <a name="register-the-todolistservice-app"></a>Rejestrowanie aplikacji TodoListService

1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) portalu.
1. Wybierz pozycję **Nowa rejestracja**.
1. Po otwarciu **strony Zarejestruj aplikację** wprowadź informacje o rejestracji aplikacji:

   a. W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji. Na przykład wprowadź **AppModelv2-NativeClient-dotnet-TodoListService**.  
   b. W przypadku **obsługiwanych typów kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.  
   c. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.

1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** , a następnie zarejestruj ją w celu późniejszego użycia. Będzie to konieczne, aby skonfigurować plik konfiguracyjny programu Visual Studio dla tego projektu (czyli `ClientId` w pliku *TodoListService\Web.config* ).
1. W sekcji **Uwidacznianie interfejsu API** wybierz pozycję **Dodaj zakres**, zaakceptuj proponowany identyfikator URI aplikacji (API://{clientId}), wybierając pozycję **Zapisz i Kontynuuj**, a następnie wprowadź następujące informacje:
 
   a. W obszarze **Nazwa zakresu**wprowadź **access_as_user**.  
   b. Dla **osób, które mogą wyrazić zgodę**, upewnij się, że wybrano opcję **Administratorzy i użytkownicy** .  
   c. W polu **Nazwa wyświetlana zgody administratora** wpisz **dostęp TodoListService jako użytkownik**.  
   d. W polu **Opis zgody administratora** wpisz dostęp do **internetowego interfejsu API TodoListService jako użytkownik**.  
   e. W polu **Nazwa wyświetlana zgody użytkownika** wpisz **dostęp TodoListService jako użytkownik**.  
   f. W polu **Opis zgody użytkownika** wpisz dostęp do **internetowego interfejsu API TodoListService jako użytkownik**.  
   przykład W przypadku **stanu**pozostaw **włączone**.  
   h. Wybierz pozycję **Dodaj zakres**.

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

   a. W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji (na przykład **NativeClient-dotnet-TodoListClient**).  
   b. W przypadku **obsługiwanych typów kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.  
   c. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
   
   > [!NOTE]
   > W pliku *app.config* projektu TodoListClient wartość domyślna `ida:Tenant` to `common` . Możliwe wartości są następujące:
   > - `common`: Możesz zalogować się przy użyciu konta służbowego lub konta osobistego firmy Microsoft (ponieważ w kroku 3B zostały wybrane **konta w dowolnym katalogu organizacyjnym** ).
   > - `organizations`: Możesz zalogować się przy użyciu konta służbowego.
   > - `consumers`: Możesz zalogować się tylko przy użyciu konta osobistego firmy Microsoft.
   >
   
1. Na stronie **Przegląd** aplikacji wybierz pozycję **uwierzytelnianie**, a następnie wykonaj następujące czynności:

   a. W obszarze **Konfiguracja platformy**wybierz przycisk **Dodaj platformę** .  
   b. W przypadku **aplikacji mobilnych i klasycznych**wybierz **aplikacje mobilne i klasyczne**.  
   c. W obszarze **identyfikatory URI przekierowania**zaznacz **https://login.microsoftonline.com/common/oauth2/nativeclient** pole wyboru.  
   d. Wybierz pozycję **Konfiguruj**.   
1. Wybierz pozycję **uprawnienia interfejsu API**, a następnie wykonaj następujące czynności:

   a. Wybierz przycisk **Dodaj uprawnienia**.  
   b. Wybierz kartę **Moje interfejsy API** .  
   c. Na liście interfejsów API wybierz pozycję **AppModelv2-NativeClient-dotnet-TODOLISTSERVICE API** lub nazwę wprowadzoną dla internetowego interfejsu API.  
   d. Zaznacz pole wyboru **access_as_user** uprawnienie, jeśli nie zostało jeszcze wybrane. W razie potrzeby użyj pola wyszukiwania.  
   e. Wybierz przycisk **Dodaj uprawnienia** .

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
1. W sekcji **Uwidacznianie interfejsu API** w obszarze **autoryzowane aplikacje klienckie**wybierz pozycję **Dodaj aplikację kliencką**.
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
