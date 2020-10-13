---
title: 'Samouczek: Konfigurowanie zasad dla automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 56aad5ce9045ca205221ba0c56d1ea75f8f3610a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850505"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zasad dla automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie kroków, które należy wykonać w temacie moje zasady i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do zasad.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa zasad](https://mypolicies.com/index.html#section10).
* Konto użytkownika w zasadach moje zasady z uprawnieniami administratora.

## <a name="assigning-users-to-mypolicies"></a>Przypisywanie użytkowników do zasad moje zasady

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do zasad. Po ustaleniu tych użytkowników i/lub grup można przypisać je do zasad, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Ważne porady dotyczące przypisywania użytkowników do zasad

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do zasad moje zasady w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do zasad, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-mypolicies-for-provisioning"></a>Skonfiguruj moje zasady dla aprowizacji

Przed skonfigurowaniem zasad dotyczących automatycznego aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim dla zasad moje zasady.

1. Skontaktuj się z przedstawicielem ds. zasad w **support@mypolicies.com** celu uzyskania tokenu tajnego, który jest wymagany do skonfigurowania APROWIZACJI Standard scim.

2.  Zapisz wartość tokenu dostarczoną przez przedstawiciela zasad. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji moje zasady w Azure Portal.

## <a name="add-mypolicies-from-the-gallery"></a>Dodawanie zasad moje zasady z galerii

Aby skonfigurować zasady dla automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz dodać moje zasady z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać moje zasady z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź moje **zasady**, wybierz pozycję Moje **zasady** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![moje zasady na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurowanie automatycznej aprowizacji użytkowników do zasad moje zasady 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w zasadach zasad opartych na przypisaniach użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć rejestrację jednokrotną opartą na protokole SAML, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](mypolicies-tutorial.md)jednokrotne. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję Moje **zasady**.

    ![Link moje zasady na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://<myPoliciesCustomDomain>.mypolicies.com/scim` **adres URL dzierżawy** , gdzie `<myPoliciesCustomDomain>` jest domeną niestandardową dla zasad. Możesz pobrać domenę klienta moje zasady z adresu URL.
Przykład: `<demo0-qa>` . mypolicies.com.

6. W polu **token tajny**wprowadź wartość tokenu, która została pobrana wcześniej. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z regułami. Jeśli połączenie nie powiedzie się, upewnij się, że konto moje zasady ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy do moje zasady**.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Zrzut ekranu przedstawiający sekcję mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z customappsso jest widoczny." border="false":::

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do zasad moje zasady w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w zasadach moje zasady dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |externalId|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Tematy pomocy|


11. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę Azure AD Provisioning dla zasad moje zasady, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić zasadom, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

14. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w ramach zasad.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* moje zasady zawsze wymagają **nazwy użytkownika**, **adresu e-mail** i **externalid**.
* moje zasady nie obsługują stałych usunięć dla atrybutów użytkownika.

## <a name="change-log"></a>Dziennik zmian

* 09/15/2020 — dodano obsługę atrybutu "Country" dla użytkowników.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
