---
title: 'Samouczek: Konfigurowanie BitaBIZ dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze BitaBIZ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d17d4dd88e29440304989b8c37eaa81125d1812
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357559"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BitaBIZ na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w BitaBIZ i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do BitaBIZ.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa BitaBIZ](https://bitabiz.dk/en/price/).
* Konto użytkownika w BitaBIZ z uprawnieniami administratora.

## <a name="assigning-users-to-bitabiz"></a>Przypisywanie użytkowników do BitaBIZ

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do BitaBIZ. Po ustaleniu tych użytkowników i/lub grup można przypisywać do BitaBIZ, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Ważne wskazówki dotyczące przypisywania użytkowników do BitaBIZ

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do BitaBIZ w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do BitaBIZ należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-bitabiz-for-provisioning"></a>Konfiguracja BitaBIZ na potrzeby aprowizacji

Przed skonfigurowaniem usługi BitaBIZ na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na BitaBIZ.

1. Zaloguj się do [konsoli administracyjnej BitaBIZ](https://www.bitabiz.com/login?lang=en). Kliknij pozycję **SETUP ADMIN** (ADMINISTRATOR KONFIGURACJI).

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną BitaBIZ z wyróżnionym administratorem ustawień." border="false":::

2.  Przejdź do **integracji**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną BitaBIZ z wyróżnioną integracją." border="false":::

2.  Przejdź do **Microsoft Azure AD aprowizacji**.  Wybierz pozycję **włączone** w obszarze Automatyczne Inicjowanie obsługi użytkowników. Skopiuj wartości dla **adresu URL punktu końcowego aprowizacji Standard scim** i  **tokenu okaziciela**. Te wartości zostaną wprowadzone w polach adres URL dzierżawy i klucz tajny tokenu na karcie aprowizacji aplikacji BitaBIZ w Azure Portal.

    ![BitaBIZ Dodaj Standard scim](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Dodaj BitaBIZ z galerii

Aby skonfigurować BitaBIZ automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać BitaBIZ z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać BitaBIZ z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **BitaBIZ** , wybierz pozycję **BitaBIZ** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja BitaBIZ na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Konfigurowanie automatycznej aprowizacji użytkowników do BitaBIZ 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie BitaBIZ na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla BitaBIZ, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](BitaBIZ-tutorial.md)jednokrotne w BitaBIZ. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla BitaBIZ w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BitaBIZ**.

    ![Link aplikacji BitaBIZ na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź wartości w polach adres **URL punktu końcowego aprowizacji Standard scim** i **token okaziciela** pobrane wcześniej w adresie URL dzierżawy i w tokenie tajnym. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą BitaBIZ. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi BitaBIZ ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do BitaBIZ**.

    ![BitaBIZ mapowania użytkowników](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do BitaBIZ w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie BitaBIZ for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![BitaBIZ atrybuty użytkownika](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla BitaBIZ, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić BitaBIZ, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie BitaBIZ.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* BitaBIZ wymaga **nazwy użytkownika** , **adresu e-mail** , **imię** i **nazwisko** jako obowiązkowe atrybuty. 
* Usługa BitaBIZ nie obsługuje obecnie stałych usunięć.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).
