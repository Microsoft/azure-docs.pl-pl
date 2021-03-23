---
title: 'Samouczek: Konfigurowanie użytkownika zarządzanego przez witrynę GitHub Enterprise dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w witrynie GitHub Enterprise Managed User.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: cbae87a005240c15a2c3c28dcb8ab126d9957ba6
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801234"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie użytkownika zarządzanego przez witrynę GitHub Enterprise dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w przypadku użytkownika zarządzanego przez usługę GitHub Enterprise, jak i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup w usłudze GitHub Enterprise zarządzanej przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze GitHub Enterprise zarządzanego użytkownika
> * Usuń użytkowników z zarządzanego użytkownika usługi GitHub Enterprise, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkownika między użytkownikiem zarządzanym usługi Azure AD i GitHub Enterprise
> * Udostępnianie grup i członkostw w grupach w usłudze GitHub Enterprise zarządzanego użytkownika
> * Logowanie jednokrotne do użytkownika zarządzanego przez firmę GitHub Enterprise (zalecane)

> [!NOTE]
> Ten łącznik aprowizacji jest włączony tylko dla uczestników programu Enterprise Managed Users (wersja beta).


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md)
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
* Użytkownicy zarządzani przez przedsiębiorstwo z obsługą usługi GitHub Enterprise i skonfigurowani do logowania za pomocą logowania jednokrotnego za pomocą usługi Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między użytkownikami zarządzanymi przez usługę Azure AD i GitHub Enterprise](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie użytkownika zarządzanego przez usługę GitHub Enterprise w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Adres URL dzierżawy to `https://api.github.com/scim/v2/enterprises/{enterprise}` . Ta wartość zostanie wprowadzona w polu adres URL dzierżawy na karcie aprowizacji aplikacji użytkownika zarządzanego przez firmę GitHub w Azure Portal.

2. Jako administrator zarządzany przez firmę GitHub Enterprise przejdź do prawego górnego rogu, > kliknij zdjęcie profilu > następnie kliknij pozycję **Ustawienia**.

3. Na pasku bocznym po lewej stronie kliknij pozycję **Ustawienia dewelopera**.

4. Na lewym pasku bocznym kliknij pozycję **osobiste tokeny dostępu**.

5. Kliknij pozycję **Generuj nowy token**.

6. Wybierz pozycję **administrator:** zakres dla tego tokenu.

7. Kliknij przycisk **Generuj token**.

8. Skopiuj i Zapisz **token tajny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji użytkownika zarządzanego przez firmę GitHub w Azure Portal.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie użytkownika zarządzanego przez usługę GitHub Enterprise z galerii aplikacji usługi Azure AD

Dodaj użytkownika zarządzanego przez usługę GitHub Enterprise z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do użytkownika zarządzanego przez firmę GitHub Enterprise. Jeśli wcześniej skonfigurowano użytkownika zarządzanego przez usługę GitHub dla przedsiębiorstw na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Podczas przypisywania użytkowników i grup do użytkownika zarządzanego przez firmę GitHub Enterprise należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia.

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Krok 5. Skonfiguruj automatyczne Inicjowanie obsługi użytkowników dla użytkownika zarządzanego przez firmę GitHub Enterprise

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkownika dla użytkownika zarządzanego przez firmę GitHub w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **użytkownik zarządzani przez firmę GitHub Enterprise**.

    ![Link zarządzany przez użytkownika w witrynie GitHub Enterprise na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy użytkownika zarządzanego w witrynie GitHub Enterprise i token klucza tajnego. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z użytkownikiem zarządzanym przez przedsiębiorstwo Jeśli połączenie nie powiedzie się, upewnij się, że konto użytkownika zarządzane przez usługę GitHub Enterprise utworzyło token tajny jako właściciela przedsiębiorstwa, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z użytkownikiem zarządzanym przez firmę GitHub Enterprise**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do użytkownika zarządzanego w witrynie GitHub Enterprise, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie zarządzanym przez firmę GitHub Enterprise dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że interfejs API użytkownika zarządzanego przez firmę GitHub Enterprise obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |externalId|Ciąg|&check;|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |role|Ciąg|
   |displayName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |wiadomości e-mail [Type EQ "Home"]. Value|Ciąg|
   |wiadomości e-mail [Type EQ "Other"]. Value|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do użytkownika zarządzanego przez firmę GitHub Enterprise**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do użytkownika zarządzanego w witrynie GitHub Enterprise w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do grup w programie użytkownika zarządzanego przez firmę GitHub dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |externalId|Ciąg|&check;|
      |displayName|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla użytkownika zarządzanego przez firmę GitHub, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić użytkownikowi zarządzanemu przez firmę GitHub, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

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