---
title: 'Szybki Start: Logowanie użytkowników i wywoływanie Microsoft Graph w aplikacji platforma uniwersalna systemu Windows | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja platforma uniwersalna systemu Windows (platformy UWP) może uzyskać token dostępu i wywołać interfejs API chroniony przez platformę tożsamości firmy Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 0257f4d97e0f6f7cef4907276423d9513568e027
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011711"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Szybki start: wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji platformy UWP

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja platforma uniwersalna systemu Windows (platformy UWP) może zalogować użytkowników i uzyskać token dostępu w celu wywołania interfejsu API Microsoft Graph. 

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> [!div renderon="docs" class="sxs-lookup"]
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `UWP-App-calling-MsGraph` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)**.
> 1. Wybierz pozycję **zarejestruj** , aby utworzyć aplikację, a następnie Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wybierz pozycję **Dodaj**  >  **aplikacje mobilne i klasyczne**.
> 1. W obszarze **identyfikatory URI przekierowania** wybierz opcję `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Wybierz pozycję **Konfiguruj**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI przekierowania jako **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-uwp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji i jest gotowy do uruchomienia.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
>
> 1. Wyodrębnij archiwum zip do folderu lokalnego w pobliżu katalogu głównego dysku. Na przykład do **C:\Azure-Samples**.
> 1. Otwórz projekt w programie Visual Studio. Jeśli zostanie wyświetlony monit, zainstaluj platforma uniwersalna systemu Windows obciążenie pracą **programistyczną** i wszystkie poszczególne składniki zestawu SDK.
> 1. W *MainPage.XAML.cs* Zmień wartość `ClientId` zmiennej na **Identyfikator aplikacji (klienta)** , która została zarejestrowana wcześniej.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    **Identyfikator aplikacji (klienta)** można znaleźć w okienku **Przegląd** aplikacji w Azure Portal (**Azure Active Directory**  >  **rejestracje aplikacji**  >  *{Twoja rejestracja aplikacji}*).
> 1. Utwórz, a następnie wybierz nowy certyfikat testu z podpisem własnym dla pakietu:
>     1. W **Eksplorator rozwiązań** kliknij dwukrotnie plik *Package. appxmanifest* .
>     1. Wybierz pozycję **pakowanie**  >  **Wybierz certyfikat...**  >  **Utwórz...**
>     1. Wprowadź hasło, a następnie wybierz przycisk **OK**.
>     1. Wybierz pozycję **Wybierz z pliku...**, a następnie wybierz utworzony plik *Native_UWP_V2_TemporaryKey. pfx* , a następnie wybierz **przycisk OK**.
>     1. Zamknij plik *Package. appxmanifest* (wybierz **przycisk OK** , jeśli zostanie wyświetlony monit o zapisanie pliku).
>     1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **Native_UWP_V2** i wybierz polecenie **Właściwości**.
>     1. Wybierz pozycję **podpisywanie**, a następnie wybierz plik PFX utworzony w polu listy rozwijanej **Wybierz klucz o silnej nazwie** .

#### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

Aby uruchomić przykładową aplikację na komputerze lokalnym:

1. Na pasku narzędzi programu Visual Studio wybierz odpowiednią platformę (prawdopodobnie **x64** lub **x86**, a nie ARM). Urządzenie docelowe powinno ulec zmianie z *urządzenia* na *komputer lokalny*.
1. Wybierz kolejno pozycje **Debuguj** > **Uruchom bez debugowania**.
    
    Jeśli zostanie wyświetlony monit, musisz najpierw włączyć **Tryb dewelopera**, a następnie **uruchomić polecenie bez debugowania** ponownie, aby uruchomić aplikację.

Gdy zostanie wyświetlone okno aplikacji, możesz wybrać przycisk **Wywołaj Microsoft Graph interfejs API** , wprowadzić poświadczenia i wyrazić zgodę na uprawnienia wymagane przez aplikację. Jeśli to się powiedzie, aplikacja wyświetli informacje o tokenie i dane uzyskane z wywołania do interfejsu API Microsoft Graph.

## <a name="how-the-sample-works"></a>Jak działa przykład

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka służąca do logowania użytkowników i żądania tokenów zabezpieczających. Tokeny zabezpieczające są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft dla deweloperów. Bibliotekę MSAL można zainstalować, uruchamiając następujące polecenie w *Konsoli menedżera pakietów* programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie MSAL jest inicjowany przy użyciu następującego kodu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

Wartość `ClientId` to **Identyfikator aplikacji (klienta)** , która została zarejestrowana w Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal.

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

MSAL ma dwie metody uzyskiwania tokenów w aplikacji platformy UWP: `AcquireTokenInteractive` i `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Niektóre sytuacje wymagają wymuszenia, aby użytkownicy mogli korzystać z punktu końcowego platformy tożsamości firmy Microsoft za pomocą okna podręcznego, aby zweryfikować swoje poświadczenia lub wyrazić zgodę. Oto niektóre przykłady:

- Logowanie do aplikacji przez użytkowników po raz pierwszy
- Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Gdy aplikacja żąda dostępu do zasobu, użytkownik musi wyrazić zgodę na
- Gdy wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes`Parametr zawiera żądane zakresy, takie jak `{ "user.read" }` dla Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` niestandardowych interfejsów API sieci Web.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Użyj `AcquireTokenSilent` metody, aby uzyskać tokeny umożliwiające dostęp do chronionych zasobów po początkowej `AcquireTokenInteractive` metodzie. Nie chcesz wymagać od użytkownika weryfikowania poświadczeń za każdym razem, gdy potrzebują dostępu do zasobu. Większość czasu, w którym ma być uzyskiwany token i odnowienie bez żadnej interakcji z użytkownikiem

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` zawiera żądane zakresy, takie jak `{ "user.read" }` dla Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` niestandardowych interfejsów API sieci Web.
* `firstAccount` Określa pierwsze konto użytkownika w pamięci podręcznej (MSAL obsługuje wielu użytkowników w jednej aplikacji).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym podręczniku Szybki start.

> [!div class="nextstepaction"]
> [UWP — Call Graph API tutorial (Samouczek dotyczący platformy UWP i wywoływania interfejsu API programu Graph)](tutorial-v2-windows-uwp.md)
