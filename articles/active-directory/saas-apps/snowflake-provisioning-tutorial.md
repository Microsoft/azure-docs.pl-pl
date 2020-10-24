---
title: 'Samouczek: Konfigurowanie płatnej śniegu w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 881b1df00f8a962087f1e57b7141ac50f5cfa18b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514830"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie płatnej śniegu na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie kroków, które należy wykonać w śniegu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup na [płatki śniegu](https://www.Snowflake.com/pricing/). Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w śniegu
> * Usuń użytkowników w śniegu, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Płatą śniegu
> * Udostępnianie grup i członkostwa w grupach w śniegu
> * [Logowanie](./snowflake-tutorial.md) jednokrotne do śniegu (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* [Dzierżawca płatka śniegu](https://www.Snowflake.com/pricing/).
* Konto użytkownika w śniegu z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i płatą śniegu](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie obsługi administracyjnej za pomocą usługi Azure AD

Przed skonfigurowaniem płatnej śniegu w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD należy włączyć funkcję aprowizacji Standard scim na śniegu.

1. Zaloguj się do konsoli administracyjnej płatnej śniegu. Wprowadź poniżej zapytanie w wyróżnionym arkuszu, a następnie kliknij przycisk **Uruchom**.

    ![Konsola administracyjna płatka śniegu](media/Snowflake-provisioning-tutorial/image00.png)

2.  Token dostępu Standard scim zostanie wygenerowany dla dzierżawy płatnej. Aby go pobrać, kliknij link wyróżniony poniżej.

    ![Zrzut ekranu arkusza w płatnej wysokości U I z tokenem dostępu S C I M o nazwie out.](media/Snowflake-provisioning-tutorial/image01.png)

3. Skopiuj wygenerowaną wartość tokenu i kliknij przycisk **gotowe**. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji płatnej śniegu w Azure Portal.

    ![Zrzut ekranu przedstawiający sekcję Szczegóły przedstawiającą token skopiowany do pola tekstowego oraz opcję Gotowe wywołana.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj płatną śnieg z galerii aplikacji usługi Azure AD

Dodaj płatną śnieg z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą na Płatę śniegu. Jeśli wcześniej skonfigurowano płatny śnieg na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do śniegu należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Krok 5. Skonfiguruj automatyczne Inicjowanie obsługi administracyjnej użytkowników na Płatę śniegu 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w śniegu na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla płatnych śniegów w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **płatka śnieg**.

    ![Link aplikacji Snowflake na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź odpowiednie wartości **w polach** **adres** URL i **token uwierzytelniania Standard scim 2,0** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z płatą Jeśli połączenie zakończy się niepowodzeniem, upewnij się, że Twoje konto płatne w sieci śnieg ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

8. Kliknij pozycję **Zapisz**.

9. W sekcji **mapowania** wybierz opcję **Synchronizuj Azure Active Directory użytkownicy z płatą śniegu**.

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w śniegu w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |userName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: defaultRole|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: defaultWarehouse|String|

11. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z płatą śniegu**.

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w śniegu dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |elementy członkowskie|Tematy pomocy|

13. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę Azure AD Provisioning dla płatka śnieg, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić śnieg, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** . Jeśli ta opcja jest niedostępna, skonfiguruj wymagane pola w obszarze poświadczenia administratora, a następnie kliknij przycisk **Zapisz** i Odśwież stronę. 

    ![Zakres aprowizacji](common/provisioning-scope.png)

16. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

    Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Tokeny Standard scim wygenerowały płaty śniegu wygaśnie w ciągu 6 miesięcy. Należy pamiętać, że należy je odświeżyć przed wygaśnięciem, aby umożliwić kontynuowanie synchronizacji aprowizacji. 

## <a name="change-log"></a>Dziennik zmian

* 07/21/2020 — możliwość usuwania nietrwałego dla wszystkich użytkowników (za pośrednictwem aktywnego atrybutu).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).