---
title: Dodawanie użytkowników współpracy B2B w Azure Portal — Azure AD
description: Pokazuje, w jaki sposób administrator może dodać użytkowników-Gości do swoich katalogów z organizacji partnerskiej za pomocą funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b503c7494d1b5e1821718fab5577bca88be65169
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442290"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal

Jako użytkownik, któremu przypisano dowolne role katalogu administratora z ograniczeniami, możesz użyć Azure Portal, aby zaprosić użytkowników współpracy B2B. Można zaprosić użytkowników-Gości do katalogu, do grupy lub do aplikacji. Po zaproszeniu użytkownika za pomocą dowolnej z tych metod konto zaproszonego użytkownika zostanie dodane do Azure Active Directory (Azure AD) z typem użytkownika *gość*. Użytkownik-Gość musi następnie wykorzystać swoje zaproszenie do uzyskania dostępu do zasobów. Zaproszenie użytkownika nie wygasa.

Po dodaniu użytkownika-gościa do katalogu można wysłać użytkownikowi gość bezpośredni link do udostępnionej aplikacji lub kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem. Aby uzyskać więcej informacji na temat procesu wykupu, zobacz Tworzenie [zaproszeń do współpracy B2B](redemption-experience.md).

> [!IMPORTANT]
> Należy postępować zgodnie z instrukcjami w temacie [: Dodawanie informacji o ochronie prywatności organizacji w Azure Active Directory](../fundamentals/active-directory-properties-area.md) , aby dodać adres URL zasad zachowania poufności informacji organizacji. W ramach procesu wykupu po raz pierwszy zaproszony użytkownik musi wyrazić zgodę na warunki zachowania poufności, aby kontynuować. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że ustawienia współpracy zewnętrznej Twojej organizacji zostały skonfigurowane tak, aby można było zapraszać Gości. Domyślnie wszyscy użytkownicy i Administratorzy mogą zapraszać Gości. Jednak zasady współpracy zewnętrznej w organizacji mogą być skonfigurowane tak, aby uniemożliwić określonym użytkownikom lub administratorom zapraszanie Gości. Aby dowiedzieć się, jak wyświetlać i ustawiać te zasady, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Dodawanie użytkowników-Gości do katalogu

Aby dodać użytkowników współpracy B2B do katalogu, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako użytkownik, który ma przypisaną rolę katalogu z ograniczoną administratorem lub rolę zapraszania gościa.
2. Wyszukaj i wybierz **Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4. Wybierz pozycję **Nowy użytkownik-gość**.

   ![Pokazuje, gdzie nowy użytkownik-Gość znajduje się w interfejsie użytkownika](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Na stronie **nowy użytkownik** wybierz pozycję **Zaproś użytkownika** , a następnie Dodaj informacje o użytkowniku gościa. 

    > [!NOTE]
    > Adresy e-mail grup nie są obsługiwane; Wprowadź adres e-mail osoby. Niektórzy dostawcy poczty e-mail umożliwiają użytkownikom dodawanie znaku plus (+) i dodatkowego tekstu do adresów e-mail, aby pomóc w filtrowaniu skrzynek odbiorczych. Jednak usługa Azure AD nie obsługuje obecnie symboli Plus w adresach e-mail. Aby uniknąć problemów z dostarczaniem, Pomiń symbol Plus i wszelkie znaki, które są następujące po nim do znaku @.

   - **Nazwij.** Imię i nazwisko użytkownika-gościa.
   - **Adres e-mail (wymagany)**. Adres e-mail użytkownika-gościa.
   - **Wiadomość osobista (opcjonalnie)** Dołącz do użytkownika-gościa osobistą wiadomość powitalną.
   - **Grupy**: można dodać użytkownika-gościa do jednej lub kilku istniejących grup lub później.
   - **Rola katalogu**: Jeśli wymagane są uprawnienia administracyjne usługi Azure AD dla użytkownika, można je dodać do roli usługi Azure AD. 

7. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. 
 
Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


![Pokazuje użytkownika B2B z typem użytkownika-gościa](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Dodawanie użytkowników-gości do grupy
Jeśli musisz ręcznie dodać użytkowników współpracy B2B do grupy, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzaj**wybierz pozycję **grupy**.
4. Wybierz grupę (lub kliknij pozycję **Nowa grupa** , aby utworzyć nową). Dobrym pomysłem jest uwzględnienie w opisie grupy, że grupa zawiera użytkowników typu gość B2B.
5. Wybierz **członków**. 
6. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-Gość już istnieje w katalogu, Wyszukaj użytkownika B2B. Wybierz użytkownika, a następnie kliknij pozycję **Wybierz** , aby dodać użytkownika do grupy.
   - Jeśli użytkownik-Gość jeszcze nie istnieje w katalogu, Zaproś go do grupy, wpisując swój adres e-mail w polu wyszukiwania, wpisując opcjonalną wiadomość osobistą, a następnie klikając przycisk **Wybierz**. Zaproszenie automatycznie przejdzie do zaproszonego użytkownika.
     
     ![Dodaj przycisk Zaproś, aby dodać członków-Gości](./media/add-users-administrator/GroupInvite.png)
   
Możesz również użyć grup dynamicznych z funkcją współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz [grupy dynamiczne i Azure Active Directory współpracy B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Dodawanie użytkowników-gości do aplikacji

Aby dodać użytkowników współpracy B2B do aplikacji, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzaj**wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
4. Wybierz aplikację, do której chcesz dodać użytkowników-Gości.
5. Na pulpicie nawigacyjnym aplikacji wybierz pozycję **łączna liczba użytkowników** , aby otworzyć okienko **Użytkownicy i grupy** .

    ![Przycisk łącznej liczby użytkowników umożliwiający dodanie otwartych użytkowników i grup](./media/add-users-administrator/AppUsersAndGroups.png)

6. Wybierz pozycję **Dodaj użytkownika**.
7. W obszarze **Dodaj przypisanie**wybierz pozycję **Użytkownicy i grupy**.
8. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-Gość już istnieje w katalogu, Wyszukaj użytkownika B2B. Wybierz użytkownika, kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz** , aby dodać użytkownika do aplikacji.
   - Jeśli użytkownik-Gość jeszcze nie istnieje w katalogu, w obszarze **Wybierz członka lub Zaproś użytkownika zewnętrznego**wpisz adres e-mail użytkownika. W polu komunikat wpisz opcjonalną wiadomość osobistą. W oknie komunikatu kliknij pozycję **Zaproś**.
           
       ![Dodaj przycisk Zaproś, aby dodać członków-Gości](./media/add-users-administrator/AppInviteUsers.png)
   
      Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz** , aby dodać użytkownika do aplikacji. Zaproszenie automatycznie przejdzie do zaproszonego użytkownika.

9. Użytkownik-Gość zostanie wyświetlony na liście **Użytkownicy i grupy** aplikacji z przypisaną rolą **domyślnego dostępu**. Jeśli chcesz zmienić rolę, wykonaj następujące czynności:
   - Wybierz użytkownika-gościa, a następnie wybierz pozycję **Edytuj**. 
   - W obszarze **Edytuj przypisanie**kliknij pozycję **Wybierz rolę**, a następnie wybierz rolę, którą chcesz przypisać do wybranego użytkownika.
   - Kliknij pozycję **Wybierz**.
   - Kliknij przycisk **Przypisz**.
 
## <a name="resend-invitations-to-guest-users"></a>Ponowne wysyłanie zaproszeń do użytkowników-gości

Jeśli użytkownik-Gość nie zakończył jeszcze zaproszenia, możesz ponownie wysłać wiadomość e-mail z zaproszeniem.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
5. Wybierz konto użytkownika.
6. W obszarze **Zarządzaj**wybierz pozycję **profil**.
7. Jeśli użytkownik jeszcze nie zaakceptował zaproszenia, dostępna jest opcja **Wyślij ponownie zaproszenie** . Wybierz ten przycisk, aby ponownie wysłać.

   ![Wyślij ponownie opcję zaproszenia w profilu użytkownika](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> W przypadku ponownego wysłania zaproszenia, które pierwotnie kierowało użytkownika do konkretnej aplikacji, należy zrozumieć, że link w nowym zaproszeniu przejmuje użytkownika do panelu dostępu najwyższego poziomu.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak Administratorzy usługi Azure AD mogą dodawać użytkowników z gościa B2B, zobacz [jak Pracownicy przetwarzający informacje dodają użytkowników współpracy B2B?](add-users-information-worker.md)
- Aby uzyskać informacje na temat wiadomości e-mail z zaproszeniem, zobacz [elementy wiadomości e-mail z zaproszeniem do współpracy B2B](invitation-email-elements.md).