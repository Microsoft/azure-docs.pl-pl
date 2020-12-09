---
title: 'Samouczek: Konfigurowanie PlayVox w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do PlayVox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862483"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie PlayVox na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie PlayVox i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników lub grup w usłudze [PlayVox](https://www.playvox.com) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania, a także często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Utwórz użytkowników w PlayVox.
> * Usuń użytkowników w usłudze PlayVox, gdy nie potrzebują już dostępu.
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i PlayVox.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji. Na przykład konto może mieć rolę Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny.
* Konto użytkownika w [PlayVox](https://www.playvox.com) z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się [, jak działa usługa aprowizacji](../app-provisioning/user-provisioning.md).

2. Określ, kto będzie [objęty zakresem aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Określ, które dane mają być [mapowane między usługą Azure AD i PlayVox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Krok 2. Konfigurowanie PlayVox w celu obsługi aprowizacji przy użyciu usługi Azure AD

1. Zaloguj się do konsoli administracyjnej usługi PlayVox i przejdź do pozycji **ustawienia > klucze interfejsu API**.

2. Wybierz pozycję **Utwórz klucz interfejsu API**.

    ![Zrzut ekranu przedstawiający lokalizację przycisku Utwórz klucz interfejsu API w interfejsie użytkownika PlayVox.](media/playvox-provisioning-tutorial/create.png)

3. Wprowadź zrozumiałą nazwę dla klucza interfejsu API, a następnie wybierz pozycję **Zapisz**. Po wygenerowaniu klucza interfejsu API wybierz pozycję **Zamknij**.

4. W utworzonym kluczu interfejsu API wybierz ikonę **szczegóły** .

    ![Zrzut ekranu przedstawiający lokalizację ikony szczegółów, która jest Lupa, w interfejsie użytkownika PlayVox.](media/playvox-provisioning-tutorial/api.png)

5. Skopiuj i Zapisz wartość **klucza Base64** . Później w Azure Portal wprowadzisz tę wartość w polu tekstowym **token tajny** na karcie **aprowizacji** aplikacji PlayVox.

    ![Zrzut ekranu przedstawiający okno komunikatu klucza interfejsu API szczegółów z wyróżnioną wartością klucza BASE64.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie PlayVox z galerii aplikacji usługi Azure AD

Aby rozpocząć zarządzanie aprowizacji do usługi PlayVox, Dodaj PlayVox do dzierżawy usługi Azure AD z galerii aplikacji. Aby dowiedzieć się więcej, zobacz [Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md).

Jeśli wcześniej skonfigurowano usługę PlayVox do logowania jednokrotnego (SSO), możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definiowanie, kto będzie w zakresie aprowizacji

Usługa Azure AD Provisioning jest używana do określania zakresu, który zostanie zainicjowany, na podstawie przypisywania do aplikacji lub atrybutów użytkownika lub grupy. Aby określić zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, zobacz [Zarządzanie przypisaniem użytkowników dla aplikacji w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) , aby dowiedzieć się, jak przypisać użytkowników lub grupy do aplikacji. Aby określić zakres, który zostanie zainicjowany wyłącznie na podstawie atrybutów użytkownika lub grupy, należy użyć filtru określania zakresu, zgodnie z opisem w temacie [Inicjowanie obsługi aplikacji opartej na atrybutach z filtrami określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Należy pamiętać o następujących kwestiach:

* Podczas przypisywania użytkowników do PlayVox należy wybrać rolę inną niż domyślny dostęp. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli domyślny dostęp jest jedyną rolą dostępną w aplikacji, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać inne role.

* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników lub grup przed przekazaniem ich do wszystkich osób. Gdy zakres aprowizacji jest oparty na przypisanych użytkownikach lub grupach, można kontrolować rozmiar zestawu, przypisując do aplikacji tylko jednego lub dwóch użytkowników lub grupy. Gdy zakres aprowizacji obejmuje wszystkich użytkowników i grupy, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) , aby ograniczyć rozmiar zestawu testowego.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do PlayVox

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w zależności od przypisań użytkowników lub grup w usłudze Azure AD.

Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla PlayVox w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający Azure Portal, z aplikacjami przedsiębiorstwa i wszystkie elementy aplikacji wyróżnione](common/enterprise-applications.png)

2. Na liście Aplikacje Wyszukaj i wybierz pozycję **PlayVox**.

    ![Zrzut ekranu przedstawiający listę aplikacji z wyróżnionym polem wyszukiwania aplikacji.](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający element menu aprowizacji.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający kartę aprowizacji, w której jest wyświetlana opcja Automatyczna wybrana w polu listy rozwijanej Tryb aprowizacji.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** PlayVox jako:

    `https://{tenant}.playvox.com/scim/v1`

    Wprowadź **token tajny** , który został skopiowany wcześniej w kroku 2. Następnie wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą PlayVox. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi PlayVox ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawiający sekcję poświadczenia administratora, w tym adres URL dzierżawy i pola tekstowe tokenu tajnego, oraz podświetlone łącze Testuj połączenie.](common/provisioning-testconnection-tenanturltoken.png)

6. W polu tekstowym **powiadomienie e-mail** wprowadź adres e-mail osoby lub grupy, która będzie otrzymywać powiadomienia o błędach aprowizacji. Następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu przedstawiający pole tekstowe powiadomienia E-mail oraz pole wyboru powiadomienie e-mail.](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do PlayVox**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do PlayVox w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie PlayVox for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), upewnij się, że interfejs API PlayVox obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |externalId|Ciąg|

10. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla PlayVox, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Część zrzutu ekranu części ustawień pokazująca stan aprowizacji ustawiony na wartość włączone.](common/provisioning-toggle-on.png)

12. W **ustawieniach** Zdefiniuj użytkowników lub grupy do PlayVox, wybierając odpowiednie wartości w **zakresie**.

    ![Zrzut ekranu przedstawiający sekcję Ustawienia, w której znajduje się pole listy rozwijanej zakres.](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający opcje Zapisz i Odrzuć.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż w późniejszych cyklach. Kolejne cykle są wykonywane co około 40 minut, pod warunkiem, że usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej na temat stanów kwarantanny, zobacz [Inicjowanie obsługi aplikacji w stanie kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)