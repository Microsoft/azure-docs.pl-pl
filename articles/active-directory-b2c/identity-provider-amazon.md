---
title: Konfigurowanie rejestracji i logowania przy użyciu konta Amazon
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi Amazon i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388446"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Amazon i zaloguj się na nim przy użyciu Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Tworzenie aplikacji w konsoli dewelopera usługi Amazon

Aby użyć konta Amazon jako dostawcy tożsamości federacyjnych w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w usłudze [Amazon Developer Services i technologiach](https://developer.amazon.com). Jeśli nie masz jeszcze konta usługi Amazon, możesz zarejestrować się w usłudze [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Użyj następujących adresów URL w **kroku 8** poniżej, zastępując `your-tenant-name` je nazwą dzierżawy. Wprowadzając nazwę dzierżawy, użyj wszystkich małych liter, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
> - Dla **dozwolonych źródeł**wpisz `https://your-tenant-name.b2clogin.com` 
> - Dla **dozwolonych zwrotnych adresów URL**wpisz `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Amazon**.
1. Wprowadź **nazwę**. Na przykład *Amazon*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta aplikacji Amazon, który został utworzony wcześniej.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
