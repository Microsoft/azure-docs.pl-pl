---
title: 'Samouczek: Konfigurowanie narzędzia Smartsheet dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze narzędzia Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103570"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie narzędzia Smartsheet na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w narzędzia Smartsheet i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do [Narzędzia Smartsheet](https://www.smartsheet.com/pricing). Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w narzędzia Smartsheet
> * Usuń użytkowników w narzędzia Smartsheet, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i narzędzia Smartsheet
> * Logowanie jednokrotne do narzędzia Smartsheet (zalecane)

> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* [Dzierżawa narzędzia Smartsheet](https://www.smartsheet.com/pricing).
* Konto użytkownika w planie narzędzia Smartsheet Enterprise lub Enterprise Premier z uprawnieniami administratora systemu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i narzędzia Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie narzędzia Smartsheet w celu obsługi aprowizacji za pomocą usługi Azure AD

Przed skonfigurowaniem usługi narzędzia Smartsheet na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na narzędzia Smartsheet.

1. Zaloguj się jako administrator **w** **[portalu narzędzia Smartsheet](https://app.smartsheet.com/b/home)** i przejdź do **konta administratora**.

    ![Administrator konta narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Przejdź do pozycji **Kontrola zabezpieczeń > funkcja autoaprowizacji użytkownika > Edytuj**.

    ![Narzędzia Smartsheet kontroli zabezpieczeń](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Dodaj i sprawdź poprawność domen poczty e-mail dla użytkowników, których planujesz udostępnić z usługi Azure AD do narzędzia Smartsheet. Wybierz pozycję **Niewłączony** , aby upewnić się, że wszystkie akcje aprowizacji pochodzą tylko z usługi Azure AD, a także upewnij się, że lista użytkowników narzędzia Smartsheet jest zsynchronizowana z przypisaniami usługi Azure AD.

    ![Narzędzia Smartsheet aprowizacji użytkowników](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po zakończeniu walidacji należy aktywować domenę. 

    ![Narzędzia Smartsheet Aktywuj domenę](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Wygeneruj **token tajny** wymagany do skonfigurowania automatycznej aprowizacji użytkowników w usłudze Azure AD, przechodząc do **aplikacji i integracji**.

    ![Zrzut ekranu przedstawiający stronę administrator narzędzia Smartsheet z awatarem użytkownika i opcją aplikacje & Integrations o nazwie.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wybierz pozycję **dostęp do interfejsu API**. Kliknij pozycję **Generuj nowy token dostępu**.

    ![Zrzut ekranu przedstawiający okno dialogowe Ustawienia osobiste z dostępem do interfejsu API i Generuj nowe opcje tokenu dostępu o nazwie wychodzące.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zdefiniuj nazwę tokenu dostępu interfejsu API. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający krok 1 z 2: generowanie tokenu dostępu API z opcją OK o nazwie out.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Skopiuj token dostępu interfejsu API i Zapisz go, ponieważ będzie można go wyświetlić. Jest to wymagane w polu **token tajny** w usłudze Azure AD.

    ![Token narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie narzędzia Smartsheet z galerii aplikacji usługi Azure AD

Dodaj narzędzia Smartsheet z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do narzędzia Smartsheet. Jeśli wcześniej skonfigurowano narzędzia Smartsheet do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do narzędzia Smartsheet należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Aby zapewnić zgodność z przypisaniami ról użytkowników między narzędzia Smartsheet i usługą Azure AD, zaleca się korzystanie z tych samych przypisań ról, które zostały wypełnione na liście pełnych użytkowników narzędzia Smartsheet. Aby pobrać tę listę użytkowników z narzędzia Smartsheet, przejdź do **administratora konta > zarządzanie użytkownikami > więcej akcji > Pobierz listę użytkowników (CSV)**.

* Aby uzyskać dostęp do pewnych funkcji w aplikacji, narzędzia Smartsheet wymaga, aby użytkownik miał wiele ról. Aby dowiedzieć się więcej o typach i uprawnieniach użytkownika w narzędzia Smartsheet, przejdź do pozycji [typy użytkowników i uprawnienia](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Jeśli użytkownik ma wiele ról przypisanych w narzędzia Smartsheet, **należy** się upewnić, że te przypisania ról są replikowane w usłudze Azure AD, aby uniknąć sytuacji, w której użytkownicy mogą trwale utracić dostęp do obiektów narzędzia Smartsheet. Każda unikatowa rola w narzędzia Smartsheet **musi** być przypisana do innej grupy w usłudze Azure AD. Użytkownik **musi** następnie dodać do każdej grupy odpowiadającej żądanym rolom. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do narzędzia Smartsheet 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie narzędzia Smartsheet na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla narzędzia Smartsheet w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Narzędzia Smartsheet**.

    ![Link narzędzia Smartsheet na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź odpowiednie wartości w polach **adres** URL **i token dostępu Standard scim 2,0** , które zostały pobrane wcześniej z narzędzia Smartsheet i **tokenu tajnego** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą narzędzia Smartsheet. Jeśli połączenie nie powiedzie się, upewnij się, że konto narzędzia Smartsheet ma uprawnienia administratora systemu, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do narzędzia Smartsheet**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do narzędzia Smartsheet w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie narzędzia Smartsheet for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |title|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |externalId|Ciąg|
   |role|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Ciąg|


10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla narzędzia Smartsheet, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić narzędzia Smartsheet, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Narzędzia Smartsheet nie obsługuje usuwania nietrwałego. Gdy **aktywny** atrybut użytkownika ma wartość FAŁSZ, narzędzia Smartsheet trwale usuwa użytkownika.

## <a name="change-log"></a>Dziennik zmian

* 06/16/2020 — dodano obsługę atrybutów rozszerzenia przedsiębiorstwa "centrum kosztów", "Wydział", "Menedżer" i "dział" dla użytkowników.
* 02/10/2021 — dodano obsługę podstawowych atrybutów "emails [Type EQ" Work "]" dla użytkowników.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)