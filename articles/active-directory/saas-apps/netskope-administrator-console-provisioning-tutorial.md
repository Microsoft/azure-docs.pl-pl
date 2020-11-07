---
title: 'Samouczek: Konfigurowanie uwierzytelniania użytkownika Netskope dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Informacje o konfigurowaniu Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w celu Netskope uwierzytelniania użytkowników.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357474"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie uwierzytelniania użytkownika Netskope na potrzeby automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Netskope uwierzytelniania użytkowników i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Netskope uwierzytelniania użytkowników.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa uwierzytelniania użytkownika Netskope](https://www.netskope.com/)
* Konto użytkownika w Netskope uwierzytelnianiu użytkowników z uprawnieniami administratora.

## <a name="assigning-users-to-netskope-user-authentication"></a>Przypisywanie użytkowników do Netskope uwierzytelniania użytkowników

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Netskope uwierzytelniania użytkowników. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Netskope uwierzytelniania użytkowników, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Ważne porady dotyczące przypisywania użytkowników do Netskope uwierzytelniania użytkowników

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Netskope uwierzytelniania użytkowników w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Netskope uwierzytelniania użytkownika należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Konfigurowanie uwierzytelniania użytkownika Netskope na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej uwierzytelniania użytkownika programu Netskope](https://netskope.goskope.com/). Przejdź do **strony głównej > ustawienia**.

    ![Konsola administracyjna uwierzytelniania użytkownika Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Przejdź do **menu Narzędzia**. W menu **Narzędzia** przejdź do pozycji **Narzędzia katalogów > integracja Standard scim**.

    ![Narzędzia do uwierzytelniania użytkowników Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope uwierzytelniania użytkowników Dodaj Standard scim](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Przewiń w dół i kliknij przycisk **Dodaj token** . W oknie dialogowym **Dodawanie nazwy klienta OAuth** Podaj **nazwę klienta** i kliknij przycisk **Zapisz** .

    ![Netskope — Dodawanie tokenu uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Nazwa klienta uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Skopiuj **adres URL serwera Standard scim** i **token**. Te wartości zostaną wprowadzone w polach adres URL dzierżawy i klucz tajny tokenu na karcie aprowizacji aplikacji Netskope User Authentication w Azure Portal.

    ![Netskope — Tworzenie tokenu uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania użytkownika Netskope z galerii

Przed skonfigurowaniem uwierzytelniania użytkowników Netskope na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać uwierzytelnianie użytkownika Netskope z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać uwierzytelnianie użytkowników Netskope z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Netskope uwierzytelnianie użytkownika** , wybierz pozycję **Netskope uwierzytelnianie użytkowników** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Netskope uwierzytelniania użytkowników na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Konfigurowanie automatycznej aprowizacji użytkowników w celu Netskope uwierzytelniania użytkowników 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Netskope uwierzytelniania użytkowników na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML na potrzeby uwierzytelniania użytkowników za pomocą usługi Netskope, postępując zgodnie z instrukcjami podanymi w [samouczku Logowanie jednokrotne uwierzytelniania użytkownika Netskope](./netskope-cloud-security-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Aby dowiedzieć się więcej na temat Standard scim punktu końcowego Netskope uwierzytelniania użytkownika, zapoznaj się z [tym](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)tematem.

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w celu Netskope uwierzytelniania użytkowników w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Netskope User Authentication**.

    ![Link uwierzytelniania użytkownika Netskope na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość **adresu URL serwera Standard scim** , która została pobrana wcześniej w **adresie URL dzierżawy**. Wprowadź wartość **tokenu** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Netskope Authentication. Jeśli połączenie nie powiedzie się, upewnij się, że konto uwierzytelniania użytkownika Netskope ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Netskope uwierzytelnianie użytkowników**.

    ![Netskope mapowania użytkowników uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby Netskope uwierzytelnianie użytkowników w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w Netskope uwierzytelniania użytkowników w ramach operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Netskope atrybuty użytkownika uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Netskope uwierzytelnianie użytkowników**.

    ![Netskope mapowania grup uwierzytelniania użytkowników](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługą Azure AD, aby Netskope uwierzytelnianie użytkowników w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Netskope uwierzytelniania użytkowników dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Netskope atrybuty grupy uwierzytelniania użytkownika](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning na potrzeby uwierzytelniania użytkowników Netskope, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby Netskope uwierzytelnianie użytkowników, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na Netskope uwierzytelniania użytkowników.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)