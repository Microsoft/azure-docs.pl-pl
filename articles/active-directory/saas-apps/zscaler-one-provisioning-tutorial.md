---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w celu rozwiązania Zscaler jednego.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937132"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w rozwiązania Zscaler jeden i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i grup w celu rozwiązania Zscaler.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji typu "oprogramowanie jako usługa" (SaaS) za pomocą Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz:

* Dzierżawa usługi Azure AD.
* Rozwiązania Zscaler jedną dzierżawę.
* Konto użytkownika w rozwiązania Zscaler jeden z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na rozwiązania Zscaler jednego interfejsu API Standard scim. Ten interfejs API jest dostępny dla rozwiązania Zscaler jednego dewelopera dla kont z pakietem Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Dodaj rozwiązania Zscaler jeden z portalu Azure Marketplace

Przed skonfigurowaniem usługi rozwiązania Zscaler na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD Dodaj rozwiązania Zscaler z witryny Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać rozwiązania Zscaler jeden z portalu Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązania Zscaler** , a następnie wybierz pozycję **rozwiązania Zscaler (jeden** ) w panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Rozwiązania Zscaler jeden na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Przypisywanie użytkowników do rozwiązania Zscaler

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler. Aby przypisać tych użytkowników lub grupy do rozwiązania Zscaler jednej, postępuj zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Ważne wskazówki dotyczące przypisywania użytkowników do rozwiązania Zscaler jednego

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD, aby rozwiązania Zscaler go w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Możesz później przypisywać dodatkowych użytkowników lub grupy.

* Po przypisaniu użytkownika do rozwiązania Zscaler go wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisanie. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurowanie automatycznej aprowizacji użytkowników w celu rozwiązania Zscaler jednego

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w rozwiązania Zscaler jednej na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla rozwiązania Zscaler. Postępuj zgodnie z instrukcjami podanymi w [samouczku logowanie](zscaler-One-tutorial.md)jednokrotne w rozwiązania Zscaler. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Po zainicjowaniu obsługi administracyjnej użytkowników i grup, zalecamy okresowe ponowne uruchomienie aprowizacji, aby upewnić się, że członkostwa w grupach zostały prawidłowo zaktualizowane. Ponowne uruchomienie spowoduje wymuszenie ponownego oszacowania wszystkich grup i zaktualizowania członkostw przez naszą usługę.  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla rozwiązania Zscaler w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **rozwiązania Zscaler jeden**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler jeden**.

    ![Łącze rozwiązania Zscaler 1 na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Rozwiązania Zscaler jedno Inicjowanie obsługi administracyjnej](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Rozwiązania Zscaler jeden tryb aprowizacji](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **poświadczenia administratora** Wypełnij pola **adres URL dzierżawy** i **token tajny** , podając ustawienia dla rozwiązania Zscaler jednego konta, zgodnie z opisem w kroku 6.

6. Aby uzyskać adres URL dzierżawy i token tajny, przejdź do pozycji **Administracja**  >  **Ustawienia uwierzytelniania** w interfejsie użytkownika rozwiązania Zscaler jednego portalu. W obszarze **Typ uwierzytelniania** wybierz pozycję **SAML**.

    ![Rozwiązania Zscaler jedno z ustawień uwierzytelniania](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Wybierz pozycję **Konfiguruj SAML** , aby otworzyć Opcje **konfigurowania protokołu SAML** .

    ![Rozwiązania Zscaler jeden Konfigurowanie SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Wybierz pozycję **Włącz obsługę administracyjną SCIM-Based** , aby pobrać ustawienia z **podstawowego adresu URL** i **tokenu okaziciela**. Następnie Zapisz ustawienia. Skopiuj ustawienie **podstawowego adresu URL** do **adresu URL dzierżawy** w Azure Portal. Skopiuj ustawienie **tokenu okaziciela** do **tokenu tajnego** w Azure Portal.

7. Po wypełnieniu pól przedstawionych w kroku 5 wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że rozwiązania Zscaler jedno konto ma uprawnienia administratora i spróbuj ponownie.

    ![Rozwiązania Zscaler jedno połączenie testowe](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Rozwiązania Zscaler jedną wiadomość E-mail z powiadomieniem](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby rozwiązania Zscaler jeden**.

    ![Rozwiązania Zscaler synchronizacji jednego użytkownika](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby rozwiązania Zscaler je w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w rozwiązania Zscaler jeden dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Rozwiązania Zscaler jeden pasujący atrybut użytkownika](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby rozwiązania Zscaler jeden**.

    ![Rozwiązania Zscaler jednej synchronizacji grupy](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, aby rozwiązania Zscaler je w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler jeden dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Rozwiązania Zscaler jeden pasujący atrybut grupy](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning for rozwiązania Zscaler, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Rozwiązania Zscaler jednego stanu aprowizacji](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić, aby rozwiązania Zscaler je. W sekcji **Ustawienia** wybierz wartości, które mają być w **zakresie**.

    ![Rozwiązania Zscaler jeden zakres](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Rozwiązania Zscaler jednym zapisem](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż w przypadku późniejszej synchronizacji. Są one wykonywane co około 40 minut, o ile jest uruchomiona usługa Azure AD Provisioning. 

Możesz użyć sekcji Szczegóły synchronizacji, aby monitorować postęp i postępować zgodnie z raportem **dotyczącym** działań aprowizacji. Ten raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie rozwiązania Zscaler.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png