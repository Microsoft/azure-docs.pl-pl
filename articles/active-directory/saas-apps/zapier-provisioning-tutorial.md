---
title: 'Samouczek: Konfigurowanie zapier dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do zapier.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 015325fe213c8cc1de347282752211750339cd65
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927253"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zapier na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie zapier i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [zapier](https://zapier.com/pricing) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w zapier
> * Usuń użytkowników w zapier, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i zapier
> * Udostępnianie grup i członkostwa w grupach w zapier
> * Logowanie jednokrotne do zapier (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto użytkownika w zapier z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i zapier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie zapier w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do [konsoli administracyjnej zapier](https://zapier.com/app/login/). Przejdź do pozycji **Ustawienia** pod identyfikatorem dzierżawy.

    ![Konsola administracyjna zapier](media/zapier-provisioning-tutorial/admin.png)

2. W obszarze **Ustawienia firmy**wybierz pozycję **Inicjowanie obsługi użytkowników**.

    ![Zapier Dodaj Standard scim](media/zapier-provisioning-tutorial/user.png)

3. Skopiuj **podstawowy adres URL Standard scim** i **token okaziciela Standard scim**. Te wartości zostaną wprowadzone w polach adres URL dzierżawy i klucz tajny tokenu odpowiednio na karcie aprowizacji aplikacji zapier w Azure Portal.

    ![Utwórz token zapier](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie zapier z galerii aplikacji usługi Azure AD

Dodaj zapier z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do zapier. Jeśli wcześniej skonfigurowano zapier do logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do zapier należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do zapier 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla zapier w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

   ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zapier**.

   ![Link zapier na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

   ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

   ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** zapier i **token tajny**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą zapier. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Zapier ma uprawnienia administratora, a następnie spróbuj ponownie.

   ![aprowizacji](./media/zapier-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

   ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do zapier**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do zapier w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie zapier for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API zapier obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Zmienna|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |externalId|Ciąg|
   |Nazwa. imię|Ciąg|
   |Nazwa. rodzina|Ciąg|
   |wiadomości e-mail [Type EQ "Work"]. Value|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do zapier**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do zapier w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w zapier dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    |Zmienna|Typ|
    |---|---|
    |displayName|Ciąg|
    |elementy członkowskie|Dokumentacja|

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla zapier, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić zapier, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

- Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
- Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
- Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
