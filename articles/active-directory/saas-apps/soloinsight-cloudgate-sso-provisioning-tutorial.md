---
title: 'Samouczek: Konfigurowanie Soloinsight-CloudGate rejestracji jednokrotnej w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w celu Soloinsight-CloudGate rejestracji jednokrotnej.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347501"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Soloinsight-CloudGate rejestracji jednokrotnej w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Soloinsight-CloudGate rejestracji jednokrotnej i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Soloinsight-CloudGate rejestracji jednokrotnej.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Soloinsight-CloudGate dzierżawy SSO](https://www.soloinsight.com/)
* Konto użytkownika w Soloinsight-CloudGate rejestracji jednokrotnej z uprawnieniami administratora.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Przypisywanie użytkowników do Soloinsight-CloudGate Logowanie jednokrotne

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Soloinsight-CloudGate rejestracji jednokrotnej. Po ustaleniu tych użytkowników i/lub grup można przypisać do Soloinsight-CloudGate rejestracji jednokrotnej, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Ważne porady dotyczące przypisywania użytkowników do Soloinsight-CloudGate rejestracji jednokrotnej

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Soloinsight-CloudGate rejestracji jednokrotnej w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Soloinsight-CloudGate rejestracji jednokrotnej należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Skonfiguruj Soloinsight-CloudGate Logowanie jednokrotne w celu aprowizacji

1. Zaloguj się do [konsoli administratora logowania jednokrotnego w usłudze Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Przejdź do **> Administracja ustawienia systemowe**.

    ![Soloinsight-CloudGate konsoli administratora logowania jednokrotnego](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Przejdź do **ogólnego**.

    ![Soloinsight-CloudGate dodawania Standard scim rejestracji jednokrotnej](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Przewiń w dół do końca strony, aby uzyskać **adres URL dzierżawy** i **token klucza tajnego**. Skopiuj **token tajny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji logowania jednokrotnego Soloinsight-CloudGate w Azure Portal.

    ![Soloinsight-CloudGate tworzenia tokenu logowania jednokrotnego](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie Soloinsight-CloudGate rejestracji jednokrotnej z galerii

Przed rozpoczęciem konfigurowania Soloinsight-CloudGate rejestracji jednokrotnej w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD należy dodać do listy zarządzanych aplikacji SaaS Soloinsight-CloudGate Logowanie jednokrotne z galerii aplikacji usługi Azure AD.

**Aby dodać Soloinsight-CloudGate Logowanie jednokrotne z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Soloinsight-CLOUDGATE SSO**, wybierz **SOLOINSIGHT-CloudGate SSO** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Soloinsight-CloudGate SSO na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurowanie automatycznego aprowizacji użytkowników w usłudze Soloinsight-CloudGate Logowanie jednokrotne 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Soloinsight-CloudGate rejestracji jednokrotnej na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Soloinsight-CloudGate rejestracji jednokrotnej, postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [usłudze Soloinsight-CloudGate](./soloinsight-cloudgate-sso-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla Soloinsight-CloudGate logowania jednokrotnego w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.

    ![Link do aplikacji Soloinsight-CloudGate SSO na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **adres URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może nawiązać połączenie z usługą Soloinsight-CloudGate SSO. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi SSO Soloinsight-CloudGate ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Soloinsight-CloudGate Logowanie jednokrotne**.

    ![Soloinsight-CloudGate mapowania użytkowników SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby Soloinsight-CloudGate Logowanie jednokrotne w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w Soloinsight-CloudGate Logowanie jednokrotne dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Soloinsight-CloudGate atrybuty użytkownika logowania jednokrotnego](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Soloinsight-CloudGate Logowanie jednokrotne**.

    ![Mapowania grup Logowanie jednokrotne Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługą Azure AD, aby Soloinsight-CloudGate Logowanie jednokrotne w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Soloinsight-CloudGate rejestracji Jednokrotnej dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Soloinsight-CloudGate atrybuty grupy logowania jednokrotnego](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Soloinsight-CloudGate rejestracji jednokrotnej, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby Soloinsight-CloudGate Logowanie jednokrotne, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na Soloinsight-CloudGate rejestracji jednokrotnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)