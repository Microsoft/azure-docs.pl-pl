---
title: 'Samouczek: Konfigurowanie narzędzia Smartsheet dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze narzędzia Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 699eb37176d6737744fb0ba01f9f3f4a2d4e55b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85318751"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie narzędzia Smartsheet na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w narzędzia Smartsheet i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do [Narzędzia Smartsheet](https://www.smartsheet.com/pricing). Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w narzędzia Smartsheet
> * Usuń użytkowników w narzędzia Smartsheet, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i narzędzia Smartsheet
> * Logowanie jednokrotne do narzędzia Smartsheet (zalecane)

> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
* [Dzierżawa narzędzia Smartsheet](https://www.smartsheet.com/pricing).
* Konto użytkownika w planie narzędzia Smartsheet Enterprise lub Enterprise Premier z uprawnieniami administratora systemu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i narzędzia Smartsheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

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

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wybierz pozycję **dostęp do interfejsu API**. Kliknij pozycję **Generuj nowy token dostępu**.

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zdefiniuj nazwę tokenu dostępu interfejsu API. Kliknij przycisk **OK**.

    ![Narzędzia Smartsheet Zainstaluj](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Skopiuj token dostępu interfejsu API i Zapisz go, ponieważ będzie można go wyświetlić. Jest to wymagane w polu **token tajny** w usłudze Azure AD.

    ![Token narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie narzędzia Smartsheet z galerii aplikacji usługi Azure AD

Dodaj narzędzia Smartsheet z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do narzędzia Smartsheet. Jeśli wcześniej skonfigurowano narzędzia Smartsheet do logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do narzędzia Smartsheet należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Aby zapewnić zgodność z przypisaniami ról użytkowników między narzędzia Smartsheet i usługą Azure AD, zaleca się korzystanie z tych samych przypisań ról, które zostały wypełnione na liście pełnych użytkowników narzędzia Smartsheet. Aby pobrać tę listę użytkowników z narzędzia Smartsheet, przejdź do **administratora konta > zarządzanie użytkownikami > więcej akcji > Pobierz listę użytkowników (CSV)**.

* Aby uzyskać dostęp do pewnych funkcji w aplikacji, narzędzia Smartsheet wymaga, aby użytkownik miał wiele ról. Aby dowiedzieć się więcej o typach i uprawnieniach użytkownika w narzędzia Smartsheet, przejdź do pozycji [typy użytkowników i uprawnienia](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Jeśli użytkownik ma wiele ról przypisanych w narzędzia Smartsheet, **należy** się upewnić, że te przypisania ról są replikowane w usłudze Azure AD, aby uniknąć sytuacji, w której użytkownicy mogą trwale utracić dostęp do obiektów narzędzia Smartsheet. Każda unikatowa rola w narzędzia Smartsheet **musi** być przypisana do innej grupy w usłudze Azure AD. Użytkownik **musi** następnie dodać do każdej grupy odpowiadającej żądanym rolom. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do narzędzia Smartsheet 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie narzędzia Smartsheet na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla narzędzia Smartsheet w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Narzędzia Smartsheet**.

    ![Link narzędzia Smartsheet na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź odpowiednie wartości w polach **adres** URL **i token dostępu Standard scim 2,0** , które zostały pobrane wcześniej z narzędzia Smartsheet i **tokenu tajnego** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą narzędzia Smartsheet. Jeśli połączenie nie powiedzie się, upewnij się, że konto narzędzia Smartsheet ma uprawnienia administratora systemu, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do narzędzia Smartsheet**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do narzędzia Smartsheet w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie narzędzia Smartsheet for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Boolean|
   |tytuł|String|
   |userName|String|
   |Nazwa. imię|String|
   |Nazwa. rodzina|String|
   |numer telefonu [typ EQ "Work"]. wartość|String|
   |numer telefonu [typ EQ "Mobile"]. Value|String|
   |numer telefonu [typ EQ "Fax"]. wartość|String|
   |externalId|String|
   |role [podstawowa EQ "true"]. Display|String|
   |role [podstawowa EQ "true"]. Type|String|
   |role [podstawowa EQ "true"]. Value|String|
   |role|String|
   urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|String|


10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla narzędzia Smartsheet, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić narzędzia Smartsheet, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Narzędzia Smartsheet nie obsługuje usuwania nietrwałego. Gdy **aktywny** atrybut użytkownika ma wartość FAŁSZ, narzędzia Smartsheet trwale usuwa użytkownika.

## <a name="change-log"></a>Dziennik zmian

* 06/16/2020 — dodano obsługę atrybutów rozszerzenia przedsiębiorstwa "centrum kosztów", "Wydział", "Menedżer" i "dział" dla użytkowników.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
