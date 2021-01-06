---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler beta dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w programie rozwiązania Zscaler beta.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937165"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler beta dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w rozwiązania Zscaler beta i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w programie rozwiązania Zscaler beta.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawa rozwiązania Zscaler beta
* Konto użytkownika w programie rozwiązania Zscaler beta z uprawnieniami administratora

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na interfejsie API Standard scim rozwiązania Zscaler beta, który jest dostępny dla deweloperów rozwiązania Zscaler beta dla kont z pakietem Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Dodawanie usługi Zscaler Beta z galerii

Przed skonfigurowaniem programu rozwiązania Zscaler beta do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać rozwiązania Zscaler beta z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler beta z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler Beta**, wybierz pozycję **Zscaler Beta** na panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Przypisywanie użytkowników do rozwiązania Zscaler beta

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do rozwiązania Zscaler beta. Po ustaleniu tych użytkowników i/lub grup można przypisywać do rozwiązania Zscaler beta, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Ważne porady dotyczące przypisywania użytkowników do programu rozwiązania Zscaler beta

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do rozwiązania Zscaler beta w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do programu rozwiązania Zscaler beta należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurowanie automatycznej aprowizacji użytkowników w programie rozwiązania Zscaler beta

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie rozwiązania Zscaler beta na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla rozwiązania Zscaler beta, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](zscaler-beta-tutorial.md)jednokrotne w rozwiązania Zscaler beta. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

> [!NOTE]
> Po zainicjowaniu obsługi administracyjnej użytkowników i grup, zalecamy okresowe ponowne uruchomienie aprowizacji, aby upewnić się, że członkostwa w grupach zostały prawidłowo zaktualizowane. Ponowne uruchomienie spowoduje wymuszenie ponownego oszacowania wszystkich grup i zaktualizowania członkostw przez naszą usługę.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla programu rozwiązania Zscaler beta w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **rozwiązania Zscaler beta**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler beta**.

    ![Link usługi Zscaler Beta na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Istnieje lista kart uporządkowanych według kategorii i zatytułowana rozwiązania Zscaler beta — Provisioning/Enterprise. Wybrana jest karta udostępnianie kategorii Zarządzaj.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Tryb automatyczny został wybrany z listy rozwijanej Tryb aprowizacji. Istnieją pola poświadczeń administratora używane do nawiązywania połączenia z interfejsem API programu rozwiązania Zscaler beta i istnieje przycisk Testuj połączenie.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** konta rozwiązania Zscaler beta, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny**, przejdź do pozycji **Administracja > ustawienia uwierzytelniania** w interfejsie użytkownika portalu rozwiązania Zscaler beta i kliknij pozycję **SAML** w obszarze **Typ uwierzytelniania**.

    ![W ustawieniach uwierzytelniania w profilu uwierzytelniania wybrany typ katalogu to hostowana baza danych, a wybrany typ uwierzytelniania to SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **Konfiguruj SAML** , aby otworzyć Opcje **SAML konfiguracji** .

    ![Na stronie Konfigurowanie protokołu SAML wybierane są opcje Włącz Inicjowanie obsługi SAML i Włącz SCIM-Based. Pola tekstowe podstawowego adresu URL i tokenu okaziciela są wyróżnione.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Wybierz pozycję **Włącz obsługę SCIM-Based** , aby pobrać **podstawowy adres URL** i **token okaziciela**, a następnie Zapisz ustawienia. Skopiuj **podstawowy adres URL** do **adresu URL dzierżawy** i token **okaziciela**  do **tokenu tajnego** w Azure Portal.

7. Po wypełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z rozwiązania Zscaler wersją beta. Jeśli połączenie nie powiedzie się, upewnij się, że konto rozwiązania Zscaler beta ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![W przypadku poświadczeń administratora pola adres URL dzierżawy i token tajny mają wartości, a przycisk Testuj połączenie jest wyróżniony.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Pole tekstowe powiadomienia E-mail jest puste, a pole wyboru Wyślij powiadomienie e-mail po wystąpieniu błędu jest wyczyszczone.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby rozwiązania Zscaler wersję beta**.

    ![Wybrano i włączono funkcję Synchronizuj Azure Active Directory użytkownicy do ZScalerBeta.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler beta w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie rozwiązania Zscaler beta dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![W sekcji mapowania atrybutów dla atrybutów użytkownika są wyświetlane atrybuty Active Directory, które znajdują się obok atrybutów Zscalar beta, z którymi są synchronizowane. Jedna para atrybutów jest pokazywana jako dopasowanie.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do rozwiązania Zscaler beta**.

    ![Wybrana i włączona Grupa synchronizacji Azure Active Directory ZScalerBeta.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler beta w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w rozwiązania Zscaler beta dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![W sekcji mapowania atrybutów dla atrybutów grupy są wyświetlane atrybuty Active Directory, które znajdują się obok atrybutów Zscalar beta, z którymi są synchronizowane. Jedna para atrybutów jest pokazywana jako dopasowanie.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning for rozwiązania Zscaler beta, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest pokazywany i ustawiony na wartość włączone.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do rozwiązania Zscaler beta, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zostanie wyświetlona lista rozwijana zakres i zostanie wybrana synchronizacja tylko przypisanych użytkowników i grup. Druga dostępna wartość to Synchronizuj wszystkich użytkowników i grupy.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zostanie wyróżniony przycisk Zapisz w górnej części rozwiązania Zscaler beta — Inicjowanie obsługi administracyjnej. Istnieje również przycisk Odrzuć.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w programie rozwiązania Zscaler beta.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png