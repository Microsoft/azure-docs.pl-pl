---
title: Platforma tożsamości firmy Microsoft Szybki start platformy uniwersalnej systemu Windows | Azure
description: Dowiedz się, jak aplikacja XAML (Universal Windows Platform) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 362f6d1f4e50e1cc78c8897499b9f6593096162b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536016"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Szybki start: wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji platformy UWP

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, jak aplikacja platformy uniwersalnej systemu Windows (UWP) może logować użytkowników za pomocą kont osobistych lub kont służbowych, pobrać token dostępu i wywołać interfejs API programu Microsoft Graph. (Zobacz [Jak działa przykład](#how-the-sample-works) dla ilustracji).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> [!div renderon="docs" class="sxs-lookup"]
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure portal — rejestracje aplikacji.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs)
> 1. Wprowadź nazwę aplikacji i kliknij pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://aka.ms/MobileAppReg)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `UWP-App-calling-MsGraph`.
>      - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)**.
>      - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
> 1. W sekcji **Redirect URIs** | **Suggested Redirect Redirect URI dla klientów publicznych (mobilnych, stacjonarnych)** zaznacz pole wyboru **https://login.microsoftonline.com/common/oauth2/nativeclient**.
> 1. Wybierz pozycję **Zapisz**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby przykładowy kod dla tego szybkiego startu działał, należy **https://login.microsoftonline.com/common/oauth2/nativeclient**dodać identyfikator URI przekierowania jako .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-uwp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikacja jest skonfigurowana i gotowa do uruchomienia
> Skonfigurowaliśmy projekt z wartościami właściwości aplikacji i jest on gotowy do uruchomienia.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
>
> 1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
> 1. Otwórz projekt w programie Visual Studio. Może zostać wyświetlony monit o zainstalowanie pakietu SDK platformy uniwersalnej systemu Windows. W takim przypadku zaakceptuj.
> 1. Edytuj **MainPage.Xaml.cs** i zastąp `ClientId` wartości pola:
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
> Gdzie:
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
>
> > [!TIP]
> > Aby znaleźć wartość *identyfikatora aplikacji,* przejdź do sekcji **Przegląd** w portalu

#### <a name="step-4-run-your-application"></a>Krok 4: Uruchom aplikację

Jeśli chcesz wypróbować przewodnik Szybki start na komputerze z systemem Windows:

1. Na pasku narzędzi Programu Visual Studio wybierz odpowiednią platformę (prawdopodobnie **x64** lub **x86**, a nie ARM). Można zauważyć, że urządzenie docelowe zmienia *się* z *Urządzenia* na
1. Wybierz debugowanie | **Uruchamianie bez debugowania**

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera więcej informacji na temat przewodnika Szybki start.

### <a name="how-the-sample-works"></a>Jak działa próbka
![Pokazuje, jak działa przykładowa aplikacja generowana przez ten szybki start](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka używana do logowania użytkowników i żądania tokenów zabezpieczających. Tokeny zabezpieczające są używane do uzyskiwania dostępu do interfejsu API chronionego przez platformę Microsoft Identity dla deweloperów. Bibliotekę MSAL można zainstalować, uruchamiając następujące polecenie w *Konsoli menedżera pakietów* programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie msal jest inicjowany przy użyciu następującego kodu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

> |Gdzie: ||
> |---------|---------|
> | `ClientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

MSAL ma dwie metody pozyskiwania tokenów w `AcquireTokenInteractive` `AcquireTokenSilent`aplikacji platformy uniwersalnej systemuśpiłnie: i .

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Niektóre sytuacje wymagają zmuszania użytkowników do interakcji z punktem końcowym platformy tożsamości firmy Microsoft za pośrednictwem okna podręcznego, aby zweryfikować swoje poświadczenia lub wyrazić zgodę. Oto niektóre przykłady:

- Użytkownicy po raz pierwszy logują się do aplikacji
- Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Gdy aplikacja żąda dostępu do zasobu, użytkownik musi wyrazić zgodę na
- Gdy wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie `{ "user.read" }` jak program `{ "api://<Application ID>/access_as_user" }` Microsoft Graph lub niestandardowe interfejsy API sieci Web. |

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Użyj `AcquireTokenSilent` metody, aby uzyskać tokeny, aby `AcquireTokenInteractive` uzyskać dostęp do chronionych zasobów po metodzie początkowej. Nie chcesz wymagać od użytkownika sprawdzania poprawności swoich poświadczeń za każdym razem, gdy jest potrzebny do uzyskania dostępu do zasobu. W większości przypadków chcesz nabyć tokenów i odnawiać bez interakcji z użytkownikiem

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie `{ "user.read" }` jak program `{ "api://<Application ID>/access_as_user" }` Microsoft Graph lub niestandardowe interfejsy API sieci Web |
> | `firstAccount` | Określa pierwsze konto użytkownika w pamięci podręcznej (msal obsługuje wielu użytkowników w jednej aplikacji) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym podręczniku Szybki start.

> [!div class="nextstepaction"]
> [UWP — Call Graph API tutorial (Samouczek dotyczący platformy UWP i wywoływania interfejsu API programu Graph)](tutorial-v2-windows-uwp.md)
