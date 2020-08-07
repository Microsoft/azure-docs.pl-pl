---
title: 'Samouczek: Konfigurowanie MediusFlow dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do MediusFlow.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: c6c65170-c5cb-44f6-81f8-25d972759e2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 724068950153e6bc4687679892edf55ab89f3bf2
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927406"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie MediusFlow na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie MediusFlow i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [MediusFlow](https://www.mediusflow.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
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
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
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

    ![Uwierzytelnianie](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Sprawdź połączenie z usługą MediusFlow.

    ![Weryfikacja](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Podaj identyfikator dzierżawy usługi Azure AD.

    ![Podaj identyfikator dzierżawy](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Więcej informacji znajdziesz na stronie [często zadawanych pytań](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) na temat tego, jak je znaleźć.

4. Zapisz konfigurację.

    ![Uwierzytelnianie](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Wybierz pozycję Inicjowanie obsługi użytkowników i kliknij przycisk **OK**.

    ![Uwierzytelnianie](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Kliknij pozycję **Generuj klucz tajny**. Skopiuj i Zapisz tę wartość. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie **aprowizacji** aplikacji MediusFLow w Azure Portal.

    ![Uwierzytelnianie](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Kliknij przycisk **OK**.

    ![Uwierzytelnianie](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Aby użytkownicy zaimportowali ze wstępnie zdefiniowanym zestawem ról, firm i innych konfiguracji ogólnych w MediusFlow, należy najpierw ją skonfigurować. Zacznij od dodania konfiguracji, klikając pozycję **Dodaj nową konfigurację**.

    ![Użytkownicy konfiguracji](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Podaj ustawienia domyślne dla użytkowników. W tym widoku można ustawić domyślny atrybut. Jeśli ustawienia standardowe są poprawne, wystarczy podać prawidłową nazwę firmy. Ponieważ te ustawienia konfiguracji są pobierane z MediusFlow, należy najpierw je skonfigurować. Aby uzyskać więcej informacji, zobacz sekcję **wymagania wstępne** w tym artykule.

    ![Użytkownicy konfiguracji](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Kliknij przycisk **Zapisz** , aby zapisać konfigurację użytkownika.

    ![Użytkownicy konfiguracji](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Aby uzyskać link aprowizacji użytkowników, kliknij **link Kopiuj Standard scim**. Skopiuj i Zapisz tę wartość. Ta wartość jest wprowadzana w polu **adres URL dzierżawy** na karcie **aprowizacji** aplikacji MediusFLow w Azure Portal.
 
    ![Użytkownicy konfiguracji](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie MediusFlow z galerii aplikacji usługi Azure AD

Dodaj MediusFlow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do MediusFlow. Jeśli wcześniej skonfigurowano MediusFlow na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do MediusFlow należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować je, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do MediusFlow 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla MediusFlow w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **MediusFlow**.

    ![Link MediusFlow na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość adresu URL dzierżawy pobraną wcześniej w **adresie URL dzierżawy**. Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą MediusFlow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi MediusFlow ma uprawnienia administratora, a następnie spróbuj ponownie.

      ![aprowizacji](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do MediusFlow**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do MediusFlow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie MediusFlow for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API MediusFlow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |wiadomości e-mail [Type EQ "Work"]. Value|Ciąg|
   |Nazwa. displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |Nazwa. imię|Ciąg|
   |Nazwa. rodzina|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |externalID|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|


10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do MediusFlow**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do MediusFlow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w MediusFlow dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalID|Ciąg|
      |elementy członkowskie|Dokumentacja|

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla MediusFlow, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić MediusFlow, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
