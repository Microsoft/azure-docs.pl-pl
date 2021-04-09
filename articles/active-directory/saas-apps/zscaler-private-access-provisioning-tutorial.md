---
title: 'Samouczek: Konfigurowanie prywatnego dostępu rozwiązania Zscaler (ZPA) w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w celu rozwiązania Zscaler prywatnego dostępu (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937148"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie prywatnego dostępu rozwiązania Zscaler (ZPA) w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w programie rozwiązania Zscaler Private Access (ZPA) i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu rozwiązania Zscaler dostępu prywatnego (ZPA).

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa rozwiązania Zscaler Private Access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Konto użytkownika w usłudze rozwiązania Zscaler Private Access (ZPA) z uprawnieniami administratora.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Przypisywanie użytkowników do prywatnego dostępu rozwiązania Zscaler (ZPA)

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do prywatnego dostępu rozwiązania Zscaler (ZPA). Po ustaleniu tych użytkowników i/lub grup można przypisywać rozwiązania Zscaler do prywatnego dostępu (ZPA), postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Ważne porady dotyczące przypisywania użytkowników do prywatnego dostępu rozwiązania Zscaler (ZPA)

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do rozwiązania Zscaler Private Access (ZPA) w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do rozwiązania Zscaler prywatnego dostępu (ZPA), należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Konfigurowanie prywatnego dostępu rozwiązania Zscaler (ZPA) na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej rozwiązania Zscaler dostępu prywatnego (ZPA)](https://admin.private.zscaler.com/). Przejdź do **> Administracja Konfiguracja dostawcy tożsamości**.

    ![Konsola administracyjna rozwiązania Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Upewnij się, że skonfigurowano dostawcy tożsamości dla **logowania jednokrotnego** . Jeśli dostawcy tożsamości nie jest skonfigurowany, Dodaj ją, klikając ikonę znaku plus w prawym górnym rogu ekranu.

    ![Rozwiązania Zscaler Private Access (ZPA) Dodaj Standard scim](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Aby dodać dostawcy tożsamości, postępuj zgodnie z instrukcjami w kreatorze **dodawania konfiguracji dostawcy tożsamości** . Pozostaw pole **Logowanie** jednokrotne ustawione na **użytkownika**. Podaj **nazwę** i wybierz **domeny** z listy rozwijanej. Kliknij przycisk **dalej** , aby przejść do następnego okna.

    ![Rozwiązania Zscaler Private Access (ZPA) Dodaj dostawcy tożsamości](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Pobierz **certyfikat dostawcy usług**. Kliknij przycisk **dalej** , aby przejść do następnego okna.

    ![Certyfikat SP rozwiązania Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. W następnym oknie Przekaż wcześniej pobrany **certyfikat dostawcy usług** .

    ![Certyfikat przekazywania prywatnego dostępu rozwiązania Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Przewiń w dół, aby podać **adres URL logowania** jednokrotnego i **Identyfikator jednostki dostawcy tożsamości**.

    ![Rozwiązania Zscaler Private Access (ZPA) — identyfikator dostawcy tożsamości](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Przewiń w dół, aby **włączyć synchronizację Standard scim**. Kliknij przycisk **Generuj nowy token** . Skopiuj **token okaziciela**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji w aplikacji rozwiązania Zscaler Private Access (ZPA) w Azure Portal.

    ![Token tworzenia prywatnego dostępu rozwiązania Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Aby znaleźć **adres URL dzierżawy**, przejdź do strony **Administracja > Konfiguracja dostawcy tożsamości**. Kliknij nazwę nowo dodanej konfiguracji dostawcy tożsamości z listy na stronie.

    ![Rozwiązania Zscaler Private Access (ZPA) — nazwa dostawcy tożsamości](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Przewiń w dół, aby wyświetlić **punkt końcowy dostawcy usług Standard scim** na końcu strony. Skopiuj **punkt końcowy dostawcy usług Standard scim**. Ta wartość zostanie wprowadzona w polu adres URL dzierżawy na karcie aprowizacji w aplikacji rozwiązania Zscaler Private Access (ZPA) w Azure Portal.

    ![Rozwiązania Zscaler Private Access (ZPA) — adres URL Standard scim](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Dodawanie prywatnego dostępu rozwiązania Zscaler (ZPA) z galerii

Przed skonfigurowaniem prywatnego dostępu rozwiązania Zscaler (ZPA) w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler prywatnego dostępu (ZPA) z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler prywatny dostęp (ZPA) z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **rozwiązania Zscaler Private Access (ZPA)**, wybierz pozycję **rozwiązania Zscaler Private Access (ZPA)** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Rozwiązania Zscaler Private Access (ZPA) na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurowanie automatycznego aprowizacji użytkowników w usłudze rozwiązania Zscaler Private Access (ZPA) 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze rozwiązania Zscaler Private Access (ZPA) na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML na potrzeby rozwiązania Zscaler prywatnego dostępu (ZPA), postępując zgodnie z instrukcjami podanymi w [samouczku Logowanie jednokrotne (rozwiązania Zscaler Private Access) (ZPA)](./zscalerprivateaccess-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Po zainicjowaniu obsługi administracyjnej użytkowników i grup, zalecamy okresowe ponowne uruchomienie aprowizacji, aby upewnić się, że członkostwa w grupach zostały prawidłowo zaktualizowane. Ponowne uruchomienie spowoduje wymuszenie ponownego oszacowania wszystkich grup i zaktualizowania członkostw przez naszą usługę.  

> [!NOTE]
> Aby dowiedzieć się więcej na temat Standard scim punktu końcowego dostępu prywatnego rozwiązania Zscaler, zapoznaj się z [tym](https://www.zscaler.com/partners/microsoft)tematem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze rozwiązania Zscaler Private Access (ZPA) w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler Private Access (ZPA)**.

    ![Link rozwiązania Zscaler Private Access (ZPA) na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość **punktu końcowego dostawcy usług Standard scim** , która została pobrana wcześniej w **adresie URL dzierżawy**. Wprowadź wartość **tokenu okaziciela** pobranego wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z dostępem prywatnym rozwiązania ZSCALER (ZPA). Jeśli połączenie nie powiedzie się, upewnij się, że konto rozwiązania Zscaler Private Access (ZPA) ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby rozwiązania Zscaler dostęp prywatny (ZPA)**.

    ![Mapowania użytkowników prywatnego dostępu rozwiązania Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler Private Access (ZPA) w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie rozwiązania Zscaler Private Access (ZPA) dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika prywatnego dostępu rozwiązania Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby rozwiązania Zscaler dostęp prywatny (ZPA)**.

    ![Mapowania grup dostępu prywatnego (rozwiązania Zscaler) (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler Private Access (ZPA) w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w programie rozwiązania Zscaler Private Access (ZPA) dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy rozwiązania Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla prywatnego dostępu rozwiązania Zscaler (ZPA), Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić rozwiązania Zscaler prywatnego dostępu (ZPA), wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na rozwiązania Zscaler prywatnego dostępu (ZPA).

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)