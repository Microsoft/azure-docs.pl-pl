---
title: Weryfikowanie zakresów i chronionych interfejsów API sieci Web dla ról aplikacji | Azure
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
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83845139"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chroniony internetowy interfejs API: weryfikowanie zakresów i ról aplikacji

W tym artykule opisano sposób dodawania autoryzacji do internetowego interfejsu API. Ta ochrona zapewnia, że interfejs API jest wywoływany tylko przez:

- Aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy.
- Aplikacje demonów, które mają odpowiednie role aplikacji.

> [!NOTE]
> Fragmenty kodu z tego artykułu są wyodrębniane z następujących przykładów, które są w pełni funkcjonalne:
>
> - [Samouczek przyrostowy interfejsu Web API ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) w witrynie GitHub
> - [Przykład interfejsu API sieci Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Aby chronić interfejs API sieci Web ASP.NET lub ASP.NET Core, należy dodać `[Authorize]` atrybut do jednego z następujących elementów:

- Kontroler, jeśli wszystkie akcje kontrolera mają być chronione
- Akcja pojedynczego kontrolera dla interfejsu API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Jednak ta ochrona jest za mała. Gwarantuje tylko, że ASP.NET i ASP.NET Core weryfikują token. Interfejs API musi sprawdzić, czy token używany do wywoływania interfejsu API jest wymagany z oczekiwanymi oświadczeniami. Te oświadczenia w szczególności wymagają weryfikacji:

- *Zakresy* , jeśli interfejs API jest wywoływany w imieniu użytkownika.
- *Role aplikacji* , jeśli interfejs API można wywołać z poziomu aplikacji demona.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Weryfikuj zakresy w interfejsach API wywoływanych w imieniu użytkowników

Jeśli aplikacja kliencka wywołuje interfejs API w imieniu użytkownika, interfejs API musi zażądać tokenu okaziciela z określonymi zakresami dla interfejsu API. Aby uzyskać więcej informacji, zobacz temat [Konfiguracja kodu | Token elementu nośnego](scenario-protected-web-api-app-configuration.md#bearer-token).

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

`VerifyUserHasAnyAcceptedScope`Metoda wykonuje podobne czynności:

- Sprawdź, czy istnieje zgłoszenie o nazwie `http://schemas.microsoft.com/identity/claims/scope` lub `scp` .
- Sprawdź, czy element Claim ma wartość zawierającą zakres oczekiwany przez interfejs API.

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

Poprzedni [przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) jest przeznaczony dla ASP.NET Core. W przypadku ASP.NET, po prostu Zastąp `HttpContext.User` `ClaimsPrincipal.Current` wartość i Zastąp typ " `"http://schemas.microsoft.com/identity/claims/scope"` z `"scp"` . Zobacz również fragment kodu w dalszej części tego artykułu.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie ról aplikacji w interfejsach API wywoływanych przez aplikacje demona

Jeśli internetowy interfejs API jest wywoływany przez [aplikację demona](scenario-daemon-overview.md), aplikacja powinna wymagać uprawnień aplikacji do internetowego interfejsu API. Jak pokazano w temacie [udostępnianie uprawnień aplikacji (ról aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), interfejs API ujawnia takie uprawnienia. Przykładem jest `access_as_application` rola aplikacji.

Teraz musisz mieć interfejs API, aby sprawdzić, czy otrzymany token zawiera `roles` zastrzeżenie i czy to zgłoszenie ma oczekiwaną wartość. Kod weryfikacyjny jest podobny do kodu, który weryfikuje delegowane uprawnienia, z tą różnicą, że akcja kontrolera testuje role zamiast zakresów:

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

`ValidateAppRole`Metoda może wyglądać następująco:

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

Tym razem fragment kodu jest przeznaczony dla ASP.NET. W przypadku ASP.NET Core po prostu Zastąp ciąg `ClaimsPrincipal.Current` opcją `HttpContext.User` i Zastąp ciąg `"roles"` nazwą usługi `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"` . Zobacz również fragment kodu znajdujący się wcześniej w tym artykule.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akceptowanie tokenów tylko do aplikacji, jeśli internetowy interfejs API powinien być wywoływany tylko przez aplikacje demona

Użytkownicy mogą również używać oświadczeń ról w wzorcach przypisania użytkownika, jak pokazano w [instrukcje: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](howto-add-app-roles-in-azure-ad-apps.md). Jeśli role można przypisać do obu, sprawdzanie ról umożliwi aplikacjom logowanie się jako użytkownicy i użytkownicy w celu zalogowania się jako aplikacje. Zalecamy zadeklarować różne role dla użytkowników i aplikacji, aby zapobiec tym nieporozumieniu.

Jeśli chcesz, aby tylko aplikacje demona wywoływały interfejs API sieci Web, Dodaj warunek, który token jest tokenem "App-Only" podczas weryfikacji roli aplikacji.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Sprawdzanie warunku odwrotnego zezwala tylko na aplikacje, które logują się do użytkownika w celu wywołania interfejsu API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-protected-web-api-production.md)
