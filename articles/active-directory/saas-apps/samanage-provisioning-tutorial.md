---
title: 'Samouczek: Konfigurowanie usługi SolarWinds Service Desk (wcześniej Samanage) w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do SolarWinds Service Desk (wcześniej Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: d68811c5e7b92698944d7b1b1736eef864d97d20
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802305"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi SolarWinds Service Desk (wcześniej Samanage) w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie SolarWinds Service Desk (wcześniej Samanage) i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w usłudze [SolarWinds Service Desk](https://www.samanage.com/pricing/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrowanie do nowej aplikacji SolarWinds Service Desk

Jeśli masz istniejącą integrację z usługą SolarWinds Service Desk, zapoznaj się z sekcją w poniższej sekcji dotyczącej nadchodzących zmian. Jeśli konfigurujesz usługę SolarWinds Service Desk po raz pierwszy, możesz pominąć tę sekcję i przejść do opcji **obsługiwane**.

#### <a name="whats-changing"></a>Co się zmieni?

* Zmiany po stronie usługi Azure AD: Metoda autoryzacji do aprowizacji użytkowników w usłudze Samange ma historyczną **autoryzację**. Wkrótce zostanie wyświetlona Metoda autoryzacji zmieniona na **długi czas** życia.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co należy zrobić, aby przeprowadzić migrację istniejącej integracji niestandardowej do nowej aplikacji?

Jeśli masz istniejącą integrację z usługą SolarWinds Service Desk z prawidłowymi poświadczeniami administratora, **nie jest wymagana żadna akcja**. Automatycznie Migrujemy klientów do nowej aplikacji. Ten proces jest wykonywany całkowicie w tle. Jeśli istniejące poświadczenia wygasną lub jeśli musisz ponownie autoryzować dostęp do aplikacji, musisz wygenerować token długiego wpisu tajnego. Aby wygenerować nowy token, zapoznaj się z częścią krok 2 tego artykułu.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak sprawdzić, czy moja aplikacja została zmigrowana? 

W przypadku migrowania aplikacji w sekcji **poświadczenia administratora** pola **Nazwa użytkownika** i **hasło** administratora zostaną zastąpione polem **klucza tajnego** .

## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze SolarWinds Service Desk
> * Usuń użytkowników w usłudze SolarWinds Service Desk, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i SolarWinds Service Desk
> * Udostępnianie grup i członkostw w grupach w SolarWinds Service Desk
> * [Logowanie](./samanage-tutorial.md) jednokrotne do usługi SolarWinds Service Desk (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa usługi SolarWinds Service](https://www.samanage.com/pricing/) z pakietem Professional.
* Konto użytkownika w SolarWinds Service Desk z uprawnieniami administratora.

> [!Note]
> Role nie powinny być edytowane ręcznie w Azure Active Directory podczas importowania ról.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługami Azure AD i SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi SolarWinds Service Desk w celu obsługi aprowizacji za pomocą usługi Azure AD

Aby wygenerować token tajny na potrzeby uwierzytelniania, zobacz [samouczek Tokens Authentication for API Integration](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie programu SolarWinds Service Desk z galerii aplikacji usługi Azure AD

Dodaj usługę SolarWinds Service Desk z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do usługi SolarWinds Service Desk. Jeśli wcześniej skonfigurowano usługę SolarWinds Service Desk dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do usługi SolarWinds Service Desk należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w usłudze SolarWinds Service Desk 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla usługi SolarWinds Service Desk w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SolarWinds Service Desk**.

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający wybraną kartę aprowizacji.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu pokazujący tryb aprowizacji ustawiony na automatyczny.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://api.samanage.com` **adres URL dzierżawy**.  Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Wybierz pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może nawiązać połączenie z usługą SolarWinds Service Desk. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi SolarWinds Service Desk ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu pokazujący wybrany przycisk Testuj połączenie.](./media/samanage-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby SolarWinds Service Desk**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi SolarWinds Service Desk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w SolarWinds Service Desk dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API usługi SolarWinds Service Desk obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      ![Samange mapowania użytkowników](./media/samanage-provisioning-tutorial/user-attributes.png)

10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory grupy, aby SolarWinds Service Desk**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi SolarWinds Service Desk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w SolarWinds Service Desk dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      ![Mapowania grup Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning Service dla kontaktu z usługą SolarWinds, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić SolarWinds usługi, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

W przypadku wybrania opcji **Synchronizuj wszystkich użytkowników i grup** i skonfigurowania wartości atrybutu **role** SolarWinds Service Desk, wartość w polu **wartość domyślna, jeśli wartość null (jest opcjonalna)** musi być wyrażona w następującym formacie:

- {"displayName": "role"}, gdzie role jest wartością domyślną.

## <a name="change-log"></a>Dziennik zmian

* 09/14/2020 — zmieniono nazwę firmy w dwóch samouczkach SaaS od Samanage do SolarWinds Service Desk (wcześniej Samanage) na https://github.com/ravitmorales .
* 04/22/2020 — Zaktualizowano metodę autoryzacji z uwierzytelniania podstawowego do długiego tokenu tajnego.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
