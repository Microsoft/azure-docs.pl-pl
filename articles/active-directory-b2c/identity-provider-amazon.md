---
title: Konfigurowanie rejestracji i logowania przy użyciu konta Amazon
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi Amazon i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188464"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Amazon i zaloguj się na nim przy użyciu Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon

Aby użyć konta Amazon jako [dostawcy tożsamości](authorization-code-flow.md) w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta usługi Amazon, możesz zarejestrować się w [https://www.amazon.com/](https://www.amazon.com/)usłudze.

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta usługi Amazon.
1. Jeśli jeszcze tego nie zrobiono, kliknij pozycję **Utwórz konto**, postępuj zgodnie z procedurami rejestracji dla deweloperów i zaakceptuj zasady.
1. Wybierz pozycję **zarejestruj nową aplikację**.
1. Wprowadź **nazwę**, **Opis**i **adres URL informacji o ochronie prywatności**, a następnie kliknij przycisk **Zapisz**. Powiadomienie o zasadach zachowania poufności informacji to strona, którą można zarządzać, która zapewnia użytkownikom informacje o ochronie prywatności.
1. W sekcji **Ustawienia sieci Web** Skopiuj wartości **Identyfikator klienta**. Wybierz pozycję **Pokaż klucz tajny** , aby uzyskać klucz tajny klienta, a następnie skopiuj go. Musisz mieć oba te elementy, aby skonfigurować konto Amazon jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. W sekcji **Ustawienia sieci Web** wybierz pozycję **Edytuj**, a następnie wprowadź `https://your-tenant-name.b2clogin.com` w polu **dozwolone źródła języka JavaScript** i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu **dozwolone adresy URL**. Zamień `your-tenant-name` na nazwę dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
1. Kliknij przycisk **Zapisz**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Amazon**.
1. Wprowadź **nazwę**. Na przykład *Amazon*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta aplikacji Amazon, który został utworzony wcześniej.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
