---
title: Skonfiguruj konto i zaloguj się przy użyciu konta LinkedIn
titleSuffix: Azure AD B2C
description: Zalogować się do klientów i zaloguj się na kontach usługi LinkedIn w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259462"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn przy użyciu Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Tworzenie aplikacji LinkedIn

Aby użyć konta LinkedIn jako [dostawcy tożsamości](authorization-code-flow.md) w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta LinkedIn, możesz zarejestrować się w usłudze [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Zaloguj się do [witryny sieci Web deweloperów serwisu LinkedIn](https://www.developer.linkedin.com/) przy użyciu poświadczeń konta usługi LinkedIn.
1. Wybierz pozycję **Moje aplikacje**, a następnie kliknij pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji**, **stronę serwisu LinkedIn**, **adres URL zasad ochrony prywatności**i **logo aplikacji**.
1. Zaakceptuj **warunki użytkowania interfejsu API** usługi LinkedIn i kliknij pozycję **Utwórz aplikację**.
1. Wybierz kartę **uwierzytelnianie** . W obszarze **klucze uwierzytelniania**Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Musisz mieć oba te elementy, aby skonfigurować konto LinkedIn jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. Wybierz pozycję Edytuj ołówek obok pozycji **autoryzowane adresy URL przekierowań dla aplikacji**, a następnie wybierz pozycję **Dodaj adres URL przekierowania**. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` wartość, zastępując `your-tenant-name` ją nazwą dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C. Wybierz pozycję **Aktualizuj**.
2. Domyślnie aplikacja LinkedIn nie jest zatwierdzona dla zakresów związanych z logowaniem. Aby zażądać przeglądu, wybierz kartę **produkty** , a następnie wybierz pozycję **Zaloguj się przy użyciu konta LinkedIn**. Po zakończeniu przeglądu wymagane zakresy zostaną dodane do aplikacji.
   > [!NOTE]
   > Zakresy, które są obecnie dozwolone dla aplikacji, można wyświetlić na karcie **uwierzytelnianie** w sekcji **zakresy OAuth 2,0** .

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
