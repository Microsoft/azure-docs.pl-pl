---
title: Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C | Microsoft Docs
description: Informacje na temat wstępnego wypełniania nazwy logowania lub przekierowania bezpośrednio do dostawcy tożsamości społecznościowej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2342ed978204284bee4d2be0f1c983aa10ade36
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585041"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Podczas konfigurowania logowania do aplikacji przy użyciu Azure Active Directory (AD) B2C można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować się do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Wstępne wypełnianie nazwy logowania

Podczas podróży użytkowników w ramach logowania aplikacja jednostki uzależnionej może kierować do określonego użytkownika lub nazwy domeny. W przypadku kierowania użytkownika aplikacja może określić, w żądaniu autoryzacji, `login_hint` parametr zapytania z nazwą logowania użytkownika. Azure AD B2C automatycznie wypełnia nazwę logowania, a użytkownik musi podać hasło.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania login_hint wyróżnionego w adresie URL](./media/direct-signin/login-hint.png)

Użytkownik może zmienić wartość w polu tekstowym logowania.

::: zone pivot="b2c-custom-policy"

Aby zapewnić obsługę parametru wskazówki logowania, Zastąp `SelfAsserted-LocalAccountSignin-Email` profil techniczny. W `<InputClaims>` sekcji Ustaw właściwość DefaultValue dla signInName `{OIDC:LoginHint}` . `{OIDC:LoginHint}`Zmienna zawiera wartość `login_hint` parametru. Azure AD B2C odczytuje wartość żądania signInName i wstępnie wypełnia pole TextBox signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Przekieruj logowanie do dostawcy społeczności

Jeśli skonfigurowano podróż do logowania dla aplikacji w celu uwzględnienia kont społecznościowych, takich jak Facebook, LinkedIn lub Google, można określić `domain_hint` parametr. Ten parametr zapytania zawiera wskazówkę dotyczącą Azure AD B2C dostawcy tożsamości społecznościowej, który ma być używany do logowania. Na przykład jeśli aplikacja jest określana `domain_hint=facebook.com` , logowanie prowadzi bezpośrednio do strony logowania w serwisie Facebook.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania domain_hint wyróżnionego w adresie URL](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

Parametr ciągu zapytania dla wskazówki domeny można ustawić na jedną z następujących domen:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- W przypadku [ogólnych połączeń OpenID Connect](identity-provider-generic-openid-connect.md), zobacz [warunek domeny](identity-provider-generic-openid-connect.md#response-mode).

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby zapewnić obsługę parametru zawiasów domeny, można skonfigurować nazwę domeny za pomocą `<Domain>domain name</Domain>` elementu XML `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

