---
title: Zaloguj się przy użyciu poświadczenia hasła właściciela zasobu przyznać | Azure
titleSuffix: Microsoft identity platform
description: Obsługa przepływów uwierzytelniania bez przeglądarki przy użyciu poświadczenia hasła właściciela zasobu (ROPC).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a282264ed3e9539bcc96babfc41376d2c6c35628
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868649"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Platforma tożsamości firmy Microsoft i poświadczenia hasła właściciela zasobów OAuth 2.0

Platforma tożsamości firmy Microsoft obsługuje [przyznanie poświadczeń haseł właściciela zasobów OAuth 2.0 (ROPC),](https://tools.ietf.org/html/rfc6749#section-4.3)które umożliwia aplikacji logowanie użytkownika przez bezpośrednie obsługiwanie hasła.  W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji.  Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

> [!WARNING]
> Firma Microsoft _zaleca,_ aby nie używać przepływu ROPC. W większości scenariuszy dostępne i zalecane są bezpieczniejsze alternatywy. Przepływ ten wymaga bardzo wysokiego poziomu zaufania do aplikacji i niesie ze sobą ryzyko, które nie są obecne w innych przepływach. Tego przepływu należy używać tylko wtedy, gdy nie można używać innych bezpieczniejszych przepływów.

> [!IMPORTANT]
>
> * Punkt końcowy platformy tożsamości firmy Microsoft obsługuje tylko ROPC dla dzierżaw usługi Azure AD, a nie kont osobistych. Oznacza to, że należy użyć punktu`https://login.microsoftonline.com/{TenantId_or_Name}`końcowego `organizations` specyficzne dla dzierżawy ( ) lub punktu końcowego.
> * Konta osobiste zaproszone do dzierżawy usługi Azure AD nie mogą używać ropc.
> * Konta, które nie mają haseł, nie mogą się zalogować za pośrednictwem ROPC. W tym scenariuszu zaleca się użycie innego przepływu dla aplikacji zamiast tego.
> * Jeśli użytkownicy muszą używać uwierzytelniania wieloskładnikowego (MFA) do logowania się do aplikacji, zostaną one zablokowane zamiast tego.
> * Ropc nie jest obsługiwany w scenariuszach [federacji tożsamości hybrydowych](/azure/active-directory/hybrid/whatis-fed) (na przykład usługi Azure AD i usługi ADFS używane do uwierzytelniania kont lokalnych). Jeśli użytkownicy są przekierowani na całą stronę do lokalnych dostawców tożsamości, usługa Azure AD nie może przetestować nazwy użytkownika i hasła względem tego dostawcy tożsamości. [Uwierzytelnianie przekazywane](/azure/active-directory/hybrid/how-to-connect-pta) jest jednak obsługiwane przez ROPC.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono przepływ ROPC.

![Diagram przedstawiający przepływ poświadczeń hasła właściciela zasobu](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Wniosek o autoryzację

Przepływ ROPC jest pojedynczym żądaniem: wysyła identyfikator klienta i poświadczenia użytkownika do IDP, a następnie odbiera tokeny w zamian. Przed wykonaniem tej potrzeby klient musi zażądać adresu e-mail użytkownika (UPN) i hasła. Natychmiast po pomyślnym żądaniu klient powinien bezpiecznie zwolnić poświadczenia użytkownika z pamięci. Nigdy ich nie uratuje.

> [!TIP]
> Spróbuj wykonać to żądanie w Postman!
> [![Spróbuj uruchomić to żądanie w postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Wymagany | Dzierżawy katalogu, który chcesz zalogować użytkownika do. Może to być identyfikator GUID lub przyjazny format nazwy. Tego parametru nie można `common` `consumers`ustawić lub , `organizations`ale może być ustawiona na . |
| `client_id` | Wymagany | Identyfikator aplikacji (klienta), który [strona Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) jest przypisana do aplikacji. |
| `grant_type` | Wymagany | Musi być `password`ustawiona na . |
| `username` | Wymagany | Adres e-mail użytkownika. |
| `password` | Wymagany | Hasło użytkownika. |
| `scope` | Zalecane | Oddzielona spacja lista [zakresów](v2-permissions-and-consent.md)lub uprawnień, których wymaga aplikacja. W przepływie interaktywnym administrator lub użytkownik musi wyrazić zgodę na te zakresy z wyprzedzeniem. |
| `client_secret`| Czasami wymagane | Jeśli aplikacja jest klientem publicznym, to `client_secret` lub `client_assertion` nie mogą być uwzględnione.  Jeśli aplikacja jest klientem poufnym, musi zostać uwzględniona. |
| `client_assertion` | Czasami wymagane | Inna forma `client_secret`, generowane przy użyciu certyfikatu.  Aby uzyskać więcej [informacji, zobacz poświadczenia certyfikatu.](active-directory-certificate-credentials.md) |

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź na uwierzytelnianie

W poniższym przykładzie przedstawiono pomyślną odpowiedź tokenu:

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
| `token_type` | Ciąg | Zawsze ustawiaj na `Bearer`. |
| `scope` | Ciągi oddzielone przestrzenią | Jeśli token dostępu został zwrócony, ten parametr zawiera listę zakresów token dostępu jest prawidłowy dla. |
| `expires_in`| int | Liczba sekund, dla których token dostępu dołączonego jest prawidłowy. |
| `access_token`| Nieprzezroczysty ciąg | Wydane dla [zakresów,](v2-permissions-and-consent.md) które zostały wymagane. |
| `id_token` | Jwt | Wystawiono, jeśli `scope` oryginalny parametr `openid` zawierał zakres. |
| `refresh_token` | Nieprzezroczysty ciąg | Wydane, jeśli `scope` oryginalny parametr `offline_access`zawiera . |

Token odświeżania służy do uzyskiwania nowych tokenów dostępu i odświeżania tokenów przy użyciu tego samego przepływu opisanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpowiedź na błąd

Jeśli użytkownik nie podał poprawnej nazwy użytkownika lub hasła lub klient nie otrzymał żądanej zgody, uwierzytelnianie zakończy się niepowodzeniem.

| Błąd | Opis | Akcja klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Uwierzytelnianie nie powiodło się | Poświadczenia były niepoprawne lub klient nie ma zgody na żądane zakresy. Jeśli zakresy nie są przyznawane, zostanie zwrócony `consent_required` błąd. W takim przypadku klient powinien wysłać użytkownika do interakcyjnego monitu za pomocą widoku internetowego lub przeglądarki. |
| `invalid_request` | Wniosek został nieprawidłowo skonstruowany | Typ dotacji nie jest obsługiwany `/common` w `/consumers` kontekstach lub uwierzytelniania.  Zamiast `/organizations` tego użyj identyfikatora dzierżawy. |

## <a name="learn-more"></a>Dowiedz się więcej

* Wypróbuj ROPC dla siebie za pomocą [przykładowej aplikacji konsoli](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Aby ustalić, czy należy używać punktu końcowego w wersji 2.0, przeczytaj o [ograniczeniach platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).
