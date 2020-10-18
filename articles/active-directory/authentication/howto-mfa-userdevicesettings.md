---
title: Zarządzanie ustawieniami użytkownika Multi-Factor Authentication platformy Azure — Azure Active Directory
description: Dowiedz się, jak skonfigurować Azure Active Directory ustawienia użytkownika dla platformy Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2006422d3516aa67076233b0b4b9d3e7c58a7232
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166520"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Zarządzanie ustawieniami użytkownika dla usługi Azure Multi-Factor Authentication

Aby ułatwić zarządzanie użytkownikami usługi Azure Multi-Factor Authentication, możesz wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji usługi MFA. W przypadku użytkowników ze zdefiniowanymi hasłami aplikacji można także usunąć te hasła, co spowoduje niepowodzenie w tych aplikacjach starszego uwierzytelniania. Te akcje mogą być niezbędne, jeśli trzeba udzielić pomocy użytkownikowi lub chcesz zresetować swój stan zabezpieczeń.

## <a name="manage-user-authentication-options"></a>Zarządzanie opcjami uwierzytelniania użytkownika

Jeśli masz przypisaną rolę *administrator uwierzytelniania* , możesz wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji MFA z obiektu użytkownika. Aby zarządzać ustawieniami użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz pozycję **metody uwierzytelniania**. W górnej części okna, a następnie wybierz jedną z następujących opcji dla użytkownika:
   - **Zresetuj hasło resetuje** hasło użytkownika i przypisuje tymczasowe hasło, które należy zmienić przy następnym logowaniu.
   - **Wymagaj ponownej rejestracji usługi MFA** powoduje, że gdy użytkownik zaloguje się w następnym momencie, prosi o skonfigurowanie nowej metody uwierzytelniania MFA.
   
      > [!NOTE]
      > Metody uwierzytelniania aktualnie zarejestrowanego użytkownika nie są usuwane, gdy administrator wymaga ponownej rejestracji usługi MFA. Po ponownym zarejestrowaniu konta usługi MFA zalecamy zapoznanie się z informacjami o zabezpieczeniach i usunięcie wszelkich wcześniej zarejestrowanych metod uwierzytelniania, które nie są już używane.
   
   - **Odwoływanie sesji MFA** czyści sesje usługi MFA zalogowanych przez użytkownika i wymaga ich do wykonania usługi MFA przy następnym zażądaniu zasad na urządzeniu.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Zarządzanie metodami uwierzytelniania z poziomu Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Usuń użytkowników istniejące hasła aplikacji

W razie konieczności można usunąć wszystkie hasła aplikacji utworzone przez użytkownika. Aplikacje nie korzystające z przeglądarki, które zostały skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji. Aby wykonać tę akcję, wymagane są uprawnienia *administratora globalnego* .

Aby usunąć hasła aplikacji użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz Poniższy przykładowy zrzut ekranu, aby wyświetlić okno pełne Azure Portal i lokalizację menu: [ ![ Wybierz Multi-Factor Authentication z okna użytkowników w usłudze Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
1. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**, a następnie zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**, jak pokazano w następującym przykładzie: ![ Usuń wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Wybierz pozycję **Zapisz**, a następnie przycisk **Zamknij**.

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak skonfigurować poszczególne ustawienia użytkownika. Aby skonfigurować ogólne ustawienia usługi Azure Multi-Factor Authentication, zobacz [Konfigurowanie ustawień Multi-Factor Authentication platformy Azure](howto-mfa-mfasettings.md).

Jeśli użytkownicy potrzebują pomocy, zapoznaj się z [podręcznikiem użytkownika dotyczącym usługi Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
