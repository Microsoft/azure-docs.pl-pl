---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951538"
---
## <a name="configure-the-relying-party-policy"></a>Konfigurowanie zasad jednostki uzależnionej

Zasady jednostki uzależnionej, na przykład [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), określają podróż użytkownika, która Azure AD B2C zostanie wykonana. Znajdź element **DefaultUserJourney** w ramach jednostki [uzależnionej](../articles/active-directory-b2c/relyingparty.md). Zaktualizuj  **ReferenceId** w taki sposób, aby odpowiadał identyfikatorowi podróży użytkownika, w którym dodano dostawcę tożsamości. 

W poniższym przykładzie w przypadku `CustomSignUpOrSignIn` podróży użytkownika **ReferenceId** jest ustawiona na `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Przekazywanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż dwa zmienione pliki zasad w następującej kolejności: zasady rozszerzenia, na przykład `TrustFrameworkExtensions.xml` zasady jednostki uzależnionej, takie jak `SignUpSignIn.xml` .



