---
title: Weryfikowanie zakresów i ról aplikacji chronionych interfejsem API sieci Web | Azure
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
ms.openlocfilehash: 018e7f9bc389e3d148ff6860dae9fef88991e5c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537172"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chroniony interfejs API sieci Web: weryfikowanie zakresów i ról aplikacji

W tym artykule opisano, jak można dodać autoryzację do internetowego interfejsu API. Ta ochrona gwarantuje, że interfejs API jest wywoływany tylko przez:

- Aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy.
- Aplikacje demonów, które mają odpowiednie role aplikacji.

> [!NOTE]
> Fragmenty kodu z tego artykułu są wyodrębniane z następujących przykładów, które są w pełni funkcjonalne:
>
> - [ASP.NET Core web API przyrostowy samouczek](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) na GitHub
> - [ASP.NET przykładowy internetowy interfejs API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Aby chronić ASP.NET lub ASP.NET core interfejsu API sieci `[Authorize]` web, należy dodać atrybut do jednego z następujących elementów:

- Sam kontroler, jeśli chcesz, aby wszystkie akcje kontrolera były chronione
- Akcja indywidualnego kontrolera dla interfejsu API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ale ta ochrona nie wystarczy. Gwarantuje tylko, że ASP.NET i ASP.NET Core sprawdź poprawność tokenu. Interfejs API musi sprawdzić, czy token używany do wywoływania interfejsu API jest wymagany z oczekiwanymi roszczeniami. Oświadczenia te wymagają w szczególności weryfikacji:

- *Zakresy,* jeśli interfejs API jest wywoływany w imieniu użytkownika.
- *Role aplikacji,* jeśli interfejs API można wywołać z aplikacji demona.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Weryfikowanie zakresów w interfejsach API wywoływanych w imieniu użytkowników

Jeśli aplikacja kliencka wywołuje interfejs API w imieniu użytkownika, interfejs API musi zażądać tokenu nośnika, który ma określone zakresy dla interfejsu API. Aby uzyskać więcej informacji, zobacz [Konfiguracja kodu | Token na okaziciela](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

Metoda `VerifyUserHasAnyAcceptedScope` wykonuje podobne kroki:

- Sprawdź, czy istnieje `http://schemas.microsoft.com/identity/claims/scope` roszczenie `scp`o nazwie lub .
- Sprawdź, czy oświadczenie ma wartość, która zawiera zakres oczekiwany przez interfejs API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Poprzedni [przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) jest dla ASP.NET Core. Dla ASP.NET `HttpContext.User` wystarczy zastąpić , `ClaimsPrincipal.Current`i zastąpić typ `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`oświadczenia . Zobacz też fragment kodu w dalszej części tego artykułu.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie ról aplikacji w interfejsach API wywoływanych przez aplikacje demonów

Jeśli internetowy interfejs API jest wywoływany przez [aplikację demona,](scenario-daemon-overview.md)ta aplikacja powinna wymagać uprawnień aplikacji do internetowego interfejsu API. Jak pokazano w [uwidacznianie uprawnień aplikacji (role aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)interfejs API udostępnia takie uprawnienia. Jednym z `access_as_application` przykładów jest rola aplikacji.

Teraz musisz mieć interfejsu API sprawdzić, czy `roles` token odbiera zawiera oświadczenie i że to oświadczenie ma wartość oczekiwaną. Kod weryfikacyjny jest podobny do kodu, który weryfikuje uprawnienia delegowane, z tą różnicą, że akcja kontrolera testuje role zamiast zakresów:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Metoda `ValidateAppRole` może być mniej więcej tak:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Tym razem fragment kodu jest dla ASP.NET. Dla ASP.NET Core wystarczy `ClaimsPrincipal.Current` zastąpić `HttpContext.User`nazwą `"roles"` oświadczenia i zastąpić `"http://schemas.microsoft.com/identity/claims/roles"`. Zobacz też fragment kodu wcześniej w tym artykule.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akceptowanie tokenów tylko do aplikacji, jeśli internetowy interfejs API powinien być wywoływany tylko przez aplikacje demonów

Użytkownicy mogą również używać oświadczeń ról we wzorcach przypisania użytkowników, jak pokazano w [jak: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie.](howto-add-app-roles-in-azure-ad-apps.md) Jeśli role można przypisać do obu, role sprawdzania pozwoli aplikacjom zalogować się jako użytkownicy i użytkownicy, aby zalogować się jako aplikacje. Zaleca się zadeklarowanie różnych ról dla użytkowników i aplikacji, aby zapobiec temu zamieszaniu.

Jeśli chcesz, aby tylko aplikacje demonów, aby wywołać interfejs API sieci web, należy dodać warunek, że token jest token tylko do aplikacji podczas sprawdzania poprawności roli aplikacji.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Sprawdzanie odwrotnego warunku umożliwia tylko aplikacjom, które logują się do użytkownika, aby wywołać interfejs API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-protected-web-api-production.md)
