---
title: 'Samouczek: Konfigurowanie Proxyclick dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb89e4e85e18e00179b894f2587bcf4127dc22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255921"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Proxyclick na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Proxyclick i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Proxyclick.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Proxyclick](https://www.proxyclick.com/pricing)
* Konto użytkownika w Proxyclick z uprawnieniami administratora.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj Proxyclick z galerii

Przed skonfigurowaniem usługi Proxyclick do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Proxyclick z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Proxyclick z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Proxyclick**, wybierz pozycję **Proxyclick** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Proxyclick na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Przypisywanie użytkowników do Proxyclick

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Proxyclick. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Proxyclick, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Proxyclick

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Proxyclick w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Proxyclick należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Proxyclick 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Proxyclick na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Proxyclick, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](proxyclick-tutorial.md)jednokrotne w Proxyclick. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Proxyclick w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Proxyclick**.

    ![Link Proxyclick na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. Aby pobrać **adres URL dzierżawy** i **klucz tajny** konta Proxyclick, postępuj zgodnie z opisem w sekcji Krok 6.

6. Zaloguj się do [konsoli administracyjnej Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Przejdź do **ustawień**  >  **Integrations**  >  **Przeglądaj Portal Marketplace**.

    ![Ustawienia Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integracje Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Wybierz pozycję **Azure AD**. Kliknij przycisk **Instaluj teraz**.

    ![Proxyclick usługi Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick Zainstaluj](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Wybierz pozycję **Inicjowanie obsługi użytkowników** , a następnie kliknij pozycję **Rozpocznij integrację**. 

    ![Proxyclick aprowizacji użytkowników](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Odpowiedni interfejs użytkownika konfiguracji ustawień powinien teraz zostać wyświetlony w obszarze **Ustawienia**  >  **integracji**. Wybierz pozycję **Ustawienia** w obszarze **Azure AD (Inicjowanie obsługi użytkowników)**.

    ![Proxyclick Utwórz](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    W tym miejscu możesz znaleźć **adres URL dzierżawy** i **token tajny** .

    ![Utwórz token Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Proxyclick. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Proxyclick ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Proxyclick**.

    ![Proxyclick mapowania użytkowników](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Proxyclick w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Proxyclick for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Proxyclick atrybuty użytkownika](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę Azure AD Provisioning dla Proxyclick, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Proxyclick, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

16. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Proxyclick.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Proxyclick wymaga, aby **wiadomości e-mail** i **Nazwa użytkownika** miały tę samą wartość źródłową. Wszystkie aktualizacje obu atrybutów spowodują modyfikację drugiej wartości.
* Proxyclick nie obsługuje aprowizacji dla grup.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

