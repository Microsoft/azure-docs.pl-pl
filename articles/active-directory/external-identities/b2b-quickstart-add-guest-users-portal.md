---
title: 'Szybki start: dodawanie użytkowników-gości w aplikacji Azure Portal — Azure AD'
description: Korzystając z tego przewodnika Szybki start, możesz dowiedzieć się, jak administratorzy usługi Azure AD mogą dodawać użytkowników-gości B2B w witrynie Azure Portal, oraz zapoznać się z przepływem pracy zaproszenia B2B.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/05/2020
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7326a35d07715eae75f70f2f33763f82946c589c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529849"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Szybki start: Dodawanie użytkowników-gości do katalogu w witrynie Azure Portal

Dodając dowolną osobę do Twojego katalogu jako użytkownika-gościa, możesz zaprosić ją do współpracy z Twoją organizacją. Następnie możesz wysłać wiadomość e-mail z zaproszeniem zawierającym link do realizacji lub wysłać bezpośredni link do aplikacji, którą chcesz udostępnić. Użytkownicy-goście mogą zalogować się za pomocą własnych tożsamości służbowych lub społecznościowych. Z tego przewodnika Szybki start dowiesz się więcej na temat dodawania użytkowników-gości w Azure Portal [,](add-users-administrator.md)za pomocą programu [PowerShell](b2b-quickstart-invite-powershell.md)lub [zbiorczo.](tutorial-bulk-invite.md)

W tym przewodniku Szybki start dodasz nowego użytkownika-gościa do katalogu usługi Azure AD za pośrednictwem witryny Azure Portal, wyślesz zaproszenie i zobaczysz, jak wygląda proces realizacji zaproszenia użytkownika-gościa.

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza zaprezentowanego w tym samouczku potrzebne są następujące elementy:

 - Rola umożliwiająca tworzenie użytkowników w Twoim katalogu dzierżawy, na przykład rola administratora globalnego lub dowolna spośród ról administratora katalogu z ograniczonymi uprawnieniami.
 - Ważne konto e-mail, które możesz dodać do swojego katalogu dzierżawy i którego możesz użyć do odebrania wiadomości e-mail z zaproszeniem testowym.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Dodawanie nowego użytkownika-gościa w usłudze Azure AD

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji Użytkownicy](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Wybierz pozycję **Nowy użytkownik-gość**.

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji Nowy użytkownik-gość](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Na stronie **Nowy użytkownik** wybierz pozycję **Zaproś użytkownika,** a następnie dodaj informacje o użytkowniku-gościu. 

   - **Nazwa.** Imię i nazwisko użytkownika-gościa.
   - **Adres e-mail (wymagany)**. Adres e-mail użytkownika-gościa.
   - **Wiadomość osobista (opcjonalnie)** Dołącz osobistą wiadomość powitaną do użytkownika-gościa.
   - **Grupy:** możesz dodać użytkownika-gościa do co najmniej jednej istniejącej grupy lub zrobić to później.
   - **Rola katalogu:** jeśli potrzebujesz uprawnień administracyjnych usługi Azure AD dla użytkownika, możesz dodać je do roli usługi Azure AD. 

6. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. W prawym górnym rogu zostanie wyświetlone powiadomienie z komunikatem **Pomyślnie zaproszono użytkownika**. 
7.  Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="assign-an-app-to-the-guest-user"></a>Przypisywanie aplikacji do użytkownika-gościa
Dodaj aplikację Salesforce do swojej dzierżawy testowej i przypisz testowego użytkownika-gościa do aplikacji.
1.  Zaloguj się do witryny Azure Portal jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**.
3.  Wybierz pozycję **Nowa aplikacja**.
4. W obszarze **Dodaj z galerii** wyszukaj pozycję **Salesforce**, a następnie wybierz ją.

    ![Zrzut ekranu przedstawiający pole wyszukiwania Dodaj z galerii](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
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

    ![Zrzut ekranu przedstawiający stronę Przeglądanie uprawnień](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

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
