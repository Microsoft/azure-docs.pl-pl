---
title: 'Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060495"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Samanage na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w Samanage i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i grup w usłudze Samanage.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji typu "oprogramowanie jako usługa" (SaaS) za pomocą Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Samanage](https://www.samanage.com/pricing/) z pakietem Professional.
* Konto użytkownika w Samanage z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API REST Samanage](https://www.samanage.com/api/). Ten interfejs API jest dostępny dla deweloperów Samanage dla kont z pakietem Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Dodawanie Samanage z witryny Azure Marketplace

Przed skonfigurowaniem usługi Samanage na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD Dodaj Samanage z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać Samanage z portalu Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Samanage** i wybierz pozycję **Samanage** w panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Samanage na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Przypisywanie użytkowników do Samanage

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Samanage. Aby przypisać tych użytkowników lub grupy do Samanage, postępuj zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Samanage

*    Dzisiaj role Samanage są automatycznie i są wypełniane dynamicznie w interfejsie użytkownika Azure Portal. Przed przypisaniem ról Samanage do użytkowników upewnij się, że synchronizacja początkowa została ukończona z Samanage, aby pobrać najnowsze role w dzierżawie Samanage.

*    Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do Samanage w celu przetestowania początkowej automatycznej konfiguracji inicjowania obsługi użytkowników. Po pomyślnym zakończeniu testów można przypisać dodatkowych użytkowników i grupy.

*    Po przypisaniu użytkownika do Samanage wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisanie. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Samanage

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie Samanage na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć rejestrację jednokrotną opartą na protokole SAML dla Samanage. Postępuj zgodnie z instrukcjami podanymi w [samouczku logowania](samanage-tutorial.md)jednokrotnego Samanage. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Samanage w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **pozycję aplikacje** > dla przedsiębiorstw**wszystkie aplikacje** > **Samanage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Samanage**.

    ![Link Samanage na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Inicjowanie obsługi Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** Samanage i **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Samanage. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Samanage ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Połączenie testowe Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Samanage**.

    ![Samanage synchronizacji użytkowników](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Samanage w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Samanage for Updates. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Samanage pasujące atrybuty użytkownika](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Aby włączyć mapowania grup, w sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z Samanage**.

    ![Synchronizacja grupy Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Ustaw wartość **tak** **, aby** synchronizować grupy. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Samanage w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Samanage for Updates. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Samanage pasujące atrybuty grupy](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning for Samanage, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Stan aprowizacji Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Samanage. W sekcji **Ustawienia** wybierz wartości, które mają być w **zakresie**. Po wybraniu opcji **Synchronizuj wszystkich użytkowników i grup** Uwzględnij ograniczenia zgodnie z opisem w poniższej sekcji "ograniczenia łącznika".

    ![Zakres Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Samanage Zapisz](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż w przypadku późniejszej synchronizacji. Są one wykonywane co około 40 minut, o ile jest uruchomiona usługa Azure AD Provisioning. 

Możesz użyć sekcji Szczegóły synchronizacji, aby monitorować postęp i postępować zgodnie z raportem **dotyczącym** działań aprowizacji. Ten raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie Samanage.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

W przypadku wybrania opcji **Synchronizuj wszystkich użytkowników i grup** oraz skonfigurowania wartości atrybutu **role** Samanage wartość w polu **wartość domyślna, jeśli wartość null (jest opcjonalna)** musi być wyrażona w następującym formacie:

- {"displayName": "role"}, gdzie role jest wartością domyślną.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
