---
title: 'Samouczek: aprowizowanie użytkowników na potrzeby usługi Slack — Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory, aby automatycznie aprowizować konta użytkowników lub cofać ich aprowizację w usłudze Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181462"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi Slack pod kątem automatycznej aprowizacji użytkowników

Celem tego samouczka jest zaprezentowanie czynności, które należy wykonać w usługach Slack i Azure AD, aby automatycznie aprowizować konta użytkowników i cofać ich aprowizację w usłudze Slack z poziomu usługi Azure AD. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze Slack
> * Usuwanie użytkowników z usługi Slack, gdy już nie potrzebują dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługami Azure AD i Slack
> * Aprowizowanie grup i członkostwa w grupach w usłudze Slack
> * [Logowanie jednokrotne](./slack-tutorial.md) w usłudze Slack (zalecane)


## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* Dzierżawę usługi Slack z włączonym planem [Plus](https://aadsyncfabric.slack.com/pricing) lub wyższym.
* Konto użytkownika w usłudze Slack z uprawnieniami Team Admin (administratora zespołu).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, jakie dane mają być [mapowane między usługami Azure AD i Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Krok 2. Dodanie aplikacji Slack z galerii aplikacji usługi Azure AD

Aby rozpocząć zarządzanie aprowizacją w usłudze Slack, dodaj aplikację Slack z galerii aplikacji usługi Azure AD. Jeśli wcześniej skonfigurowano logowanie jednokrotne do usługi Slack, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Krok 3. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do usługi Slack musisz wybrać rolę inną niż **dostęp domyślny**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Krok 4. Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Slack 

Ta sekcja zawiera instrukcje łączenia usługi Azure AD z interfejsem API aprowizacji kont użytkowników usługi Slack oraz konfigurowania usługi aprowizacji pod kątem tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze Slack na podstawie przypisań użytkowników i grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizację kont użytkowników usługi Slack w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Slack**.

    ![Link do usługi Slack na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. To spowoduje otwarcie okna dialogowego autoryzacji usługi Slack w nowym oknie przeglądarki.

    ![Zrzut ekranu przedstawiający przycisk Autoryzuj poświadczenia administratora.](media/slack-provisioning-tutorial/authorization.png)


6. W tym nowym oknie zaloguj się do usługi Slack przy użyciu konta administratora zespołu. W wyświetlonym oknie dialogowym autoryzacji wybierz zespół usługi Slack, dla którego chcesz włączyć aprowizację, a następnie wybierz pozycję **Authorize** (Autoryzuj). Po wykonaniu tych czynności wróć do witryny Azure Portal, aby dokończyć konfigurowanie aprowizacji.

    ![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/slackauthorize.png)

7. W witrynie Azure Portal kliknij polecenie **Testuj połączenie**, aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Slack. Jeśli połączenie nie powiedzie się, sprawdź, czy użyte konto usługi Slack ma uprawnienia Team Admin i ponownie spróbuj wykonać krok dotyczący autoryzacji.

8. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą Slack**.

11. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkowników, które będą synchronizowane z usługi Azure AD do usługi Slack. Zwróć uwagę, że atrybuty wybrane jako właściwości **dopasowywania** będą używane do dopasowywania kont użytkowników w usłudze Slack na potrzeby operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Wartość logiczna|
   |externalId|Ciąg|
   |displayName|Ciąg|
   |name.familyName|Ciąg|
   |name.givenName|Ciąg|
   |title|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |userName|Ciąg|
   |nickName|Ciąg|
   |addresses[type eq "untyped"].streetAddress|Ciąg|
   |addresses[type eq "untyped"].locality|Ciąg|
   |addresses[type eq "untyped"].region|Ciąg|
   |addresses[type eq "untyped"].postalCode|Ciąg|
   |addresses[type eq "untyped"].country|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |roles[primary eq "True"].value|Ciąg|
   |locale|Ciąg|
   |name.honorificPrefix|Ciąg|
   |photos[type eq "photo"].value|Ciąg|
   |profileUrl|Ciąg|
   |timezone|Ciąg|
   |userType|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.department|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Tematy pomocy|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.division|Ciąg|

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą Slack**.

13. W sekcji **Mapowania atrybutów** przejrzyj atrybuty grup, które będą synchronizowane z usługi Azure AD do usługi Slack. Zwróć uwagę, że atrybuty wybrane jako właściwości **dopasowywania** będą używane do dopasowywania grup w usłudze Slack na potrzeby operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |elementy członkowskie|Tematy pomocy|

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę aprowizacji Azure AD dla usługi Slack, zmień **Stan aprowizacji** na **Wł.** w sekcji **Ustawienia**

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy do aprowizacji w usłudze Slack, wybierając odpowiednie wartości w obszarze **Zakres** w sekcji **Ustawienia**.

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-5-monitor-your-deployment"></a>Krok 5. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Podczas konfigurowania atrybutu **displayName** usługi Slack pamiętaj o następujących zagadnieniach związanych z jego działaniem:

  * Wartości nie są całkowicie unikatowe (na przykład dwóch użytkowników może mieć taką samą nazwę wyświetlaną)

  * Obsługiwane są znaki spoza alfabetu angielskiego, spacje i wielkie litery. 
  
  * Dozwolone znaki interpunkcyjne to kropki, podkreślenia, łączniki, apostrofy, nawiasy (na przykład **( [ { } ] )** ) oraz separatory (na przykład **, / ;** ).
  
  * Właściwość displayName nie może zawierać znaku „@”. Jeśli zawiera znak „@”, w dziennikach aprowizacji może pojawić się pominięte zdarzenie z opisem „AttributeValidationFailed” (Niepowodzenie walidacji atrybutu).

  * Wartość zostanie zaktualizowana tylko w przypadku skonfigurowania następujących dwóch ustawień w organizacji/miejscu pracy w usłudze Slack — **Profile syncing is enabled** (Włączono synchronizację profili) oraz **Users cannot change their display name** (Użytkownicy nie mogą zmienić swojej nazwy wyświetlanej).

* Atrybut **userName** usługi Slack musi być unikatowy i nie może mieć więcej niż 21 znaków.

* Usługa Slack umożliwia dopasowanie tylko do atrybutów **userName** i **email**.  
  
* Typowe kody błędów można znaleźć w oficjalnej dokumentacji usługi Slack — https://api.slack.com/scim#errors

## <a name="change-log"></a>Dziennik zmian

* 2020-06-16 — zmodyfikowano atrybut DisplayName, tak aby był aktualizowany tylko podczas tworzenia nowego użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)