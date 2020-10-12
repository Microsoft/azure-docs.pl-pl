---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi GitHub i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388191"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub za pomocą Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

Aby użyć konta usługi GitHub jako [dostawcy tożsamości](authorization-code-flow.md) w programie Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta usługi GitHub, możesz zarejestrować się w usłudze [https://www.github.com/](https://www.github.com/) .

1. Zaloguj się do witryny sieci Web dla [deweloperów usługi GitHub](https://github.com/settings/developers) przy użyciu swoich poświadczeń w usłudze GitHub.
1. Wybierz pozycję **aplikacje OAuth** , a następnie wybierz pozycję **Nowa aplikacja OAuth**.
1. Wprowadź **nazwę aplikacji** i **adres URL strony głównej**.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adres URL wywołania zwrotnego autoryzacji**. Zamień `your-tenant-name` na nazwę dzierżawy Azure AD B2C. Użyj wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
1. Kliknij pozycję **zarejestruj aplikację**.
1. Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Musisz dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **GitHub (wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład serwis *GitHub*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta utworzonej wcześniej aplikacji usługi GitHub.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
