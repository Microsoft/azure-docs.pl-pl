---
title: 'Samouczek: Konfigurowanie usługi firmy Symantec w sieci Web Security (WSS) do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w usłudze firmy Symantec w sieci Web.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354720"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi firmy Symantec w sieci Web Security (WSS) do automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w usłudze Symantec Web Security Service (WSS) i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w usłudze Symantec Web Security Service (WSS).

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa usługi zabezpieczeń internetowych firmy Symantec (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Konto użytkownika w usłudze Symantec Web Security Service (WSS) z uprawnieniami administratora.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Przypisywanie użytkowników do usługi firmy Symantec w sieci Web Security (WSS)

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi firmy Symantec w sieci Web. Po ustaleniu tych użytkowników i/lub grup do usługi firmy Symantec w sieci Web Security (WSS) można przypisywać te grupy, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Ważne porady dotyczące przypisywania użytkowników do usługi firmy Symantec w sieci Web.

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi firmy Symantec w sieci Web Security (WSS) w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do usługi firmy Symantec w sieci Web Security (WSS) należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Konfigurowanie usługi firmy Symantec w sieci Web Security (WSS) na potrzeby aprowizacji

Przed skonfigurowaniem usługi firmy Symantec w sieci Web Security (WSS) do automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę Standard scim przy użyciu usługi Symantec Web Security Service (WSS).

1. Zaloguj się do [konsoli administracyjnej usługi zabezpieczeń sieci Web firmy Symantec](https://portal.threatpulse.com/login.jsp). Przejdź do   >  **usługi Solutions Service**.

    ![Usługa zabezpieczeń internetowych firmy Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Przejdź do obszarze integracja z integracją z **obsługą**  >    >  **nowej integracji**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Wybierz opcję Użytkownicy innych firm **& synchronizacja grup**. 

    ![Zrzut ekranu przedstawiający opcję Synchronizacja grup & użytkowników innych firm.](media/symantec-web-security-service/third-party-users.png)

4.  Skopiuj **adres URL** i **token** Standard scim. Te wartości zostaną wprowadzone w polu **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji Symantec Web Security Service (WSS) w Azure Portal.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa integracja z polami tekstowymi S i w języku R L i tokenem.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Dodaj usługę Symantec Web Security Service (WSS) z galerii

Aby skonfigurować usługę Symantec Web Security Service (WSS) do automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać usługę Symantec Web Security Service (WSS) z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Symantec Web Security Service (WSS) z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **Symantec Web Security**, wybierz pozycję **Symantec Web Security Service** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Usługa Symantec Web Security Service (WSS) na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze zabezpieczeń internetowych firmy Symantec (WSS)

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Symantec Web Security Service (WSS) na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla usługi Symantec Web Security Service (WSS), postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [usłudze Symantec Web Security (WSS)](symantec-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze Microsoft Web Security Service (WSS) w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Usługa zabezpieczeń sieci Web firmy Symantec**.

    ![Link usługi Symantec Web Security Service (WSS) na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź odpowiednie **wartości w polach adres** URL **i** **token** **Standard scim** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą zabezpieczeń firmy Symantec. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi firmy Symantec w sieci Web Security (WSS) ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z usługą Symantec Web Security Service (WSS)**.

    ![Zrzut ekranu przedstawiający sekcję mapowania zawierającej opcję Synchronizuj Azure Active Directory użytkownicy z usługą firmy Symantec w sieci Web Security W systemie.](media/symantec-web-security-service/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD do usługi zabezpieczeń sieci Web firmy Symantec (WSS), w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w usłudze Symantec Web Security Service (WSS) dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowanie przypisywania pokazujący 16 pasujących właściwości.](media/symantec-web-security-service/userattribute.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do usługi zabezpieczeń sieci Web firmy Symantec**.

    ![Zrzut ekranu przedstawiający sekcję mapowania zawierającej opcję Synchronizuj grupy Azure Active Directory z usługą firmy Symantec w sieci Web Security W systemie.](media/symantec-web-security-service/groupmapping.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą zabezpieczeń internetowych firmy Symantec (WSS) w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usłudze Symantec Web Security Service (WSS) dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowanie przypisywania z trzema zgodnymi właściwościami.](media/symantec-web-security-service/groupattribute.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning Service for Symantec Web Security, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić firmie Symantec Web Security Service (WSS), wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo będzie trwać dla użytkowników i/lub grup, zobacz [jak długo trwa inicjowanie obsługi użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w usłudze zabezpieczeń internetowych firmy Symantec. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
