---
title: 'Samouczek: Konfigurowanie Looop dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Looop.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835071"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Looop na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Looop i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Looop.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Looop](https://www.looop.co/pricing/)
* Konto użytkownika na Looop z uprawnieniami administratora.

## <a name="assign-users-to-looop"></a>Przypisywanie użytkowników do Looop

Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Looop. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Looop, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Looop

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Looop w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Looop należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-looop-for-provisioning"></a>Konfigurowanie Looop na potrzeby aprowizacji

Przed skonfigurowaniem usługi Looop do automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z Looop.

1. Zaloguj się do [konsoli administracyjnej Looop](https://app.looop.co/#/login) i wybierz pozycję **konto**. W obszarze **Ustawienia konta** wybierz pozycję **uwierzytelnianie**.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną Looop. Karta konto jest wyróżniona i otwarta. W obszarze Ustawienia konta zostanie wyróżnione uwierzytelnianie." border="false":::

2. Wygeneruj nowy token, klikając pozycję **Zresetuj token** w obszarze **integracja z standard scim**.

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Zrzut ekranu sekcji integracja S C I M strony w konsoli administracyjnej Looop. Przycisk Resetuj token jest wyróżniony." border="false":::

3. Skopiuj **punkt końcowy Standard scim** i **token**. Te wartości zostaną wprowadzone w polach **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji Looop w Azure Portal. 

    ![Utwórz token Looop](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Dodaj Looop z galerii

Aby skonfigurować Looop automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Looop z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Looop**, a następnie wybierz pozycję **Looop** w panelu wyniki. 

    ![Looop na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Utwórz konto dla Looop** , który przekieruje Cię do strony logowania usługi Looop. 

    ![Looop OIDC Dodaj](media/looop-provisioning-tutorial/signup.png)

6. Ponieważ Looop jest aplikacją OpenIDConnect, wybierz logowanie do Looop przy użyciu konta służbowego firmy Microsoft.

    ![Looop OIDC logowania](media/looop-provisioning-tutorial/msftlogin.png)

7. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta Looop.

    ![Looop OIDc](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Looop 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Looop na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Looop w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Looop**.

    ![Link Looop na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://<organisation_domain>.looop.co/scim/v2` **adres URL dzierżawy**. Na przykład `https://demo.looop.co/scim/v2`. Wprowadź wartość, która została pobrana i zapisana wcześniej z Looop w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Looop. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Looop ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Looop**.

    ![Looop mapowania użytkowników](media/looop-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Looop w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Looop for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |externalId|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: użytkownik: obszar|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: custom_1|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: custom_2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: custom_3|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: employee_id|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: Location|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: Position|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: Looop: 2.0: User: startAt|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do łącznika meta Networks**.

    ![Mapowania grup Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do łącznika meta Networks w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w łączniku meta Networks dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
    |---|---|---|
    |displayName|Ciąg|&check;|
    |elementy członkowskie|Tematy pomocy|
    |externalId|Ciąg|


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Looop, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Looop, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Looop.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)


