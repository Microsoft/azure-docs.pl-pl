---
title: 'Samouczek: Konfigurowanie macierzy priorytetowej w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników na potrzeby macierzy priorytetowych.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357865"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie macierzy priorytetowej dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach macierzy priorytetów i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup na potrzeby macierzy priorytetowych.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa macierzy priorytetowej](https://appfluence.com/pricing/)
* Konto użytkownika na macierzy priorytetowej z uprawnieniami administratora.

## <a name="assign-users-to-priority-matrix"></a>Przypisywanie użytkowników do macierzy priorytetowej

Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do macierzy priorytetowej. Po ustaleniu tych użytkowników i/lub grup można przypisać do macierzy priorytetowej, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Ważne porady dotyczące przypisywania użytkowników do macierzy priorytetowej

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do macierzy priorytetowej w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do macierzy priorytetowej należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-priority-matrix-for-provisioning"></a>Konfigurowanie macierzy priorytetów na potrzeby aprowizacji

Przed skonfigurowaniem macierzy priorytetów w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z macierzy priorytetowej.

1. Zaloguj się do [konsoli administracyjnej macierzy priorytetowej](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Kliknij pozycję **token logowania OAuth** dla macierzy priorytetowej

    ![Dodawanie Standard scim do macierzy priorytetów](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Kliknij przycisk **Pobierz nowy token** . Skopiuj **ciąg tokenu**. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie inicjowanie aplikacji w macierzy priorytetowej w Azure Portal. 

    ![Priorytet — Tworzenie tokenu](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Dodaj macierz priorytetową z galerii

Aby skonfigurować macierz priorytetową dla automatycznej aprowizacji użytkowników w usłudze Azure AD, należy dodać macierz priorytetową z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **macierz priorytetowa** , a następnie wybierz pozycję **macierz priorytetowa** w panelu wyniki. 

    ![Priorytetowa macierz na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Utwórz konto macierzy z priorytetem** , który przekieruje Cię do strony logowania do macierzy priorytetowej. 

    ![Dodawanie OIDC macierzy priorytetów](media/priority-matrix-provisioning-tutorial/signup.png)

6. Jako macierz priorytetową jest aplikacją OpenIDConnect, wybierz opcję zalogowanie się do macierzy priorytetowej przy użyciu konta służbowego firmy Microsoft.

    ![Identyfikator logowania OIDC macierzy priorytetów](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta macierzy priorytetowej.

    ![Priorytet OIDc w macierzy](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurowanie automatycznej aprowizacji użytkowników do macierzy priorytetowej 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w macierzy priorytetowej na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym Standard scim macierzy priorytetów, zapoznaj się z tematem [Inicjowanie obsługi użytkowników i macierz priorytetów](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną dla macierzy priorytetowej w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **macierz priorytetów**.

    ![Link macierzy priorytetowej na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://sync.appfluence.com/scim/v2/` **adres URL dzierżawy**. Wprowadź wartość, która została pobrana i zapisana wcześniej z priorytetowej macierzy w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może połączyć się z matrycą priorytetową. Jeśli połączenie nie powiedzie się, upewnij się, że konto macierzy priorytetowej ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy do priorytetowej macierzy**.

    ![Mapowania użytkowników macierzy priorytetów](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do macierzy priorytetowej w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w macierzy priorytetowej dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Priorytetowe atrybuty użytkownika macierzy](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning na potrzeby macierzy priorytetowej, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do macierzy priorytetowej, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w macierzy priorytetowej.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)


