---
title: 'Samouczek: Konfigurowanie łącznika tożsamości usługi Brivo OnAir w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników na Brivo OnAir Identity Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998093"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie łącznika tożsamości usługi Brivo OnAir w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w łączniku tożsamości Brivo OnAir i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup na potrzeby Brivo OnAir Identity Connector.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa łącznika tożsamości Brivo OnAir](https://www.brivo.com/lp/quote)
* Konto użytkownika w łączniku tożsamości Brivo OnAir z uprawnieniami administratora wyższego poziomu.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Przypisywanie użytkowników do łącznika tożsamości Brivo OnAir

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do łącznika tożsamości Brivo OnAir. Po ustaleniu tych użytkowników i/lub grup można przypisać do łącznika tożsamości OnAir Brivo, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Ważne porady dotyczące przypisywania użytkowników do łącznika tożsamości usługi Brivo OnAir

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do łącznika tożsamości usługi Brivo OnAir w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do łącznika Identity Brivo OnAir, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Konfigurowanie łącznika tożsamości usługi Brivo OnAir na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej łącznika tożsamości programu Brivo OnAir](https://acs.brivo.com/login/). Przejdź do **konta > ustawienia konta**.

   ![Konsola administracyjna łącznika Brivo OnAir Identity](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Kliknij kartę **Azure AD** . Na stronie Szczegóły **usługi Azure AD** wprowadź ponownie hasło konta administratora wyższego poziomu. Kliknij pozycję **Prześlij**.

   ![Łącznik tożsamości Brivo OnAir Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Kliknij przycisk **Kopiuj token** i Zapisz **token tajny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji Brivo OnAir Identity Connector w Azure Portal.

   ![Token łącznika Brivo OnAir Identity](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Dodawanie łącznika tożsamości usługi Brivo OnAir z galerii

Przed skonfigurowaniem łącznika tożsamości usługi Brivo OnAir w celu automatycznego aprowizacji użytkowników w usłudze Azure AD musisz dodać łącznik Brivo OnAir Identity z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać łącznik Brivo OnAir Identity z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Brivo OnAir Identity Connector**, wybierz pozycję **Brivo OnAir Identity Connector** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Łącznik tożsamości Brivo OnAir na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurowanie automatycznego aprowizacji użytkowników w łączniku Brivo OnAir Identity 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w łączniku tożsamości Brivo OnAir na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla łącznika tożsamości usługi Brivo OnAir w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Brivo OnAir Identity Connector**.

    ![Łącze łącznika tożsamości Brivo OnAir na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://scim.brivo.com/ActiveDirectory/v2/` **adres URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z łącznikiem tożsamości Brivo OnAir. Jeśli połączenie nie powiedzie się, upewnij się, że konto łącznika tożsamości usługi Brivo OnAir ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Brivo OnAir Identity Connector**.

    ![Mapowania użytkowników łącznika tożsamości Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Brivo OnAir Identity Connector w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w łączniku tożsamości Brivo OnAir dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika łącznika tożsamości Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Brivo OnAir Identity Connector**.

    ![Mapowania grup łączników tożsamości Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługą Azure AD do Brivo OnAir Identity Connector w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w łączniku Brivo OnAir Identity dla operacji Update. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy łączników tożsamości Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning Connector dla łącznika tożsamości Brivo OnAir, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Brivo OnAir łącznika tożsamości, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na łączniku tożsamości Brivo OnAir.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

