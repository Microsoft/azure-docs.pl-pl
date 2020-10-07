---
title: Zaloguj się przy użyciu hasła właściciela zasobu Przyznaj | Azure
titleSuffix: Microsoft identity platform
description: Obsługa przepływów uwierzytelniania bez przeglądarki przy użyciu uprawnień hasła właściciela zasobu (ROPC).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 39cd25c2c84e92a0b06bc2ee6c6229ecb2d296d5
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812543"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Poświadczenia hasła właściciela zasobu Microsoft Identity platform i OAuth 2,0

Platforma tożsamości firmy Microsoft obsługuje [przyznanie poświadczeń hasła właściciela zasobu OAuth 2,0 (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), które umożliwia aplikacji Logowanie użytkownika przez bezpośrednią obsługę hasła.  W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

> [!WARNING]
> Firma Microsoft zaleca, aby _nie_ używać przepływu ROPC. W większości scenariuszy są dostępne i zalecane bardziej bezpieczne alternatywy. Ten przepływ wymaga bardzo wysokiego stopnia zaufania w aplikacji i przenosi ryzyko, które nie występują w innych przepływach. Tego przepływu należy używać tylko wtedy, gdy nie można użyć innych bardziej bezpiecznych przepływów.

> [!IMPORTANT]
>
> * Punkt końcowy platformy tożsamości firmy Microsoft obsługuje tylko ROPC dla dzierżawców usługi Azure AD, a nie konta osobiste. Oznacza to, że należy użyć punktu końcowego określonego dla dzierżawy ( `https://login.microsoftonline.com/{TenantId_or_Name}` ) lub `organizations` punktu końcowego.
> * Konta osobiste, które są zapraszane do dzierżawy usługi Azure AD, nie mogą używać ROPC.
> * Konta, które nie mają haseł, nie mogą się zalogować za poorednictwem ROPC. W tym scenariuszu zalecamy użycie w zamian innego przepływu dla aplikacji.
> * Jeśli użytkownicy muszą korzystać z [uwierzytelniania wieloskładnikowego (MFA)](../authentication/concept-mfa-howitworks.md) do logowania się do aplikacji, zostaną one zablokowane.
> * ROPC nie jest obsługiwane w scenariuszach [federacji tożsamości hybrydowej](../hybrid/whatis-fed.md) (na przykład usługi Azure AD i AD FS używane do uwierzytelniania kont lokalnych). Jeśli użytkownicy są przekierowani do lokalnych dostawców tożsamości, usługa Azure AD nie będzie w stanie testować nazwy użytkownika i hasła względem tego dostawcy tożsamości. [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md) jest jednak obsługiwane w przypadku ROPC.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono przepływ ROPC.

![Diagram przedstawiający przepływ poświadczeń hasła właściciela zasobu](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Żądanie autoryzacji

Przepływ ROPC jest pojedynczym żądaniem: wysyła identyfikator klienta i poświadczenia użytkownika do dostawcy tożsamości, a następnie odbiera tokeny zwracane przez program. Przed wykonaniem tych czynności klient musi zażądać adresu e-mail użytkownika (UPN) i hasła. Natychmiast po pomyślnym żądaniu klient powinien bezpiecznie zwolnić poświadczenia użytkownika z pamięci. Nigdy nie należy ich zapisywać.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster!
> [![Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Dzierżawa katalogu, w której ma być zalogowany użytkownik. Może to być w formacie identyfikatora GUID lub przyjaznej nazwy. Ten parametr nie może być ustawiony na wartość `common` lub `consumers` , ale może być ustawiony na `organizations` . |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `grant_type` | Wymagane | Musi być ustawiony na `password` . |
| `username` | Wymagane | Adres e-mail użytkownika. |
| `password` | Wymagane | Hasło użytkownika. |
| `scope` | Zalecane | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md)lub uprawnień wymaganych przez aplikację. W przepływie interaktywnym administrator lub użytkownik musi wyrazić zgodę na te zakresy przed czasem. |
| `client_secret`| Czasami wymagane | Jeśli aplikacja jest klientem publicznym, a następnie `client_secret` `client_assertion` nie można jej uwzględnić.  Jeśli aplikacja jest klientem poufnym, należy ją uwzględnić. |
| `client_assertion` | Czasami wymagane | Inna forma `client_secret` , wygenerowana przy użyciu certyfikatu.  Aby uzyskać więcej informacji, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md) . |

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź uwierzytelniania

W poniższym przykładzie przedstawiono Pomyślne odpowiedzi tokenu:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Format | Opis |
| --------- | ------ | ----------- |
| `token_type` | String | Zawsze ustawiaj na `Bearer` . |
| `scope` | Ciągi rozdzielone spacją | Jeśli został zwrócony token dostępu, ten parametr wyświetla listę zakresów, dla których token dostępu jest prawidłowy. |
| `expires_in`| int | Liczba sekund, przez jaką jest ważny włączony token dostępu. |
| `access_token`| Ciąg nieprzezroczysty | Wystawiony dla żądanych [zakresów](v2-permissions-and-consent.md) . |
| `id_token` | JWT | Wystawiony, jeśli oryginalny `scope` parametr zawiera `openid` zakres. |
| `refresh_token` | Ciąg nieprzezroczysty | Wystawiony, jeśli zostanie `scope` uwzględniony oryginalny parametr `offline_access` . |

Możesz użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu i odświeżać tokeny przy użyciu tego samego przepływu opisanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpowiedź na błąd

Jeśli użytkownik nie podał prawidłowej nazwy użytkownika lub hasła lub klient nie otrzyma żądanej zgody, uwierzytelnienie zakończy się niepowodzeniem.

| Błąd | Opis | Akcja klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Uwierzytelnianie nie powiodło się | Poświadczenia były nieprawidłowe lub klient nie ma zgody na żądane zakresy. Jeśli zakresy nie zostaną przyznane, `consent_required` zostanie zwrócony błąd. W takim przypadku klient powinien wysłać użytkownika do interakcyjnego monitu przy użyciu widoku WebView lub przeglądarki. |
| `invalid_request` | Żądanie zostało nieprawidłowo skonstruowane | Typ grantu nie jest obsługiwany w `/common` przypadku `/consumers` kontekstów uwierzytelniania lub.  Użyj `/organizations` zamiast tego identyfikatora dzierżawy. |

## <a name="learn-more"></a>Dowiedz się więcej

Przykład użycia ROPC można znaleźć w przykładowym kodzie [aplikacji konsoli .NET Core](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) w witrynie GitHub.
