---
title: Weryfikowanie zakresów i chronionych interfejsów API sieci Web dla ról aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Sprawdź, czy interfejs API jest wywoływany tylko przez aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy i aplikacje demona, które mają odpowiednie role aplikacji.
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
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442909"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chroniony internetowy interfejs API: weryfikowanie zakresów i ról aplikacji

W tym artykule opisano sposób dodawania autoryzacji do internetowego interfejsu API. Ta ochrona zapewnia, że interfejs API jest wywoływany tylko przez:

- Aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy.
- Aplikacje demonów, które mają odpowiednie role aplikacji.

> [!NOTE]
> Fragmenty kodu w tym artykule są wyodrębniane z następujących przykładów kodu w witrynie GitHub:
>
> - [Samouczek przyrostowy interfejsu Web API ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [Przykład interfejsu API sieci Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

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

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Weryfikuj zakresy dla każdej akcji kontrolera

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope`Metoda wykonuje podobne czynności:

- Sprawdź, czy istnieje zgłoszenie o nazwie `http://schemas.microsoft.com/identity/claims/scope` lub `scp` .
- Sprawdź, czy element Claim ma wartość zawierającą zakres oczekiwany przez interfejs API.


#### <a name="verify-the-scopes-more-globally"></a>Weryfikuj zakresy bardziej globalnie

Zalecanym podejściem jest zdefiniowanie szczegółowych zakresów dla internetowego interfejsu API i zweryfikowanie zakresów w każdej akcji kontrolera. Możliwe jest jednak również zweryfikowanie zakresów na poziomie aplikacji lub kontrolera za pomocą ASP.NET Core. Aby uzyskać szczegółowe informacje, zobacz [Autoryzacja oparta na żądaniach](/aspnet/core/security/authorization/claims) w dokumentacji ASP.NET Core.

### <a name="net-mvc"></a>.NET MVC

W przypadku ASP.NET, po prostu Zastąp `HttpContext.User` `ClaimsPrincipal.Current` wartość i Zastąp typ " `"http://schemas.microsoft.com/identity/claims/scope"` z `"scp"` . Zobacz również fragment kodu w dalszej części tego artykułu.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie ról aplikacji w interfejsach API wywoływanych przez aplikacje demona

Jeśli internetowy interfejs API jest wywoływany przez [aplikację demona](scenario-daemon-overview.md), aplikacja powinna wymagać uprawnień aplikacji do internetowego interfejsu API. Jak pokazano w temacie [udostępnianie uprawnień aplikacji (ról aplikacji)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), interfejs API ujawnia takie uprawnienia. Przykładem jest `access_as_application` rola aplikacji.

Teraz musisz mieć interfejs API, aby sprawdzić, czy otrzymany token zawiera `roles` zastrzeżenie i czy to zgłoszenie ma oczekiwaną wartość. Kod weryfikacyjny jest podobny do kodu, który weryfikuje delegowane uprawnienia, z tą różnicą, że akcja kontrolera testuje role zamiast zakresów:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole`Metoda jest definiowana w Microsoft. Identity. Web w [RolesRequiredHttpContextExtensions.cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28).

### <a name="aspnet-mvc"></a>ASP.NET MVC

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

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-protected-web-api-production.md).
