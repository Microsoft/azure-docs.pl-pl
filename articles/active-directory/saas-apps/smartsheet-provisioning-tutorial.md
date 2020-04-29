---
title: 'Samouczek: Konfigurowanie narzędzia Smartsheet dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze narzędzia Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063207"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie narzędzia Smartsheet na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w narzędzia Smartsheet i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do narzędzia Smartsheet.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa narzędzia Smartsheet](https://www.smartsheet.com/pricing)
* Konto użytkownika w planie narzędzia Smartsheet Enterprise lub Enterprise Premier z uprawnieniami administratora systemu.

## <a name="assign-users-to-smartsheet"></a>Przypisywanie użytkowników do narzędzia Smartsheet

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do narzędzia Smartsheet. Po ustaleniu tych użytkowników i/lub grup można przypisywać do narzędzia Smartsheet, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Ważne wskazówki dotyczące przypisywania użytkowników do narzędzia Smartsheet

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do narzędzia Smartsheet w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do narzędzia Smartsheet należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

* Aby zapewnić zgodność z przypisaniami ról użytkowników między narzędzia Smartsheet i usługą Azure AD, zaleca się korzystanie z tych samych przypisań ról, które zostały wypełnione na liście pełnych użytkowników narzędzia Smartsheet. Aby pobrać tę listę użytkowników z narzędzia Smartsheet, przejdź do **administratora konta > zarządzanie użytkownikami > więcej akcji > Pobierz listę użytkowników (CSV)**.

* Aby uzyskać dostęp do pewnych funkcji w aplikacji, narzędzia Smartsheet wymaga, aby użytkownik miał wiele ról. Aby dowiedzieć się więcej o typach i uprawnieniach użytkownika w narzędzia Smartsheet, przejdź do pozycji [typy użytkowników i uprawnienia](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Jeśli użytkownik ma wiele ról przypisanych w narzędzia Smartsheet, **należy** się upewnić, że te przypisania ról są replikowane w usłudze Azure AD, aby uniknąć sytuacji, w której użytkownicy mogą trwale utracić dostęp do obiektów narzędzia Smartsheet. Każda unikatowa rola w narzędzia Smartsheet **musi** być przypisana do innej grupy w usłudze Azure AD. Użytkownik **musi** następnie dodać do każdej grupy odpowiadającej żądanym rolom. 

## <a name="set-up-smartsheet-for-provisioning"></a>Konfigurowanie narzędzia Smartsheet na potrzeby aprowizacji

Przed skonfigurowaniem usługi narzędzia Smartsheet na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na narzędzia Smartsheet.

1. Zaloguj się jako administrator **w** **[portalu narzędzia Smartsheet](https://app.smartsheet.com/b/home)** i przejdź do **konta administratora**.

    ![Administrator konta narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Przejdź do pozycji **Kontrola zabezpieczeń > funkcja autoaprowizacji użytkownika > Edytuj**.

    ![Narzędzia Smartsheet kontroli zabezpieczeń](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Dodaj i sprawdź poprawność domen poczty e-mail dla użytkowników, których planujesz udostępnić z usługi Azure AD do narzędzia Smartsheet. Wybierz pozycję **Niewłączony** , aby upewnić się, że wszystkie akcje aprowizacji pochodzą tylko z usługi Azure AD, a także upewnij się, że lista użytkowników narzędzia Smartsheet jest zsynchronizowana z przypisaniami usługi Azure AD.

    ![Narzędzia Smartsheet aprowizacji użytkowników](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po zakończeniu walidacji należy aktywować domenę. 

    ![Narzędzia Smartsheet Aktywuj domenę](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Wygeneruj **token tajny** wymagany do skonfigurowania automatycznej aprowizacji użytkowników w usłudze Azure AD, przechodząc do **aplikacji i integracji**.

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wybierz pozycję **dostęp do interfejsu API**. Kliknij pozycję **Generuj nowy token dostępu**.

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zdefiniuj nazwę tokenu dostępu interfejsu API. Kliknij przycisk **OK**.

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Skopiuj token dostępu interfejsu API i Zapisz go, ponieważ będzie można go wyświetlić. Jest to wymagane w polu **token tajny** w usłudze Azure AD.

    ![Token narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Dodaj narzędzia Smartsheet z galerii

Aby skonfigurować narzędzia Smartsheet automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać narzędzia Smartsheet z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Narzędzia Smartsheet**, a następnie wybierz pozycję **Narzędzia Smartsheet** w panelu wyniki. 

    ![Narzędzia Smartsheet na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Utwórz konto w usłudze narzędzia Smartsheet** , który przekieruje Cię do strony logowania usługi narzędzia Smartsheet. 

    ![Narzędzia Smartsheet OIDC Dodaj](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Ponieważ narzędzia Smartsheet jest aplikacją OpenIDConnect, wybierz logowanie do narzędzia Smartsheet przy użyciu konta służbowego firmy Microsoft.

    ![Narzędzia Smartsheet OIDC logowania](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta narzędzia Smartsheet.

    ![Narzędzia Smartsheet OIDc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurowanie automatycznej aprowizacji użytkowników do narzędzia Smartsheet 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie narzędzia Smartsheet na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla narzędzia Smartsheet w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Narzędzia Smartsheet**.

    ![Link narzędzia Smartsheet na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://scim.smartsheet.com/v2/` **adres URL dzierżawy**. Wprowadź wartość, która została pobrana i zapisana wcześniej z narzędzia Smartsheet w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą narzędzia Smartsheet. Jeśli połączenie nie powiedzie się, upewnij się, że konto narzędzia Smartsheet ma uprawnienia administratora systemu, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do narzędzia Smartsheet**.

    ![Narzędzia Smartsheet mapowania użytkowników](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do narzędzia Smartsheet w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie narzędzia Smartsheet for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Narzędzia Smartsheet atrybuty użytkownika](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla narzędzia Smartsheet, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić narzędzia Smartsheet, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie narzędzia Smartsheet.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Narzędzia Smartsheet nie obsługuje usuwania nietrwałego. Gdy **aktywny** atrybut użytkownika ma wartość FAŁSZ, narzędzia Smartsheet trwale usuwa użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
