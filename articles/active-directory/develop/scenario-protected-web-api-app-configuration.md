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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882475"
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

Oto przykład kodu w języku C#, który pokazuje klienta wywołującego interfejs API po uzyskaniu tokenu z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET):

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
> Aplikacja kliencka żąda tokenu okaziciela do punktu końcowego platformy tożsamości firmy Microsoft *dla internetowego interfejsu API*. Internetowy interfejs API jest jedyną aplikacją, która powinna zweryfikować token i wyświetlić zawarte w nim oświadczenia. Aplikacje klienckie nigdy nie powinny podejmować próby sprawdzenia oświadczeń w tokenach.
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

### <a name="code-initialization"></a>Inicjowanie kodu

Gdy aplikacja jest wywoływana w ramach akcji kontrolera, która zawiera atrybut **[autoryzuje]** , ASP.NET i ASP.NET Core Wyodrębnij token dostępu z tokenu okaziciela nagłówka autoryzacji. Token dostępu jest następnie przekazywany do oprogramowania pośredniczącego JwtBearer, które wywołuje rozszerzenia Microsoft IdentityModel dla platformy .NET.

W ASP.NET Core to oprogramowanie pośredniczące jest inicjowane w pliku Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące jest dodawane do internetowego interfejsu API przez tę instrukcję:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Obecnie szablony ASP.NET Core tworzą interfejsy API sieci Web Azure Active Directory (Azure AD), które logują użytkowników w organizacji lub dowolnej organizacji. Użytkownicy nie logują się przy użyciu kont osobistych. Można jednak zmienić szablony tak, aby korzystały z punktu końcowego platformy tożsamości firmy Microsoft przez dodanie tego kodu do Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Poprzedni fragment kodu został wyodrębniony z ASP.NET Coreowego samouczka interfejsu API sieci Web w [witrynie Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoda **AddProtectedWebApi** , która wykonuje więcej niż fragment kodu, jest wywoływana z Startup.cs.

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

Moduły walidacji są skojarzone z właściwościami klasy **TokenValidationParameters** . Właściwości są inicjowane z ASP.NET i konfiguracji ASP.NET Core.

W większości przypadków nie trzeba zmieniać parametrów. Aplikacje, które nie są pojedynczymi dzierżawcami, są wyjątkami. Te aplikacje sieci Web akceptują użytkowników z dowolnej organizacji lub z osobistych kont Microsoft. W takim przypadku należy sprawdzić poprawność wystawców.

## <a name="token-validation-in-azure-functions"></a>Sprawdzanie poprawności tokenu w Azure Functions

Możesz również sprawdzić poprawność przychodzących tokenów dostępu w Azure Functions. Przykłady takiej weryfikacji można znaleźć w [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)i [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Weryfikowanie zakresów i ról aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md)
