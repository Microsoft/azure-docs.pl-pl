---
title: Dodawanie logowania z firmą Microsoft do ASP.NET Core Web Apps — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji internetowej ASP.NET Core przy użyciu połączenia OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: db488e4a9ec9aa0f4f12c8de45f123dba1a93cdf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82112715"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core
W tym przewodniku szybki start użyjesz przykładowego kodu, aby dowiedzieć się, w jaki sposób aplikacja sieci Web ASP.NET Core może się zalogować na kontach osobistych (hotmail.com, outlook.com, inne) i służbowych z dowolnego wystąpienia Azure Active Directory (Azure AD). (Zobacz [, jak działa przykład](#how-the-sample-works) dla ilustracji).
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-Quickstart`.
>    - W obszarze **URI przekierowania**Dodaj `https://localhost:44321/`i wybierz pozycję **zarejestruj**.
> 1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
>    - W obszarze **identyfikatory URI przekierowania**Dodaj `https://localhost:44321/signin-oidc`i wybierz pozycję **Zapisz**.
>    - W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
>    - W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
>    - Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu w tym przewodniku Szybki Start działał, należy dodać adresy URL odpowiedzi jako `https://localhost:44321/` i `https://localhost:44321/signin-oidc`, dodać adres URL wylogowywania jako `https://localhost:44321/signout-oidc`, a następnie zażądać, aby tokeny Identyfikatorów były wystawiane przez punkt końcowy autoryzacji.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

> [!div renderon="docs"]
> [Pobierz rozwiązanie Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji i jest gotowy do uruchomienia.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3. uruchamianie projektu programu Visual Studio
> 1. Wyodrębnij plik zip do folderu lokalnego w ramach folderu głównego, na przykład **C:\Azure-Samples**.
> 1. Otwórz rozwiązanie w programie Visual Studio
> 1. Edytuj plik **appsettings.json**. Znajdź `ClientId` i zaktualizuj wartość `ClientId` przy użyciu wartości **identyfikatora aplikacji (klienta)** zarejestrowanej aplikacji.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Gdzie:
> - `Enter_the_Application_Id_here` to **identyfikator aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. **Identyfikator aplikacji (klienta)** możesz znaleźć na stronie aplikacji **Przegląd** aplikacji.
> - `Enter_the_Tenant_Info_Here` to jedna z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta tylko w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Wszyscy użytkownicy kont Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników. Ten przegląd może być przydatny do zrozumienia sposobu działania kodu, głównych argumentów oraz, jeśli chcesz dodać logowanie do istniejącej aplikacji ASP.NET Core.

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft.AspNetCore.Authentication* używa klasy początkowej, która jest wykonywana podczas inicjowania procesu hostingu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoda `AddAuthentication` konfiguruje usługę do dodawania uwierzytelniania opartego na plikach cookie, który jest używany w scenariuszach przeglądarki i do ustawiania wyzwania na OpenID Connect Connect.

W wierszu zawierającym `.AddAzureAd` dodano uwierzytelnianie platformy tożsamości firmy Microsoft do aplikacji. Następnie jest on konfigurowany do logowania za pomocą punktu końcowego platformy tożsamości firmy Microsoft.

> |Lokalizacja  |  |
> |---------|---------|
> | ClientId  | Identyfikator aplikacji (klient) z aplikacji zarejestrowanej w witrynie Azure Portal. |
> | Urząd | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. Zazwyczaj jest to adres <https://login.microsoftonline.com/{tenant}/v2.0> dla chmury publicznej, gdzie parametr {tenant} jest nazwą dzierżawy, identyfikatorem dzierżawy lub ma wartość *common* na potrzeby odwołania do wspólnego punktu końcowego (używany dla aplikacji z wieloma dzierżawami) |
> | TokenValidationParameters | Lista parametrów na potrzeby weryfikacji tokenu. W tym przypadku parametr `ValidateIssuer` ustawiono na wartość `false`, aby wskazać, że może akceptować logowania z dowolnych kont osobistych i służbowych. |


> [!NOTE]
> Ustawienie `ValidateIssuer = false` to uproszczenie dla tego przewodnika Szybki Start. W rzeczywistych aplikacjach należy sprawdzić poprawność wystawcy.
> Zapoznaj się z przykładami, aby dowiedzieć się, jak to zrobić.
>
> Należy również zwrócić `Configure` uwagę na metodę, która zawiera dwie `app.UserCookiePolicy()` ważne metody: i`app.UseAuthentication()`

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more core
    app.UseCookiePolicy();
    app.UseAuthentication();
    // more core
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Kontroler lub jego metody można chronić za pomocą atrybutu `[Authorize]`. Ten atrybut ogranicza dostęp do kontrolera lub metod przez zezwolenie tylko uwierzytelnionym użytkownikom, co oznacza, że można uruchomić wyzwanie uwierzytelniania w celu uzyskania dostępu do kontrolera, jeśli użytkownik nie jest uwierzytelniony.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z repozytorium GitHub dla tego ASP.NET Coreego samouczka, aby uzyskać więcej informacji, w tym instrukcje dotyczące sposobu dodawania uwierzytelniania do nowej aplikacji sieci Web ASP.NET Core, sposobu wywoływania Microsoft Graph i innych interfejsów API firmy Microsoft, sposobu wywoływania własnych interfejsów API, dodawania autoryzacji, logowania użytkowników w chmurach narodowych lub tożsamości społecznościowych itp. :

> [!div class="nextstepaction"]
> [Samouczek aplikacji sieci Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
