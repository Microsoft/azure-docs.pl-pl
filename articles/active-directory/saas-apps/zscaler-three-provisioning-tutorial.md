---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler trzech na potrzeby automatycznego inicjowania obsługi użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: W ramach tego samouczka dowiesz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników, aby rozwiązania Zscaler trzy.
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
ms.openlocfilehash: d217f21b0a2dbdad56f2147c8800bbb50b0fa45e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265531"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler trzech dla automatycznej aprowizacji użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i anulowania aprowizacji użytkowników i/lub grup, aby rozwiązania Zscaler trzy.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest oparty na usłudze aprowizacji użytkowników usługi Azure AD. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania oraz odpowiedzi na często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i Cofanie udostępniania do aplikacji SaaS przy użyciu Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebne są następujące elementy:

* Dzierżawa usługi Azure AD.
* Rozwiązania Zscaler trzy dzierżawy.
* Konto użytkownika w rozwiązania Zscaler trzy z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na interfejsie API rozwiązania Zscaler ZSCloud Standard scim, który jest dostępny dla kont przedsiębiorstwa.

## <a name="adding-zscaler-three-from-the-gallery"></a>Dodawanie rozwiązania Zscaler trzech z galerii

Przed przystąpieniem do konfigurowania rozwiązania Zscaler trzech dla automatycznej aprowizacji użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler trzy z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**:

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wprowadź **rozwiązania Zscaler trzy**. W wynikach wybierz pozycję **rozwiązania Zscaler** , a następnie wybierz pozycję **Dodaj**.

![Lista wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Przypisywanie użytkowników do rozwiązania Zscaler trzech

Użytkownicy usługi Azure AD muszą mieć przypisany dostęp do wybranych aplikacji, zanim będą mogli z nich korzystać. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które są przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler trzech. Po podjęciu decyzji o tym można przypisać tych użytkowników i grupy do rozwiązania Zscaler trzech, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Ważne porady dotyczące przypisywania użytkowników do rozwiązania Zscaler trzech

* Zalecamy, aby najpierw przypisać pojedynczy użytkownik usługi Azure AD, aby rozwiązania Zscaler trzy do testowania automatycznej konfiguracji inicjowania obsługi użytkowników. Możesz później przypisać więcej użytkowników i grup.

* Po przypisaniu użytkownika do rozwiązania Zscaler trzech, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznego aprowizacji użytkowników

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w rozwiązania Zscaler trzech opartych na przypisaniach użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla rozwiązania Zscaler trzech. Jeśli to zrobisz, postępuj zgodnie z instrukcjami podanymi w [samouczku logowania](zscaler-three-tutorial.md)jednokrotnego rozwiązania Zscaler. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, ale te dwie funkcje uzupełniają się wzajemnie.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **rozwiązania Zscaler trzy**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler trzy**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** :

    ![Rozwiązania Zscaler trzy Provisioning](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**:

    ![Ustaw tryb aprowizacji](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** swojego rozwiązania zscalerego konta użytkownika, zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny**, przejdź do pozycji **Administracja**  >  **Ustawienia uwierzytelniania** w rozwiązania Zscaler trzech Portal i wybierz pozycję **SAML** w obszarze **Typ uwierzytelniania**:

    ![Rozwiązania Zscaler trzy ustawienia uwierzytelniania](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Wybierz pozycję **Konfiguruj SAML** , aby otworzyć okno **Konfigurowanie protokołu SAML** :

    ![Konfigurowanie okna SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Wybierz pozycję **włącz SCIM-Based aprowizacji** i skopiuj **podstawowy adres URL** oraz **token okaziciela**, a następnie Zapisz ustawienia. W Azure Portal wklej **podstawowy adres URL** w polu **adres URL dzierżawy** i **token okaziciela** do pola **token klucza tajnego** .

7. Po wprowadzeniu wartości w polach **adres URL dzierżawy** i **token tajny** wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z rozwiązania zscalerą trzy. Jeśli połączenie nie powiedzie się, upewnij się, że rozwiązania Zscaler trzy konto ma uprawnienia administratora i spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Wybierz opcję **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**:

    ![Konfigurowanie wiadomości e-mail z powiadomieniem](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do ZscalerThree**:

    ![Synchronizuj użytkowników usługi Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, aby rozwiązania Zscaler trzy w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w rozwiązania Zscaler trzy dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetloną siedem mapowań.](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do ZscalerThree**:

    ![Synchronizowanie grup usługi Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, aby rozwiązania Zscaler trzy w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler trzy dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetlonymi trzema mapowaniami.](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę Azure AD Provisioning dla rozwiązania Zscaler trzy, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** :

    ![Stan aprowizacji](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby rozwiązania Zscaler trzy, wybierając odpowiednie wartości w obszarze **zakres** w sekcji **Ustawienia** :

    ![Wartości zakresu](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**:

    ![Wybieranie pozycji Zapisz](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w obszarze **zakres** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Postęp można monitorować w sekcji **szczegóły synchronizacji** . Możesz również śledzić łącza do raportu działań aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w rozwiązania Zscaler trzy.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
