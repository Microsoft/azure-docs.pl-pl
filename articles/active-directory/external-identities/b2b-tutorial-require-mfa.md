---
title: Samouczek — uwierzytelnianie wieloskładnikowe dla B2B — Azure AD
description: W tym samouczku dowiesz się, jak wymagać uwierzytelniania wieloskładnikowego (MFA), gdy korzystasz z usługi Azure AD B2B do współpracy z użytkownikami zewnętrznymi i organizacjami partnerów.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5f7f823d6c5f5860c2e78fe8cacdd1d788c581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95974123"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Samouczek: wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników-gości B2B

W przypadku współpracy z zewnętrznymi użytkownikami-gośćmi B2B dobrym pomysłem jest ochrona aplikacji za pomocą zasad uwierzytelniania wieloskładnikowego (MFA). W takiej sytuacji nazwa użytkownika i hasło nie wystarczą użytkownikom zewnętrznym do uzyskania dostępu do Twoich zasobów. W Azure Active Directory (Azure AD) można osiągnąć ten cel za pomocą zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego w celu uzyskania dostępu. Zasady MFA można wymuszać na poziomie dzierżawy, aplikacji lub pojedynczego użytkownika-gościa, tak samo jak są one włączane dla członków w Twojej organizacji.

Przykład:

![Diagram przedstawiający Logowanie użytkownika-gościa do aplikacji firmy](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Administrator lub pracownik w firmie A zaprasza użytkownika-gościa do użycia aplikacji w chmurze lub lokalnej, która jest konfigurowana do wymagania usługi MFA w celu uzyskania dostępu.
2.  Użytkownik-gość loguje się za pomocą własnej tożsamości służbowej lub społecznościowej. 
3.  Użytkownik jest proszony o ukończenie testu usługi MFA. 
4.  Użytkownik konfiguruje usługę MFA w firmie A i wybiera jej opcję usługi MFA. Użytkownik może uzyskiwać dostęp do aplikacji.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Testowanie środowiska logowania przed uruchomieniem instalacji usługi MFA.
> * Utwórz zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do aplikacji w chmurze w danym środowisku. W tym samouczku użyjemy aplikacji Microsoft Azure Management, aby zilustrować ten proces.
> * Symulacja logowania w usłudze MFA zostanie przeprowadzona przy użyciu narzędzia What If.
> * Przetestuj zasady dostępu warunkowego.
> * Czyszczenie użytkownika testowego i zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza zaprezentowanego w tym samouczku potrzebne są następujące elementy:

 - **Dostęp do wersji Azure AD — wersja Premium**, która obejmuje możliwości zasad dostępu warunkowego. Aby wymusić uwierzytelnianie wieloskładnikowe, należy utworzyć zasady dostępu warunkowego usługi Azure AD. Pamiętaj, że zasady MFA są zawsze wymuszane w organizacji, niezależnie od tego, czy partner ma możliwość obsługi usługi MFA. W przypadku konfigurowania usługi MFA w organizacji trzeba upewnić się, że masz wystarczającą liczbę licencji usługi Azure AD Premium dla usługi użytkowników-gości. 
 - **Prawidłowe zewnętrzne konto e-mail**, które można dodać do katalogu dzierżawy jako użytkownika-gościa i którego można używać do logowania. Jeśli nie wiesz, jak utworzyć konto gościa, zobacz [Add a B2B guest user in the Azure portal (Dodawanie użytkownika-gościa B2B w witrynie Azure Portal)](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Dodawanie testowego użytkownika-gościa w usłudze Azure AD

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4.  Wybierz pozycję **Nowy użytkownik-gość**.

    ![Zrzut ekranu przedstawiający miejsce wybrania nowej opcji użytkownika-gościa](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  W obszarze **Nazwa użytkownika** wprowadź adres e-mail użytkownika zewnętrznego. Opcjonalnie możesz dodać komunikat powitalny. 

    ![Zrzut ekranu przedstawiający lokalizację, w której ma zostać wprowadzony komunikat zaproszenia gościa](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. Zostanie wyświetlony komunikat **Pomyślnie zaproszono użytkownika**. 
7.  Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testowanie środowiska logowania przed uruchomieniem instalacji usługi MFA
1.  Użyj nazwy użytkownika testowego i hasła do zalogowania się do witryny [Azure Portal](https://portal.azure.com/).
2.  Zwróć uwagę, że do uzyskania dostępu do witryny Azure Portal wystarczą Ci poświadczenia logowania. Dodatkowe uwierzytelnianie nie jest wymagane.
3.  Wyloguj się.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Tworzenie zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego
1.  Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator zabezpieczeń lub administrator dostępu warunkowego.
2.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**. 
3.  Na stronie **Azure Active Directory** w sekcji **zabezpieczenia** wybierz pozycję **dostęp warunkowy**.
4.  Na stronie **Dostęp warunkowy** na pasku narzędzi w górnej części wybierz pozycję **Nowe zasady**.
5.  Na stronie **Nowe** w polu tekstowym **Nazwa** wpisz frazę **Wymagaj usługi MFA do dostępu do portalu B2B**.
6.  W sekcji **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
7.  Na stronie **Użytkownicy i grupy** wybierz pozycję **Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Wszyscy użytkownicy-goście (wersja zapoznawcza)**.

    ![Zrzut ekranu przedstawiający wybieranie wszystkich użytkowników-Gości](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Kliknij **Gotowe**.
10. Na stronie **Nowy** w sekcji **Przypisania** wybierz pozycję **Aplikacje w chmurze**.
11. Na stronie **Aplikacje w chmurze** wybierz pozycję **Wybierz aplikację**, a następnie wybierz pozycję **Wybierz**.

    ![Zrzut ekranu przedstawiający stronę aplikacje w chmurze i opcję Wybierz](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Na stronie **Wybieranie** wybierz pozycję **Microsoft Azure Management**, a następnie wybierz pozycję **Wybierz**.

    ![Zrzut ekranu, który wyróżnia opcję Microsoft Azure zarządzania.](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Na stronie **Aplikacje w chmurze** wybierz pozycję **Gotowe**.
14. Na stronie **Nowy** w sekcji **kontrole dostępu** wybierz pozycję **Udziel**.
15. Na stronie **Udzielanie** wybierz pozycję **Udziel dostępu**, zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wybierz**.

    ![Zrzut ekranu przedstawiający opcję Wymagaj uwierzytelniania wieloskładnikowego](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. W obszarze **Włącz zasady** wybierz pozycję **na**.

    ![Zrzut ekranu przedstawiający opcję Włącz zasady ustawione na wartość włączone](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Wybierz przycisk **Utwórz**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Symulowanie logowania przy użyciu opcji What If

1.  Na stronie **zasady dostępu warunkowego** wybierz pozycję **What If**. 

    ![Zrzut ekranu, który podświetla, gdzie wybrać opcję co zrobić na stronie zasady dostępu warunkowego.](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Wybierz pozycję **Użytkownik**, wybierz testowego użytkownika-gościa, a następnie wybierz pozycję **Wybierz**.

    ![Zrzut ekranu przedstawiający wybraną użytkownika-gościa](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Wybierz pozycję **Aplikacje w chmurze**.
4.  Na stronie **Aplikacje w chmurze** wybierz pozycję **Wybierz aplikację**, a następnie kliknij pozycję **Wybierz**. Na liście aplikacji wybierz pozycję **Microsoft Azure Management**, a następnie kliknij pozycję **Wybierz**. 

    ![Zrzut ekranu przedstawiający wybraną aplikację do zarządzania Microsoft Azure](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Na stronie **Aplikacje w chmurze** wybierz pozycję **Gotowe**.
6.  Wybierz pozycję **What If** i sprawdź, czy nowe zasady są wyświetlane w obszarze **Wyniki oceny** na karcie **Zasady, które będą stosowane**.

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji co zrobić](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego
1.  Użyj nazwy użytkownika testowego i hasła do zalogowania się do witryny [Azure Portal](https://portal.azure.com/).
2.  Powinno zostać wyświetlone żądanie dotyczące dodatkowych metod uwierzytelniania. Pamiętaj, że zastosowanie zasad może potrwać pewien czas.

    ![Zrzut ekranu przedstawiający komunikat o większej liczbie wymaganych informacji](media/tutorial-mfa/mfa-required.png)
 
3.  Wyloguj się.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy nie jest już potrzebne, Usuń użytkownika testowego i testowe zasady dostępu warunkowego.
1.  Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4.  Wybierz użytkownika testowego, a następnie wybierz pozycję **Usuń użytkownika**.
5.  W lewym okienku wybierz pozycję **Azure Active Directory**.
6.  W obszarze **Zabezpieczenia** wybierz pozycję **Dostęp warunkowy**.
7.  Na liście **Nazwa zasad** wybierz menu kontekstowe (...) dla zasad testów, a następnie wybierz pozycję **Usuń**. Kliknij przycisk **Tak**, aby potwierdzić.

## <a name="next-steps"></a>Następne kroki
W tym samouczku zostały utworzone zasady dostępu warunkowego, które wymagają, aby użytkownicy-Goście korzystali z usługi MFA podczas logowania się do jednej z aplikacji w chmurze. Aby dowiedzieć się więcej na temat dodawania użytkowników-gości do współpracy, zobacz [Add Azure Active Directory B2B collaboration users in the Azure portal (Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure Portal)](add-users-administrator.md).
