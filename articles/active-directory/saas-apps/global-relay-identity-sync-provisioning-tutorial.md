---
title: 'Samouczek: Konfigurowanie synchronizacji tożsamości usługi Global Identity dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do synchronicznej synchronizacji tożsamości przekaźnika.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: d003a512ebde626b8726dfccc58110e53f1cd467
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180918"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie globalnej synchronizacji tożsamości przekaźnika dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w przypadku synchronizacji tożsamości usługi Global Identity, jak i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w przypadku synchronizacji tożsamości usługi globalne w usłudze Azure AD. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze globalna synchronizacja tożsamości przekaźnika
> * Usuń użytkowników z synchronizacji tożsamości w usłudze Global Identity, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a synchronizacją globalnego przekazywania tożsamości
> * Inicjowanie obsługi administracyjnej grup i członkostw w grupach w usłudze globalna synchronizacja tożsamości przekaźnika


> [!NOTE]
> Łącznik obsługi administracyjnej synchronizacji tożsamości przekaźnika globalnego używa metody autoryzacji Standard scim, która nie jest już obsługiwana z powodu problemów z bezpieczeństwem. Wysiłki odbywają się w drodze do przełączenia do bezpieczniejszej metody autoryzacji.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD a synchronizacją globalnego przekazywania tożsamości](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie synchronicznej synchronizacji tożsamości przekaźnika w celu obsługi aprowizacji za pomocą usługi Azure AD

Skontaktuj się z przedstawicielem swojej firmy w celu uzyskania adresu URL dzierżawy. Ta wartość zostanie wprowadzona w polu **adres URL dzierżawy** na karcie aprowizacji aplikacji do synchronizacji tożsamości przekaźnika globalnego w Azure Portal.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie synchronicznej synchronizacji tożsamości przekaźnika z galerii aplikacji usługi Azure AD

Dodaj synchroniczną synchronizację tożsamości przekaźnika z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą administracyjną do synchronizacji tożsamości w usłudze Global Relay. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Krok 5. Skonfiguruj automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze globalne przekazywanie tożsamości 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji synchronicznej synchronizacji tożsamości przekaźnikowej na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną dla synchronicznej synchronizacji tożsamości przekaźnika w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **globalna synchronizacja tożsamości przekaźnika**.

    ![Link do synchronicznej synchronizacji tożsamości przekaźnika na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** synchronizacji tożsamości przekaźnika globalnego. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z synchronizacją tożsamości usługi Global Relay Jeśli połączenie nie powiedzie się, upewnij się, że konto synchronizacji tożsamości usługi Relay ma uprawnienia administratora, a następnie skontaktuj się z przedstawicielem globalnego, aby rozwiązać ten problem.

    ![Przycisk autoryzacji](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy z synchronizacją globalną tożsamość przekaźnika**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do synchronicznej synchronizacji tożsamości przekaźnika w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w globalnej synchronizacji tożsamości przekaźnika dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że interfejs API synchronizacji tożsamości przekaźnika globalnego obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |title|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |adresy [typ EQ "Work"]. sformatowane|Ciąg|
   |adresy [typ EQ "Work"]. streetAddress|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |adresy [typ EQ "Work"]. locale|Ciąg|
   |addresss [Type EQ "Work"]. region|Ciąg|
   |addresss [Type EQ "Work"]. KodPocztowy|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |adresy [Type EQ "Other"]. sformatowane|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|Ciąg|
   |externalId|Ciąg|
   |name.honorificPrefix|Ciąg|
   |Name. honorificSuffix|Ciąg|
   |nickName|Ciąg|
   |userType|Ciąg|
   |locale|Ciąg|
   |timezone|Ciąg|
   |wiadomości e-mail [Type EQ "Home"]. Value|Ciąg|
   |wiadomości e-mail [Type EQ "Other"]. Value|Ciąg|
   |numer telefonu [typ EQ "Home"]. Value|Ciąg|
   |braky [Type EQ "Other"]. Value|Ciąg|
   |numer telefonu [typ EQ "pager"]. wartość|Ciąg|
   |adresy [Type EQ "Home"]. streetAddress|Ciąg|
   |adresy [Type EQ "Home"]. locale|Ciąg|
   |adresy [Type EQ "Home"]. region|Ciąg|
   |adresy [Type EQ "Home"]. KodPocztowy|Ciąg|
   |adresy [Type EQ "Home"]. Country|Ciąg|
   |adresy [Type EQ "Home"]. sformatowane|Ciąg|
   |adresy [Type EQ "Other"]. streetAddress|Ciąg|
   |adresy [Type EQ "Other"]. locale|Ciąg|
   |addresss [Type EQ "Other"]. region|Ciąg|
   |adresy [Type EQ "Other"]. KodPocztowy|Ciąg|
   |adresy [Type EQ "Other"]. Country|Ciąg|
   |role [podstawowa EQ "true"]. Display|Ciąg|
   |role [podstawowa EQ "true"]. Type|Ciąg|
   |roles[primary eq "True"].value|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: proxyAddresses|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute1|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute3|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute4|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute5|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute6|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute7|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute8|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute9|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute10|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute11|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute12|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute13|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute14|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: GlobalRelay: 2.0: User: extensionAttribute15|Ciąg|



10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory grupy do synchronizowania tożsamości przekaźnika globalnego**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do synchronicznej synchronizacji tożsamości przekaźnika w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w globalnej synchronizacji tożsamości przekaźnika dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla synchronicznej synchronizacji tożsamości przekaźnika, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić globalną synchronizację tożsamości przekaźnika, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)