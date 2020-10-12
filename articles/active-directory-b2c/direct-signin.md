---
title: Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C | Microsoft Docs
description: Informacje na temat wstępnego wypełniania nazwy logowania lub przekierowania bezpośrednio do dostawcy tożsamości społecznościowej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388599"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C

Podczas konfigurowania logowania do aplikacji przy użyciu Azure Active Directory (AD) B2C można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować się do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Wstępne wypełnianie nazwy logowania

Podczas podróży użytkowników w ramach logowania aplikacja jednostki uzależnionej może kierować do określonego użytkownika lub nazwy domeny. W przypadku kierowania użytkownika aplikacja może określić, w żądaniu autoryzacji, `login_hint` parametr zapytania z nazwą logowania użytkownika. Azure AD B2C automatycznie wypełnia nazwę logowania, a użytkownik musi podać hasło.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania login_hint wyróżnionego w adresie URL](./media/direct-signin/login-hint.png)

Użytkownik może zmienić wartość w polu tekstowym logowania.

Jeśli używasz zasad niestandardowych, Zastąp `SelfAsserted-LocalAccountSignin-Email` profil techniczny. W `<InputClaims>` sekcji Ustaw właściwość DefaultValue dla signInName `{OIDC:LoginHint}` . `{OIDC:LoginHint}`Zmienna zawiera wartość `login_hint` parametru. Azure AD B2C odczytuje wartość żądania signInName i wstępnie wypełnia pole TextBox signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Przekieruj logowanie do dostawcy społeczności

Jeśli skonfigurowano podróż do logowania dla aplikacji w celu uwzględnienia kont społecznościowych, takich jak Facebook, LinkedIn lub Google, można określić `domain_hint` parametr. Ten parametr zapytania zawiera wskazówkę dotyczącą Azure AD B2C dostawcy tożsamości społecznościowej, który ma być używany do logowania. Na przykład jeśli aplikacja jest określana `domain_hint=facebook.com` , logowanie prowadzi bezpośrednio do strony logowania w serwisie Facebook.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania domain_hint wyróżnionego w adresie URL](./media/direct-signin/domain-hint.png)

Jeśli używasz zasad niestandardowych, możesz skonfigurować nazwę domeny za pomocą `<Domain>domain name</Domain>` elementu XML dowolnego `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


