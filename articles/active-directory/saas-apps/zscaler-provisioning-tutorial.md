---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze rozwiązania Zscaler.
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
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936587"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w rozwiązania Zscaler i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do rozwiązania Zscaler.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD.
* Dzierżawa rozwiązania Zscaler.
* Konto użytkownika w rozwiązania Zscaler z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na interfejsie API rozwiązania Zscaler Standard scim, który jest dostępny dla deweloperów rozwiązania Zscaler dla kont z pakietem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie rozwiązania Zscaler z galerii

Przed skonfigurowaniem usługi rozwiązania Zscaler do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązania Zscaler**, wybierz pozycję **rozwiązania Zscaler** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Rozwiązania Zscaler na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Przypisywanie użytkowników do rozwiązania Zscaler

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler. Po ustaleniu tych użytkowników i/lub grup można przypisywać do rozwiązania Zscaler, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Ważne wskazówki dotyczące przypisywania użytkowników do rozwiązania Zscaler

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do rozwiązania Zscaler w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do rozwiązania Zscaler należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurowanie automatycznej aprowizacji użytkowników do rozwiązania Zscaler

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie rozwiązania Zscaler na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla rozwiązania Zscaler, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](zscaler-tutorial.md)jednokrotne w rozwiązania Zscaler. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

> [!NOTE]
> Po zainicjowaniu obsługi administracyjnej użytkowników i grup, zalecamy okresowe ponowne uruchomienie aprowizacji, aby upewnić się, że członkostwa w grupach zostały prawidłowo zaktualizowane. Ponowne uruchomienie spowoduje wymuszenie ponownego oszacowania wszystkich grup i zaktualizowania członkostw przez naszą usługę. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla rozwiązania Zscaler w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler**.

    ![Link rozwiązania Zscaler na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu paska bocznego aplikacji dla przedsiębiorstw rozwiązania Zscaler o aprowizacji z wyróżnioną opcją aprowizacji.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu strony aprowizacji z trybem aprowizacji ustawionym na automatyczny.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** konta rozwiązania Zscaler, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny**, przejdź do pozycji **Administracja > ustawienia uwierzytelniania** w interfejsie użytkownika portalu rozwiązania Zscaler, a następnie kliknij pozycję **SAML** w obszarze **Typ uwierzytelniania**.

    ![Zrzut ekranu strony ustawień uwierzytelniania.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **Konfiguruj SAML** , aby otworzyć Opcje **SAML konfiguracji** .

    ![Zrzut ekranu przedstawiający okno dialogowe Konfigurowanie S A M L z polami tekstowymi podstawowe U i tokenem okaziciela.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Wybierz pozycję **Włącz obsługę SCIM-Based** , aby pobrać **podstawowy adres URL** i **token okaziciela**, a następnie Zapisz ustawienia. Skopiuj **podstawowy adres URL** do **adresu URL dzierżawy** i token **okaziciela**  do **tokenu tajnego** w Azure Portal.

7. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi rozwiązania Zscaler ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawiający sekcję poświadczenia administratora z opcją testowania połączenia o nazwie out.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Zrzut ekranu przedstawiający pole tekstowe powiadomienia E-mail.](./media/zscaler-provisioning-tutorial/notification.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do rozwiązania Zscaler**.

    ![Zrzut ekranu sekcji mapowania z wyróżnioną opcją Synchronizuj Azure Active Directory użytkownicy do rozwiązania Zscaler.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do rozwiązania Zscaler w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie rozwiązania Zscaler for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetloną siedem mapowań.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do rozwiązania Zscaler**.

    ![Zrzut ekranu sekcji mapowania z wyróżnioną opcją Synchronizuj Azure Active Directory grupy do rozwiązania Zscaler.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do rozwiązania Zscaler w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetlonymi trzema mapowaniami.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning dla rozwiązania Zscaler, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Zrzut ekranu przedstawiający opcję stanu aprowizacji ustawioną na wartość włączone.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić rozwiązania Zscaler, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zrzut ekranu ustawienia zakresu z wyróżnioną opcją Synchronizuj tylko przypisane Użytkownicy i grupy.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zrzut ekranu paska bocznego aplikacji rozwiązania Zscaler — Inicjowanie obsługi administracyjnej z opcją Zapisz o nazwie out.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie rozwiązania Zscaler.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png