---
title: 'Samouczek: Konfigurowanie usługi Dropbox dla firm w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w usłudze Dropbox dla firm.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938770"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Dropbox dla firm na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności do wykonania w usłudze Dropbox dla firm i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w usłudze Dropbox dla firm.

> [!IMPORTANT]
> Firma Microsoft i usługa Dropbox będą wycofać starą integrację z usługą Dropbox 04/01/2021. Aby uniknąć przerw w działaniu usługi, zalecamy przeprowadzenie migracji do nowej integracji Dropbox, która obsługuje grupy. Aby przeprowadzić migrację do nowej integracji usługi Dropbox, Dodaj i skonfiguruj nowe wystąpienie usługi Dropbox na potrzeby aprowizacji w dzierżawie usługi Azure AD, wykonując poniższe kroki. Po skonfigurowaniu nowej integracji usługi Dropbox należy wyłączyć funkcję aprowizacji na starej integracji usługi Dropbox, aby uniknąć konfliktów aprowizacji. Aby uzyskać bardziej szczegółowe instrukcje dotyczące migracji do nowej integracji usługi Dropbox, zobacz artykuł [Aktualizacja najnowszej aplikacji usługi Dropbox dla firm przy użyciu usług Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa usługi Dropbox dla firm](https://www.dropbox.com/business/pricing)
* Konto użytkownika w usłudze Dropbox dla firm z uprawnieniami administratora.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dodawanie usługi Dropbox dla firm z galerii

Przed skonfigurowaniem usługi Dropbox dla firm w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać usługę Dropbox dla firm z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Dropbox dla firm z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **Dropbox dla firm**, wybierz pozycję **Dropbox dla firm** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Dropbox dla Firm na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do usługi Dropbox dla firm

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi Dropbox dla firm. Po ustaleniu tych użytkowników i/lub grup można przypisać do usługi Dropbox dla firm, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Dropbox dla firm

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do programu Dropbox dla firm w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do usługi Dropbox dla firm należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurowanie automatycznego aprowizacji użytkowników w usłudze Dropbox dla firm 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Dropbox dla firm na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla usługi Dropbox dla firm, postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [usłudze Dropbox dla firm](dropboxforbusiness-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla usługi Dropbox dla firm w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Dropbox dla firm**.

    ![Link aplikacji Dropbox dla Firm na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Otwiera okno dialogowe logowania do usługi Dropbox dla firm w nowym oknie przeglądarki.

    ![Inicjowanie obsługi ](common/provisioning-oauth.png)

6. Zaloguj się do dzierżawy **usługi Azure AD w usłudze Dropbox dla firm** i sprawdź swoją tożsamość.

    ![Logowanie do usługi Dropbox dla firm](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po wykonaniu kroków 5 i 6 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Dropbox dla firm. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Dropbox dla firm ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-oauth.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy do usługi Dropbox**.

    ![Mapowania użytkowników usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z usługą Dropbox, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze Dropbox dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z usługą Dropbox**.

    ![Mapowania grup usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą Dropbox, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usłudze Dropbox dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning dla usługi Dropbox, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do usługi Dropbox, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w usłudze Dropbox.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika
 
* Usługa Dropbox nie obsługuje zawieszania zaproszonych użytkowników. Jeśli zaproszony użytkownik zostanie zawieszony, ten użytkownik zostanie usunięty.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

