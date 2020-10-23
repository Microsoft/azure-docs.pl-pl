---
title: 'Samouczek: Konfigurowanie Menedżera szkoły firmy Apple w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w programie Apple szkoły Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: ee2eced63b8f06f1d54973a603dabaeb14ee7f7e
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457881"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Menedżera szkoły firmy Apple w celu automatycznego aprowizacji użytkowników



W tym samouczku opisano kroki, które należy wykonać w programie Apple szkoły Manager i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników w programie [Apple szkoły Manager](https://school.apple.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Menedżerze szkoły firmy Apple
> * Usuwanie użytkowników w Menedżerze szkoły firmy Apple, gdy nie wymagają już dostępu
> * Zachowaj synchronizację określonych atrybutów użytkownika między usługą Azure AD i programem Apple szkoły Manager

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto programu Apple szkoln Manager z rolą administratora, Site Manager lub Menedżera osób.

> [!NOTE]
> Transfer tokenu do usługi Azure AD i ustanawianie pomyślnego połączenia należy wykonać w 4 dniach kalendarzowych lub należy ponownie uruchomić proces.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i programem Apple szkoły Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie programu Apple szkoły Manager do obsługi aprowizacji za pomocą usługi Azure AD

1. W programie Apple szkoły Manager Zaloguj się przy użyciu konta z rolą administratora, Site Manager lub Menedżera osób.
2. Kliknij pozycję Ustawienia u dołu paska bocznego, kliknij pozycję źródło danych poniżej ustawienia organizacji, a następnie kliknij pozycję Połącz ze źródłem danych.
3. Kliknij przycisk Połącz obok pozycji Standard scim, uważnie przeczytaj ostrzeżenie, kliknij przycisk Kopiuj, a następnie kliknij przycisk Zamknij.
[Okno łączenia z usługą Standard scim, w którym znajduje się token i przycisk kopiowania.] Pozostaw to okno otwarte, aby skopiować adres URL dzierżawy z programu Apple Business Manager do usługi Azure AD, czyli: " https://federation.apple.com/feeds/school/scim "

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Tokenu tajnego nie należy udostępniać innym osobom niż administrator usługi Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie programu Apple szkoł Manager z galerii aplikacji usługi Azure AD

Dodaj menadżera firmy Apple z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą administracyjną w programie Apple szkoln Manager. Jeśli wcześniej skonfigurowano program Apple szkoły Manager na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników do programu Apple szkoły Manager należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w programie Apple szkoln Manager

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Menedżer szkoły firmy Apple**.

    ![Menedżer szkoły firmy Apple na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź odpowiednie wartości w polach adres **URL i token dostępu Standard scim 2,0** , które zostały pobrane z programu Apple szkoły Manager w **adresie URL dzierżawy** i w **tokenie tajnym** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z Menedżerem firmy Apple. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto programu Apple szkoln Manager ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>W przypadku pomyślnego nawiązania połączenia usługa Apple szkoły Manager wyświetli połączenie Standard scim jako aktywne. Ten proces może potrwać do 60 sekund dla programu Apple szkoły Manager, aby odzwierciedlał najnowszy stan połączenia.

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników w programie Apple szkoły Manager**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do programu Apple szkoły Manager, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w Menedżerze firmy Apple dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Wartość logiczna|
   |userName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |name.givenName|Ciąg|
   |externalId|Ciąg|
   |locale|Ciąg|
   |timezone|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla programu Apple szkoł Manager, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić menedżerowi firmy Apple, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Przejrzyj wymagania standard scim dla programu Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Jak identyfikator osoby jest używany w programie Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Importowanie użytkowników do programu Apple szkoły Manager za pomocą Standard scim](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Rozwiązywanie konfliktów kont użytkowników Standard scim w Menedżerze szkoły firmy Apple](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Usuwanie kont usługi Azure AD, które są wyświetlane w Menedżerze szkoły firmy Apple](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Wyświetlanie działania Standard scim w programie Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Zarządzanie istniejącym tokenem Standard scim i połączeniami w programie Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Rozłącz połączenie usługi Standard scim w programie Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Rozwiązywanie problemów z połączeniem Standard scim w programie Apple szkoły Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)