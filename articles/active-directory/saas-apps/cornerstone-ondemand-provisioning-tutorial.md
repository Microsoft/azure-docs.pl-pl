---
title: 'Samouczek: Konfigurowanie OnDemand kamień dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę przy OnDemandach.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358460"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie OnDemand kamień dla automatycznej aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w OnDemandach kamień i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników lub grup w OnDemandach.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji typu "oprogramowanie jako usługa" (SaaS) za pomocą Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawa OnDemand.
* Konto użytkownika w OnDemand Kamieńal z uprawnieniami administratora.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [usłudze sieci Web OnDemand](https://www.cornerstoneondemand.com/). Ta usługa jest dostępna dla zespołów z OnDemandami.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Dodaj OnDemandy kamień z portalu Azure Marketplace

Przed skonfigurowaniem OnDemandu firmy Microsoft do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD Dodaj do swojej listy zarządzane aplikacje SaaS OnDemande z witryny Marketplace.

Aby dodać OnDemand kamień z portalu Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **OnDemand** , a następnie wybierz pozycję **kamień OnDemand** w panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Aplikacja Cornerstone OnDemand na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Przypisywanie użytkowników do OnDemand kamień

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do OnDemandu. Aby przypisać tych użytkowników lub grupy do OnDemandu, postępuj zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Ważne wskazówki dotyczące przypisywania użytkowników do OnDemandu kamień

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do OnDemandu, aby przetestować konfigurację automatycznej aprowizacji użytkowników. Możesz później przypisywać dodatkowych użytkowników lub grupy.

* W przypadku przypisywania użytkownika do OnDemand kamień, wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisanie. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurowanie automatycznej aprowizacji użytkowników na potrzeby OnDemand kamień

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w OnDemandach kamień na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla OnDemandu kamień w usłudze Azure AD, wykonaj następujące kroki.

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz kolejno pozycje **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **kamień OnDemand**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **kamień OnDemand**.

    ![Link OnDemand węgielny na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![OnDemanda kamień](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![OnDemand — tryb aprowizacji](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. W sekcji **poświadczenia administratora** wprowadź nazwę użytkownika administratora, hasło administratora i domenę konta OnDemanda kamień:

    * W polu **Nazwa użytkownika administratora** Wypełnij domenę lub nazwę użytkownika konta administratora w dzierżawie OnDemand. Przykładem jest Contoso\admin.

    * W polu **hasło administratora** wprowadź hasło odpowiadające nazwie użytkownika administratora.

    * W polu **domena** wprowadź adres URL usługi sieci Web dzierżawy OnDemand. Na przykład usługa znajduje się w lokalizacji `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` , a dla contoso jest to domena `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` . Aby uzyskać więcej informacji na temat pobierania adresu URL usługi sieci Web, zobacz [ten plik PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po wypełnieniu pól przedstawionych w kroku 5 wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z OnDemandiem kamień. Jeśli połączenie nie powiedzie się, upewnij się, że konto z OnDemandymi jest kontem administratora, a następnie spróbuj ponownie.

    ![Połączenie z testem węgielnym OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Wybierz pozycję **Zapisz**.

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z OnDemandą kamień**.

    ![Synchronizacja OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do OnDemand kamień, w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w OnDemandach kamień dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![OnDemand — podstawowe mapowania atrybutów](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę Azure AD Provisioning dla OnDemandu, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Stan aprowizacji OnDemanda kamień](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Zdefiniuj użytkowników lub grupy, które mają być objęte OnDemandą. W sekcji **Ustawienia** wybierz wartości, które mają być w **zakresie**.

    ![OnDemandowy zasięg](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![OnDemand kamień węgielny](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż w przypadku późniejszej synchronizacji. Są one wykonywane co około 40 minut, o ile jest uruchomiona usługa Azure AD Provisioning. 

Możesz użyć sekcji Szczegóły synchronizacji, aby monitorować postęp i postępować zgodnie z raportem **dotyczącym** działań aprowizacji. Ten raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w OnDemandach kamień.

Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

OnDemandowy atrybut **położenia** jest oczekiwany wartości, która odnosi się do ról w portalu kamień OnDemand. Aby uzyskać listę prawidłowych wartości **pozycji** , przejdź do obszaru **edytowanie rekordu użytkownika > struktury organizacji > pozycji** w portalu kamień OnDemand.

![OnDemanda — edytowanie rekordu użytkownika](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![OnDemanda, pozycja aprowizacji](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista pozycji udostępniania OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
