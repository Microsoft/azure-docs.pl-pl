---
title: 'Samouczek: Konfigurowanie Wrike dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95988131"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Wrike na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności wykonywanych w Wrike i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników lub grup w usłudze Wrike.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania w aplikacjach typu "oprogramowanie jako usługa" (SaaS) z Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Wrike](https://www.wrike.com/price/)
* Konto użytkownika w Wrike z uprawnieniami administratora

## <a name="assign-users-to-wrike"></a>Przypisywanie użytkowników do Wrike
Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Wrike. Następnie przypisz tych użytkowników lub grupy do Wrike, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Wrike

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do Wrike w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników lub grup można przypisywać później.

* Po przypisaniu użytkownika do Wrike należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-wrike-for-provisioning"></a>Konfigurowanie Wrike na potrzeby aprowizacji

Przed skonfigurowaniem usługi Wrike do automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę zarządzania tożsamościami między domenami (standard scim) w systemie Wrike.

1. Zaloguj się do [konsoli administracyjnej Wrike](https://www.Wrike.com/login/). Przejdź do swojego identyfikatora dzierżawy. Wybierz pozycję **aplikacje & integracji**.

    ![Aplikacje & integracje](media/Wrike-provisioning-tutorial/admin.png)

2.  Przejdź do **usługi Azure AD** i wybierz ją.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Wybierz pozycję Standard scim. Skopiuj **podstawowy adres URL**.

    ![Podstawowy adres URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Wybierz pozycję **interfejs API**  >  **Azure Standard scim**.

    ![Azure Standard scim](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Zostanie otwarte okno podręczne. Wprowadź to samo hasło, które zostało utworzone wcześniej, aby utworzyć konto.

    ![Utwórz token Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Skopiuj **token tajny** i wklej go w usłudze Azure AD. Wybierz pozycję **Zapisz** , aby zakończyć konfigurację aprowizacji w systemie Wrike.

    ![Stały token dostępu](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Dodaj Wrike z galerii

Przed skonfigurowaniem usługi Wrike na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD Dodaj Wrike z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać Wrike z galerii aplikacji usługi Azure AD, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Wrike**, wybierz pozycję **Wrike** w panelu wyników, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![Wrike na liście wyników](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Wrike 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie Wrike na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Aby włączyć rejestrację jednokrotną opartą na protokole SAML dla Wrike, postępuj zgodnie z instrukcjami zawartymi w [samouczku logowanie](wrike-tutorial.md)jednokrotne w Wrike. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Wrike w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Wszystkie aplikacje](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Wrike**.

    ![Link Wrike na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Tryb aprowizacji ustawiony na automatyczny](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora **wprowadź odpowiednie wartości** w polach adres URL i **token dostępu trwałego** , które zostały pobrane wcześniej, odpowiednio do **adresu URL dzierżawy** i **tokenu tajnego**. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Wrike. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Wrike ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Wybierz pozycję **Zapisz**.

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Wrike**.

    ![Wrike mapowania użytkowników](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Wrike w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Wrike for Updates. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Wrike atrybuty użytkownika](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę Azure AD Provisioning dla Wrike, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Wrike, wybierając odpowiednie wartości z **zakresu** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

14. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo trwa inicjowanie obsługi użytkowników lub grup, zobacz [jak długo trwa inicjowanie obsługi administracyjnej użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie Wrike. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)