---
title: 'Samouczek: konfigurowanie usługi G Suite do automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i coprowizować konta użytkowników z usługi Azure AD do usługi G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: b8513f62b6f181a1490d136062c5de81db847ba7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533394"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi G Suite do automatycznego aprowizowania użytkowników

W tym samouczku opisano kroki, które należy wykonać w usłudze G Suite i usłudze Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje użytkowników i grupy w usłudze [G Suite](https://gsuite.google.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> W tym samouczku opisano łącznik zbudowany na podstawie usługi Azure AD User Provisioning Service. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w pakiecie G Suite
> * Usuwanie użytkowników z pakietu G Suite, gdy nie wymagają już dostępu
> * Synchronizuj atrybuty użytkownika między usługą Azure AD i usługą G Suite
> * Aprowizowanie grup i członkosstwa w grupach w uakiecie G Suite
> * [Logowanie pojedyncze do pakietu](./google-apps-tutorial.md) G Suite (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* [Dzierżawa usługi G Suite](https://gsuite.google.com/pricing.html)
* Konto użytkownika w pakiecie G Suite z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, jakie dane mają [być mapowe między usługą Azure AD i usługą G Suite.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi G Suite do obsługi aprowizowania w usłudze Azure AD

Przed skonfigurowaniem usługi G Suite do automatycznego aprowizowania użytkowników w usłudze Azure AD należy włączyć aprowizowanie standardu SCIM w usłudze G Suite.

1. Zaloguj się do konsoli [administracyjnej usługi G Suite przy](https://admin.google.com/) użyciu konta administratora, a następnie wybierz pozycję **Zabezpieczenia.** Jeśli nie widzisz linku, może on być ukryty w menu **Więcej** kontrolek w dolnej części ekranu.

    ![Zabezpieczenia G Suite](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Na stronie **Zabezpieczenia** wybierz pozycję Dokumentacja **interfejsu API**.

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Wybierz pozycję **Włącz dostęp do interfejsu API.**

    ![Włączony interfejs API usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Dla każdego użytkownika, który zamierzasz aprowizować w usłudze G Suite, jego nazwa w usłudze Azure AD **musi** być powiązana z domeną niestandardową. Na przykład nazwy użytkowników, które wyglądają jak , bob@contoso.onmicrosoft.com nie są akceptowane przez usługę G Suite. Z drugiej strony jest bob@contoso.com akceptowana. Możesz zmienić domenę istniejącego użytkownika, korzystając z instrukcji podanych [tutaj.](../fundamentals/add-custom-domain.md)

4. Po dodaniu i zweryfikowaniu żądanych domen niestandardowych w usłudze Azure AD należy zweryfikować je ponownie w usłudze G Suite. Aby zweryfikować domeny w pakiecie G Suite, zapoznaj się z następującymi krokami:

    a. W konsoli [administracyjnej usługi G Suite](https://admin.google.com/)wybierz pozycję **Domeny.**

    ![Domeny usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Wybierz **pozycję Dodaj domenę lub alias domeny.**

    ![Dodawanie domeny usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Wybierz **pozycję Dodaj kolejną domenę,** a następnie wpisz nazwę domeny, którą chcesz dodać.

    ![Dodawanie kolejnego pakietu G Suite](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Wybierz **pozycję Kontynuuj i sprawdź własność domeny.** Następnie wykonaj kroki, aby sprawdzić, czy jesteś właścicielem nazwy domeny. Aby uzyskać kompleksowe instrukcje dotyczące weryfikowania domeny za pomocą usługi Google, zobacz [Verify your site ownership (Weryfikowanie własności witryny).](https://support.google.com/webmasters/answer/35179)

    e. Powtórz powyższe kroki dla wszystkich dodatkowych domen, które zamierzasz dodać do usługi G Suite.

5. Następnie określ konto administratora, którego chcesz użyć do zarządzania aprowizowania użytkowników w uakiecie G Suite. Przejdź do **pozycji Role administratora.**

    ![Administrator usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. W przypadku **roli administratora** tego konta edytuj **uprawnienia** dla tej roli. Upewnij się, że wszystkie **uprawnienia interfejsu API administratora są** włączane, aby można było używać tego konta do aprowowania.

    ![Uprawnienia administratora usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usługi G Suite z galerii aplikacji usługi Azure AD

Dodaj usługę G Suite z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizowaniami w usłudze G Suite. Jeśli wcześniej s konfigurowano usługę G Suite na użytek logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do pakietu G Suite musisz wybrać rolę inną niż **Domyślny dostęp.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników w uakiecie G Suite 

Ta sekcja zawiera instrukcje konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej na temat punktu końcowego interfejsu API katalogów usługi G Suite, zapoznaj się z [tematem Directory API (Interfejs API katalogu).](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla usługi G Suite w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**. Użytkownicy będą musieli zalogować się do portal.azure.com i nie będą mogli korzystać z aad.portal.azure.com

    ![Blok Aplikacje dla przedsiębiorstw](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Blok Wszystkie aplikacje](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Na liście aplikacji wybierz pozycję **G Suite.**

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

3. Wybierz **kartę Aprowizowanie.** Kliknij pozycję **Get started (Wprowadzenie).**

    ![Zrzut ekranu przedstawiający opcje zarządzania z wywołaną opcją Aprowizowanie.](common/provisioning.png)

      ![Blok Wprowadzenie](./media/g-suite-provisioning-tutorial/get-started.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną Tryb aprowizowania z wywołaną opcją Automatyczne.](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do okna dialogowego autoryzacji Google w nowym oknie przeglądarki.

      ![Autoryzacja w pakiecie G Suite](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Potwierdź, że chcesz nadać usłudze Azure AD uprawnienia do zmiany dzierżawy usługi G Suite. Wybierz pozycję **Zaakceptuj**.

     ![Uwierzytelnianie dzierżawy usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. W witrynie Azure Portal pozycję **Testuj** połączenie, aby upewnić się, że usługa Azure AD może połączyć się z usługą G Suite. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto usługi G Suite ma uprawnienia administratora, i spróbuj ponownie. Następnie spróbuj ponownie **wykonać krok Autoryzuj.**

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania wybierz** pozycję Aprowizuj Azure Active Directory **użytkowników.**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi G Suite w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako Właściwości **dopasowywania** są używane do dopasowywania kont użytkowników w uakiecie G Suite w przypadku operacji aktualizacji. Jeśli zdecydujesz się zmienić pasujący [atrybut docelowy,](../app-provisioning/customize-application-attributes.md)upewnij się, że interfejs API usługi G Suite obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|
   |---|---|
   |primaryEmail|Ciąg|
   |Stosunków. [type eq "manager"].value|Ciąg|
   |name.familyName|Ciąg|
   |name.givenName|Ciąg|
   |Zawieszone|Ciąg|
   |externalIds. [type eq "custom"].value|Ciąg|
   |externalIds. [type eq "organization"].value|Ciąg|
   |Adresy. [type eq "work"].country|Ciąg|
   |Adresy. [type eq "work"].streetAddress|Ciąg|
   |Adresy. [type eq "work"].region|Ciąg|
   |Adresy. [type eq "work"].locality|Ciąg|
   |Adresy. [type eq "work"].postalCode|Ciąg|
   |Wiadomości e-mail. [type eq "work"].address|Ciąg|
   |Organizacji. [type eq "work"].department|Ciąg|
   |Organizacji. [type eq "work"].title|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "work"].value|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "mobile"].value|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "work_fax"].value|Ciąg|
   |Wiadomości e-mail. [type eq "work"].address|Ciąg|
   |Organizacji. [type eq "work"].department|Ciąg|
   |Organizacji. [type eq "work"].title|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "work"].value|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "mobile"].value|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "work_fax"].value|Ciąg|
   |Adresy. [type eq "home"].country|Ciąg|
   |Adresy. [type eq "home"].formatted|Ciąg|
   |Adresy. [type eq "home"].locality|Ciąg|
   |Adresy. [type eq "home"].postalCode|Ciąg|
   |Adresy. [type eq "home"].region|Ciąg|
   |Adresy. [type eq "home"].streetAddress|Ciąg|
   |Adresy. [type eq "other"].country|Ciąg|
   |Adresy. [type eq "other"].formatted|Ciąg|
   |Adresy. [type eq "other"].locality|Ciąg|
   |Adresy. [type eq "other"].postalCode|Ciąg|
   |Adresy. [type eq "other"].region|Ciąg|
   |Adresy. [type eq "other"].streetAddress|Ciąg|
   |Adresy. [type eq "work"].formatted|Ciąg|
   |changePasswordAtNextLogin|Ciąg|
   |Wiadomości e-mail. [type eq "home"].address|Ciąg|
   |Wiadomości e-mail. [type eq "other"].address|Ciąg|
   |externalIds. [type eq "account"].value|Ciąg|
   |externalIds. [type eq "custom"].customType|Ciąg|
   |externalIds. [type eq "customer"].value|Ciąg|
   |externalIds. [type eq "login_id"].value|Ciąg|
   |externalIds. [type eq "network"].value|Ciąg|
   |gender.type|Ciąg|
   |GeneratedImmutableId|Ciąg|
   |Identyfikator|Ciąg|
   |Ims. [type eq "home"].protocol|Ciąg|
   |Ims. [type eq "other"].protocol|Ciąg|
   |Ims. [type eq "work"].protocol|Ciąg|
   |includeInGlobalAddressList|Ciąg|
   |ipWhitelisted|Ciąg|
   |Organizacji. [type eq "school"].costCenter|Ciąg|
   |Organizacji. [type eq "school"].department|Ciąg|
   |Organizacji. [type eq "school"].domain|Ciąg|
   |Organizacji. [type eq "school"].fullTimeEquivalent|Ciąg|
   |Organizacji. [type eq "school"].location|Ciąg|
   |Organizacji. [type eq "school"].name|Ciąg|
   |Organizacji. [type eq "school"].symbol|Ciąg|
   |Organizacji. [type eq "school"].title|Ciąg|
   |Organizacji. [type eq "work"].costCenter|Ciąg|
   |Organizacji. [type eq "work"].domain|Ciąg|
   |Organizacji. [type eq "work"].fullTimeEquivalent|Ciąg|
   |Organizacji. [type eq "work"].location|Ciąg|
   |Organizacji. [type eq "work"].name|Ciąg|
   |Organizacji. [type eq "work"].symbol|Ciąg|
   |OrgUnitPath|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "home"].value|Ciąg|
   |phoneNumbers (Numery telefonów). [type eq "other"].value|Ciąg|
   |Stron internetowych. [type eq "home"].value|Ciąg|
   |Stron internetowych. [type eq "other"].value|Ciąg|
   |Stron internetowych. [type eq "work"].value|Ciąg|
   

10. W sekcji **Mapowania wybierz** pozycję **Aprowizuj Azure Active Directory grupy.**

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi G Suite w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako Właściwości **dopasowywania** są używane do dopasowywania grup w pakiecie G Suite dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

      |Atrybut|Typ|
      |---|---|
      |poczta e-mail|Ciąg|
      |Elementy członkowskie|Ciąg|
      |name|Ciąg|
      |description (opis)|Ciąg|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę aprowizowania Azure AD dla usługi G Suite, zmień stan **aprowowania** na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować  w uakiecie G Suite, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

> [!NOTE]
> Jeśli użytkownicy mają już istniejące konto osobiste/konto użytkownika korzystające z adresu e-mail użytkownika usługi Azure AD, może to spowodować problem, który można rozwiązać za pomocą narzędzia Google Transfer Przed wykonaniem synchronizacji katalogów.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Dziennik zmian

* 10/17/2020 — dodano obsługę dodatkowych atrybutów użytkowników i grup usługi G Suite.
* 10/17/2020 — zaktualizowano nazwy atrybutów docelowych pakietu G Suite, aby dopasować je do zdefiniowanych [w tym miejscu.](https://developers.google.com/admin-sdk/directory)
* 2020-10-17 — zaktualizowane mapowania atrybutów domyślnych.
* 2021-03-18 — wiadomość e-mail menedżera jest teraz synchronizowana zamiast identyfikatora dla wszystkich nowych użytkowników. W przypadku wszystkich istniejących użytkowników, którzy zostali aprowizowany przy użyciu menedżera jako identyfikatora, można wykonać ponowne uruchomienie za pośrednictwem usługi Microsoft Graph z zakresem "pełnym", aby upewnić się, że wiadomość [e-mail](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http&preserve-view=true) jest aprowizowana. Ta zmiana ma wpływ tylko na zadanie aprowizowania GSuite, a nie na starsze zadanie aprowowania, począwszy od goov2OutDelta. Pamiętaj, że wiadomość e-mail menedżera jest aprowizowana przy pierwszym utworzeniu użytkownika lub zmianie menedżera. Wiadomość e-mail menedżera nie jest aprowizowana, jeśli menedżer zmieni swój adres e-mail. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
