---
title: 'Samouczek: Konfigurowanie logowania jednokrotnego Soloinsight-CloudGate w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników na potrzeby logowania jednokrotnego do Soloinsight-CloudGate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063199"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie logowania jednokrotnego Soloinsight-CloudGate w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Soloinsight-CloudGate SSO i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Soloinsight-CloudGate logowania jednokrotnego.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Konto użytkownika w usłudze Soloinsight — CloudGate Logowanie jednokrotne z uprawnieniami administratora.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Przypisywanie użytkowników do Soloinsight — CloudGate Logowanie jednokrotne

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do logowania jednokrotnego Soloinsight-CloudGate. Po ustaleniu tych użytkowników i/lub grup można przypisać do Soloinsight-CloudGate rejestracji jednokrotnej, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Ważne porady dotyczące przypisywania użytkowników do Soloinsight-CloudGate rejestracji jednokrotnej

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do logowania jednokrotnego w usłudze Soloinsight-CloudGate w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Soloinsight-CloudGate rejestracji jednokrotnej należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Konfigurowanie logowania jednokrotnego Soloinsight-CloudGate na potrzeby aprowizacji

1. Zaloguj się do [konsoli administratora logowania jednokrotnego w usłudze Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Przejdź do **> Administracja ustawienia systemowe**.

    ![Soloinsight-CloudGate — Konsola administratora rejestracji jednokrotnej](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Przejdź do **ogólnego**.

    ![Soloinsight-CloudGate SSO Add Standard scim](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Przewiń w dół do końca strony, aby uzyskać **adres URL dzierżawy** i **token klucza tajnego**. Skopiuj **token tajny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji SSO Soloinsight-CloudGate w Azure Portal.

    ![Soloinsight — CloudGate — Tworzenie tokenu logowania jednokrotnego](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego Soloinsight-CloudGate z galerii

Przed skonfigurowaniem rejestracji jednokrotnej Soloinsight-CloudGate w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Logowanie jednokrotne Soloinsight-CloudGate z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Logowanie jednokrotne Soloinsight-CloudGate z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Soloinsight-CLOUDGATE SSO**, wybierz **SOLOINSIGHT-CloudGate SSO** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Soloinsight-CloudGate SSO na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Soloinsight — CloudGate Logowanie jednokrotne 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Soloinsight-CloudGate SSO na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML na potrzeby logowania jednokrotnego w usłudze Soloinsight-CloudGate, postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [Soloinsight-CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla logowania jednokrotnego w usłudze Soloinsight-CloudGate w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.

    ![Link do aplikacji Soloinsight-CloudGate SSO na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **adres URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij przycisk **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Soloinsight-CloudGate SSO Jeśli połączenie nie powiedzie się, upewnij się, że konto logowania jednokrotnego w usłudze Soloinsight-CloudGate ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Soloinsight-CloudGate Logowanie jednokrotne**.

    ![Soloinsight — CloudGate mapowania użytkowników SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Soloinsight-CloudGate Logowanie jednokrotne w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w usłudze Soloinsight-CloudGate SSO dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Soloinsight — CloudGate — atrybuty użytkownika SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory grupy, aby Soloinsight-CloudGate Logowanie jednokrotne**.

    ![Mapowania grup Logowanie jednokrotne w usłudze Soloinsight — CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Soloinsight-CloudGate SSO w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Soloinsight-CloudGate SSO dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Soloinsight — atrybuty grupy rejestracji jednokrotnej CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla logowania jednokrotnego w usłudze Soloinsight — CloudGate, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby Soloinsight-CloudGate Logowanie jednokrotne, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na Soloinsight-CloudGate SSO.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

