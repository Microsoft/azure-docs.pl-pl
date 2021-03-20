---
title: Logowanie jednokrotne do aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs
description: Włącz logowanie jednokrotne dla opublikowanych aplikacji lokalnych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD w Azure Portal.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254006"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Przechowywanie haseł dla logowania jednokrotnego przy użyciu serwera proxy aplikacji

Serwer proxy aplikacji usługi Azure Active Directory ułatwia zwiększenie produktywności przez publikowanie aplikacji lokalnych, dzięki czemu zdalni pracownicy mogą również bezpiecznie uzyskać do nich dostęp. W Azure Portal można także skonfigurować Logowanie jednokrotne (SSO) do tych aplikacji. Użytkownicy muszą tylko uwierzytelniać się w usłudze Azure AD i mogą uzyskać dostęp do aplikacji przedsiębiorstwa bez konieczności ponownego logowania.

Serwer proxy aplikacji obsługuje kilka [trybów logowania](sso-options.md#choosing-a-single-sign-on-method)jednokrotnego. Logowanie oparte na hasłach jest przeznaczone dla aplikacji, które używają kombinacji nazwy użytkownika/hasła na potrzeby uwierzytelniania. Podczas konfigurowania logowania opartego na hasłach dla aplikacji użytkownicy muszą jednokrotnie zalogować się do aplikacji lokalnej. Po tym Azure Active Directory przechowuje informacje logowania i automatycznie udostępnia je aplikacji, gdy użytkownicy uzyskują do niej dostęp zdalnie.

Twoja aplikacja powinna już być opublikowana i przetestowana z serwerem proxy aplikacji. Jeśli nie, wykonaj kroki opisane w temacie [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md) następnie wróć tutaj.

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurowanie magazynu haseł dla aplikacji

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Z listy wybierz aplikację, którą chcesz skonfigurować przy użyciu logowania jednokrotnego.  
1. Wybierz pozycję **serwer proxy aplikacji**. 
1. Zmień **Typ uwierzytelniania wstępnego** na **Passthrough** i wybierz pozycję **Zapisz**. Później możesz ponownie wrócić do typu **Azure Active Directory** . 
1. Wybierz pozycję **Logowanie jednokrotne**.

   ![Wybierz pozycję Logowanie jednokrotne na stronie Przegląd aplikacji](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. W przypadku trybu logowania jednokrotnego wybierz pozycję **Logowanie oparte na haśle**.
1. W polu adres URL logowania wprowadź adres URL strony, na której użytkownicy wprowadzają nazwę użytkownika i hasło, aby zalogować się do aplikacji poza siecią firmową. Może to być zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji za pomocą serwera proxy aplikacji.

   ![Wybierz pozycję Logowanie oparte na hasłach i wprowadź adres URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **serwer proxy aplikacji**. 
1. Zmień **typ wstępnego uwierzytelniania** na **Azure Active Directory** i wybierz pozycję **Zapisz**. 
1. Wybierz pozycję **Użytkownicy i grupy**.
1. Przypisz użytkowników do aplikacji, wybierając pozycję **Dodaj użytkownika**. 
1. Jeśli chcesz wstępnie zdefiniować poświadczenia dla użytkownika, zaznacz pole wyboru Nazwa użytkownika i wybierz pozycję **Aktualizuj poświadczenia**.
1. Wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **wszystkie aplikacje**.
1. Z listy wybierz aplikację, którą skonfigurowano z logowaniem jednokrotnym przy użyciu hasła.
1. Wybierz **znakowanie**. 
1. Zaktualizuj **adres URL strony głównej** przy użyciu **adresu URL logowania na** stronie Logowanie jednokrotne, a następnie wybierz pozycję **Zapisz**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testowanie aplikacji

Przejdź do portalu My Apps. Zaloguj się przy użyciu swoich poświadczeń (lub poświadczeń dla konta testowego, które zostało skonfigurowane przy użyciu programu Access). Po pomyślnym zalogowaniu kliknij ikonę aplikacji. Może to spowodować zainstalowanie rozszerzenia przeglądarki Moje aplikacje bezpieczne logowanie. Jeśli użytkownik ma wstępnie zdefiniowane poświadczenia, uwierzytelnianie w aplikacji powinno nastąpić automatycznie. w przeciwnym razie należy określić nazwę użytkownika lub hasło po raz pierwszy. 

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o innych sposobach implementacji [logowania jednokrotnego](what-is-single-sign-on.md)
- Poznaj [kwestie dotyczące zabezpieczeń dotyczące zdalnego uzyskiwania dostępu do aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-security.md)
