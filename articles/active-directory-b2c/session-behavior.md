---
title: Skonfiguruj zachowanie sesji — Azure Active Directory B2C | Microsoft Docs
description: Skonfiguruj zachowanie sesji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 249b9bb282024431d0ecd38c62d8d780602e6709
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229973"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurowanie zachowania sesji w Azure Active Directory B2C

Ta funkcja zapewnia precyzyjną kontrolę na [podstawie przepływu poszczególnych użytkowników](user-flow-overview.md):

- Okresy istnienia sesji aplikacji sieci Web zarządzanych przez Azure AD B2C.
- Zachowanie logowania jednokrotnego w wielu aplikacjach i przepływach użytkowników w dzierżawie Azure AD B2C.

Te ustawienia nie są dostępne na potrzeby przepływów użytkownika resetowania hasła.

Azure AD B2C obsługuje [protokół uwierzytelniania OpenID Connect Connect](openid-connect.md) do włączania bezpiecznego logowania do aplikacji sieci Web. Aby zarządzać sesjami aplikacji sieci Web, można użyć następujących właściwości:

## <a name="session-behavior-properties"></a>Właściwości zachowania sesji

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — okres istnienia pliku cookie sesji usługi Azure AD B2C's w przeglądarce użytkownika po pomyślnym uwierzytelnieniu.
    - Wartość domyślna = 1440 min.
    - Minimum (włącznie) = 15 minut.
    - Maksimum (włącznie) = 1440 min.
- **Limit czasu sesji aplikacji sieci Web** — Jeśli ten przełącznik jest ustawiony na wartość **bezwzględną**, użytkownik jest zmuszony do uwierzytelnienia ponownie po upływie czasu określonego przez okres **istnienia sesji aplikacji sieci Web (w minutach)** . Jeśli ten przełącznik jest ustawiony na wartość **roll** (ustawienie domyślne), użytkownik pozostanie zalogowany, o ile użytkownik będzie stale aktywny w aplikacji sieci Web.
- **Konfiguracja logowania** jednokrotnego Jeśli masz wiele aplikacji i przepływów użytkowników w dzierżawie usługi B2C, możesz zarządzać interakcjami użytkowników w nich przy użyciu właściwości **Konfiguracja logowania** jednokrotnego. Właściwość można ustawić na jedno z następujących ustawień:
    - **Dzierżawca** — to ustawienie jest domyślne. Użycie tego ustawienia umożliwia wielu aplikacjom i przepływom użytkowników w dzierżawie B2C współużytkowanie tej samej sesji użytkownika. Na przykład, gdy użytkownik loguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego, farmaceuty firmy Contoso, po uzyskaniu do niego dostępu.
    - **Aplikacja** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz, aby użytkownik logował się do usługi contoso apteki (z tymi samymi poświadczeniami), nawet jeśli użytkownik jest już zalogowany do kupowania w firmie Contoso, inna aplikacja w tej samej dzierżawie B2C.
    - **Zasady** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla przepływu użytkownika niezależnie od aplikacji, z których korzysta. Na przykład jeśli użytkownik jest już zalogowany i ukończył krok uwierzytelniania wieloskładnikowego (MFA), użytkownik może uzyskać dostęp do części o wyższym poziomie zabezpieczeń wielu aplikacji, o ile sesja związana z przepływem użytkownika nie wygasa.
    - **Wyłączone** — to ustawienie wymusza uruchomienie użytkownika przez cały przepływ użytkownika przy każdym wykonaniu zasad.

Następujące przypadki użycia są włączone przy użyciu tych właściwości:

- Zapoznaj się z wymaganiami dotyczącymi zabezpieczeń i zgodności w branży, ustawiając odpowiednie okresy istnienia sesji aplikacji sieci Web.
- Wymuś uwierzytelnianie po upływie określonego czasu podczas interakcji użytkownika z częścią aplikacji sieci Web o wysokim poziomie zabezpieczeń.

## <a name="configure-the-properties"></a>Konfigurowanie właściwości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)**.
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz pozycję **Właściwości**.
7. Skonfiguruj **okres istnienia sesji aplikacji sieci Web (minuty)**, **limit czasu sesji aplikacji sieci Web**, **konfigurację logowania**jednokrotnego i **Wymagaj tokenu identyfikatora w żądaniach wylogowywania** zgodnie z potrzebami.

    ![Ustawienia właściwości zachowanie sesji w Azure Portal](./media/session-behavior/session-behavior.png)

8. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).