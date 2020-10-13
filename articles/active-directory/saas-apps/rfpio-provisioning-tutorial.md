---
title: 'Samouczek: Konfigurowanie RFPIO dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze RFPIO.
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
ms.openlocfilehash: 98f92a6b0169c573b97788c7ffaf76255796cfa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278944"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie RFPIO na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w RFPIO i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do RFPIO.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa RFPIO](https://www.rfpio.com/product/).
* Konto użytkownika w RFPIO z uprawnieniami administratora.

## <a name="assigning-users-to-rfpio"></a>Przypisywanie użytkowników do RFPIO

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do RFPIO. Po ustaleniu tych użytkowników i/lub grup można przypisywać do RFPIO, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Ważne wskazówki dotyczące przypisywania użytkowników do RFPIO

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do RFPIO w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do RFPIO należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-rfpio-for-provisioning"></a>Konfiguracja RFPIO na potrzeby aprowizacji

Przed skonfigurowaniem usługi RFPIO na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na RFPIO.

1.  Zaloguj się do konsoli administracyjnej RFPIO. W lewym dolnym rogu konsoli administracyjnej kliknij pozycję **dzierżawca**.

    ![Konsola administracyjna RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Kliknij pozycję **Ustawienia organizacji**.
    
    ![Administrator RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Przejdź do Standard scim zabezpieczeń **zarządzania użytkownikami**  >  **SECURITY**  >  **SCIM**.

    ![RFPIO Dodaj Standard scim](media/rfpio-provisioning-tutorial/scim.png)

4.  Upewnij się, że **Funkcja autoaprowizacji użytkowników** jest włączona. Kliknij pozycję **Generuj token interfejsu API Standard scim**.

    ![Zrzut ekranu przedstawiający sekcję S C I M z opcją Generuj S C I M, która została wywołana.](media/rfpio-provisioning-tutorial/generate.png)

5.  Zapisz **token interfejsu API Standard scim** , ponieważ ten token nie zostanie ponownie wyświetlony do celów zabezpieczeń. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie APROWIZACJI aplikacji RFPIO w Azure Portal.

    ![Zrzut ekranu przedstawiający sekcję S C I M z ostrzeżeniem wyświetlanym po wybraniu opcji Prześlij.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Dodaj RFPIO z galerii

Aby skonfigurować RFPIO automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać RFPIO z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać RFPIO z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **RFPIO**, wybierz pozycję **RFPIO** w panelu wyników, a następnie kliknij przycisk    **Dodaj** , aby dodać aplikację.

    ![RFPIO na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Konfigurowanie automatycznej aprowizacji użytkowników do RFPIO 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie RFPIO na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla RFPIO, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](rfpio-tutorial.md)jednokrotne w RFPIO. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla RFPIO w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **RFPIO**.

    ![Link RFPIO na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` **adres URL dzierżawy**. Przykładowa wartość to `https://Azure-test1.rfpio.com/rfpserver/scim/v2` . Wprowadź wartość **tokenu interfejsu API Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą RFPIO. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi RFPIO ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do RFPIO**.

    ![RFPIO mapowania użytkowników](media/rfpio-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do RFPIO w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie RFPIO for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![RFPIO atrybuty użytkownika](media/rfpio-provisioning-tutorial/userattributes.png)

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla RFPIO, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić RFPIO, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie RFPIO.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Usługa RFPIO nie obsługuje obecnie aprowizacji grup.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
