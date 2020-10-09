---
title: 'Samouczek: Konfigurowanie usługi Oracle Fusion ERP do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w programie Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 45a1ae281a8a1acb1cfd12efe500f5a623033ef3
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850049"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Oracle Fusion ERP do automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności do wykonania w programie Oracle Fusion ERP i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w usłudze Oracle Fusion ERP.

> [!NOTE]
>  Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa programu Oracle Fusion](https://www.oracle.com/applications/erp/).
* Konto użytkownika w programie Oracle Fusion ERP z uprawnieniami administratora.

## <a name="assign-users-to-oracle-fusion-erp"></a>Przypisywanie użytkowników do programu Oracle Fusion ERP 
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do programu Oracle Fusion ERP. Po ustaleniu tych użytkowników i/lub grup można przypisać do programu Oracle Fusion ERP, postępując zgodnie z poniższymi instrukcjami:
 
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Ważne porady dotyczące przypisywania użytkowników do programu Oracle Fusion ERP 

 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do programu Oracle Fusion ERP w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do programu Oracle Fusion ERP należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Konfigurowanie usługi Oracle Fusion ERP na potrzeby aprowizacji

Przed skonfigurowaniem programu Oracle Fusion ERP do automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć funkcję aprowizacji Standard scim w usłudze Oracle Fusion ERP.

1. Logowanie się do [konsoli administratora ERP programu Oracle Fusion](https://cloud.oracle.com/sign-in)

2. Kliknij Nawigator w lewym górnym rogu. W obszarze **Narzędzia**wybierz pozycję **konsola zabezpieczeń**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Zrzut ekranu przedstawiający stronę nawigatora w konsoli administratora programu Oracle Fusion E P. Narzędzia i konsola zabezpieczeń są wyróżnione." border="false":::

3. Przejdź do **użytkowników**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Zrzut ekranu przedstawiający stronę nawigatora w konsoli administratora programu Oracle Fusion E P. Narzędzia i konsola zabezpieczeń są wyróżnione." border="false":::

4. Zapisz nazwę użytkownika i hasło dla konta użytkownika administratora, którego będziesz używać do logowania się do konsoli administratora usługi ERP Oracle Fusion. Te wartości należy wprowadzić w polach **Nazwa użytkownika** i **hasło** administratora na karcie aprowizacji aplikacji ERP programu Oracle w Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Dodawanie programu Oracle Fusion ERP z galerii

Aby skonfigurować funkcję Oracle Fusion ERP do automatycznego inicjowania obsługi użytkowników w usłudze Azure AD, musisz dodać program Oracle Fusion ERP z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program Oracle Fusion ERP z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź ciąg **Oracle Fusion ERP**, a następnie wybierz pozycję **Oracle Fusion ERP** w panelu wyniki.

    ![Aplikacja Oracle Fusion ERP na liście wyników](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Oracle Fusion ERP 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Oracle Fusion ERP na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla programu Oracle Fusion ERP, postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [usłudze Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym usługi Standard scim na platformie ERP firmy Oracle, zapoznaj się z [interfejsem API REST w przypadku typowych funkcji w chmurze aplikacji Oracle](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Fuze w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Oracle Fusion ERP**.

    ![Link do aplikacji Oracle Fusion ERP na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **adres URL dzierżawy**. Wprowadź nazwę użytkownika i hasło administratora, które zostały pobrane wcześniej do pól Nazwa użytkownika i **hasło** **administratora** . Kliknij **Test connection** między usługami Azure AD i Oracle Fusion ERP. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Zrzut ekranu przedstawiający stronę nawigatora w konsoli administratora programu Oracle Fusion E P. Narzędzia i konsola zabezpieczeń są wyróżnione." border="false":::

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do programu Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Zrzut ekranu przedstawiający stronę nawigatora w konsoli administratora programu Oracle Fusion E P. Narzędzia i konsola zabezpieczeń są wyróżnione." border="false":::

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do programu Oracle Fusion ERP w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w programie Oracle Fusion ERP for Update. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Zrzut ekranu przedstawiający stronę nawigatora w konsoli administratora programu Oracle Fusion E P. Narzędzia i konsola zabezpieczeń są wyróżnione." border="false":::

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do programu Oracle Fusion ERP**.

    ![Mapowania grup ERP programu Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą Oracle Fusion ERP, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w ramach operacji programu Oracle Fusion ERP for Update. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy ERP programu Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu Oracle Fusion ERP, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić usłudze Oracle Fusion ERP, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

    Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w usłudze Oracle Fusion ERP.

    Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Program Oracle Fusion ERP obsługuje tylko uwierzytelnianie podstawowe dla swojego punktu końcowego Standard scim.
* Usługa Oracle Fusion ERP nie obsługuje udostępniania grup.
* Role w usłudze Oracle Fusion ERP są mapowane na grupy w usłudze Azure AD. Aby przypisać role do użytkowników w usłudze Oracle Fusion ERP z usługi Azure AD, należy przypisać użytkowników do żądanych grup usługi Azure AD, które są nazwane po rolach w programie Oracle Fusion ERP.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
