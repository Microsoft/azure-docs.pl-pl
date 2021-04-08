---
title: 'Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w usłudze G Suite.
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
ms.openlocfilehash: f6ef2f91d178db01e618c19df55705bc9c1209e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583755"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w systemie G Suite i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w usłudze [G Suite](https://gsuite.google.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze G Suite
> * Usuń użytkowników w usłudze G Suite, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a programem G Suite
> * Udostępnianie grup i członkostw w grupach w usłudze G Suite
> * [Logowanie](./google-apps-tutorial.md) jednokrotne do pakietu G Suite (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* [Dzierżawa G Suite](https://gsuite.google.com/pricing.html)
* Konto użytkownika w pakiecie G Suite z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługami Azure AD i G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi G Suite do obsługi aprowizacji w usłudze Azure AD

Przed skonfigurowaniem pakietu G dla automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć Inicjowanie obsługi Standard scim w usłudze G Suite.

1. Zaloguj się do [konsoli administracyjnej G Suite](https://admin.google.com/) przy użyciu konta administratora, a następnie wybierz pozycję **zabezpieczenia**. Jeśli łącze nie jest widoczne, może być ukryte w menu **Więcej kontrolek** w dolnej części ekranu.

    ![Zabezpieczenia w usłudze G Suite](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Na stronie **zabezpieczenia** wybierz pozycję **Dokumentacja interfejsu API**.

    ![Interfejs API usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Wybierz pozycję **Włącz dostęp do interfejsu API**.

    ![Włączono interfejs API usługi G Suite](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Dla każdego użytkownika, który zamierzasz udostępnić do usługi G Suite, jego nazwa użytkownika w usłudze Azure AD **musi** być powiązana z domeną niestandardową. Na przykład nazwy użytkowników, które wyglądają jak bob@contoso.onmicrosoft.com nie są akceptowane przez pakiet G Suite. Z drugiej strony bob@contoso.com zostanie zaakceptowany. Istniejącą domenę użytkownika można zmienić, postępując zgodnie z instrukcjami znajdującymi się [tutaj](../fundamentals/add-custom-domain.md).

4. Po dodaniu i zweryfikowaniu żądanych domen niestandardowych za pomocą usługi Azure AD należy zweryfikować je ponownie z pakietem G Suite. Aby sprawdzić domeny w usłudze G Suite, zapoznaj się z następującymi krokami:

    a. W [konsoli administracyjnej programu G Suite](https://admin.google.com/)wybierz pozycję **domeny**.

    ![Domeny pakietu G Suite](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Wybierz pozycję **Dodaj domenę lub alias domeny**.

    ![Dodaj domenę zestawu G Suite](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Wybierz pozycję **Dodaj inną domenę**, a następnie wpisz nazwę domeny, którą chcesz dodać.

    ![G Suite Dodaj kolejną](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Wybierz pozycję **Kontynuuj i sprawdź własność domeny**. Następnie postępuj zgodnie z instrukcjami, aby sprawdzić, czy jesteś właocicielem nazwy domeny. Aby uzyskać wyczerpujące instrukcje dotyczące weryfikowania domeny za pomocą usługi Google, zobacz [Sprawdzanie własności lokacji](https://support.google.com/webmasters/answer/35179).

    e. Powtórz powyższe kroki dla wszystkich dodatkowych domen, które mają zostać dodane do usługi G Suite.

5. Następnie określ konto administratora, które ma być używane do zarządzania aprowizacji użytkowników w usłudze G Suite. Przejdź do **ról administratora**.

    ![Administrator pakietu G Suite](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. W przypadku **roli administratora** tego konta Edytuj **uprawnienia** dla tej roli. Upewnij się, że wszystkie **uprawnienia administratora interfejsu API** są włączone, aby można było używać tego konta do obsługi administracyjnej.

    ![Uprawnienia administratora w usłudze G Suite](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj pakiet G Suite z galerii aplikacji usługi Azure AD

Dodaj pakiet G Suite z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do usługi G Suite. Jeśli wcześniej skonfigurowano usługę G Suite dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do zestawu G Suite należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w usłudze G Suite 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym interfejsu API katalogu usługi G, zapoznaj się z [interfejsem API katalogu](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla pakietu G Suite w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**. Użytkownicy będą musieli zalogować się do portal.azure.com i nie będą mogli używać aad.portal.azure.com

    ![Blok Aplikacje dla przedsiębiorstw](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Blok Wszystkie aplikacje](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Na liście Aplikacje wybierz pozycję **G Suite**.

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** . Kliknij pozycję **Rozpocznij**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

      ![Wprowadzenie — blok](./media/g-suite-provisioning-tutorial/get-started.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do okna dialogowego autoryzacji Google w nowym oknie przeglądarki.

      ![G Suite — Autoryzuj](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Potwierdź, że chcesz nadać uprawnienia usługi Azure AD, aby wprowadzić zmiany w dzierżawie w usłudze G Suite. Wybierz pozycję **Zaakceptuj**.

     ![Uwierzytelnianie dzierżawy w usłudze G Suite](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z pakietem G Suite. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi G Suite ma uprawnienia administratora, a następnie spróbuj ponownie. Następnie spróbuj ponownie wykonać krok **Autoryzuj** .

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory użytkownikom**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze G Suite dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API usługi G Suite obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |primaryEmail|Ciąg|
   |relacje. [Type EQ "Manager"]. Value|Ciąg|
   |name.familyName|Ciąg|
   |name.givenName|Ciąg|
   |wieszon|Ciąg|
   |externalIds. [Type EQ "Custom"]. Value|Ciąg|
   |externalIds. [typ EQ "organizacja"]. wartość|Ciąg|
   |adres. [Type EQ "Work"]. Country|Ciąg|
   |adres. [wpisz EQ "Work"]. streetAddress|Ciąg|
   |adres. [Type EQ "Work"]. region|Ciąg|
   |adres. [typ EQ "Work"]. locale|Ciąg|
   |adres. [Type EQ "Work"]. KodPocztowy|Ciąg|
   |Zamów. [Type EQ "Work"]. Address|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. Department|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. title|Ciąg|
   |numer telefonu. [Type EQ "Work"]. Value|Ciąg|
   |numer telefonu. [Type EQ "Mobile"]. Value|Ciąg|
   |numer telefonu. [Type EQ "work_fax"]. Value|Ciąg|
   |Zamów. [Type EQ "Work"]. Address|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. Department|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. title|Ciąg|
   |numer telefonu. [Type EQ "Work"]. Value|Ciąg|
   |numer telefonu. [Type EQ "Mobile"]. Value|Ciąg|
   |numer telefonu. [Type EQ "work_fax"]. Value|Ciąg|
   |adres. [Type EQ "Home"]. Country|Ciąg|
   |adres. [Type EQ "Home"]. sformatowana|Ciąg|
   |adres. [Type EQ "Home"]. locale|Ciąg|
   |adres. [Type EQ "Home"]. KodPocztowy|Ciąg|
   |adres. [Type EQ "Home"]. region|Ciąg|
   |adres. [Type EQ "Home"]. streetAddress|Ciąg|
   |adres. [Type EQ "Other"]. Country|Ciąg|
   |adres. [Type EQ "Other"]. sformatowane|Ciąg|
   |adres. [Type EQ "Other"]. locale|Ciąg|
   |adres. [Type EQ "Other"]. KodPocztowy|Ciąg|
   |adres. [Type EQ "Other"]. region|Ciąg|
   |adres. [Type EQ "Other"]. streetAddress|Ciąg|
   |adres. [wpisz EQ "Work"]. sformatowane|Ciąg|
   |changePasswordAtNextLogin|Ciąg|
   |Zamów. [Type EQ "Home"]. Address|Ciąg|
   |Zamów. [Type EQ "Other"]. Address|Ciąg|
   |externalIds. [typ EQ "konto"]. wartość|Ciąg|
   |externalIds. [Type EQ "Custom"]. CustomType|Ciąg|
   |externalIds. [typ EQ "klient"]. wartość|Ciąg|
   |externalIds. [Type EQ "login_id"]. Value|Ciąg|
   |externalIds. [typ EQ "Sieć"]. wartość|Ciąg|
   |płeć. typ|Ciąg|
   |GeneratedImmutableId|Ciąg|
   |Identyfikator|Ciąg|
   |ISP. [Type EQ "Home"]. Protocol|Ciąg|
   |ISP. [Type EQ "Other"]. protokół|Ciąg|
   |ISP. [Type EQ "Work"]. protokół|Ciąg|
   |includeInGlobalAddressList|Ciąg|
   |ipWhitelisted|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. costCenter|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Dział|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. domena|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. fullTimeEquivalent|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Lokalizacja|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Name|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. symbol|Ciąg|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. title|Ciąg|
   |przedsiębiorstwa. [wpisz EQ "Work"]. costCenter|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. domena|Ciąg|
   |przedsiębiorstwa. [wpisz EQ "Work"]. fullTimeEquivalent|Ciąg|
   |przedsiębiorstwa. [typ EQ "Work"]. Lokalizacja|Ciąg|
   |przedsiębiorstwa. [wpisz EQ "Work"]. Name|Ciąg|
   |przedsiębiorstwa. [Type EQ "Work"]. symbol|Ciąg|
   |OrgUnitPath|Ciąg|
   |numer telefonu. [Type EQ "Home"]. Value|Ciąg|
   |numer telefonu. [Type EQ "Other"]. Value|Ciąg|
   |zaufany. [Type EQ "Home"]. Value|Ciąg|
   |zaufany. [Type EQ "Other"]. Value|Ciąg|
   |zaufany. [Type EQ "Work"]. Value|Ciąg|
   

10. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory grupy**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usłudze G Suite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |poczta e-mail|Ciąg|
      |Elementy członkowskie|Ciąg|
      |name|Ciąg|
      |description (opis)|Ciąg|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usługi G Suite, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do zbioru G Suite, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

> [!NOTE]
> Jeśli użytkownicy mają już istniejące konto osobiste/konsumencki przy użyciu adresu e-mail użytkownika usługi Azure AD, może to spowodować pewne problemy, które można rozwiązać za pomocą narzędzia Google transfer przed przeprowadzeniem synchronizacji katalogów.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Dziennik zmian

* 10/17/2020 — dodano obsługę dodatkowych atrybutów użytkowników i grup usługi G Suite.
* 10/17/2020 — Zaktualizowano nazwy atrybutów docelowych G Suite, aby odpowiadały wartości zdefiniowanej w [tym miejscu](https://developers.google.com/admin-sdk/directory).
* 10/17/2020 — Zaktualizowano domyślne mapowania atrybutów.
* 03/18/2021 — wiadomość e-mail z menedżerem jest teraz synchronizowana zamiast identyfikatora dla wszystkich nowych użytkowników. W przypadku wszystkich istniejących użytkowników, którzy zostali zainicjowani przy użyciu Menedżera jako identyfikatora, można wykonać ponowne uruchomienie za pomocą [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) z zakresem "Full", aby upewnić się, że poczta e-mail została zainicjowana. Ta zmiana ma wpływ tylko na zadanie aprowizacji GSuite, a nie starsze zadanie probisioning, rozpoczynając od Goov2OutDelta. Pamiętaj, że wiadomość e-mail Menedżera jest inicjowana, gdy użytkownik jest najpierw tworzony lub gdy Menedżer zmieni się. Nie zainicjowano obsługi poczty e-mail menedżera, jeśli Menedżer zmieni swój adres e-mail. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
