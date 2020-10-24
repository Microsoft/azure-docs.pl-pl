---
title: 'Samouczek: Konfigurowanie usługi Tableau online w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze Tableau online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fa28b299a33f5386edc6ce14c523d2c332b0767b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520510"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Tableau online w celu automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w Tableau online i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i grup w usłudze Tableau online.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji typu "oprogramowanie jako usługa" (SaaS) za pomocą Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz:

*   Dzierżawa usługi Azure AD.
*   [Dzierżawa usługi Tableau online](https://www.tableau.com/).
*   Konto użytkownika w usłudze Tableau online z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API REST Tableau online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Ten interfejs API jest dostępny dla Tableau deweloperów w trybie online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Dodawanie Tableau online z portalu Azure Marketplace
Przed skonfigurowaniem usługi Tableau online w celu automatycznego aprowizacji użytkowników w usłudze Azure AD Dodaj Tableau online z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać Tableau online z portalu Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Tableau online** i wybierz pozycję **Tableau online** w panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Tableau online na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Przypisywanie użytkowników do Tableau online

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Tableau online. Aby przypisać tych użytkowników lub grupy do Tableau online, postępuj zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Ważne porady dotyczące przypisywania użytkowników do Tableau online

*   Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do Tableau online w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Możesz później przypisywać dodatkowych użytkowników lub grupy.

*   Po przypisaniu użytkownika do Tableau online wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisanie. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Tableau online

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w usłudze Tableau online na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na protokole SAML dla usługi Tableau online. Postępuj zgodnie z instrukcjami w samouczku Logowanie jednokrotne w [usłudze Tableau online](tableauonline-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Tableau online w usłudze Azure AD

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **Tableau online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Tableau online**.

    ![Link Tableau online na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Inicjowanie obsługi Tableau online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Tableau tryb aprowizacji online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość w polach domena, nazwa użytkownika administratora, hasło administratora i adres URL zawartości konta usługi Tableau online:

   * W polu **domena** Wypełnij poddomenę w oparciu o krok 6.

   * W polu **Nazwa użytkownika administratora** wprowadź nazwę użytkownika konta administratora w dzierżawie usługi Tableau online. Może to być na przykład admin@contoso.com.

   * W polu **hasło administratora** wprowadź hasło konta administratora, które odnosi się do nazwy użytkownika administratora.

   * W polu **adres URL zawartości** Wypełnij poddomenę w oparciu o krok 6.

6. Po zalogowaniu się do konta administracyjnego usługi Tableau online można uzyskać wartości dla **domeny** i **adresu URL** z adresu URL strony administracyjnej.

    * **Domenę** dla konta usługi Tableau online można skopiować z tej części adresu URL:

        ![Tableau domenę online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Adres URL zawartości** dla konta usługi Tableau online można skopiować z tej sekcji. Jest to wartość zdefiniowana podczas konfigurowania konta. W tym przykładzie wartość jest "contoso":

        ![Adres URL zawartości Tableau online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > **Domena** może się różnić od podanej tutaj.

7. Po wypełnieniu pól przedstawionych w kroku 5 wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Tableau online. Jeśli połączenie nie powiedzie się, upewnij się, że konto online Tableau ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Połączenie testowe Tableau online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem Tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Tableau**.

    ![Tableau synchronizacji użytkowników online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Tableau online w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w usłudze Tableau online dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Tableau w trybie online pasujące atrybuty użytkownika](./media/tableau-online-provisioning-tutorial/attribute.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Tableau**.

    ![Tableau synchronizacji grup online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Tableau online w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w usłudze Tableau online dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Tableau w trybie online pasujące atrybuty grupy](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning for Tableau online, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Tableau stanu aprowizacji online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Tableau online. W sekcji **Ustawienia** wybierz wartości, które mają być w **zakresie**.

    ![Tableau zakres online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Tableau zapisywanie online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż w przypadku późniejszej synchronizacji. Są one wykonywane co około 40 minut, o ile jest uruchomiona usługa Azure AD Provisioning. 

Możesz użyć sekcji Szczegóły synchronizacji, aby monitorować postęp i postępować zgodnie z raportem **dotyczącym** działań aprowizacji. Ten raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Tableau online.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Dziennik zmian
* 09/30/2020 — dodano obsługę atrybutu "authSetting" dla użytkowników.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png