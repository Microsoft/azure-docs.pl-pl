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
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 92aeedd25a4a60b49de54aaaa90ee4a31490c444
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359837"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konsoli infrastruktury w chmurze firmy Oracle do automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w konsoli infrastruktury chmurowej firmy Oracle i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w [konsoli infrastruktury chmurowej firmy Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w konsoli infrastruktury w chmurze firmy Oracle
> * Usuń użytkowników w konsoli infrastruktury firmy Oracle, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i konsolą infrastruktury w chmurze firmy Oracle
> * Udostępnianie grup i członkostw w grupach w konsoli infrastruktury w chmurze firmy Oracle
> * [Logowanie](./oracle-cloud-tutorial.md) jednokrotne do konsoli infrastruktury firmy Oracle w chmurze (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* [Dzierżawa](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)usługi Cloud Infrastructure kontroli firmy Oracle.
* Konto użytkownika w kontrolce infrastruktury chmurowej Oracle z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i konsolą infrastruktury w chmurze firmy Oracle](../app-provisioning/customize-application-attributes.md). 

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

Dodaj konsolę infrastruktury firmy Oracle z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą w konsoli infrastruktury firmy Oracle. Jeśli wcześniej skonfigurowano konsolę usługi Oracle Cloud Infrastructure dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do konsoli infrastruktury w chmurze firmy Oracle należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do konsoli infrastruktury w chmurze firmy Oracle 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną dla konsoli infrastruktury chmury firmy Oracle w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **konsola infrastruktury w chmurze firmy Oracle**.

    ![Link konsoli infrastruktury w chmurze firmy Oracle na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** w formacie `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Na przykład: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z konsolą infrastruktury firmy Oracle. Jeśli połączenie nie powiedzie się, upewnij się, że konto konsoli infrastruktury w chmurze firmy Oracle ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawia okno dialogowe poświadczenia administratora, w którym można wprowadzić dzierżawę U R L i wpis tajny.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z konsolą infrastruktury w chmurze firmy Oracle**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do konsoli infrastruktury w chmurze firmy Oracle, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w konsoli infrastruktury w chmurze Oracle dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API konsoli infrastruktury chmury firmy Oracle obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |userName|Ciąg|
      |aktywne|Wartość logiczna|
      |title|Ciąg|
      |emails[type eq "work"].value|Ciąg|
      |preferredLanguage|Ciąg|
      |name.givenName|Ciąg|
      |name.familyName|Ciąg|
      |adresy [typ EQ "Work"]. sformatowane|Ciąg|
      |adresy [typ EQ "Work"]. locale|Ciąg|
      |addresss [Type EQ "Work"]. region|Ciąg|
      |addresss [Type EQ "Work"]. KodPocztowy|Ciąg|
      |addresss [Type EQ "Work"]. Country|Ciąg|
      |adresy [typ EQ "Work"]. streetAddress|Ciąg|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|
      |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|Ciąg|
      |urn: IETF: params: Standard scim: schematy: Oracle: IDCs: Extension: User: User: bypassNotification|Wartość logiczna|
      |urn: IETF: params: Standard scim: schematy: Oracle: IDCs: Extension: User: User: isFederatedUser|Wartość logiczna|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do konsoli infrastruktury w chmurze firmy Oracle**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do konsoli infrastruktury w chmurze firmy Oracle, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do grup w konsoli infrastruktury w chmurze firmy Oracle dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu Oracle Cloud Infrastructure Console, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w konsoli infrastruktury chmurowej firmy Oracle, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)