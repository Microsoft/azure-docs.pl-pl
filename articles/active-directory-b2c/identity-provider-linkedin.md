---
title: Skonfiguruj konto i zaloguj się przy użyciu konta LinkedIn
titleSuffix: Azure AD B2C
description: Zalogować się do klientów i zaloguj się na kontach usługi LinkedIn w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188104"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn przy użyciu Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Tworzenie aplikacji LinkedIn

Aby użyć konta LinkedIn jako [dostawcy tożsamości](authorization-code-flow.md) w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta LinkedIn, możesz zarejestrować się w [https://www.linkedin.com/](https://www.linkedin.com/)usłudze.

1. Zaloguj się do [witryny sieci Web deweloperów serwisu LinkedIn](https://www.developer.linkedin.com/) przy użyciu poświadczeń konta usługi LinkedIn.
1. Wybierz pozycję **Moje aplikacje**, a następnie kliknij pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę firmy**, **nazwę aplikacji**, **Opis aplikacji**, **logo aplikacji**, **użycie aplikacji**, **adres URL witryny sieci Web**, **służbowe wiadomości e-mail**i **telefon służbowy**.
1. Zaakceptuj **warunki użytkowania interfejsu API usługi LinkedIn** i kliknij pozycję **Prześlij**.
1. Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Można je znaleźć w obszarze **klucze uwierzytelniania**. Oba te elementy będą potrzebne do skonfigurowania serwisu LinkedIn jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu **autoryzowane adresy URL przekierowań**. Zamień `your-tenant-name` na nazwę dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C. Wybierz pozycję **Dodaj**, a następnie kliknij pozycję **Aktualizuj**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurowanie konta LinkedIn jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **LinkedIn**.
1. Wprowadź **nazwę**. Na przykład *serwis LinkedIn*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta aplikacji LinkedIn, który został utworzony wcześniej.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do programu v 2.0

Serwis LinkedIn ostatnio [zaktualizował interfejsy API z wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). W ramach migracji Azure AD B2C jest w stanie uzyskać pełną nazwę użytkownika serwisu LinkedIn podczas rejestracji. Jeśli adres e-mail jest jednym z atrybutów, które są zbierane podczas rejestracji, użytkownik musi ręcznie wprowadzić adres e-mail i sprawdzić jego poprawność.
