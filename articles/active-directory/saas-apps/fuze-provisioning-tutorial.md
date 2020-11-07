---
title: 'Samouczek: Konfigurowanie Fuze dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Fuze.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 130bb108af5e44ddf61b639c666cb0dba64d69cb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356896"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Fuze na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Fuze i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do [Fuze](https://www.fuze.com/). Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w Fuze
> * Usuń użytkowników w Fuze, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Fuze
> * [Logowanie](./fuze-tutorial.md) jednokrotne do Fuze (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* [Dzierżawa Fuze](https://www.fuze.com/).
* Konto użytkownika w Fuze z uprawnieniami administratora.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i Fuze](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie Fuze w celu obsługi aprowizacji za pomocą usługi Azure AD

Przed skonfigurowaniem usługi Fuze na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na Fuze. 

1. Zacznij od skontaktowania się z przedstawicielem usługi Fuze, aby uzyskać następujące wymagane informacje:

    * Lista jednostek SKU produktu Fuze, które są obecnie używane w Twojej firmie.
    * Lista kodów lokalizacji dla lokalizacji firmy.
    * Lista kodów działu dla firmy.

2. Te jednostki SKU i kody można znaleźć w ramach kontraktu Fuze i dokumentów konfiguracyjnych lub kontaktując się z przedstawicielem Fuze.

3. Po otrzymaniu wymagań przedstawiciel Fuze dostarczy token uwierzytelniania Fuze, który jest wymagany do włączenia integracji. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji Fuze w Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Fuze z galerii aplikacji usługi Azure AD

Dodaj Fuze z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Fuze. Jeśli wcześniej skonfigurowano Fuze do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników do Fuze należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przetestuj się z małym zestawem użytkowników przed wprowadzeniem ich do wszystkich osób. Jeśli zakres dla aprowizacji jest ustawiony na przypisanych użytkowników, można kontrolować ten sposób, przypisując jeden lub dwóch użytkowników do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników, można określić [Filtr określania zakresu atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do Fuze 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Fuze na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Fuze w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Fuze**.

    ![Link aplikacji Fuze na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość w polu **podstawowy adres URL Standard scim 2,0 i token uwierzytelniania Standard scim** pobrane wcześniej od przedstawiciela Fuze w **adresie URL dzierżawy** i w **tokenie klucza tajnego**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Fuze. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Fuze ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token adresu URL dzierżawy](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Fuze**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Fuze w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Fuze for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |aktywne|Wartość logiczna|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Fuze, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Fuze, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Fuze obsługuje niestandardowe atrybuty Standard scim o nazwie **uprawnienia**. Te atrybuty można tworzyć i nie aktualizować. 

## <a name="change-log"></a>Dziennik zmian

* 06/15/2020 — Limit szybkości integracji dostosowany do 10 żądań na sekundę.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).