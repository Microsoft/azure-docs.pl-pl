---
title: 'Samouczek: Konfigurowanie MediusFlow dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 71dc05e3949c2675be6255723530693304e1aa3e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850085"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie MediusFlow na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie MediusFlow i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [MediusFlow](https://www.mediusflow.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w MediusFlow
> * Usuń użytkowników w MediusFlow, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i MediusFlow
> * Udostępnianie grup i członkostwa w grupach w MediusFlow
> * Logowanie jednokrotne do MediusFlow (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Aktywna subskrypcja usługi MediusFlow z gwarancją jakości lub dzierżawą produkcyjną.
* Konto użytkownika w MediusFlow z prawami dostępu administratora, aby można było przeprowadzić konfigurację w programie MediusFlow.
* Firmy dodane w dzierżawie MediusFlow, w której użytkownicy powinni być obsługiwani.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie MediusFlow w celu obsługi aprowizacji za pomocą usługi Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktywuj aplikację Microsoft 365 w MediusFlow
Zacznij od włączenia dostępu do usługi Azure AD login i funkcji konfiguracji usługi Azure AD w ramach usługi MediusFlow, wykonując następujące czynności:

#### <a name="user-login"></a>Logowanie użytkownika
Aby włączyć przepływ logowania do Microsoft 365/Azure AD, zobacz [this] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) artykuł.

#### <a name="user-transfer-configuration"></a>Konfiguracja transferu użytkownika
Aby włączyć Portal konfiguracji użytkowników do aprowizacji z usługi Azure AD, zobacz [ten](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artykuł.

#### <a name="configure-user-provisioning"></a>Konfigurowanie aprowizacji użytkowników

1.  Zaloguj się do [konsoli administracyjnej MediusFlow](https://office365.cloudapp.mediusflow.com/) , podając identyfikator dzierżawy.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

2. Sprawdź połączenie z usługą MediusFlow.

    ![Weryfikacja](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Podaj identyfikator dzierżawy usługi Azure AD.

    ![Podaj identyfikator dzierżawy](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Więcej informacji znajdziesz na stronie [często zadawanych pytań](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) na temat tego, jak je znaleźć.

4. Zapisz konfigurację.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

5. Wybierz pozycję Inicjowanie obsługi użytkowników i kliknij przycisk **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

6. Kliknij pozycję **Generuj klucz tajny**. Skopiuj i Zapisz tę wartość. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie **aprowizacji** aplikacji MediusFLow w Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

7. Kliknij przycisk **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

8. Aby użytkownicy zaimportowali ze wstępnie zdefiniowanym zestawem ról, firm i innych konfiguracji ogólnych w MediusFlow, należy najpierw ją skonfigurować. Zacznij od dodania konfiguracji, klikając pozycję **Dodaj nową konfigurację**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

9. Podaj ustawienia domyślne dla użytkowników. W tym widoku można ustawić domyślny atrybut. Jeśli ustawienia standardowe są poprawne, wystarczy podać prawidłową nazwę firmy. Ponieważ te ustawienia konfiguracji są pobierane z MediusFlow, należy najpierw je skonfigurować. Aby uzyskać więcej informacji, zobacz sekcję **wymagania wstępne** w tym artykule.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

10. Kliknij przycisk **Zapisz** , aby zapisać konfigurację użytkownika.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

11. Aby uzyskać link aprowizacji użytkowników, kliknij **link Kopiuj Standard scim**. Skopiuj i Zapisz tę wartość. Ta wartość jest wprowadzana w polu **adres URL dzierżawy** na karcie **aprowizacji** aplikacji MediusFLow w Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Zrzut ekranu przedstawiający konsolę administracyjną MediusFlow. Pole nazwy dzierżawy MediusFlow i przycisk uwierzytelniania są wyróżnione w pierwszym kroku integracji." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie MediusFlow z galerii aplikacji usługi Azure AD

Dodaj MediusFlow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do MediusFlow. Jeśli wcześniej skonfigurowano MediusFlow na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do MediusFlow należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować je, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do MediusFlow 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla MediusFlow w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **MediusFlow**.

    ![Link MediusFlow na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość adresu URL dzierżawy pobraną wcześniej w **adresie URL dzierżawy**. Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą MediusFlow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi MediusFlow ma uprawnienia administratora, a następnie spróbuj ponownie.

      ![Zrzut ekranu przedstawia okno dialogowe poświadczenia administratora, w którym można wprowadzić dzierżawę U R L i wpis tajny.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do MediusFlow**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do MediusFlow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie MediusFlow for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API MediusFlow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |Nazwa. displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |externalID|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Tematy pomocy|


10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do MediusFlow**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do MediusFlow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w MediusFlow dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalID|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla MediusFlow, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić MediusFlow, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
