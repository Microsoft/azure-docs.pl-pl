---
title: Konfigurowanie przepływu resetowania hasła
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ resetowania hasła w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618830"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu resetowania hasła w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Przepływ REST hasła

Zasady resetowania hasła umożliwiają użytkownikom resetowanie zapomnianego hasła. Przepływ resetowania hasła obejmuje następujące kroki: 
1. Na stronie Rejestracja i logowanie użytkownik klika polecenie "nie pamiętasz hasła?". . Azure AD B2C zwraca do aplikacji kod błędu AADB2C90118. Aplikacja obsługuje ten kod błędu przez wywołanie zasad resetowania hasła. 
1. Użytkownicy udostępniają i weryfikują swoje wiadomości e-mail za pomocą jednorazowego kodu dostępu.
1. Wprowadź nowe hasło.

![Przepływ resetowania hasła](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić użytkownikom aplikacji Resetowanie hasła, należy użyć przepływu użytkownika resetowania hasła.

1. W menu przegląd Azure AD B2C dzierżawy wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz przepływ użytkownika **resetowania hasła** . 
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *passwordreset1*.
1. W przypadku **dostawców tożsamości** Włącz **Resetowanie hasła przy użyciu adresu e-mail**.
2. W obszarze oświadczenia aplikacji kliknij przycisk **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
3. Kliknij przycisk **OK**.
4. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, Sprawdź adres e-mail konta, które zostało utworzone wcześniej, a następnie wybierz pozycję **Kontynuuj**.
1. Masz teraz możliwość zmiany hasła dla użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Tworzenie zasad resetowania haseł

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C w celu zdefiniowania podróży użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, takimi jak: rejestrowanie i logowanie, resetowanie haseł i zasady edytowania profilu. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Następne kroki

Skonfiguruj [przepływ edytowania profilu](add-profile-editing-policy.md).