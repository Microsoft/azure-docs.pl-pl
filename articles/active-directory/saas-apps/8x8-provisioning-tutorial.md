---
title: 'Samouczek: Konfigurowanie 8x8 dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do 8x8.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 25767a7ddb83acf3ad10698f9f2ff8949a0ad55f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357202"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie 8x8 na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach obu 8x8 Configuration Manager i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [8x8](https://www.8x8.com) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w 8x8
> * Usuń użytkowników w 8x8, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i 8x8
> * [Logowanie](./8x8virtualoffice-tutorial.md) jednokrotne do 8x8 (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* Subskrypcja 8x8 serii X na dowolnym poziomie.
* Konto użytkownika 8x8 z uprawnieniami administratora w [Configuration Manager](https://vo-cm.8x8.com).
* [Jeden Sign-On z usługą Azure AD](./8x8virtualoffice-tutorial.md) został już skonfigurowany.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i 8x8](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie 8x8 w celu obsługi aprowizacji za pomocą usługi Azure AD

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi 8x8 w celu obsługi aprowizacji w usłudze Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Aby skonfigurować token dostępu użytkownika do aprowizacji w programie 8x8 Configuration Manager:

1. Zaloguj się do [Configuration Manager](https://vo-cm.8x8.com). Wybierz pozycję **Zarządzanie tożsamościami**.

   ![Zarządzanie tożsamością](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Kliknij link **Pokaż informacje o aprowizacji użytkowników** , aby wygenerować token.

   ![Pokaż Inicjowanie obsługi użytkowników](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Skopiuj **adresy URL 8x8** i **token API 8x8** . Te wartości zostaną wprowadzone w polach **adres URL dzierżawy** i **klucz tajny tokenu** odpowiednio na karcie aprowizacji aplikacji 8x8 w Azure Portal.

   ![Kopiuj adres URL i token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie 8x8 z galerii aplikacji usługi Azure AD

Dodaj 8x8 z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do 8x8. Jeśli wcześniej skonfigurowano 8x8 do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jest to prostsze rozwiązanie i jest używane przez większość osób.

Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do 8x8 należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do 8x8 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie 8x8 na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla 8x8 w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Blok Wszystkie aplikacje](./media/8x8-provisioning-tutorial/all-applications.png)

2. Na liście Aplikacje wybierz pozycję **8x8**.

    ![Link 8x8 na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** . Kliknij pozycję **Rozpocznij**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

   ![Wprowadzenie — blok](./media/8x8-provisioning-tutorial/get-started.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** Skopiuj **adres url 8x8** z Configuration Manager na **adres URL dzierżawy**. Skopiuj **token interfejsu API 8x8** z Configuration Manager do **tokenu tajnego**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą 8x8. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi 8x8 ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawia okno dialogowe poświadczenia administratora, w którym można wprowadzić dzierżawę U R L i wpis tajny.](./media/8x8-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory użytkownikom**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do 8x8 w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie 8x8 for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API 8x8 obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Uwagi|
   |---|---|---|
   |userName|Ciąg|Ustawia nazwę użytkownika i identyfikator Federacji|
   |externalId|Ciąg||
   |aktywne|Wartość logiczna||
   |title|Ciąg||
   |emails[type eq "work"].value|Ciąg||
   |name.givenName|Ciąg||
   |name.familyName|Ciąg||
   |phoneNumbers[type eq "mobile"].value|Ciąg|Osobisty numer osoby kontaktowej|
   |phoneNumbers[type eq "work"].value|Ciąg|Osobisty numer osoby kontaktowej|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg||
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: 8x8:1.1: użytkownik: Witryna|Ciąg|Nie można zaktualizować po utworzeniu użytkownika|
   |locale|Ciąg|Domyślnie Niemapowane|
   |timezone|Ciąg|Domyślnie Niemapowane|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla 8x8, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić 8x8, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)