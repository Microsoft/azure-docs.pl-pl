---
title: Skonfiguruj zachowanie sesji — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować zachowanie sesji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961038"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurowanie zachowania sesji w Azure Active Directory B2C

Usługa logowania jednokrotnego [(SSO)](session-overview.md) w programie Azure Active Directory B2C (Azure AD B2C) umożliwia administratorowi sterowanie interakcją z użytkownikiem po jego uwierzytelnieniu. Administrator może na przykład określić, czy jest wyświetlany wybór dostawców tożsamości, czy też należy wprowadzić ponownie szczegóły konta. W tym artykule opisano sposób konfigurowania ustawień logowania jednokrotnego dla Azure AD B2C.

## <a name="session-behavior-properties"></a>Właściwości zachowania sesji

Aby zarządzać sesjami aplikacji sieci Web, można użyć następujących właściwości:

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — okres istnienia pliku cookie sesji usługi Azure AD B2C's w przeglądarce użytkownika po pomyślnym uwierzytelnieniu.
    - Wartość domyślna = 1440 min.
    - Minimum (włącznie) = 15 minut.
    - Maksimum (włącznie) = 1440 min.
- **Limit czasu sesji aplikacji sieci Web** — [Typ wygaśnięcia sesji](session-overview.md#session-expiry-type), wartość *krocząca*lub *bezwzględna*. 
- **Konfiguracja logowania** jednokrotnego — [zakres sesji](session-overview.md#session-scope) zachowań logowania jednokrotnego (SSO) w wielu aplikacjach i przepływach użytkowników w dzierżawie Azure AD B2C.


## <a name="configure-the-properties"></a>Konfigurowanie właściwości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika**.
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz pozycję **Właściwości**.
7. Skonfiguruj **okres istnienia sesji aplikacji sieci Web (minuty)**, **limit czasu sesji aplikacji sieci Web**, **konfigurację logowania**jednokrotnego i **Wymagaj tokenu identyfikatora w żądaniach wylogowywania** zgodnie z potrzebami.

    ![Ustawienia właściwości zachowanie sesji w Azure Portal](./media/session-behavior/session-behavior.png)

8. Kliknij przycisk **Zapisz**.

## <a name="configure-sign-out-behavior"></a>Skonfiguruj zachowanie wylogowywania

### <a name="secure-your-logout-redirect"></a>Zabezpiecz przekierowanie wylogowania

Po wylogowaniu użytkownik zostanie przekierowany na identyfikator URI określony w `post_logout_redirect_uri` parametrze, bez względu na adresy URL odpowiedzi określone dla aplikacji. Jeśli jednak zostanie `id_token_hint` przekierowany prawidłowy, a **token Wymagaj tokenu w żądaniach wylogowania** jest włączony, Azure AD B2C sprawdza, czy wartość jest `post_logout_redirect_uri` zgodna z skonfigurowanymi identyfikatorami URI przekierowań aplikacji przed przekazaniem. Jeśli nie skonfigurowano zgodnego adresu URL odpowiedzi dla aplikacji, zostanie wyświetlony komunikat o błędzie i użytkownik nie zostanie przekierowany. Aby wymagać tokenu identyfikatora w żądaniach wylogowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Właściwości**.
1. Włącz **token Wymagaj identyfikatora w żądaniach wylogowania**.
1. Wróć do  **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację.
1. Wybierz pozycję **Uwierzytelnianie**.
1. W polu tekstowym **adres URL wylogowywania** wpisz identyfikator URI przekierowania wylogowania, a następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
