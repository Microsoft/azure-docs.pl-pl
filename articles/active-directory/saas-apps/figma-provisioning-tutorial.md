---
title: 'Samouczek: Konfigurowanie Figma automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353922"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Figma na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Figma i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Figma.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Figma](https://www.figma.com/pricing/).
* Konto użytkownika w Figma z uprawnieniami administratora.

## <a name="assign-users-to-figma"></a>Przypisz użytkowników do Figma.
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Figma. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Figma, postępując zgodnie z poniższymi instrukcjami:
 
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Figma

 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Figma w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Figma należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-figma-for-provisioning"></a>Konfigurowanie Figma na potrzeby aprowizacji

Przed skonfigurowaniem usługi Figma do automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z Figma.

1. Zaloguj się do [konsoli administracyjnej Figma](https://www.Figma.com/). Kliknij ikonę koła zębatego obok dzierżawy.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną Figma. Jest widoczny dzierżawca o nazwie test D Standard scim. Obok dzierżawy zostanie wyróżniona ikona koła zębatego." border="false":::

2. Przejdź do **ogólne > Aktualizuj logowanie w ustawieniach**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Zrzut ekranu przedstawiający kartę Ogólne w konsoli administracyjnej Figma. W obszarze Logowanie i Inicjowanie obsługi administracyjnej są wyróżnione aktualizacje logowania w ustawieniach." border="false":::

3. Skopiuj **Identyfikator dzierżawy**. Ta wartość zostanie użyta do skonstruowania adresu URL punktu końcowego Standard scim, który zostanie wprowadzony do pola **adresu URL dzierżawy** na karcie aprowizacji aplikacji Figma w Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Zrzut ekranu przedstawiający sekcję S A M L s S w konsoli administracyjnej Figma. Etykieta identyfikatora dzierżawy i sąsiadujące łącze, które wyświetlają kopię są wyróżnione." border="false":::

4. Przewiń w dół i kliknij pozycję **Generuj token interfejsu API**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Zrzut ekranu przedstawiający sekcję C Provisioning języka S w konsoli administracyjnej Figma. Link z etykietą Generuj token P I jest wyróżniony." border="false":::

5. Skopiuj wartość  **tokenu interfejsu API** . Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji Figma w Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Zrzut ekranu strony w konsoli administracyjnej Figma. W ramach aprowizacji tokenu P I zostanie wyróżniony symbol zastępczy dla tokenu." border="false":::

## <a name="add-figma-from-the-gallery"></a>Dodaj Figma z galerii

Aby skonfigurować Figma automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Figma z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Figma**, wybierz pozycję **Figma** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Figma na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Figma 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Figma na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Figma, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](figma-tutorial.md)jednokrotne w Figma. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Figma w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Figma**.

    ![Link Figma na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://www.figma.com/scim/v2/<TenantID>` wartość w polu **adres URL dzierżawy** , gdzie **TenantID** jest wartością pobieraną z Figma wcześniej. Wprowadź wartość **tokenu interfejsu API** w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Figma. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Figma ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Figma**.

    ![Figma mapowania użytkowników](media/Figma-provisioning-tutorial/figma05.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Figma w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Figma for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Figma atrybuty użytkownika](media/Figma-provisioning-tutorial/figma06.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Figma, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Figma, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Figma.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)