---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi Twitter
titleSuffix: Azure AD B2C
description: Zalogować się do klientów za pomocą kont usługi Twitter w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051463"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Twitter i zaloguj się przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby używać usługi Twitter jako dostawcy tożsamości w Azure AD B2C, musisz utworzyć aplikację w usłudze Twitter. Jeśli nie masz jeszcze konta usługi Twitter, możesz zarejestrować się w [https://twitter.com/signup](https://twitter.com/signup)usłudze.

1. Zaloguj się do witryny sieci Web [deweloperów usługi Twitter](https://developer.twitter.com/en/apps) przy użyciu poświadczeń konta w usłudze Twitter.
1. Wybierz pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji** i **Opis aplikacji**.
1. W polu **adres URL witryny sieci Web**wprowadź `https://your-tenant.b2clogin.com`. Zamień `your-tenant` na nazwę dzierżawy. Na przykład `https://contosob2c.b2clogin.com`.
1. W polu **adres URL wywołania zwrotnego**wprowadź `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`wartość. Zastąp `your-tenant` wartość nazwą swojej dzierżawy i `your-user-flow-Id` identyfikatorem przepływu użytkownika. Na przykład `b2c_1A_signup_signin_twitter`. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy i identyfikator przepływu użytkownika, nawet jeśli są one zdefiniowane przy użyciu wielkich liter w Azure AD B2C.
1. W dolnej części strony Przeczytaj i zaakceptuj warunki, a następnie wybierz pozycję **Utwórz**.
1. Na stronie **Szczegóły aplikacji** wybierz opcję **Edytuj > Edytuj szczegóły**, zaznacz pole wyboru **Włącz logowanie przy użyciu usługi Twitter**, a następnie wybierz pozycję **Zapisz**.
1. Wybierz **klucze i tokeny** , a następnie Zapisz **klucz interfejsu API konsumenta** oraz wartości **klucza tajnego interfejsu API konsumenta** , aby użyć ich później.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Twitter**.
1. Wprowadź **nazwę**. Na przykład *serwis Twitter*.
1. W polu **Identyfikator klienta**wprowadź klucz interfejsu API klienta utworzonego wcześniej.
1. Dla **wpisu tajnego klienta**Wprowadź zarejestrowany klucz tajny interfejsu API konsumenta.
1. Wybierz pozycję **Zapisz**.
