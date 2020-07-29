---
title: 'Samouczek: Konfigurowanie szkolenia w zakresie zabezpieczeń programu Webroot dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD na potrzeby szkolenia w zakresie zabezpieczeń Webroot.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: 0bed20dfd087783e865dd2e68897870ad56507c2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294643"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie szkolenia w zakresie zabezpieczeń programu Webroot dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach szkolenia w zakresie zabezpieczeń programu Webroot i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w ramach [szkolenia w zakresie zabezpieczeń](https://www.webroot.com/) w usłudze Azure AD. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w ramach szkolenia w programie Webroot Security
> * Usuń użytkowników w szkoleniu do uczenia zabezpieczeń w programie Webroot, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a programem Webroot Security Training
> * Udostępnianie grup i członkostw w grupach w usłudze Webroot Security Training

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
* Konsola dostawcy usług zarządzanych z programem Webroot Security Training jest włączona dla co najmniej jednej z witryn.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługami Azure AD i Webroot Security Training](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie szkolenia w zakresie zabezpieczeń programu Webroot w celu obsługi aprowizacji za pomocą usługi Azure AD

### <a name="obtain-a-secret-token"></a>Uzyskaj token tajny

Aby połączyć swoją lokację z usługą Azure AD, musisz uzyskać **token tajny** dla tej lokacji w konsoli zarządzania Webroot.

1. Zaloguj się do [konsoli zarządzania programu Webroot](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. Na karcie **Lokacje** kliknij ikonę koła zębatego w kolumnie szkolenia dotyczącej świadomości bezpieczeństwa dla witryny, którą chcesz połączyć z usługą Azure AD.

    ![Ikona koła zębatego](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Kliknij przycisk, aby **skonfigurować integrację z usługą Azure AD**.

    ![Konfigurowanie integracji z usługą Azure AD](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Skopiuj i Zapisz **token tajny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji szkoleniowej dotyczącej świadomości zabezpieczeń w programie Webroot w Azure Portal.

5. Kliknij pozycję **Gotowe**.

    ![Kopiuj token tajny](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie szkoleń dotyczących świadomości zabezpieczeń firmy Webroot z galerii aplikacji usługi Azure AD

Dodaj szkolenie dotyczące uczenia zabezpieczeń programu Webroot z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do szkolenia w zakresie zabezpieczeń Webroot. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do szkoleń dotyczących świadomości zabezpieczeń w programie Webroot należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w usłudze Webroot Security Training 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w przypadku szkoleń dotyczących świadomości zabezpieczeń w usłudze Webroot w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Webroot zabezpieczenia uczenie**się.

    ![Link do szkolenia w zakresie zabezpieczeń Webroot na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://awarenessapi.webrootanywhere.com/api/v2/scim` **adres URL dzierżawy**. Wprowadź wcześniej pobraną wartość tokenu tajnego w polu **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z programem Webroot Security Training. Jeśli połączenie nie powiedzie się, upewnij się, że konto szkoleniowe usługi Webroot Security ma uprawnienia administratora i spróbuj ponownie.

    ![aprowizacji](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory użytkownikom**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do programu Webroot Security uczenia się w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w programie Webroot Security Training for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API szkolenia w zakresie zabezpieczeń Webroot obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |externalId|String|&check;|
   |Nazwa. imię|String|
   |Nazwa. rodzina|String|
   |wiadomości e-mail [Type EQ "Work"]. Value|String|

10. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory grupy**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do programu Webroot Security uczenia się w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w programie Webroot Security Training for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |displayName|String|&check;|
      |elementy członkowskie|Dokumentacja|
      |externalId|String|

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla szkolenia w zakresie zabezpieczeń Webroot, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do szkolenia w celu uczenia zabezpieczeń, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
