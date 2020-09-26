---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler dwóch dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: W ramach tego samouczka dowiesz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w celu rozwiązania Zscaler dwóch.
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
ms.openlocfilehash: b3cd69876eb5fa18d9763bd7f89233d7608384ef
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317547"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler dwóch dla automatycznej aprowizacji użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i/lub grup, aby rozwiązania Zscaler dwa.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest oparty na usłudze aprowizacji użytkowników usługi Azure AD. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania oraz odpowiedzi na często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i Cofanie udostępniania do aplikacji SaaS przy użyciu Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebne są następujące elementy:

* Dzierżawa usługi Azure AD.
* Rozwiązania Zscaler dwie dzierżawy.
* Konto użytkownika w rozwiązania Zscaler dwa z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na dwóch rozwiązania Zscaler interfejsie API Standard scim, który jest dostępny dla kont przedsiębiorstwa.

## <a name="add-zscaler-two-from-the-gallery"></a>Dodaj rozwiązania Zscaler dwa z galerii

Przed skonfigurowaniem rozwiązania Zscaler dwóch dla automatycznego inicjowania obsługi użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler dwa z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**:

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wpisz **rozwiązania Zscaler dwa**. W wynikach wybierz pozycję **rozwiązania Zscaler** , a następnie wybierz pozycję **Dodaj**.

![Lista wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Przypisywanie użytkowników do rozwiązania Zscaler dwóch

Użytkownicy usługi Azure AD muszą mieć przypisany dostęp do wybranych aplikacji, zanim będą mogli z nich korzystać. W kontekście automatycznej aprowizacji użytkowników synchronizacja dotyczy tylko użytkowników lub grup przypisanych do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler dwóch. Po podjęciu decyzji o tym można przypisać tych użytkowników i grupy do rozwiązania Zscaler dwóch, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Ważne wskazówki dotyczące przypisywania użytkowników do rozwiązania Zscaler dwóch

* Zalecamy, aby najpierw przypisać pojedynczy użytkownik usługi Azure AD do rozwiązania Zscaler dwóch, aby przetestować automatyczną konfigurację inicjowania obsługi użytkowników. Możesz później przypisać więcej użytkowników i grup.

* Po przypisaniu użytkownika do rozwiązania Zscaler dwóch, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznego aprowizacji użytkowników

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w programie rozwiązania Zscaler w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla rozwiązania Zscaler dwóch. Jeśli to zrobisz, postępuj zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego w rozwiązania Zscaler](zscaler-two-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, ale te dwie funkcje uzupełniają się wzajemnie.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **rozwiązania Zscaler dwa**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler dwa**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** :

    ![Rozwiązania Zscaler dwie aprowizacji](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**:

    ![Ustaw tryb aprowizacji](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** konta rozwiązania Zscaler, zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **token wpisu tajnego**, przejdź do pozycji **Administracja**  >  **Ustawienia uwierzytelniania** w dwóch portalach rozwiązania Zscaler i wybierz pozycję **SAML** w obszarze **Typ uwierzytelniania**:

    ![Rozwiązania Zscaler dwa ustawienia uwierzytelniania](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Wybierz pozycję **Konfiguruj SAML** , aby otworzyć okno **Konfigurowanie protokołu SAML** :

    ![Konfigurowanie okna SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Zaznacz opcję **Włącz Inicjowanie obsługi opartej na Standard scim** i skopiuj **podstawowy adres URL** oraz **token okaziciela**, a następnie Zapisz ustawienia. W Azure Portal wklej **podstawowy adres URL** w polu **adres URL dzierżawy** i **token okaziciela** do pola **token klucza tajnego** .

7. Po wprowadzeniu wartości w polach **adres URL dzierżawy** i **token tajny** wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że konto rozwiązania Zscaler ma uprawnienia administratora i spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Wybierz opcję **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**:

    ![Konfigurowanie wiadomości e-mail z powiadomieniem](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do ZscalerTwo**:

    ![Synchronizuj użytkowników usługi Azure AD](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, aby rozwiązania Zscaler dwa w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w rozwiązania Zscaler dwóch dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetloną siedem mapowań.](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do ZscalerTwo**:

    ![Synchronizowanie grup usługi Azure AD](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, aby rozwiązania Zscaler dwa w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler dwie dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetlonymi trzema mapowaniami.](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę Azure AD Provisioning dla rozwiązania Zscaler dwóch, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** :

    ![Stan aprowizacji](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do rozwiązania Zscaler dwóch, wybierając odpowiednie wartości w obszarze **zakres** w sekcji **Ustawienia** :

    ![Wartości zakresu](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**:

    ![Wybieranie pozycji Zapisz](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w obszarze **zakres** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Postęp można monitorować w sekcji **szczegóły synchronizacji** . Możesz również śledzić łącza do raportu działań aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie rozwiązania Zscaler.

Informacje o sposobie odczytywania dzienników aprowizacji usługi Azure AD znajdują się w temacie [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
