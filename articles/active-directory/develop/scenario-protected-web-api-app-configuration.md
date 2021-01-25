---
title: Konfigurowanie chronionych aplikacji internetowego interfejsu API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i skonfigurować kod aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a26157949ff6ef69c9c009dfdd40781b47bc761
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753581"
---
# <a name="protected-web-api-code-configuration"></a>Chroniony internetowy interfejs API: Konfiguracja kodu

Aby skonfigurować kod chronionego internetowego interfejsu API, należy zapoznać się z tematem:

- Co definiuje interfejsy API jako chronione.
- Jak skonfigurować token okaziciela.
- Sprawdzanie poprawności tokenu.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Co definiuje ASP.NET i ASP.NET Core interfejsów API jako chronione?

Podobnie jak aplikacje sieci Web, interfejsy API sieci Web ASP.NET i ASP.NET Core są chronione, ponieważ ich akcje kontrolera są poprzedzone prefiksem **[autoryzuje]** . Akcje kontrolera można wywołać tylko wtedy, gdy interfejs API jest wywoływany z autoryzowaną tożsamością.

Zastanów się nad następującymi pytaniami:

- Tylko aplikacja może wywołać internetowy interfejs API. Jak interfejs API wie tożsamość aplikacji, która ją wywołuje?
- Jeśli aplikacja wywołuje interfejs API w imieniu użytkownika, co to jest tożsamość użytkownika?

## <a name="bearer-token"></a>Token okaziciela

Token okaziciela, który jest ustawiony w nagłówku, gdy aplikacja jest wywoływana, zawiera informacje o tożsamości aplikacji. Zawiera również informacje o użytkowniku, chyba że aplikacja sieci Web zaakceptuje wywołania Service to Service z aplikacji demona.

Oto przykład kodu w języku C#, który pokazuje klienta wywołującego interfejs API po uzyskaniu tokenu za pomocą biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Aplikacja kliencka żąda tokenu okaziciela do platformy tożsamości firmy Microsoft dla internetowego *interfejsu API*. Internetowy interfejs API jest jedyną aplikacją, która powinna zweryfikować token i wyświetlić zawarte w nim oświadczenia. Aplikacje klienckie nigdy nie powinny podejmować próby sprawdzenia oświadczeń w tokenach.
>
> W przyszłości internetowy interfejs API może wymagać szyfrowania tokenu. Ten wymóg uniemożliwia dostęp dla aplikacji klienckich, które mogą wyświetlać tokeny dostępu.

## <a name="jwtbearer-configuration"></a>Konfiguracja JwtBearer

W tej sekcji opisano sposób konfigurowania tokenu okaziciela.

### <a name="config-file"></a>Plik konfiguracji

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Przypadek użycia niestandardowego identyfikatora aplikacji dla internetowego interfejsu API

Jeśli zaakceptujesz identyfikator URI aplikacji proponowany przez portal rejestracji aplikacji, nie musisz określać odbiorców (zobacz [Identyfikator URI i zakresy identyfikatorów aplikacji](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). W przeciwnym razie należy dodać `Audience` Właściwość, której wartością jest identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Inicjowanie kodu

Gdy aplikacja jest wywoływana w ramach akcji kontrolera, która zawiera atrybut **[autoryzuje]** , ASP.NET i ASP.NET Core Wyodrębnij token dostępu z tokenu okaziciela nagłówka autoryzacji. Token dostępu jest następnie przekazywany do oprogramowania pośredniczącego JwtBearer, które wywołuje rozszerzenia Microsoft IdentityModel dla platformy .NET.

#### <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Firma Microsoft zaleca używanie pakietu NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) podczas tworzenia internetowego interfejsu API z ASP.NET Core.

_Microsoft. Identity. Web_ oferuje klej między ASP.NET Core, oprogramowanie pośredniczące uwierzytelniania i [Biblioteka Microsoft Authentication Library (MSAL)](msal-overview.md) dla platformy .NET. Pozwala to na wyraźniejsze i bardziej niezawodne środowisko programistyczne oraz wykorzystuje możliwości platformy tożsamości firmy Microsoft i Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Korzystanie z szablonów Microsoft. Identity. Web

Interfejs API sieci Web można utworzyć od podstaw przy użyciu szablonów projektów Microsoft. Identity. Web. Aby uzyskać szczegółowe informacje, zobacz [szablon projektu internetowego interfejsu API Microsoft. Identity. Web-Web](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Począwszy od istniejącej aplikacji ASP.NET Core 3,1

Obecnie ASP.NET Core 3,1 używa biblioteki Microsoft. AspNetCore. AzureAD. UI. Oprogramowanie pośredniczące jest inicjowane w pliku Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące jest dodawane do internetowego interfejsu API przez tę instrukcję:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Obecnie szablony ASP.NET Core tworzą interfejsy API sieci Web Azure Active Directory (Azure AD), które logują użytkowników w organizacji lub dowolnej organizacji. Użytkownicy nie logują się przy użyciu kont osobistych. Można jednak zmienić szablony w taki sposób, aby korzystały z platformy tożsamości firmy Microsoft przy użyciu [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) zastępując kod w *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

Możesz również napisać następujące (co jest równoważne)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Jeśli używasz Microsoft. Identity. Web i nie ustawisz `Audience` w *appsettings.jsna*, używane są następujące elementy:
> -  `$"{ClientId}"` w przypadku ustawienia [wersji zaakceptowanej tokenu dostępu](scenario-protected-web-api-app-registration.md#accepted-token-version) na `2` lub Azure AD B2C interfejsów API sieci Web.
> - `$"api://{ClientId}` we wszystkich innych przypadkach (dla [tokenów dostępu](access-tokens.md)w wersji 1.0).
> Aby uzyskać szczegółowe informacje, zobacz Microsoft. Identity. Web [Code Source](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83).

Poprzedni fragment kodu jest wyodrębniany z [wielostopniowego samouczka interfejsu API sieci web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). Szczegóły **AddMicrosoftIdentityWebApiAuthentication** są dostępne w [witrynie Microsoft. Identity. Web](microsoft-identity-web.md). Ta metoda wywołuje [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?preserve-view=true&view=azure-dotnet-preview), która sama nakazuje oprogramowaniu pośredniczącemu na sprawdzanie poprawności tokenu.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

W poprzednim fragmencie kodu JwtBearer oprogramowanie pośredniczące, takie jak OpenID Connect Połącz oprogramowanie pośredniczące w usłudze Web Apps, sprawdza token na podstawie wartości `TokenValidationParameters` . Token jest odszyfrowywany w razie konieczności, oświadczenia są wyodrębniane, a podpis zostanie zweryfikowany. Oprogramowanie pośredniczące sprawdza token, sprawdzając, czy są to dane:

- Odbiorcy: token jest przeznaczony dla internetowego interfejsu API.
- Sub: został wystawiony dla aplikacji, która może wywołać internetowy interfejs API.
- Wystawca: został wystawiony przez zaufaną usługę tokenu zabezpieczającego (STS).
- Wygaśnięcie: jego okres istnienia należy do zakresu.
- Sygnatura: nie została naruszona.

Mogą być również specjalne walidacje. Można na przykład sprawdzić, czy klucze podpisywania, gdy są osadzone w tokenie, są zaufane i że token nie jest odtwarzany. Na koniec niektóre protokoły wymagają określonych walidacji.

### <a name="validators"></a>Modułów sprawdzania

Kroki walidacji są przechwytywane w modułach walidacji, które są udostępniane przez [rozszerzenia Microsoft IdentityModel dla biblioteki Open Source dla programu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) . Moduły sprawdzania poprawności są zdefiniowane w pliku źródłowym biblioteki [Microsoft. IdentityModel. tokeny/walidators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

W tej tabeli opisano moduły sprawdzania poprawności:

| Walidacj | Opis |
|---------|---------|
| **ValidateAudience** | Zapewnia, że token jest przeznaczony dla aplikacji, która sprawdza poprawność tokenu. |
| **ValidateIssuer** | Zapewnia, że token został wystawiony przez zaufaną usługę STS, co oznacza, że pochodzi od zaufanej osoby. |
| **ValidateIssuerSigningKey** | Gwarantuje, że aplikacja walidacji tokenu ufa klucz, który został użyty do podpisania tokenu. Istnieje specjalny przypadek, w którym klucz jest osadzony w tokenie. Jednak ten przypadek zazwyczaj nie występuje. |
| **ValidateLifetime** | Zapewnia, że token jest nadal lub jest już ważny. Moduł sprawdzania poprawności sprawdza, czy okres istnienia tokenu znajduje się w zakresie określonym przez **NotBefore** i **wygasają** oświadczenia. |
| **ValidateSignature** | Zapewnia, że token nie został naruszony. |
| **ValidateTokenReplay** | Zapewnia, że token nie jest odtwarzany. W przypadku niektórych protokołów jednorazowej są stosowane specjalne przypadki. |

#### <a name="customizing-token-validation"></a>Dostosowywanie weryfikacji tokenu

Moduły walidacji są skojarzone z właściwościami klasy **TokenValidationParameters** . Właściwości są inicjowane z ASP.NET i konfiguracji ASP.NET Core.

W większości przypadków nie trzeba zmieniać parametrów. Aplikacje, które nie są pojedynczymi dzierżawcami, są wyjątkami. Te aplikacje sieci Web akceptują użytkowników z dowolnej organizacji lub z osobistych kont Microsoft. W takim przypadku należy sprawdzić poprawność wystawców. Firma Microsoft. Identity. Web zajmuje się również sprawdzaniem poprawności wystawcy. Aby uzyskać szczegółowe informacje, zobacz Microsoft. Identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

W ASP.NET Core, jeśli chcesz dostosować parametry sprawdzania poprawności tokenu, użyj następującego fragmentu kodu w *Startup.cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

W przypadku ASP.NET MVC Poniższy przykładowy kod pokazuje, jak przeprowadzić walidację tokenów niestandardowych:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Sprawdzanie poprawności tokenu w Azure Functions

Możesz również sprawdzić poprawność przychodzących tokenów dostępu w Azure Functions. Przykłady takiej weryfikacji można znaleźć w następujących przykładach kodu w witrynie GitHub:

- .NET: [Azure-Samples/MS-Identity-dotnet-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/MS-Identity-NodeJS-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/MS-Identity-Python-WebAPI-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Zweryfikuj zakresy i role aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md).