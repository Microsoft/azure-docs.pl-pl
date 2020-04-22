---
title: Konfigurowanie logowania do organizacji usługi Azure AD
titleSuffix: Azure AD B2C
description: Konfigurowanie logowania dla określonej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678066"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla określonej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C

Aby użyć usługi Azure Active Directory (Azure AD) jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure AD B2C, należy utworzyć aplikację, która go reprezentuje. W tym artykule pokazano, jak włączyć logowanie dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w usłudze Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja katalogu +** w górnym menu i wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Nowy dostawca **OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź *contoso azure ad*.
1. W przypadku **adresu URL metadanych**wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. W przypadku **identyfikatora klienta**wprowadź identyfikator aplikacji, który został wcześniej zarejestrowany.
1. W przypadku **klucza tajnego klienta**wprowadź klucz tajny klienta, który został wcześniej zarejestrowany.
1. W przypadku **pola** `openid profile`Zakres wprowadź plik .
1. Pozostaw wartości domyślne dla **typu odpowiedzi**i **trybu odpowiedzi**.
1. (Opcjonalnie) Aby uzyskać **wskazówkę dotyczącą domeny,** wprowadź `contoso.com`. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości**wybierz następujące oświadczenia:

    * **Identyfikator użytkownika**: *oid*
    * **Nazwa wyświetlana**: *nazwa*
    * **Imię i nazwisko**: *given_name*
    * **Nazwisko**: *family_name*
    * **E-mail**: *unique_name*

1. Wybierz pozycję **Zapisz**.
