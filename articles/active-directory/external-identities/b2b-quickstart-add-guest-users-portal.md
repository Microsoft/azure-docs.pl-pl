---
title: 'Szybki Start: Dodawanie użytkowników-Gości w Azure Portal — Azure AD'
description: Korzystając z tego przewodnika Szybki start, możesz dowiedzieć się, jak administratorzy usługi Azure AD mogą dodawać użytkowników-gości B2B w witrynie Azure Portal, oraz zapoznać się z przepływem pracy zaproszenia B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f134a11b6d0a561a8b53156c927906afe2874a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87906087"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Szybki start: Dodawanie użytkowników-gości do katalogu w witrynie Azure Portal

Dodając dowolną osobę do Twojego katalogu jako użytkownika-gościa, możesz zaprosić ją do współpracy z Twoją organizacją. Następnie możesz wysłać wiadomość e-mail z zaproszeniem zawierającym link do realizacji lub wysłać bezpośredni link do aplikacji, którą chcesz udostępnić. Użytkownicy-goście mogą zalogować się za pomocą własnych tożsamości służbowych lub społecznościowych. Wraz z tym przewodnikiem Szybki Start można dowiedzieć się więcej o dodawaniu użytkowników-Gości [w Azure Portal](add-users-administrator.md)za pośrednictwem [programu PowerShell](b2b-quickstart-invite-powershell.md)lub [zbiorczo](tutorial-bulk-invite.md).

W tym przewodniku szybki start dodasz nowego użytkownika-gościa do katalogu usługi Azure AD za pośrednictwem Azure Portal, wyślesz zaproszenie i zobaczysz, jak wygląda proces wykupu zaproszeń użytkownika-gościa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza zaprezentowanego w tym samouczku potrzebne są następujące elementy:

 - Rola umożliwiająca tworzenie użytkowników w Twoim katalogu dzierżawy, na przykład rola administratora globalnego lub dowolna spośród ról administratora katalogu z ograniczonymi uprawnieniami.
 - Ważne konto e-mail, które możesz dodać do swojego katalogu dzierżawy i którego możesz użyć do odebrania wiadomości e-mail z zaproszeniem testowym.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Dodawanie nowego użytkownika-gościa w usłudze Azure AD

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji użytkownicy](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Wybierz pozycję **Nowy użytkownik-gość**.

    ![Zrzut ekranu przedstawiający miejsce wybrania nowej opcji użytkownika-gościa](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Na stronie **nowy użytkownik** wybierz pozycję **Zaproś użytkownika** , a następnie Dodaj informacje o użytkowniku gościa. 

   - **Nazwij.** Imię i nazwisko użytkownika-gościa.
   - **Adres e-mail (wymagany)**. Adres e-mail użytkownika-gościa.
   - **Wiadomość osobista (opcjonalnie)** Dołącz do użytkownika-gościa osobistą wiadomość powitalną.
   - **Grupy**: można dodać użytkownika-gościa do jednej lub kilku istniejących grup lub później.
   - **Rola katalogu**: Jeśli wymagane są uprawnienia administracyjne usługi Azure AD dla użytkownika, można je dodać do roli usługi Azure AD. 

6. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. W prawym górnym rogu zostanie wyświetlone powiadomienie z komunikatem **Pomyślnie zaproszono użytkownika**. 
7.  Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="assign-an-app-to-the-guest-user"></a>Przypisywanie aplikacji do użytkownika-gościa
Dodaj aplikację Salesforce do swojej dzierżawy testowej i przypisz testowego użytkownika-gościa do aplikacji.
1.  Zaloguj się do witryny Azure Portal jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**.
3.  Wybierz pozycję **Nowa aplikacja**.
4. W obszarze **Dodaj z galerii** wyszukaj pozycję **Salesforce**, a następnie wybierz ją.

    ![Zrzut ekranu przedstawiający Dodawanie z galerii pola wyszukiwania](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Wybierz pozycję **Dodaj**.
6. W obszarze **Zarządzaj** wybierz pozycję **Logowanie jednokrotne**, a w obszarze **Tryb logowania jednokrotnego** wybierz pozycję **Logowanie oparte na haśle** i kliknij przycisk **Zapisz**.
7. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy i grupy** > **Dodaj użytkownika** > **Użytkownicy i grupy**.
8. Za pomocą pola wyszukiwania wyszukaj użytkownika testowego (jeśli to konieczne) i wybierz go z listy. Następnie kliknij pozycję **Wybierz**.
9. Wybierz opcję **Przypisz**. 

## <a name="accept-the-invitation"></a>Akceptowanie zaproszenia
Teraz zaloguj się jako użytkownik-gość, aby zobaczyć zaproszenie.
1.  Zaloguj się do konta e-mail testowego użytkownika-gościa.
2.  W skrzynce odbiorczej znajdź wiadomość e-mail „Zaproszenie”.

    ![Zrzut ekranu przedstawiający wiadomość e-mail z zaproszeniem B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  W treści wiadomości e-mail wybierz pozycję **Rozpocznij**. W przeglądarce zostanie otwarta strona **Przejrzyj uprawnienia**. 

    ![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Wybierz pozycję **Zaakceptuj**. Zostanie otwarty Panel dostępu zawierający listę aplikacji, do których może uzyskać dostęp użytkownik-gość.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Usuń testowego użytkownika-gościa i aplikację testową, gdy przestaną być potrzebne.
1.  Zaloguj się do witryny Azure Portal jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzaj** wybierz pozycję **Aplikacje dla przedsiębiorstw**.
4.  Otwórz aplikację **Salesforce**, a następnie wybierz pozycję **Usuń**.
5.  W lewym okienku wybierz pozycję **Azure Active Directory**.
6.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
7.  Wybierz użytkownika testowego, a następnie wybierz pozycję **Usuń użytkownika**.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzyliśmy użytkownika-gościa w witrynie Azure Portal i wysłaliśmy zaproszenie do udostępnienia aplikacji. Następnie zobaczyliśmy proces realizacji z punktu widzenia użytkownika-gościa i sprawdziliśmy, czy aplikacja pojawiła się na jego Panelu dostępu. Aby dowiedzieć się więcej na temat dodawania użytkowników-gości do współpracy, zobacz [Add Azure Active Directory B2B collaboration users in the Azure portal (Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure Portal)](add-users-administrator.md).
