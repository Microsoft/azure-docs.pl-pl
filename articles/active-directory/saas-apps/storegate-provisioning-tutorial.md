---
title: 'Samouczek: Konfigurowanie Storegate dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357947"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Storegate na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Storegate i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Storegate.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Storegate](https://www.storegate.com)
* Konto użytkownika na Storegate z uprawnieniami administratora.

## <a name="assign-users-to-storegate"></a>Przypisywanie użytkowników do Storegate

Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Storegate. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Storegate, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Storegate

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Storegate w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Storegate należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-storegate-for-provisioning"></a>Konfigurowanie Storegate na potrzeby aprowizacji

Przed skonfigurowaniem usługi Storegate do automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z Storegate.

1. Zaloguj się do [konsoli administracyjnej Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) i przejdź do ustawień, klikając ikonę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Ustawienia konta**.

    ![Storegate Dodaj Standard scim](media/storegate-provisioning-tutorial/admin.png)

2. W obszarze Ustawienia przejdź do **ustawień Team >** i sprawdź, czy przełącznik przełączania jest włączony w sekcji **Logowanie** jednokrotne.

    ![Ustawienia Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Przycisk przełączania Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Skopiuj **adres URL dzierżawy** i **token**. Te wartości zostaną wprowadzone w polach **adres URL dzierżawy** i **klucz tajny tokenu** odpowiednio na karcie aprowizacji aplikacji Storegate w Azure Portal. 

    ![Utwórz token Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Dodaj Storegate z galerii

Aby skonfigurować Storegate automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Storegate z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Storegate** , a następnie wybierz pozycję **Storegate** w panelu wyniki. 

    ![Storegate na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Utwórz konto w usłudze Storegate** , który przekieruje Cię do strony logowania usługi Storegate. 

    ![Storegate OIDC Dodaj](media/storegate-provisioning-tutorial/signup.png)

6.  Zaloguj się do [konsoli administracyjnej Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) i przejdź do ustawień, klikając ikonę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Ustawienia konta**.

    ![Storegate logowanie](media/storegate-provisioning-tutorial/admin.png)

7. W obszarze Ustawienia przejdź do **ustawień Team >** i kliknij pozycję Przełącz przełącznik w sekcji Logowanie jednokrotne, co spowoduje rozpoczęcie wyrażania zgody. Kliknij pozycję **Aktywuj**.

    ![Zespół Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Storegate Logowanie jednokrotne](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate Aktywuj](media/storegate-provisioning-tutorial/activate.png)

8. Ponieważ Storegate jest aplikacją OpenIDConnect, wybierz logowanie do Storegate przy użyciu konta służbowego firmy Microsoft.

    ![Storegate OIDC logowania](media/storegate-provisioning-tutorial/login.png)

9. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta Storegate.

    ![Storegate OIDc](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Storegate 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Storegate na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym Standard scim Storegate, zapoznaj się z [tym](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)tematem.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Storegate w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Storegate**.

    ![Link Storegate na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://dialpad.com/scim` **adres URL dzierżawy**. Wprowadź wartość, która została pobrana i zapisana wcześniej z Storegate w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Storegate. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Storegate ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Storegate**.

    ![Storegate mapowania użytkowników](media/storegate-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Storegate w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Storegate for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Storegate atrybuty użytkownika](media/storegate-provisioning-tutorial/userattributes.png)

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Storegate, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Storegate, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Storegate.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
