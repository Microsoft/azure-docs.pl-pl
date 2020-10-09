---
title: 'Samouczek: Konfigurowanie 4me dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze 4me.
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
ms.openlocfilehash: 8350ebc92575911991ca772229912941f2af10e0
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851292"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie 4me na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w 4me i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do 4me.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa 4me](https://www.4me.com/trial/)
* Konto użytkownika w 4me z uprawnieniami administratora.

## <a name="add-4me-from-the-gallery"></a>Dodaj 4me z galerii

Przed skonfigurowaniem usługi 4me do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać 4me z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać 4me z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **4me**, wybierz pozycję **4me** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja 4me na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Przypisywanie użytkowników do 4me

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do 4me. Po ustaleniu tych użytkowników i/lub grup można przypisywać do 4me, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Ważne wskazówki dotyczące przypisywania użytkowników do 4me

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do 4me w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do 4me należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurowanie automatycznej aprowizacji użytkowników do 4me 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie 4me na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla 4me, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](4me-tutorial.md)jednokrotne w 4me. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla 4me w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **4me**.

    ![Link 4me na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. Aby pobrać **adres URL dzierżawy** i **klucz tajny** konta 4me, postępuj zgodnie z opisem w sekcji Krok 6.

6. Zaloguj się do konsoli administracyjnej 4me. Przejdź do **ustawień**.

    ![Ustawienia 4me](media/4me-provisioning-tutorial/4me01.png)

    Wpisz w **aplikacjach** na pasku wyszukiwania.

    ![aplikacje 4me](media/4me-provisioning-tutorial/4me02.png)

    Otwórz listę rozwijaną **Standard scim** , aby pobrać token tajny i punkt końcowy Standard scim.

    ![4me Standard scim](media/4me-provisioning-tutorial/4me03.png)

7. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą 4me. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi 4me ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Zrzut ekranu strony mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z FourMe jest wyróżniony." border="false":::
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do 4me w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie 4me for Updates. Upewnij się, że [4me obsługuje filtrowanie](https://developer.4me.com/v1/scim/users/) według wybranego pasującego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Zrzut ekranu strony mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z FourMe jest wyróżniony." border="false":::
    
12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Zrzut ekranu strony mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z FourMe jest wyróżniony." border="false":::
    
13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do 4me w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w 4me dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Mapowania grup 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning dla 4me, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić 4me, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie 4me.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* 4me ma różne adresy URL Standard scim punktów końcowych dla środowisk testowych i produkcyjnych. Dawniej z **. funkcja pytań i odpowiedzi** jest zakończona. **.com**
* Tokeny wygenerowanego klucza tajnego 4me mają datę wygaśnięcia miesiąca od wygenerowania.
* 4me nie obsługuje operacji **usuwania**

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
