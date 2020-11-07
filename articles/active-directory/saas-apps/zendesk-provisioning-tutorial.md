---
title: 'Samouczek: Konfigurowanie systemu Zendesk dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze systemu Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359718"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie systemu Zendesk na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w systemu Zendesk i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i grup w usłudze systemu Zendesk.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji typu "oprogramowanie jako usługa" (SaaS) za pomocą Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawa systemu Zendesk z planem Professional lub lepszym włączona.
* Konto użytkownika w systemu Zendesk z uprawnieniami administratora.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Dodawanie systemu Zendesk z witryny Azure Marketplace

Przed skonfigurowaniem usługi systemu Zendesk na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD Dodaj systemu Zendesk z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać systemu Zendesk z portalu Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **systemu Zendesk** i wybierz pozycję **systemu Zendesk** w panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Aplikacja Zendesk na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Przypisywanie użytkowników do systemu Zendesk

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do systemu Zendesk. Aby przypisać tych użytkowników lub grupy do systemu Zendesk, postępuj zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Ważne wskazówki dotyczące przypisywania użytkowników do systemu Zendesk

* Dzisiaj role systemu Zendesk są automatycznie i są wypełniane dynamicznie w interfejsie użytkownika Azure Portal. Przed przypisaniem ról systemu Zendesk do użytkowników upewnij się, że synchronizacja początkowa została ukończona z systemu Zendesk, aby pobrać najnowsze role w dzierżawie systemu Zendesk.

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do systemu Zendesk w celu przetestowania początkowej automatycznej konfiguracji inicjowania obsługi użytkowników. Po pomyślnym zakończeniu testów można przypisywać dodatkowych użytkowników lub grupy.

* Po przypisaniu użytkownika do systemu Zendesk wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisanie. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurowanie automatycznej aprowizacji użytkowników do systemu Zendesk 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie systemu Zendesk na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć rejestrację jednokrotną opartą na protokole SAML dla systemu Zendesk. Postępuj zgodnie z instrukcjami podanymi w [samouczku logowania](zendesk-tutorial.md)jednokrotnego systemu Zendesk. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla systemu Zendesk w usłudze Azure AD

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **systemu Zendesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **systemu Zendesk**.

    ![Link systemu Zendesk na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Inicjowanie obsługi systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Tryb aprowizacji systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. W sekcji **poświadczenia administratora** wprowadź nazwę użytkownika admin, klucz tajny i domenę konta systemu Zendesk. Przykłady tych wartości:

   * W polu **Nazwa użytkownika administratora** wprowadź nazwę użytkownika konta administratora w dzierżawie systemu Zendesk. Może to być na przykład admin@contoso.com.

   * W polu **token Secret** Wypełnij token tajny, zgodnie z opisem w kroku 6.

   * W polu **domena** Wypełnij poddomenę dzierżawy systemu Zendesk. Na przykład w przypadku konta z adresem URL dzierżawy dla `https://my-tenant.zendesk.com` domeny podrzędnej jest **moja dzierżawa**.

6. Token tajny dla konta usługi systemu Zendesk znajduje się w **Admin** obszarze  >  **Ustawienia interfejsu API** administratora  >  **Settings**. Upewnij się, że **dostęp do tokenu** jest ustawiony na **włączone**.

    ![Ustawienia administratora systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Systemu Zendesk — token tajny](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po wypełnieniu pól przedstawionych w kroku 5 wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą systemu Zendesk. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi systemu Zendesk ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Połączenie testowe systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do systemu Zendesk**.

    ![Systemu Zendesk synchronizacji użytkowników](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do systemu Zendesk w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie systemu Zendesk for Updates. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Systemu Zendesk pasujące atrybuty użytkownika](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do systemu Zendesk**.

    ![Synchronizacja grupy systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do systemu Zendesk w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w systemu Zendesk dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Systemu Zendesk pasujące atrybuty grupy](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning for systemu Zendesk, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Stan aprowizacji systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić systemu Zendesk. W sekcji **Ustawienia** wybierz wartości, które mają być w **zakresie**.

    ![Zakres systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Systemu Zendesk Zapisz](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż w przypadku późniejszej synchronizacji. Są one wykonywane co około 40 minut, o ile jest uruchomiona usługa Azure AD Provisioning. 

Możesz użyć sekcji Szczegóły synchronizacji, aby monitorować postęp i postępować zgodnie z raportem **dotyczącym** działań aprowizacji. Ten raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie systemu Zendesk.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Systemu Zendesk obsługuje używanie grup tylko dla użytkowników z rolami **agentów** . Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją systemu Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Gdy rola niestandardowa zostanie przypisana do użytkownika lub grupy, usługa automatycznego aprowizacji użytkowników w usłudze Azure AD przypisze również domyślnego **agenta** roli. Tylko agenci mogą mieć przypisaną rolę niestandardową. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API systemu Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
