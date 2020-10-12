---
title: Konfigurowanie logowania dla organizacji usługi Azure AD
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie do konkretnej organizacji Azure Active Directory w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004463"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla konkretnej organizacji Azure Active Directory w programie Azure Active Directory B2C

Aby użyć Azure Active Directory (Azure AD) jako [dostawcy tożsamości](authorization-code-flow.md) w Azure AD B2C, należy utworzyć aplikację, która go reprezentuje. W tym artykule opisano sposób włączania logowania dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź nazwę *contoso Azure AD*.
1. W polu **adres URL metadanych**wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Na przykład `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. W polu **Identyfikator klienta**wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **zakresu**, wprowadź `openid profile` .
1. Pozostaw wartości domyślne dla **typu odpowiedzi**i **trybu odpowiedzi**.
1. Obowiązkowe Dla wskazówki dotyczącej **domeny**wprowadź wartość `contoso.com` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości**wybierz następujące oświadczenia:

    * **Identyfikator użytkownika**: *OID*
    * **Nazwa wyświetlana**: *Nazwa*
    * **Imię:** *given_name*
    * **Nazwisko**: *family_name*
    * **Adres e-mail**: *unique_name*

1. Wybierz pozycję **Zapisz**.
