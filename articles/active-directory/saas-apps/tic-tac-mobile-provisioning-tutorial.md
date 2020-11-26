---
title: 'Samouczek: Konfigurowanie Tic-Tac Mobile dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z usługi Azure AD w celu Tic-Tac urządzeń przenośnych.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: a09594d1bc6037f252ba71855ae302208b4980d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182227"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Tic-Tac mobilnej na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w Tic-Tac Mobile i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i unieważnia użytkowników i grupy do [kółko i krzyżyk-TAC Mobile](https://www.tictacmobile.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać informacje na temat tego, jak działa ta usługa, jak to się dzieje, i często zadawanych pytań, zobacz [Automatyzacja aprowizacji użytkowników i cofanie obsługi w aplikacjach typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze Tic-Tac Mobile.
> * Usuń użytkowników w usłudze Tic-Tac Mobile, gdy nie wymagają już dostępu.
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i Tic-Tac Mobile.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji. Przykładami są administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny.
* Konto [mobilne kółko i krzyżyk-TAC](https://www.tictacmobile.com/) z rolą administratora.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Określ, które dane mają być [mapowane między usługą Azure AD a Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi Tic-Tac Mobile do obsługi aprowizacji w usłudze Azure AD

Skontaktuj się z nami support@tictacmobile.com , aby uzyskać **adres URL dzierżawy** i **token tajny**. Aby otrzymać token, musisz mieć rolę administratora w usłudze Tic-Tac Mobile. Token zostanie wprowadzony w polu **token tajny** na karcie **aprowizacji** aplikacji mobilnej Tic-Tac w Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Tic-Tac Mobile z galerii aplikacji usługi Azure AD

Dodaj Tic-Tac mobilne z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do Tic-Tac mobilnego. Jeśli wcześniej skonfigurowano Tic-Tac Mobile do logowania jednokrotnego, możesz użyć tej samej aplikacji. Podczas wstępnego testowania integracji utwórz oddzielną aplikację. Aby dowiedzieć się więcej na temat dodawania aplikacji z galerii, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Za pomocą usługi Azure AD Provisioning można określić zakres, który zostanie zainicjowany na podstawie przydziału do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany do aplikacji na podstawie przypisywania, wykonaj kroki opisane w sekcji [Zarządzanie przypisaniem użytkowników dla aplikacji w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany w oparciu o atrybuty użytkownika lub grupy, Użyj filtru określania zakresu, zgodnie z opisem w temacie [Inicjowanie obsługi aplikacji opartej na atrybutach z filtrami określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Podczas przypisywania użytkowników i grup do Tic-Tac mobilnej należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról.
* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników i grup, Zanim przeprowadzisz wszystko do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można zachować kontrolę, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w usłudze Tic-Tac Mobile

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie TestApp na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Tic-Tac Mobile w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **kółko i krzyżyk-TAC Mobile**.

    ![Zrzut ekranu przedstawiający link Tic-Tac Mobile na liście aplikacji.](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający kartę aprowizacji.](common/provisioning.png)

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu pokazujący automatyczną opcję karty aprowizacji.](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** mobilnej Tic-Tac i **token klucza tajnego**. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z usługą Tic-Tac Mobile. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Tic-Tac Mobile ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu przedstawiający pole tokenu tajnego.](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu przedstawiający pole powiadomień E-mail.](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby Tic-Tac mobilne**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby Tic-Tac mobilne w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w Tic-Tac Mobile for Updates. Jeśli zmienisz [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że Tic-Tac Mobile API obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |externalId|Ciąg|
   |title|Ciąg|
   |wiadomości e-mail [Type EQ "Work"]. Value|Ciąg|
   |preferredLanguage|Ciąg|
   |externalId|Ciąg|
   |userType|Ciąg|
   |locale|Ciąg|
   |timezone|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|

1. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla Tic-Tac Mobile, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, które mają zostać objęte usługą Tic-Tac Mobile, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zrzut ekranu pokazujący zakres aprowizacji.](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający Zapisywanie konfiguracji aprowizacji.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia.

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
1. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
1. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej na temat stanów kwarantanny, zobacz [Inicjowanie obsługi aplikacji w stanie kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)