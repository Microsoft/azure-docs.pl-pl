---
title: 'Samouczek: Konfigurowanie Templafy OpenID Connect Connect dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w programie Templafy OpenID Connect Connect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: f9e462d66e438e42c40b16ba9b818ddd330e9e5a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614763"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Templafy OpenID Connect Connect dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w programie Templafy OpenID Connect Connect i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Templafy OpenID Connect Connect.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Templafy](https://www.templafy.com/pricing/).
* Konto użytkownika w Templafy z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i Templafy OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="assigning-users-to-templafy-openid-connect"></a>Przypisywanie użytkowników do Templafy OpenID Connect Connect

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Templafy OpenID Connect Connect. Po ustaleniu tych użytkowników i/lub grup można przypisać do Templafy OpenID Connect Connect, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Ważne porady dotyczące przypisywania użytkowników do Templafy OpenID Connect Connect

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Templafy OpenID Connect Connect, aby przetestować konfigurację automatycznej aprowizacji użytkowników. Więcej użytkowników i/lub grup można przypisać później.

* Podczas przypisywania użytkownika do Templafy OpenID Connect Connect należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi Templafy OpenID Connect Connect, aby obsługiwać obsługę administracyjną w usłudze Azure AD

Przed skonfigurowaniem usługi Templafy OpenID Connect Connect w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na Templafy OpenID Connect Connect.

1. Zaloguj się do konsoli administracyjnej Templafy. Kliknij pozycję **Administracja**.

    ![Konsola administracyjna Templafy](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Kliknij pozycję **Metoda uwierzytelniania**.

    ![Zrzut ekranu przedstawiający sekcję administrowanie Templafy z opcją Metoda uwierzytelniania o nazwie out.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Skopiuj wartość **klucz API Standard scim-Key** . Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji Templafy OpenID Connect Connect w Azure Portal.

    ![Zrzut ekranu przedstawiający S C I M A P.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>Krok 3. Dodawanie Templafy OpenID Connect Connect z galerii

Aby skonfigurować usługę Templafy OpenID Connect Connect w celu automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz dodać Templafy OpenID Connect Connect z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Templafy OpenID Connect Connect z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Templafy OpenID Connect Connect**, wybierz pozycję **Templafy OpenID Connect Connect** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Templafy OpenID Connect Connect na liście wyników](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>Krok 4. Konfigurowanie automatycznego aprowizacji użytkowników w programie Templafy OpenID Connect Connect 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Templafy OpenID Connect Connect na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na programie OpenID Connect Connect dla Templafy, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](templafy-tutorial.md)jednokrotne w Templafy. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w programie Templafy OpenID Connect Connect w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Templafy OpenID Connect Connect**.

    ![Link Templafy OpenID Connect Connect na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://scim.templafy.com/scim` **adres URL dzierżawy**. Wprowadź wartość **klucza API Standard scim** , która została pobrana wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Templafy. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Templafy ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Templafy OpenID Connect Connect**.

    ![Templafy OpenID Connect Połącz mapowania użytkowników](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Templafy OpenID Connect Connect w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Templafy OpenID Connect Connect for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Templafy OpenID Connect Połącz atrybuty użytkownika](media/templafy-openid-connect-provisioning-tutorial/user-attribute.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Templafy**.

    ![Mapowania grup połączeń Templafy OpenID Connect](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Templafy OpenID Connect Connect w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w programie Templafy OpenID Connect Connect for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy połączeń Templafy OpenID Connect](media/templafy-openid-connect-provisioning-tutorial/group-attribute.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu Templafy OpenID Connect Connect, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do Templafy OpenID Connect Connect, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

    Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w programie Templafy OpenID Connect Connect.

## <a name="step-5-monitor-your-deployment"></a>Krok 5. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
* Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
