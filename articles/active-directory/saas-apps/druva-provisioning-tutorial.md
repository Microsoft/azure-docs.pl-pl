---
title: 'Samouczek: Konfigurowanie Druva dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Druva.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 9163c64370e0718d6b9acf37bb7a9fc28c936948
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851156"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Druva na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Druva i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Druva.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Druva](https://www.druva.com/products/pricing-plans/).
* Konto użytkownika w Druva z uprawnieniami administratora.

## <a name="assigning-users-to-druva"></a>Przypisywanie użytkowników do Druva

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Druva. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Druva, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Druva

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Druva w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Druva należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-druva-for-provisioning"></a>Konfiguracja Druva na potrzeby aprowizacji

Przed skonfigurowaniem usługi Druva na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na Druva.

1. Zaloguj się do [konsoli administracyjnej Druva](https://console.druva.com). Przejdź do **Druva > synchronizacji**.

    ![Konsola administracyjna Druva](media/druva-provisioning-tutorial/menubar.png)

2. Przejdź do **narzędzia Zarządzanie**  >  **wdrożeniami**  >  **Użytkownicy**.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną Druva. Polecenie Zarządzaj jest wyróżnione i widoczne jest menu Zarządzaj. W tym menu, w obszarze wdrożenia, wyróżniono użytkowników." border="false":::

3.  Przejdź do **ustawień**. Kliknij przycisk **Generuj token**.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną Druva. Polecenie Zarządzaj jest wyróżnione i widoczne jest menu Zarządzaj. W tym menu, w obszarze wdrożenia, wyróżniono użytkowników." border="false":::

4.  Skopiuj wartość **tokenu uwierzytelniania** . Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji Druva w Azure Portal.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną Druva. Polecenie Zarządzaj jest wyróżnione i widoczne jest menu Zarządzaj. W tym menu, w obszarze wdrożenia, wyróżniono użytkowników." border="false":::

## <a name="add-druva-from-the-gallery"></a>Dodaj Druva z galerii

Aby skonfigurować Druva automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Druva z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Druva z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Druva**, wybierz pozycję **Druva** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Druva na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Druva 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Druva na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Druva, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](druva-tutorial.md)jednokrotne w Druva. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Druva w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Druva**.

    ![Link do aplikacji Druva na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5.  W sekcji poświadczenia administratora wprowadź `https://apis.druva.com/insync/scim` **adres URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania** w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Druva. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Druva ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie wybierz opcję **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Druva**.

    ![Druva mapowania użytkowników](media/druva-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Druva w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Druva for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Druva atrybuty użytkownika](media/druva-provisioning-tutorial/userattribute.png)


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Druva, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Druva, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

    Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Druva.

    Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Ograniczenia łącznika

* Druva wymaga **wiadomości e-mail** jako obowiązkowego atrybutu. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).
