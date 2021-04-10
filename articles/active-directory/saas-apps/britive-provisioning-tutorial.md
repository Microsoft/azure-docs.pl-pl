---
title: 'Samouczek: Konfigurowanie Britive dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225769"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Britive na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie Britive i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [Britive](https://www.britive.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Britive
> * Usuń użytkowników w Britive, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Britive
> * Udostępnianie grup i członkostwa w grupach w Britive
> * [Logowanie](britive-tutorial.md) jednokrotne do Britive (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Dzierżawa [Britive](https://www.britive.com/) .
* Konto użytkownika w Britive z uprawnieniami administratora.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Określ, które dane mają być [mapowane między usługą Azure AD i Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie Britive w celu obsługi aprowizacji za pomocą usługi Azure AD

Aplikację należy skonfigurować ręcznie, wykonując czynności opisane poniżej:
1. Zaloguj się do aplikacji Britive przy użyciu uprawnień administratora
1. Kliknij pozycję **administrator >Administracja użytkownikami — >dostawcy tożsamości**
1. Kliknij pozycję **Dodaj dostawcę tożsamości**. Wprowadź nazwę i opis. Kliknij przycisk Dodaj dostawcę tożsamości.

    ![Dostawca tożsamości](media/britive-provisioning-tutorial/identity.png)

1. Zostanie wyświetlona strona konfiguracji podobna do poniższej wyświetlonej poniżej.

    ![Strona konfiguracji](media/britive-provisioning-tutorial/configuration.png)

1. Kliknij kartę **Standard scim** . Zmień dostawcę Standard scim z ogólnego na platformę Azure i Zapisz zmiany. Skopiuj adres URL Standard scim i zanotuj go. Te wartości zostaną wprowadzone w polach **adres URL dzierżawy** na karcie aprowizacji aplikacji Britive w Azure Portal.

    ![Strona Standard scim](media/britive-provisioning-tutorial/scim.png)

1. Kliknij pozycję **Utwórz token**. W razie potrzeby wybierz ważność tokenu, a następnie kliknij przycisk Utwórz token.

    ![Utwórz token](media/britive-provisioning-tutorial/create-token.png)

1. Skopiuj wygenerowany token i zanotuj go. Kliknij przycisk OK. Należy pamiętać, że użytkownik nie będzie mógł ponownie zobaczyć tokenu. Kliknij przycisk Re-Create, aby wygenerować nowy token w razie potrzeby. Te wartości zostaną wprowadzone w polach **token tajny** i adres URL dzierżawy na karcie aprowizacji aplikacji getAbstract w Azure Portal.

    ![Kopiuj token](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Britive z galerii aplikacji usługi Azure AD

Dodaj Britive z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Britive. Jeśli wcześniej skonfigurowano Britive do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do Britive należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do Britive 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Britive na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Britive w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **Britive**.

    ![Link Britive na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

1. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy Britive i token tajny. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Britive. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Britive ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Britive**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Britive w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Britive for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API Britive obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |title|Ciąg|
   |externalId|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |nickName|Ciąg|
   |userType|Ciąg|
   |locale|Ciąg|
   |timezone|Ciąg|
   |wiadomości e-mail [Type EQ "Home"]. Value|Ciąg|
   |wiadomości e-mail [Type EQ "Other"]. Value|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |numer telefonu [typ EQ "Home"]. Value|Ciąg|
   |braky [Type EQ "Other"]. Value|Ciąg|
   |numer telefonu [typ EQ "pager"]. wartość|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|Ciąg|
   |adresy [typ EQ "Work"]. sformatowane|Ciąg|
   |adresy [typ EQ "Work"]. streetAddress|Ciąg|
   |adresy [typ EQ "Work"]. locale|Ciąg|
   |addresss [Type EQ "Work"]. region|Ciąg|
   |addresss [Type EQ "Work"]. KodPocztowy|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |adresy [Type EQ "Home"]. sformatowane|Ciąg|
   |adresy [Type EQ "Home"]. streetAddress|Ciąg|
   |adresy [Type EQ "Home"]. locale|Ciąg|
   |adresy [Type EQ "Home"]. region|Ciąg|
   |adresy [Type EQ "Home"]. KodPocztowy|Ciąg|
   |adresy [Type EQ "Home"]. Country|Ciąg|
   |adresy [Type EQ "Other"]. sformatowane|Ciąg|
   |adresy [Type EQ "Other"]. streetAddress|Ciąg|
   |adresy [Type EQ "Other"]. locale|Ciąg|
   |addresss [Type EQ "Other"]. region|Ciąg|
   |adresy [Type EQ "Other"]. KodPocztowy|Ciąg|
   |adresy [Type EQ "Other"]. Country|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Odwołanie|


1. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Britive**.

1. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do Britive w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Britive dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |displayName|Ciąg|&check;
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

1. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla Britive, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Britive, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

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