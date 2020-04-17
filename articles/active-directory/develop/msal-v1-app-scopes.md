---
title: Zakresy aplikacji w wersji 1.0 (MSAL) | Azure
description: Dowiedz się więcej o zakresach aplikacji w wersji 1.0 przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536186"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptującego tokeny w wersji 1.0

Uprawnienia OAuth2 są zakresami uprawnień udostępnianych aplikacjom klienckim usługi Azure Active Directory (Azure AD) dla deweloperów (w wersji 1.0) w sieci Web API (zasobie). Te zakresy uprawnień mogą być przyznawane aplikacjom klienckim podczas udzielania zgody. Zobacz sekcję `oauth2Permissions` dotyczącą odwołania [do manifestu aplikacji usługi Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji w wersji 1.0

Aby uzyskać tokeny dla określonych zakresów aplikacji w wersji 1.0 (na przykład interfejsu API programu Microsoft Graph, czyli https://graph.microsoft.com)tworzenie zakresów przez łączenie żądanego identyfikatora zasobu z żądanym uprawnieniem OAuth2 dla tego zasobu.

Na przykład, aby uzyskać dostęp w imieniu użytkownika do internetowego interfejsu API `ResourceId`w wersji 1.0, w którym identyfikator URI identyfikatora aplikacji jest:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Aby odczytywać i pisać za pomocą MSAL.NET usługi Azure\/AD przy użyciu interfejsu API programu Microsoft Graph (https: /graph.microsoft.com/), należy utworzyć listę zakresów, jak pokazano w poniższych przykładach:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Aby zapisać zakres odpowiadający interfejsowi API usługi\/Azure Resource Manager (https: /management.core.windows.net/), należy zażądać następującego zakresu (zwróć uwagę na dwa ukośniki):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Należy użyć dwóch ukośników, ponieważ interfejs API usługi Azure Resource Manager oczekuje ukośnika w oświadczeniu odbiorców (aud), a następnie istnieje ukośnik, aby oddzielić nazwę interfejsu API od zakresu.

Logika używana przez usługę Azure AD jest następująca:

- Dla punktu końcowego usługi ADAL (Azure AD w wersji 1.0) z tokenem dostępu w wersji 1.0 (jedyny możliwy), aud=resource
- Dla punktu końcowego MSAL (Microsoft identity platform (v2.0)) z prośbą o token dostępu dla zasobu akceptującego tokeny w wersji 2.0,`aud=resource.AppId`
- W przypadku usługi MSAL (punktu końcowego w wersji 2.0) z prośbą o token dostępu dla zasobu, który akceptuje token dostępu w wersji 1.0 (co jest w powyższym przypadku), usługa Azure AD analizuje żądaną grupę odbiorców z żądanego zakresu, biorąc wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. W związku z tym, jeśli https:\//database.windows.net oczekuje odbiorców\/"https: /database.windows.net/", musisz poprosić o zakres\/"https: /database.windows.net//.default". Zobacz także GitHub problem [#747: Url zasobów końcowe ukośnika jest pomijany, co spowodowało niepowodzenie sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do wszystkich uprawnień aplikacji w wersji 1.0

Jeśli chcesz uzyskać token dla wszystkich zakresów statycznych aplikacji w wersji 1.0, dołącz ".default" do identyfikatora URI identyfikatora aplikacji interfejsu API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Zakresy do żądania przepływu poświadczeń klienta/aplikacji demona

W przypadku przepływu poświadczeń klienta zakres `/.default`do przekazania będzie również . To mówi do usługi Azure AD: "wszystkie uprawnienia na poziomie aplikacji, które administrator wyraził zgodę w rejestracji aplikacji.
