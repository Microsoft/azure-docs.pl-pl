---
title: 'Samouczek: Konfigurowanie iPass SmartConnect dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w programie iPass SmartConnect.
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
ms.openlocfilehash: fa9723b466045ebe7b932f837e0fa7c45c898b6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353888"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie iPass SmartConnect dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w iPass SmartConnect i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do iPass SmartConnect.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa IPass SmartConnect](https://www.ipass.com/buy-ipass/).
* Konto użytkownika w programie iPass SmartConnect z uprawnieniami administratora.

## <a name="assigning-users-to-ipass-smartconnect"></a>Przypisywanie użytkowników do iPass SmartConnect

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do iPass SmartConnect. Po ustaleniu tych użytkowników i/lub grup można przypisać do iPass SmartConnect, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Ważne wskazówki dotyczące przypisywania użytkowników do iPass SmartConnect

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do iPass SmartConnect w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do iPass SmartConnect, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Konfigurowanie iPass SmartConnect na potrzeby aprowizacji

Przed skonfigurowaniem usługi iPass SmartConnect w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać informacje o konfiguracji z konsoli administratora iPass SmartConnect:

1. Aby pobrać token okaziciela, który jest wymagany do uwierzytelnienia w punkcie końcowym iPass SmartConnect Standard scim, zapoznaj się z pierwszym ustawieniem iPass SmartConnect, ponieważ ta wartość jest dostępna tylko wtedy. 
2. Jeśli nie masz tokenu okaziciela, skontaktuj się z [zespołem pomocy technicznej IPass SmartConnect](mailto:help@ipass.com) , aby pobrać nowy.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Dodaj iPass SmartConnect z galerii

Aby skonfigurować usługę iPass SmartConnect dla automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać iPass SmartConnect z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać iPass SmartConnect z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **IPass SmartConnect**, wybierz pozycję **iPass SmartConnect** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![iPass SmartConnect na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Konfigurowanie automatycznej aprowizacji użytkowników do iPass SmartConnect 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze iPass SmartConnect na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
>  Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla BitaBIZ, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](ipasssmartconnect-tutorial.md)jednokrotne w iPass SmartConnect. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla iPass SmartConnect w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **IPass SmartConnect**.

    ![Link iPass SmartConnect na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://openmobile.ipass.com/moservices/scim/v1` **adres URL dzierżawy**. Wprowadź token okaziciela pobrany wcześniej w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z iPass SmartConnect. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi iPass SmartConnect ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby iPass SmartConnect**.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/usermapping.png" alt-text="Zrzut ekranu przedstawiający sekcję mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z iPass SmartConnect jest widoczny." border="false":::

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do iPass SmartConnect w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w iPass SmartConnect dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/userattribute.png" alt-text="Zrzut ekranu przedstawiający stronę mapowania atrybutów. W tabeli wymieniono Azure Active Directory i atrybuty SmartConnect iPass oraz pierwszeństwo dopasowywania." border="false":::


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla iPass SmartConnect, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do iPass SmartConnect, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie iPass SmartConnect.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* iPass SmartConnect akceptuje tylko nazwy użytkowników, którzy mają zarejestrowane domeny w konsoli administratora iPass SmartConnect.  

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
