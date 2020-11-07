---
title: 'Samouczek: Konfigurowanie automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358613"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w odwiedzaniu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników lub grup do odwiedzania.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania w aplikacjach typu "oprogramowanie jako usługa" (SaaS) z Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa w trakcie odwiedzin](https://www.visitly.io/pricing/)
* Konto użytkownika w odwiedzaniu z uprawnieniami administratora

## <a name="assign-users-to-visitly"></a>Przypisywanie użytkowników do odwiedzania 

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do nich. Następnie przypisz tych użytkowników lub grupy do odwiedzania, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Ważne porady dotyczące przypisywania użytkowników do odwiedzania 

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników lub grup można przypisywać później.

* Po przypisaniu użytkownika do odwiedzania należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisanie. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-visitly-for-provisioning"></a>Skonfiguruj odwiedzanie w celu aprowizacji

Przed rozpoczęciem konfigurowania programu w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD należy włączyć funkcję inicjowania obsługi administracyjnej między domenami (standard scim) na stronie odwiedzającej.

1. Zaloguj [się, aby](https://app.visitly.io/login)uzyskać więcej. Wybierz pozycję **integracja**  >  **hosta synchronizacja**.

    ![Synchronizacja hosta](media/Visitly-provisioning-tutorial/login.png)

2. Wybierz sekcję **usługi Azure AD** .

    ![Sekcja usługi Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Skopiuj **klucz interfejsu API**. Te wartości są wprowadzane w polu **token tajny** na karcie **aprowizacji** aplikacji do odwiedzania w Azure Portal.

    ![Klucz interfejsu API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Dodaj wizyty z galerii

Aby skonfigurować odwiedzanie w celu automatycznego aprowizacji użytkowników w usłudze Azure AD, Dodaj witrynę z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać witrynę z galerii aplikacji usługi Azure AD, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **odwiedzający** , wybierz pozycję **odwiedź** w panelu wyników, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![Aplikacja Visitly na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurowanie automatycznej aprowizacji użytkowników w celu odwiedzania 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w odwiedzaniu opartym na przypisaniach użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Aby włączyć logowanie jednokrotne oparte na protokole SAML, postępuj zgodnie z instrukcjami w [samouczku Logowanie jednokrotne](Visitly-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi użytkowników w usłudze Azure AD

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Wszystkie aplikacje](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Visitly**.

    ![Link do aplikacji Visitly na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Tryb aprowizacji ustawiony na automatyczny](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź `https://api.visitly.io/v1/usersync/SCIM` odpowiednio wartości i **klucze interfejsu API** pobrane wcześniej w **adresie URL dzierżawy** i w **tokenie tajnym**. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z witryną sieci Web. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto odwiedzające ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby odwiedzać**.

    ![Zapoznaj się z mapowaniami użytkowników](media/visitly-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby odwiedzać się w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w odniesieniu do operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Odwiedzaj atrybuty użytkownika](media/visitly-provisioning-tutorial/userattribute.png)

10. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning w odwiedzaniu, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić do odwiedzania, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo trwa inicjowanie obsługi użytkowników lub grup, zobacz [jak długo trwa inicjowanie obsługi administracyjnej użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie sieci Web. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

Nie obsługuje żadnych stałych usunięć. Wszystko jest tylko usuwaniem nieelastycznym.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
