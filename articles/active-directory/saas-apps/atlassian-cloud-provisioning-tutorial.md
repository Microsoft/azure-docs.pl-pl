---
title: 'Samouczek: Konfigurowanie chmury Atlassian na potrzeby automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w chmurze Atlassian.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77059355"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie chmury Atlassian na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w chmurze Atlassian i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w chmurze Atlassian.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa w chmurze Atlassian](https://www.atlassian.com/licensing/cloud)
* Konto użytkownika w chmurze Atlassian z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na **interfejsie API Standard scim w chmurze Atlassian**, który jest dostępny dla Atlassian zespołów w chmurze.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Dodaj chmurę Atlassian z galerii

Przed skonfigurowaniem chmury Atlassian na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać chmurę Atlassian z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać chmurę Atlassian z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Atlassian Cloud**, wybierz pozycję **Atlassian Cloud** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Atlassian Cloud na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Przypisywanie użytkowników do chmury Atlassian

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do chmury Atlassian. Po ustaleniu tych użytkowników i/lub grup można przypisać do chmury Atlassian, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Ważne porady dotyczące przypisywania użytkowników do chmury Atlassian

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do chmury Atlassian w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do chmury Atlassian należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurowanie automatycznej aprowizacji użytkowników w chmurze Atlassian 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w chmurze Atlassian na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla chmury Atlassian, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](atlassian-cloud-tutorial.md)jednokrotne w chmurze Atlassian. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w chmurze Atlassian w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **chmura Atlassian**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Atlassian Cloud**.

    ![Link do usługi Atlassian Cloud na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Przejdź do programu [Atlassian Organization Manager](https://admin.atlassian.com) **,> wybierz katalog > organizacji**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Kliknij pozycję **Inicjowanie obsługi użytkowników** i kliknij pozycję **Utwórz katalog**. Skopiuj odpowiednio **podstawowy adres URL katalogu** i **token okaziciela** do pól **adresu URL dzierżawy** i **tokenu tajnego** .

    ![](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Inicjowanie obsługi administracyjnej w chmurze Atlassian Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Cloud Provisioning Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **token wpisu tajnego** konta w chmurze Atlassian. Przykłady tych wartości:

   * W polu **adres URL dzierżawy** Wypełnij określony punkt końcowy dzierżawy otrzymany z Atlassian, zgodnie z opisem w kroku 6. Na przykład: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * W polu **token tajny** Wypełnij token tajny zgodnie z opisem w kroku 6.

8. Po wypełnieniu pól przedstawionych w kroku 7 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z chmurą Atlassian. Jeśli połączenie nie powiedzie się, upewnij się, że konto w chmurze Atlassian ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Kliknij przycisk **Zapisz**.

11. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy z chmurą Atlassian**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Atlassian Cloud w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w chmurze Atlassian dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z chmurą Atlassian**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Atlassian Cloud w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w chmurze Atlassian dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Aby włączyć usługę Azure AD Provisioning dla chmury Atlassian, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w chmurze Atlassian, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w chmurze Atlassian.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Chmura Atlassian umożliwia inicjowanie obsługi użytkowników tylko z [zweryfikowanych domen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Chmura Atlassian nie obsługuje już dzisiaj nazw grup. Oznacza to, że wszelkie zmiany wartości displayName grupy w usłudze Azure AD nie będą aktualizowane i odzwierciedlane w chmurze Atlassian.
* Wartość atrybutu użytkownika **poczty** w usłudze Azure AD jest wypełniana tylko wtedy, gdy użytkownik ma skrzynkę pocztową programu Microsoft Exchange. Jeśli użytkownik nie ma takiego elementu, zaleca się zamapowanie innego żądanego atrybutu na atrybut **wiadomości e-mail** w chmurze Atlassian.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png