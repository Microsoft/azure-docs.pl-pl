---
title: 'Samouczek: Konfigurowanie Workgrid dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Workgrid.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77062816"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Workgrid na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Workgrid i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Workgrid.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Workgrid](https://www.workgrid.com/)
* Konto użytkownika w Workgrid z uprawnieniami administratora.

## <a name="assigning-users-to-workgrid"></a>Przypisywanie użytkowników do Workgrid 

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Workgrid. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Workgrid, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Workgrid 

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Workgrid w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Workgrid należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-workgrid-for-provisioning"></a>Konfigurowanie Workgrid na potrzeby aprowizacji

Przed skonfigurowaniem usługi Workgrid na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na Workgrid.

1. Zaloguj się do Workgrid. Przejdź do **użytkowników, > aprowizacji użytkowników**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. W obszarze **interfejs API zarządzania kontami**kliknij pozycję **Utwórz poświadczenia**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Skopiuj wartości z **punktów końcowych Standard scim** i **tokenu dostępu** . Zostaną one wprowadzone w polu **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji Workgrid w Azure Portal.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Dodaj Workgrid z galerii

Aby skonfigurować Workgrid automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Workgrid z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Workgrid z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Workgrid**, wybierz pozycję **Workgrid** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Workgrid na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Workgrid  

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Workgrid na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Workgrid, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](Workgrid-tutorial.md)jednokrotne w Workgrid. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Workgrid w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Workgrid**.

    ![Link Workgrid na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź odpowiednio dane dotyczące **punktu końcowego Standard scim** i **tokenu dostępu** pobrane wcześniej w **adresie URL dzierżawy** i w **tokenie tajnym** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Workgrid. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Workgrid ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Workgrid**.

    ![Workgrid mapowania użytkowników](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Workgrid w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Workgrid for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Workgrid atrybuty użytkownika](media/Workgrid-provisioning-tutorial/userattribute.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Workgrid**

    ![Workgrid mapowania użytkowników](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do Workgrid w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Workgrid for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Workgrid mapowania użytkowników](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę Azure AD Provisioning dla Workgrid, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Workgrid, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

16. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo będzie trwać dla użytkowników i/lub grup, zobacz [jak długo trwa inicjowanie obsługi użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie Workgrid. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
