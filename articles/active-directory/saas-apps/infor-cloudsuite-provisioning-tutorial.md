---
title: 'Samouczek: Konfigurowanie infor CloudSuite dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w programie infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353905"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie infor CloudSuite dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w infor CloudSuite i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do infor CloudSuite.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa infor CloudSuite](https://www.infor.com/products/infor-os)
* Konto użytkownika w programie infor CloudSuite z uprawnieniami administratora.

## <a name="assigning-users-to-infor-cloudsuite"></a>Przypisywanie użytkowników do infor CloudSuite

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do infor CloudSuite. Po ustaleniu tych użytkowników i/lub grup można przypisać do infor CloudSuite, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Ważne wskazówki dotyczące przypisywania użytkowników do infor CloudSuite

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do infor CloudSuite w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do infor CloudSuite, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Konfigurowanie usługi infor CloudSuite na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej programu infor CloudSuite](https://www.infor.com/customer-center). Kliknij ikonę użytkownika, a następnie przejdź do okna **Zarządzanie użytkownikami**.

    ![Konsola administracyjna infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Kliknij ikonę menu w lewym górnym rogu ekranu. Kliknij pozycję **Zarządzaj**.

    ![Infor CloudSuite Dodaj Standard scim](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Przejdź do **konta Standard scim**.

    ![Konto infor CloudSuite Standard scim](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Dodaj użytkownika administrator, klikając ikonę znaku plus. Podaj **hasło Standard scim** i wpisz to samo hasło w obszarze **Potwierdź hasło**. Aby zapisać hasło, kliknij ikonę folderu. Zostanie wyświetlony **Identyfikator użytkownika** wygenerowany dla administratora.

    ![Administrator infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite hasło](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną infor CloudSuite z wyróżnionym wierszem tabeli. Ten wiersz zawiera identyfikator użytkownika, hasła i sygnaturę czasową." border="false":::

5. Aby wygenerować token okaziciela, skopiuj **Identyfikator użytkownika** i **hasło Standard scim**. Wklej je w Notatniku + + oddzielone dwukropkiem. Zakoduj wartość ciągu, przechodząc do **wtyczek > narzędzia MIME > kodowanie Basic64**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Zrzut ekranu przedstawiający dokument programu Notatnik + +. W menu wtyczki narzędzia MIME są wyróżnione. W menu Narzędzia MIME kodowanie Base64 jest wyróżnione." border="false":::

3.  Skopiuj token okaziciela. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji infor CloudSuite w Azure Portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Dodaj infor CloudSuite z galerii

Przed skonfigurowaniem usługi infor CloudSuite w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać infor CloudSuite z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać infor CloudSuite z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **infor CloudSuite**, wybierz pozycję **infor CloudSuite** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Infor CloudSuite na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurowanie automatycznej aprowizacji użytkowników do infor CloudSuite 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze infor CloudSuite na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla infor CloudSuite, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](./infor-cloud-suite-tutorial.md)jednokrotne CloudSuite infor. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym Standard scim infor CloudSuite, zapoznaj się z [tym](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)tematem.

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla infor CloudSuite w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **infor CloudSuite**.

    ![Link infor CloudSuite na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **adres URL dzierżawy**. Wprowadź wartość tokenu okaziciela pobranego wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z infor CloudSuite. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi infor CloudSuite ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby infor CloudSuite**.

    ![Mapowania użytkowników infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do infor CloudSuite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w infor CloudSuite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do infor CloudSuite**.

    ![Infor mapowania grup CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do infor CloudSuite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w infor CloudSuite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla infor CloudSuite, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do infor CloudSuite, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie infor CloudSuite.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)