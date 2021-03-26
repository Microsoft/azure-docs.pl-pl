---
title: 'Samouczek: Konfigurowanie Olfeo SAAS dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549322"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Olfeo SAAS dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w Olfeo SAAS, jak i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w celu [OLFEO SaaS](https://www.olfeo.com) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze Olfeo SAAS
> * Usuń użytkowników z Olfeo SAAS, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Olfeo SAAS
> * Udostępnianie grup i członkostwa w grupach w usłudze Olfeo SAAS
> * [Logowanie](olfeo-saas-tutorial.md) jednokrotne do Olfeo SaaS (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa OLFEO SaaS](https://www.olfeo.com/).
* Konto użytkownika w programie Olfeo SAAS z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Określ, które dane mają być [mapowane między usługą Azure AD i OLFEO SaaS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi Olfeo SAAS w celu obsługi aprowizacji w usłudze Azure AD

1. Zaloguj się do konsoli administracyjnej Olfeo SAAS. 
1. Przejdź do **konfiguracji > Annuaires**.
1. Utwórz nowy katalog, a następnie nadaj mu nazwę.
1. Wybierz pozycję Dostawca **platformy Azure** , a następnie kliknij pozycję **CR er** , aby zapisać nowy katalog. 
1. Przejdź do karty **Synchronizacja** , aby wyświetlić **adres URL dzierżawy** i **wpis tajny Jeton**. Te wartości zostaną skopiowane i wklejone w polach **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji Olfeo SaaS w Azure Portal.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Olfeo SAAS z galerii aplikacji usługi Azure AD

Dodaj Olfeo SAAS z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do Olfeo SAAS. Jeśli wcześniej skonfigurowano usługę Olfeo SAAS dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do Olfeo SAAS, należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do Olfeo SAAS 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w aplikacji Olfeo SAAS na podstawie przypisań użytkowników i grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Olfeo SAAS w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **OLFEO SaaS**.

    ![Link Olfeo SAAS na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

1.  Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** Olfeo SaaS i informacje o **tokenie tajnym** . Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Olfeo SaaS. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Olfeo SAAS ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Olfeo SaaS**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Olfeo SAAS w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Olfeo SaaS dla operacji aktualizacji. Jeśli zmienisz [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz upewnić się, że interfejs API usługi Olfeo SaaS obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |externalId|Ciąg|  

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Olfeo SaaS**.

1. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do Olfeo SAAS w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Olfeo SaaS dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |displayName|Ciąg|&check;|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

1. Aby skonfigurować filtry zakresu, zobacz instrukcje zawarte w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla Olfeo SAAS, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Olfeo SAAS, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne cykle, co jest wykonywane co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy byli pomyślnie lub niepowodzeniem.
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji i zakończenie jego kończenia.
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej na temat stanów kwarantanny, zobacz temat [stan aprowizacji aplikacji dla kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Więcej zasobów

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)