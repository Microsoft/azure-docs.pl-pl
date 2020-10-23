---
title: 'Samouczek: Konfigurowanie AlertMedia dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do AlertMedia.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: 2ae06a8ed59f7987f58aba8b8017833315e6529f
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92429000"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie AlertMedia na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie AlertMedia i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [AlertMedia](https://www.alertmedia.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w AlertMedia
> * Usuń użytkowników w AlertMedia, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i AlertMedia
> * Udostępnianie grup i członkostwa w grupach w AlertMedia
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) jednokrotne do AlertMedia (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa AlertMedia](https://dashboard.alertmedia.com/#/login).
* Konto użytkownika w AlertMedia z uprawnieniami administratora do konfigurowania integracji interfejsu API.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i AlertMedia](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie AlertMedia w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do konta AlertMedia. Przejdź do **firmowej > interfejsu API**.
2. Kliknij przycisk **Dodaj nowy**.
3. Wybierz, aby nawiązać **integrację z interfejsem API** w celu ułatwienia rozpoznawania, gdzie są używane klucze.
4. Wybierz administratora, z którym chcesz skojarzyć integrację.
5. Kliknij przycisk **Generuj klucze** i **Zapisz** .
6. Skopiuj i Zapisz **token klienta** z integracji. Jest on używany jako **token tajny** na karcie aprowizacji aplikacji AlertMedia w Azure Portal.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie AlertMedia z galerii aplikacji usługi Azure AD

Dodaj AlertMedia z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do AlertMedia. Jeśli wcześniej skonfigurowano AlertMedia na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do AlertMedia należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do AlertMedia 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla AlertMedia w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **AlertMedia**.

    ![Link AlertMedia na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** AlertMedia jako jedną z poniższych opcji.
      * (brak domeny niestandardowej) https://dashboard.alertmedia.com/api/scim/v3

      * (domena niestandardowa) https://subdomain.alertmedia.com/api/scim/v3

      Wprowadź **token tajny** jako pobrany wcześniej w kroku 2. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą AlertMedia. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi AlertMedia ma uprawnienia administratora, a następnie spróbuj ponownie.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do AlertMedia**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do AlertMedia w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie AlertMedia for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API AlertMedia obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: first_name|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: last_name|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: email|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: email2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: Email3|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: title|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone_post_dial|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone2_post_dial|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone3|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: mobile_phone3_post_dial|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: home_phone|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: home_phone_post_dial|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: office_phone|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: office_phone_post_dial|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: Address|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: Address2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: miasto|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: State|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: Country|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: kod pocztowy|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: uwagi|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: customer_user_id|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: alertmedia: 2.0: CustomAttribute: User: user_type|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do AlertMedia**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do AlertMedia w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w AlertMedia dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla AlertMedia, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić AlertMediaAlertMedia, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
