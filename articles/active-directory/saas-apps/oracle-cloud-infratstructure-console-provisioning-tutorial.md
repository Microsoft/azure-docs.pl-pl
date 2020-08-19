---
title: 'Samouczek: Konfigurowanie konsoli infrastruktury w chmurze firmy Oracle do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w konsoli infrastruktury chmury firmy Oracle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0c3d68698621fe963074c7216e4636208d93deca
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543875"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konsoli infrastruktury w chmurze firmy Oracle do automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w konsoli infrastruktury chmurowej firmy Oracle i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w [konsoli infrastruktury chmurowej firmy Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w konsoli infrastruktury w chmurze firmy Oracle
> * Usuń użytkowników w konsoli infrastruktury firmy Oracle, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i konsolą infrastruktury w chmurze firmy Oracle
> * Udostępnianie grup i członkostw w grupach w konsoli infrastruktury w chmurze firmy Oracle
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) jednokrotne do konsoli infrastruktury firmy Oracle w chmurze (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)usługi Cloud Infrastructure kontroli firmy Oracle.
* Konto użytkownika w kontrolce infrastruktury chmurowej Oracle z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i konsolą infrastruktury w chmurze firmy Oracle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie konsoli infrastruktury w chmurze firmy Oracle do obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do portalu administracyjnego konsoli infrastruktury w chmurze firmy Oracle. W lewym górnym rogu ekranu przejdź do **tożsamości > federacyjnej**.

    ![Administrator Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Kliknij adres URL wyświetlany na stronie obok pozycji Oracle Identity Cloud Service Console.

    ![Adres URL programu Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Kliknij pozycję **Dodaj dostawcę tożsamości** , aby utworzyć nowego dostawcę tożsamości. Zapisz identyfikator dostawcy tożsamości, który ma być używany jako część adresu URL dzierżawy. Kliknij ikonę plusa obok karty **aplikacje** , aby utworzyć klienta OAuth i udzielić IDCS APPROLE administrator domeny tożsamości.

    ![Ikona chmury Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Aby skonfigurować aplikację, postępuj zgodnie z poniższymi zrzutami ekranu. Po zakończeniu konfiguracji kliknij pozycję **Zapisz**.

    ![Konfiguracja programu Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Zasady wystawiania tokenów Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na karcie konfiguracje aplikacji rozwiń opcję **Informacje ogólne** , aby pobrać identyfikator klienta i klucz tajny klienta.

    ![Generowanie tokenu Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Aby wygenerować klucz tajny Base64, zakodować identyfikator klienta i klucz tajny klienta w formacie **Identyfikator klienta: wpis tajny klienta**. Zapisz token tajny. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji w konsoli infrastruktury w chmurze firmy Oracle w Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie konsoli środowiska Oracle Cloud Infrastructure z galerii aplikacji usługi Azure AD

Dodaj konsolę infrastruktury firmy Oracle z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą w konsoli infrastruktury firmy Oracle. Jeśli wcześniej skonfigurowano konsolę usługi Oracle Cloud Infrastructure dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do konsoli infrastruktury w chmurze firmy Oracle należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do konsoli infrastruktury w chmurze firmy Oracle 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną dla konsoli infrastruktury chmury firmy Oracle w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **konsola infrastruktury w chmurze firmy Oracle**.

    ![Link konsoli infrastruktury w chmurze firmy Oracle na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** w formacie `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Na przykład: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z konsolą infrastruktury firmy Oracle. Jeśli połączenie nie powiedzie się, upewnij się, że konto konsoli infrastruktury w chmurze firmy Oracle ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![aprowizacji](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z konsolą infrastruktury w chmurze firmy Oracle**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do konsoli infrastruktury w chmurze firmy Oracle, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w konsoli infrastruktury w chmurze Oracle dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API konsoli infrastruktury chmury firmy Oracle obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|String|
      |userName|String|
      |aktywne|Boolean|
      |tytuł|String|
      |wiadomości e-mail [Type EQ "Work"]. Value|String|
      |preferredLanguage|String|
      |Nazwa. imię|String|
      |Nazwa. rodzina|String|
      |adresy [typ EQ "Work"]. sformatowane|String|
      |adresy [typ EQ "Work"]. locale|String|
      |addresss [Type EQ "Work"]. region|String|
      |addresss [Type EQ "Work"]. KodPocztowy|String|
      |addresss [Type EQ "Work"]. Country|String|
      |adresy [typ EQ "Work"]. streetAddress|String|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|String|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|String|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|String|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|String|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|String|
      |urn: IETF: params: Standard scim: schematy: Oracle: IDCs: Extension: User: User: bypassNotification|Boolean|
      |urn: IETF: params: Standard scim: schematy: Oracle: IDCs: Extension: User: User: isFederatedUser|Boolean|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do konsoli infrastruktury w chmurze firmy Oracle**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do konsoli infrastruktury w chmurze firmy Oracle, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do grup w konsoli infrastruktury w chmurze firmy Oracle dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|String|
      |externalId|String|
      |elementy członkowskie|Dokumentacja|

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu Oracle Cloud Infrastructure Console, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w konsoli infrastruktury chmurowej firmy Oracle, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
* Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
* Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
