---
title: 'Samouczek: Konfigurowanie łącznika meta Networks dla automatycznej aprowizacji użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w łączniku meta Networks.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: bcddaec1660082c2d3fed42e0c10cbdde987693c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273527"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie łącznika meta Networks dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie kroków, które należy wykonać w łączniku meta Networks i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do łącznika meta Networks.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawca łącznika sieci meta](https://www.metanetworks.com/)
* Konto użytkownika w łączniku meta Networks z uprawnieniami administratora.

## <a name="assigning-users-to-meta-networks-connector"></a>Przypisywanie użytkowników do łącznika meta Networks

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do łącznika meta Networks. Po ustaleniu tych użytkowników i/lub grup do łącznika meta Networks należy wykonać następujące instrukcje:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Ważne porady dotyczące przypisywania użytkowników do łącznika meta Networks

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do łącznika meta Networks w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do łącznika meta Networks należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Łącznik konfiguracji meta Networks na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej łącznika meta Networks](https://login.metanetworks.com/login/) przy użyciu nazwy organizacji. Przejdź do **> Administracja klucze interfejsu API**.

    ![Konsola administracyjna łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kliknij znak plus w prawym górnym rogu ekranu, aby utworzyć nowy **klucz interfejsu API**.

    ![Ikona łącznika "meta Networks"](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Ustaw **nazwę klucza interfejsu API** i **Opis klucza interfejsu API**.

    ![Tworzenie tokenu łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Włącz uprawnienia do **zapisu** dla **grup** i **użytkowników**.

    ![Uprawnienia łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Kliknij przycisk **Dodaj**. Skopiuj **klucz tajny** i Zapisz go, ponieważ będzie można go wyświetlić. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji łącznika meta Networks w Azure Portal.

    ![Tworzenie tokenu łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Dodaj dostawcy tożsamości, przechodząc do **ustawień administracja > > dostawcy tożsamości > Utwórz nowy**.

    ![Łącznik meta Networks Add dostawcy tożsamości](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na stronie **Konfiguracja dostawcy tożsamości** można **nazwać** konfigurację dostawcy tożsamości i wybrać **ikonę**.

    ![Nazwa dostawcy tożsamości łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ikona dostawcy tożsamości łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  W obszarze **Konfiguruj Standard scim** wybierz nazwę klucza interfejsu API utworzoną w poprzednich krokach. Kliknij pozycję **Zapisz**.

    ![Standard scim konfigurowania łącznika sieci](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Przejdź do **> Administration Settings > kartę dostawcy tożsamości**. Kliknij nazwę konfiguracji dostawcy tożsamości utworzonej w poprzednich krokach, aby wyświetlić **Identyfikator dostawcy tożsamości**. Ten **Identyfikator** jest dodawany na końcu **adresu URL dzierżawy** podczas wprowadzania wartości w polu **adres URL dzierżawy** na karcie aprowizacji aplikacji łącznika meta Networks w Azure Portal.

    ![Identyfikator dostawcy tożsamości łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Dodaj łącznik meta Networks z galerii

Przed skonfigurowaniem łącznika meta Networks do automatycznej aprowizacji użytkowników w usłudze Azure AD należy dodać łącznik meta Networks z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać łącznik meta Networks z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **Łącznik meta Networks**, wybierz pozycję **Łączniki meta Networks** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Meta Networks Connector na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurowanie łącznika automatycznego inicjowania obsługi użytkowników w sieciach meta Networks 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w łączniku meta Networks na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć opcję logowania jednokrotnego opartego na protokole SAML dla łącznika meta Networks, postępując zgodnie z instrukcjami podanymi w [samouczku Logowanie jednokrotne łącznika usługi meta Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla łącznika meta Networks w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Meta Networks Connector**.

    ![Link do aplikacji Meta Networks Connector na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://api.metanetworks.com/v1/scim/<IdP ID>` **adres URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z łącznikiem meta Networks. Jeśli połączenie nie powiedzie się, upewnij się, że konto łącznika meta Network ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy do łącznika meta Networks**.

    ![Mapowania użytkowników łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do łącznika meta Networks w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w łączniku meta Networks dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika łącznika meta Networks](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do łącznika meta Networks**.

    ![Mapowania grup łączników metadanych](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do łącznika meta Networks w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w łączniku meta Networks dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy łączników meta Networks](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć łącznik usługi Azure AD aprowizacji dla sieci, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić łącznikowi meta Networks, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w łączniku meta Networks.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

