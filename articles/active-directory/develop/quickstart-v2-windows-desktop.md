---
title: 'Szybki Start: Logowanie użytkowników i wywoływanie Microsoft Graph w aplikacji klasycznej platforma uniwersalna systemu Windows | Azure'
description: W tym przewodniku szybki start dowiesz się, jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez platformę tożsamości firmy Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 70fbaab8a409aaef8b1130abd40930a7aec79541
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583098"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji klasycznej w systemie Windows

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak aplikacja Windows Desktop .NET (WPF) może zalogować użytkowników i uzyskać token dostępu do wywołania interfejsu API Microsoft Graph. 

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) z zainstalowanym obciążeniem [programowania platforma uniwersalna systemu Windows](/windows/uwp/get-started/get-set-up)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `Win-App-calling-MsGraph` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)**.
> 1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wybierz pozycję **Dodaj**  >  **aplikacje mobilne i klasyczne**.
> 1. W sekcji **identyfikatory URI przekierowania** wybierz pozycję `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Wybierz pozycję **Konfiguruj**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku szybki start do pracy, Dodaj **Identyfikator URI przekierowania** `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-windows-desktop/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji i jest gotowy do uruchomienia.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
> 1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
> 1. Otwórz projekt w programie Visual Studio.
> 1. Edytuj plik **App.Xaml.cs** i zastąp wartości pól `ClientId` i `Tenant` następującym kodem:
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> Gdzie:
> - `Enter_the_Application_Id_here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Info_Here` to wartość ustawiana na jedną z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb **Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Konta w moim katalogu organizacyjnym i osobiste konta Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Bibliotekę MSAL można zainstalować, uruchamiając następujące polecenie w **Konsoli menedżera pakietów** programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Gdzie: | Opis |
> |---------|---------|
> | `ClientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `AcquireTokenInteractive` i `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Niektóre sytuacje wymagają wymuszenia, aby użytkownicy korzystali z platformy tożsamości firmy Microsoft za pomocą okna podręcznego do weryfikacji poświadczeń lub wyrazić zgodę. Oto niektóre przykłady:

- Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
- Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Gdy aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
- Gdy wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Gdzie:| Opis |
> |---------|---------|
> | `_scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` niestandardowych interfejsów API sieci Web. |

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Nie chcesz, aby użytkownik musiał weryfikować poświadczenia za każdym razem, gdy musi uzyskać dostęp do zasobu. Przez większość czasu uzyskiwanie i odnawianie tokenów powinno odbywać się bez żadnej interakcji z użytkownikiem. Możesz użyć metody `AcquireTokenSilent` do uzyskiwania tokenów umożliwiających dostęp do chronionych zasobów po początkowym skorzystaniu z metody `AcquireTokenInteractive`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Gdzie: | Opis |
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` niestandardowych interfejsów API sieci Web. |
> | `firstAccount` | Określa pierwszego użytkownika w pamięci podręcznej (biblioteka MSAL obsługuje wielu użytkowników w jednej aplikacji). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym podręczniku Szybki start.

> [!div class="nextstepaction"]
> [Call Graph API tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph)](./tutorial-v2-windows-desktop.md)
