---
title: 'Samouczek: Konfigurowanie usługi ServiceNow dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do usługi ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: da62efff5db5c71b087657b0eec93f8dd4702665
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84751500"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi ServiceNow na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie usługi ServiceNow i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [usługi ServiceNow](https://www.servicenow.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usługi ServiceNow
> * Usuń użytkowników w usługi ServiceNow, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i usługi ServiceNow
> * Udostępnianie grup i członkostwa w grupach w usługi ServiceNow
> * [Logowanie](servicenow-tutorial.md) jednokrotne do usługi ServiceNow (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Wystąpienie usługi ServiceNow](https://www.servicenow.com/) o wartości Calgary lub wyższej
* [Wystąpienie usługi ServiceNow Express](https://www.servicenow.com/) w wersji Helsinki lub nowszej
* Konto użytkownika w usługi ServiceNow z rolą administratora

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i usługi ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi ServiceNow w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zidentyfikuj nazwę wystąpienia usługi ServiceNow. Nazwę wystąpienia można znaleźć w adresie URL używanym do uzyskiwania dostępu do usługi ServiceNow. W poniższym przykładzie nazwa wystąpienia to dev35214.

   ![Wystąpienie usługi ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Uzyskaj poświadczenia dla administratora w usługi ServiceNow. Przejdź do profilu użytkownika w programie usługi ServiceNow i sprawdź, czy użytkownik ma rolę administratora. 

   ![Rola administratora usługi ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Upewnij się, że następujące ustawienia są **wyłączone** w usługi ServiceNow:

   1. Wybierz pozycję **zabezpieczenia systemu**  >  **Ustawienia wysokie zabezpieczenia**  >  **wymagają uwierzytelniania podstawowego dla przychodzących żądań schematu**.
   2. Wybieranie **Właściwości systemu**  >  **usługi sieci Web**  >  **wymagają podstawowej autoryzacji dla przychodzących żądań SOAP**.
     
   > [!IMPORTANT]
   > Jeśli te ustawienia są *włączone*, aparat aprowizacji nie będzie mógł komunikować się z usługą usługi ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usługi ServiceNow z galerii aplikacji usługi Azure AD

Dodaj usługi ServiceNow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do usługi ServiceNow. Jeśli wcześniej skonfigurowano usługi ServiceNow do logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do usługi ServiceNow należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do usługi ServiceNow 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla usługi ServiceNow w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ServiceNow**.

    ![Link do usługi ServiceNow na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź poświadczenia administratora usługi ServiceNow i nazwę użytkownika. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi usługi ServiceNow ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![aprowizacji](./media/servicenow-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do usługi ServiceNow**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie usługi ServiceNow for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API usługi ServiceNow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do usługi ServiceNow**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usługi ServiceNow dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usługi ServiceNow, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić usługi ServiceNow, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* **InvalidLookupReference:** W przypadku inicjowania obsługi niektórych atrybutów, takich jak dział i lokalizacja w usługi ServiceNow, wartości muszą już istnieć w tabeli referencyjnej w usługi ServiceNow. Na przykład możesz mieć dwie lokalizacje (Seattle, Los Angeles) i trzy działy (sprzedaż, finanse, Marketing) w tabeli **Wstaw nazwę tabeli** w usługi ServiceNow. Jeśli próbujesz zainicjować obsługę administracyjną użytkownika, gdzie jego dział jest "sprzedaż", a lokalizacja to "Seattle", zostanie ona zainicjowana pomyślnie. W przypadku próby aprowizacji użytkownika z działem "sprzedaż" i lokalizacją "LA" użytkownik nie zostanie zainicjowany. Lokalizację LA należy dodać do tabeli referencyjnej w usługi ServiceNow lub atrybut użytkownika w usłudze Azure AD musi zostać zaktualizowany w taki sposób, aby był zgodny z formatem w usługi ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Przejrzyj [mapowania atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) , aby zidentyfikować pasujący atrybut. Ta wartość musi być obecna dla użytkownika lub grupy, którą próbujesz udostępnić. 
* Zapoznaj się z [interfejsem API protokołu SOAP usługi ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) , aby poznać wszelkie wymagania lub ograniczenia (na przykład format, aby określić kod kraju dla użytkownika).
* Żądania aprowizacji są domyślnie wysyłane do protokołu https://{nazwa wystąpienia}. Service-Now. com/{Table-Name}. Jeśli potrzebujesz niestandardowego adresu URL dzierżawy, możesz podać cały adres URL w polu Nazwa wystąpienia.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Ten błąd wskazuje na problem z komunikacją z wystąpieniem usługi ServiceNow. Sprawdź podwójne, aby upewnić się, że następujące ustawienia są *wyłączone* w usługi ServiceNow:
   
   1. Wybierz pozycję **zabezpieczenia systemu**  >  **Ustawienia wysokie zabezpieczenia**  >  **wymagają uwierzytelniania podstawowego dla przychodzących żądań schematu**.
   2. Wybieranie **Właściwości systemu**  >  **usługi sieci Web**  >  **wymagają podstawowej autoryzacji dla przychodzących żądań SOAP**.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
