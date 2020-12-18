---
title: 'Samouczek: Konfigurowanie Clarizen jeden dla automatycznej aprowizacji użytkowników w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z usługi Azure AD w celu Clarizen jednego.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 3754a56d1520118937a9d4aad406ae657b4dd23b
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673668"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Clarizen jeden dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach obu Clarizen i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i unieważnia użytkowników i grupy, aby [Clarizen je](https://www.clarizen.com/) za pomocą usługi Azure AD Provisioning. Aby uzyskać informacje na temat tego, jak działa ta usługa, jak to się dzieje, i często zadawanych pytań, zobacz [Automatyzacja aprowizacji użytkowników i cofanie obsługi w aplikacjach typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
> * Utwórz użytkowników w Clarizen.
> * Usuń użytkowników w Clarizen jeden, gdy nie wymagają już dostępu.
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD a Clarizen.
> * Udostępnianie grup i członkostwa w grupach w Clarizen.
> * Zalecane jest logowanie jednokrotne [(SSO)](./clarizen-tutorial.md) do Clarizen.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji. Przykładami są administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny.
* Konto użytkownika w Clarizen jednym z uprawnieniami **użytkownika integracji** i **administratora uproszczonego** [](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Określ, które dane mają być [mapowane między usługą Azure AD i Clarizen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Krok 2. Skonfiguruj usługę Clarizen, aby obsługiwać obsługę administracyjną w usłudze Azure AD

1. Wybierz jeden z czterech następujących adresów URL dzierżawców według Clarizen jednego środowiska i centrum danych:
      * Centrum danych produkcyjnych USA: https://servicesapp2.clarizen.com/scim/v2
      * Produkcyjne centrum danych w Unii Europejskiej: https://serviceseu1.clarizen.com/scim/v2
      * Centrum danych w piaskownicy USA: https://servicesapp.clarizentb.com/scim/v2
      * Centrum danych w piaskownicy UE: https://serviceseu.clarizentb.com/scim/v2

1. Generuj [klucz interfejsu API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Ta wartość zostanie wprowadzona w polu **token tajny** na karcie **aprowizacji** Clarizen jednej aplikacji w Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj Clarizen jeden z galerii aplikacji usługi Azure AD

Dodaj Clarizen jeden z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Clarizen. Jeśli wcześniej skonfigurowano Clarizen jeden na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Podczas wstępnego testowania integracji utwórz oddzielną aplikację. Aby dowiedzieć się więcej na temat dodawania aplikacji z galerii, zobacz [Dodawanie aplikacji do dzierżawy usługi Azure AD](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Za pomocą usługi Azure AD Provisioning można określić zakres, który zostanie zainicjowany na podstawie przydziału do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany do aplikacji na podstawie przypisywania, wykonaj kroki opisane w sekcji [Zarządzanie przypisaniem użytkowników dla aplikacji w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany w oparciu o atrybuty użytkownika lub grupy, Użyj filtru określania zakresu, zgodnie z opisem w temacie [Inicjowanie obsługi aplikacji opartej na atrybutach z filtrami określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Po przypisaniu użytkowników i grup do Clarizen jednej, należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról.
* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników i grup, Zanim przeprowadzisz wszystko do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można zachować kontrolę, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w celu Clarizen jednego

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie TestApp na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Clarizen w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

      ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **Clarizen jeden**.

      ![Zrzut ekranu przedstawiający link Clarizen one na liście aplikacji.](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

      ![Zrzut ekranu przedstawiający kartę aprowizacji.](common/provisioning.png)

1. Ustaw **tryb aprowizacji** na **automatyczny**.

      ![Zrzut ekranu pokazujący automatyczną opcję karty aprowizacji.](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wprowadź **adres URL Clarizen jednego dzierżawy** i **token tajny**. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Clarizen. Jeśli połączenie nie powiedzie się, upewnij się, że Clarizen jedno konto ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu przedstawiający pole tokenu tajnego.](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu przedstawiający pole powiadomień E-mail.](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Clarizen jeden**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby Clarizen je w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Clarizen jeden dla operacji aktualizacji. Jeśli zmienisz [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że Clarizen jeden interfejs API obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |title|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |wiadomości e-mail [Type EQ "Home"]. Value|Ciąg|
   |wiadomości e-mail [Type EQ "Other"]. Value|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |name.honorificPrefix|Ciąg|
   |Name. honorificSuffix|Ciąg|
   |adresy [Type EQ "Other"]. sformatowane|Ciąg|
   |adresy [typ EQ "Work"]. sformatowane|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |addresss [Type EQ "Work"]. region|Ciąg|
   |adresy [typ EQ "Work"]. locale|Ciąg|
   |addresss [Type EQ "Work"]. KodPocztowy|Ciąg|
   |adresy [typ EQ "Work"]. streetAddress|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|Ciąg|
   |numer telefonu [typ EQ "Home"]. Value|Ciąg|
   |braky [Type EQ "Other"]. Value|Ciąg|
   |numer telefonu [typ EQ "pager"]. wartość|Ciąg|
   |externalId|Ciąg|
   |nickName|Ciąg|
   |locale|Ciąg|
   |role [podstawowa EQ "true". Type]|Ciąg|
   |role [podstawowa EQ "true". Value]|Ciąg|
   |timezone|Ciąg|
   |userType|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|Ciąg|

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Clarizen jeden**.

1. Przejrzyj atrybuty grup, które są synchronizowane z usługą Azure AD, aby Clarizen je w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Clarizen jeden dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

1. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w  [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning for Clarizen, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

      ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić, aby Clarizen jeden, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

      ![Zrzut ekranu pokazujący zakres aprowizacji.](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

      ![Zrzut ekranu przedstawiający Zapisywanie konfiguracji aprowizacji.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia.

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
1. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
1. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej na temat stanów kwarantanny, zobacz [Inicjowanie obsługi aplikacji w stanie kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Po przypisaniu użytkownika do aplikacji Clarizen z galerii wybierz tylko rolę **użytkownika** . Następujące role są nieprawidłowe:

* Administrator (Administrator)
* Użytkownik raportu wiadomości e-mail
* Użytkownik zewnętrzny
* Użytkownik finansowy
* Użytkownik społecznościowy
* Administratora
* Czas & użytkownika wydatków

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)