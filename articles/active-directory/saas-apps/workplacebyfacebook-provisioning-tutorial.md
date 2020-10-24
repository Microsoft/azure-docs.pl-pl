---
title: 'Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Zapoznaj się z krokami, które należy wykonać w obu miejscach pracy przez serwis Facebook i Azure Active Directory (Azure AD), aby skonfigurować automatyczne Inicjowanie obsługi użytkowników.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: 6415ead09c98d85191440fc8d8fd5900ad44b85e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520115"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w obu miejscach pracy przez serwis Facebook i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w [miejscu pracy za](https://work.workplace.com/) pomocą usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w miejscu pracy przez serwis Facebook
> * Usuwanie użytkowników w miejscu pracy przez serwis Facebook, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkownika między usługą Azure AD i miejscem pracy przez serwis Facebook
> * [Logowanie](./workplacebyfacebook-tutorial.md) jednokrotne do miejsca pracy przez serwis Facebook (zalecane)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego)
* Miejsce pracy w ramach subskrypcji z włączoną obsługą logowania jednokrotnego w serwisie Facebook

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska w wersji próbnej usługi Azure AD, możesz w [tym miejscu](https://azure.microsoft.com/pricing/free-trial/)uzyskać miesięczną wersję próbną.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ dane, które mają być [mapowane między usługą Azure AD i miejscem pracy przez serwis Facebook](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie miejsca pracy przez serwis Facebook w celu obsługi aprowizacji za pomocą usługi Azure AD

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do miejsca pracy przez aplikację w serwisie Facebook. Po ustaleniu tych użytkowników możesz przypisać je do miejsca pracy przez aplikację Facebook, postępując zgodnie z poniższymi instrukcjami:

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do miejsca pracy przez serwis Facebook w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do miejsca pracy przez serwis Facebook należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie miejsca pracy przez serwis Facebook z galerii aplikacji usługi Azure AD

Dodaj miejsce pracy przez serwis Facebook z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą w miejscu pracy przez serwis Facebook. Jeśli wcześniej skonfigurowano miejsce pracy w usłudze Facebook dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do miejsca pracy przez serwis Facebook należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **miejsce pracy w serwisie Facebook**.

    ![Link aplikacji Workplace by Facebook na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do miejsca pracy przy użyciu strony autoryzacji w serwisie Facebook. Wprowadź miejsce pracy według nazwy użytkownika w usłudze Facebook i kliknij przycisk **Kontynuuj** . Kliknij pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może połączyć się z miejscem pracy przez serwis Facebook. Jeśli połączenie nie powiedzie się, upewnij się, że miejsce pracy przez konto w serwisie Facebook ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawia okno dialogowe poświadczenia administratora z opcją Autoryzuj.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoryzacja](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników w miejscu pracy przez serwis Facebook**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do miejsca pracy przez serwis Facebook, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w miejscu pracy przez serwis Facebook dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że w miejscu pracy przez interfejs API usługi Facebook jest obsługiwany Filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |tytuł|Boolean|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|String|
   |adresy [typ EQ "Work"]. sformatowane|String|
   |adresy [typ EQ "Work"]. streetAddress|String|
   |adresy [typ EQ "Work"]. locale|String|
   |addresss [Type EQ "Work"]. region|String|
   |addresss [Type EQ "Work"]. Country|String|
   |addresss [Type EQ "Work"]. KodPocztowy|String|
   |adresy [Type EQ "Other"]. sformatowane|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|String|
   |externalId|Ciąg|
   |preferredLanguage|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.department|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.division|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Ciąg|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Ciąg|
   |urn: Standard scim: schematy: rozszerzenie: Facebook: auth_method: 1.0: auth_method|String|
   |urn: Standard scim: schematy: rozszerzenie: Facebook: teraźniejszości: 1.0.is_frontline|Boolean|
   |urn: Standard scim: schematy: rozszerzenie: Facebook: starttermdates: 1.0. startDate|Liczba całkowita|


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning w miejscu pracy przez serwis Facebook, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w miejscu pracy przez serwis Facebook, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
*  Jeśli zobaczysz, że użytkownik zostanie niepomyślnie utworzony i istnieje zdarzenie dziennika inspekcji z kodem "1789003", oznacza to, że użytkownik pochodzi z niezweryfikowanej domeny.

## <a name="change-log"></a>Dziennik zmian

* 09/10/2020 — dodano obsługę atrybutów przedsiębiorstwa "dzielenie", "organizacja", "costCenter" i "employeeNumber". Dodano obsługę atrybutów niestandardowych "startDate", "auth_method" i "teraźniejszości"

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)