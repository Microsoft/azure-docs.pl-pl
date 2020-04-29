---
title: 'Samouczek: Konfigurowanie Peakon automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Peakon.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063399"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Peakon na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Peakon i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Peakon.

> [!NOTE]
>  Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne

* Dzierżawa usługi Azure AD.
* [Dzierżawa Peakon](https://peakon.com/us/pricing/).
* Konto użytkownika w Peakon z uprawnieniami administratora.

## <a name="assigning-users-to-peakon"></a>Przypisywanie użytkowników do Peakon

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Peakon. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Peakon, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Peakon 

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Peakon w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Peakon należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-peakon-for-provisioning"></a>Konfigurowanie Peakon na potrzeby aprowizacji

1.  Zaloguj się do [konsoli administracyjnej Peakon](https://app.Peakon.com/login). Kliknij pozycję **Konfiguracja**. 

    ![Konsola administracyjna Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Wybierz pozycję **integracji**.
    
    ![Peakon — obsługa administracyjna](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Włącz **obsługę pracowników**.

    ![Peakon — obsługa administracyjna](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Skopiuj wartości dla **adresu URL standard scim 2,0** i **tokenu okaziciela OAuth**. Te wartości zostaną wprowadzone w polu **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji Peakon w Azure Portal.

    ![Utwórz token Peakon](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Dodaj Peakon z galerii

Aby skonfigurować Peakon do automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz dodać Peakon z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Peakon**, wybierz pozycję **Peakon** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Peakon na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Peakon 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Peakon na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Peakon, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](peakon-tutorial.md)jednokrotne w Peakon. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Peakon w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Peakon**.

    ![Link Peakon na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź odpowiednio wartość **adresu URL Standard scim 2,0** i **token okaziciela OAuth** pobrane wcześniej w **adresie URL dzierżawy** i w **tokenie tajnym** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Peakon. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Peakon ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Peakon**.

    ![Peakon mapowania użytkowników](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Peakon w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Peakon for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Peakon atrybuty użytkownika](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Peakon.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Wszystkie niestandardowe atrybuty użytkownika w Peakon muszą zostać rozszerzone z niestandardowego rozszerzenia użytkownika Standard scim Peakon `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)