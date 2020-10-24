---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler ZSCloud dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: W ramach tego samouczka dowiesz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze rozwiązania Zscaler ZSCloud.
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
ms.openlocfilehash: 4579fa3c6dd1e34072a31747fda5113a5ac1be2a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517433"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler ZSCloud dla automatycznej aprowizacji użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i/lub grup do rozwiązania Zscaler ZSCloud.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest oparty na usłudze aprowizacji użytkowników usługi Azure AD. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania oraz odpowiedzi na często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i Cofanie udostępniania do aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebne są następujące elementy:

* Dzierżawa usługi Azure AD.
* Dzierżawa rozwiązania Zscaler ZSCloud.
* Konto użytkownika w programie rozwiązania Zscaler ZSCloud z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na interfejsie API rozwiązania Zscaler ZSCloud Standard scim, który jest dostępny dla kont przedsiębiorstwa.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Dodaj rozwiązania Zscaler ZSCloud z galerii

Przed skonfigurowaniem usługi rozwiązania Zscaler ZSCloud w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler ZSCloud z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**:

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wprowadź **rozwiązania Zscaler ZSCloud**. W wynikach wybierz pozycję **rozwiązania Zscaler ZSCloud** , a następnie wybierz pozycję **Dodaj**.

![Lista wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Przypisywanie użytkowników do rozwiązania Zscaler ZSCloud

Użytkownicy usługi Azure AD muszą mieć przypisany dostęp do wybranych aplikacji, zanim będą mogli z nich korzystać. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które są przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler ZSCloud. Po podjęciu decyzji o tym można przypisać tych użytkowników i grupy do rozwiązania Zscaler ZSCloud, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Ważne wskazówki dotyczące przypisywania użytkowników do rozwiązania Zscaler ZSCloud

* Zalecamy, aby najpierw przypisać pojedynczego użytkownika usługi Azure AD do rozwiązania Zscaler ZSCloud w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Możesz później przypisać więcej użytkowników i grup.

* Po przypisaniu użytkownika do rozwiązania Zscaler ZSCloud, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznego aprowizacji użytkowników

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w usłudze rozwiązania Zscaler ZSCloud na podstawie przypisań użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć rejestrację jednokrotną opartą na protokole SAML dla rozwiązania Zscaler ZSCloud. Jeśli to zrobisz, postępuj zgodnie z instrukcjami podanymi w [samouczku logowanie](zscaler-zsCloud-tutorial.md)jednokrotne w programie rozwiązania Zscaler ZSCloud. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, ale te dwie funkcje uzupełniają się wzajemnie.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **rozwiązania Zscaler ZSCloud**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler ZSCloud**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** :

    ![Inicjowanie obsługi administracyjnej rozwiązania Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**:

    ![Ustaw tryb aprowizacji](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** konta rozwiązania Zscaler ZSCloud, zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny**, przejdź do pozycji **Administracja**  >  **Ustawienia uwierzytelniania** w portalu rozwiązania Zscaler ZSCloud i wybierz pozycję **SAML** w obszarze **Typ uwierzytelniania**:

    ![Rozwiązania Zscaler ZSCloud — ustawienia uwierzytelniania](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Wybierz pozycję **Konfiguruj SAML** , aby otworzyć okno **Konfigurowanie protokołu SAML** :

    ![Konfigurowanie okna SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Wybierz pozycję **włącz SCIM-Based aprowizacji** i skopiuj **podstawowy adres URL** oraz **token okaziciela**, a następnie Zapisz ustawienia. W Azure Portal wklej **podstawowy adres URL** w polu **adres URL dzierżawy** i **token okaziciela** do pola **token klucza tajnego** .

7. Po wprowadzeniu wartości w polach **adres URL dzierżawy** i **token tajny** wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą rozwiązania Zscaler ZSCloud. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi rozwiązania Zscaler ZSCloud ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Wybierz opcję **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**:

    ![Konfigurowanie wiadomości e-mail z powiadomieniem](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do ZscalerZSCloud**:

    ![Synchronizuj użytkowników usługi Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do rozwiązania Zscaler ZSCloud w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w rozwiązania Zscaler ZSCloud dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetloną siedem mapowań.](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do ZscalerZSCloud**:

    ![Synchronizowanie grup usługi Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do rozwiązania Zscaler ZSCloud w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler ZSCloud dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetlonymi trzema mapowaniami.](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning dla rozwiązania Zscaler ZSCloud, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** :

    ![Stan aprowizacji](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić rozwiązania Zscaler ZSCloud, wybierając odpowiednie wartości w obszarze **zakres** w sekcji **Ustawienia** :

    ![Wartości zakresu](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**:

    ![Wybieranie pozycji Zapisz](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w obszarze **zakres** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Postęp można monitorować w sekcji **szczegóły synchronizacji** . Możesz również śledzić łącza do raportu działań aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie rozwiązania Zscaler ZSCloud.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png