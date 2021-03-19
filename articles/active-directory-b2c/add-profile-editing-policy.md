---
title: Konfigurowanie przepływu edycji profilu
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ edycji profilu w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581902"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu edycji profilu w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Przepływ edycji profilu

Zasady edytowania profilów umożliwiają użytkownikom zarządzanie swoimi atrybutami profilu, takimi jak nazwa wyświetlana, nazwisko, imię i nazwisko, miasto i inne. Przepływ edycji profilu obejmuje następujące kroki: 

1. Utwórz konto lub Zaloguj się przy użyciu konta lokalnego lub społecznościowego. Jeśli sesja jest nadal aktywna, Azure AD B2C autoryzuje użytkownika i przechodzi do następnego kroku.
1. Azure AD B2C odczytuje profil użytkownika z katalogu i zezwala użytkownikowi na edytowanie atrybutów.

![Przepływ edycji profilu](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz umożliwić użytkownikom edytowanie ich profilu w aplikacji, Użyj profilu użytkownika edytującego profil.

1. W menu strony Przegląd dzierżawy Azure AD B2C wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz **profil edytowanie profilu** użytkownika. 
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
1. W przypadku **dostawców tożsamości** wybierz pozycję **Logowanie za pośrednictwem poczty e-mail**.
1. W polu **atrybuty użytkownika** wybierz atrybuty, które mają być edytowane przez klienta w swoim profilu. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz zarówno atrybuty, **jak i oświadczenia** dotyczące **nazwy wyświetlanej** i stanowiska. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu utworzonego wcześniej konta.
1. Teraz można zmienić nazwę wyświetlaną i stanowisko użytkownika. Kliknij przycisk **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Tworzenie zasad edytowania profilów

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C w celu zdefiniowania podróży użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, takimi jak: rejestrowanie i logowanie, resetowanie haseł i zasady edytowania profilu. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Następne kroki

* Dodaj [Logowanie przy użyciu dostawcy tożsamości społecznościowej](add-identity-provider.md).