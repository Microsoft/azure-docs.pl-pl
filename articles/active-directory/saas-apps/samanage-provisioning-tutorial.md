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
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707270"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi SolarWinds Service Desk (wcześniej Samanage) w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie SolarWinds Service Desk (wcześniej Samanage) i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w usłudze [SolarWinds Service Desk](https://www.samanage.com/pricing/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrowanie do nowej aplikacji SolarWinds Service Desk

Jeśli masz istniejącą integrację z usługą SolarWinds Service Desk, zapoznaj się z sekcją w poniższej sekcji dotyczącej nadchodzących zmian. Jeśli konfigurujesz usługę SolarWinds Service Desk po raz pierwszy, możesz pominąć tę sekcję i przejść do opcji **obsługiwane**.

#### <a name="whats-changing"></a>Co się zmieni?

* Zmiany po stronie usługi Azure AD: Metoda autoryzacji do aprowizacji użytkowników w usłudze Samange ma historyczną **autoryzację**. Wkrótce zostanie wyświetlona Metoda autoryzacji zmieniona na **długi czas**życia.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co należy zrobić, aby przeprowadzić migrację istniejącej integracji niestandardowej do nowej aplikacji?

Jeśli masz istniejącą integrację z usługą SolarWinds Service Desk z prawidłowymi poświadczeniami administratora, **nie jest wymagana żadna akcja**. Automatycznie Migrujemy klientów do nowej aplikacji. Ten proces jest wykonywany całkowicie w tle. Jeśli istniejące poświadczenia wygasną lub jeśli musisz ponownie autoryzować dostęp do aplikacji, musisz wygenerować token długiego wpisu tajnego. Aby wygenerować nowy token, zapoznaj się z częścią krok 2 tego artykułu.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak sprawdzić, czy moja aplikacja została zmigrowana? 

W przypadku migrowania aplikacji w sekcji **poświadczenia administratora** pola **Nazwa użytkownika** i **hasło** administratora zostaną zastąpione polem **klucza tajnego** .

## <a name="capabilities-supported"></a>Obsługiwane możliwości

> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze SolarWinds Service Desk
> * Usuń użytkowników w usłudze SolarWinds Service Desk, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i SolarWinds Service Desk
> * Udostępnianie grup i członkostw w grupach w SolarWinds Service Desk
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) jednokrotne do usługi SolarWinds Service Desk (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa usługi SolarWinds Service](https://www.samanage.com/pricing/) z pakietem Professional.
* Konto użytkownika w SolarWinds Service Desk z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługami Azure AD i SolarWinds Service Desk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi SolarWinds Service Desk w celu obsługi aprowizacji za pomocą usługi Azure AD

Aby wygenerować token tajny na potrzeby uwierzytelniania, zobacz [samouczek Tokens Authentication for API Integration](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie programu SolarWinds Service Desk z galerii aplikacji usługi Azure AD

Dodaj usługę SolarWinds Service Desk z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do usługi SolarWinds Service Desk. Jeśli wcześniej skonfigurowano usługę SolarWinds Service Desk dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do usługi SolarWinds Service Desk należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w usłudze SolarWinds Service Desk 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla usługi SolarWinds Service Desk w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SolarWinds Service Desk**.

3. Wybierz kartę **aprowizacji** .

    ![Zrzut ekranu przedstawiający wybraną kartę aprowizacji.](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu pokazujący tryb aprowizacji ustawiony na automatyczny.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://api.samanage.com` **adres URL dzierżawy**.  Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Wybierz pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może nawiązać połączenie z usługą SolarWinds Service Desk. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi SolarWinds Service Desk ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu pokazujący wybrany przycisk Testuj połączenie.](./media/samanage-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby SolarWinds Service Desk**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi SolarWinds Service Desk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w SolarWinds Service Desk dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API usługi SolarWinds Service Desk obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      ![Samange mapowania użytkowników](./media/samanage-provisioning-tutorial/user-attributes.png)

10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory grupy, aby SolarWinds Service Desk**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi SolarWinds Service Desk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w SolarWinds Service Desk dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      ![Mapowania grup Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning Service dla kontaktu z usługą SolarWinds, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić SolarWinds usługi, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Ograniczenia łącznika

W przypadku wybrania opcji **Synchronizuj wszystkich użytkowników i grup** i skonfigurowania wartości atrybutu **role** SolarWinds Service Desk, wartość w polu **wartość domyślna, jeśli wartość null (jest opcjonalna)** musi być wyrażona w następującym formacie:

- {"displayName": "role"}, gdzie role jest wartością domyślną.

## <a name="change-log"></a>Dziennik zmian

* 09/14/2020 — zmieniono nazwę firmy w dwóch samouczkach SaaS od Samanage do SolarWinds Service Desk (wcześniej Samanage) na https://github.com/ravitmorales .
* 04/22/2020 — Zaktualizowano metodę autoryzacji z uwierzytelniania podstawowego do długiego tokenu tajnego.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
