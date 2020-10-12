---
title: Skonfiguruj konto i zaloguj się przy użyciu konta Weibo
titleSuffix: Azure AD B2C
description: Podaj konto i zaloguj się do klientów za pomocą kont usługi Weibo w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 08aa7e4af6dc5d5e5bff470bc4c5d023e25b3014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387885"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania przy użyciu konta usługi Weibo za pomocą Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Tworzenie aplikacji Weibo

Aby użyć konta Weibo jako dostawcy tożsamości w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta Weibo, możesz zarejestrować się w usłudze [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) .

1. Zaloguj się do [portalu deweloperów Weibo](https://open.weibo.com/) przy użyciu poświadczeń konta Weibo.
1. Po zalogowaniu wybierz swoją nazwę wyświetlaną w prawym górnym rogu.
1. Z listy rozwijanej wybierz pozycję **编辑开发者信息** (Edytuj informacje dla deweloperów).
1. Wprowadź wymagane informacje i wybierz pozycję **提交** (Prześlij).
1. Ukończ proces weryfikacji poczty e-mail.
1. Przejdź do [strony weryfikacji tożsamości](https://open.weibo.com/developers/identity/edit).
1. Wprowadź wymagane informacje i wybierz pozycję **提交** (Prześlij).

### <a name="register-a-weibo-application"></a>Rejestrowanie aplikacji Weibo

1. Przejdź do [strony Nowa rejestracja aplikacji Weibo](https://open.weibo.com/apps/new).
1. Wprowadź wymagane informacje o aplikacji.
1. Wybierz pozycję **创建** (Utwórz).
1. Skopiuj wartości **klucza aplikacji** i **wpisu tajnego aplikacji**. Musisz mieć oba te elementy, aby dodać dostawcę tożsamości do dzierżawy.
1. Przekaż wymagane Zdjęcia i wprowadź wymagane informacje.
1. Wybierz pozycję **保存以上信息** (Zapisz).
1. Wybierz pozycję **高级信息** (informacje zaawansowane).
1. Wybierz pozycję **编辑** (Edytuj) obok pola dla protokołu OAuth 2.0 **授权设置** (adres URL przekierowania).
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w przypadku protokołu OAuth 2.0 **授权设置** (adres URL przekierowania). Na przykład jeśli nazwa dzierżawy to contoso, ustaw adres URL na `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Wybierz pozycję **提交** (Prześlij).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurowanie konta Weibo jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Weibo (wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład *Weibo*.
1. W polu **Identyfikator klienta**wprowadź klucz aplikacji utworzonej wcześniej aplikacji Weibo.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny aplikacji.
1. Wybierz pozycję **Zapisz**.
