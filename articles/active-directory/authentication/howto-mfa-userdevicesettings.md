---
title: Zarządzanie ustawieniami użytkownika Multi-Factor Authentication platformy Azure — Azure Active Directory
description: Dowiedz się, jak skonfigurować Azure Active Directory ustawienia użytkownika dla platformy Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309771"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Zarządzanie ustawieniami użytkownika dla usługi Azure Multi-Factor Authentication

Aby ułatwić zarządzanie użytkownikami usługi Azure Multi-Factor Authentication, możesz wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji usługi MFA. W przypadku użytkowników ze zdefiniowanymi hasłami aplikacji można także usunąć te hasła, co spowoduje niepowodzenie w tych aplikacjach starszego uwierzytelniania. Te akcje mogą być niezbędne, jeśli trzeba udzielić pomocy użytkownikowi lub chcesz zresetować swój stan zabezpieczeń.

## <a name="manage-user-authentication-options"></a>Zarządzanie opcjami uwierzytelniania użytkownika

Jeśli masz przypisaną rolę *administrator uwierzytelniania* , możesz wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji usługi MFA z obiektu użytkownika. Aby zarządzać ustawieniami użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz pozycję **metody uwierzytelniania**. W górnej części okna, a następnie wybierz jedną z następujących opcji dla użytkownika:
   - **Zresetuj hasło resetuje** hasło użytkownika i przypisuje tymczasowe hasło, które należy zmienić przy następnym logowaniu.
   - **Wymagaj ponownej rejestracji usługi MFA** powoduje, że gdy użytkownik zaloguje się w następnym momencie, prosi o skonfigurowanie nowej metody uwierzytelniania MFA.
   - **Odwoływanie sesji MFA** czyści sesje usługi MFA zalogowanych przez użytkownika i wymaga ich do wykonania usługi MFA przy następnym zażądaniu zasad na urządzeniu.

   ![Zarządzanie metodami uwierzytelniania z poziomu Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Usuń użytkowników istniejące hasła aplikacji

W razie konieczności można usunąć wszystkie hasła aplikacji utworzone przez użytkownika. Aplikacje nie korzystające z przeglądarki, które zostały skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji. Aby wykonać tę akcję, wymagane są uprawnienia *administratora globalnego* .

Aby usunąć hasła aplikacji użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz **Multi-Factor Authentication**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz Poniższy przykładowy zrzut ekranu, aby zobaczyć pełne okno Azure Portal i lokalizację menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Wybierz Multi-Factor Authentication z okna użytkowników w usłudze Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
1. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**, a następnie zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**, jak pokazano w następującym przykładzie: ![Usuń wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Wybierz pozycję **Zapisz**, a następnie przycisk **Zamknij**.

## <a name="next-steps"></a>Następne kroki

Ten artykuł pomaga skonfigurować ustawienia poszczególnych użytkowników. Aby skonfigurować ustawienia usługi Azure Multi-Factor Authentication, zobacz [Konfigurowanie ustawień Multi-Factor Authentication platformy Azure](howto-mfa-mfasettings.md)

Jeśli użytkownicy potrzebują pomocy, zapoznaj się z [podręcznikiem użytkownika dotyczącym usługi Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
