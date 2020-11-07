---
title: 'Samouczek: Konfigurowanie usługi ServiceNow dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do usługi ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 1d6213d49c98f5e09f22e7310183315800d0c6f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359786"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi ServiceNow na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie usługi ServiceNow i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [usługi ServiceNow](https://www.servicenow.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usługi ServiceNow
> * Usuń użytkowników w usługi ServiceNow, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i usługi ServiceNow
> * Udostępnianie grup i członkostwa w grupach w usługi ServiceNow
> * [Logowanie](servicenow-tutorial.md) jednokrotne do usługi ServiceNow (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../users-groups-roles/directory-assign-admin-roles.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* [Wystąpienie usługi ServiceNow](https://www.servicenow.com/) o wartości Calgary lub wyższej
* [Wystąpienie usługi ServiceNow Express](https://www.servicenow.com/) w wersji Helsinki lub nowszej
* Konto użytkownika w usługi ServiceNow z rolą administratora

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i usługi ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi ServiceNow w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zidentyfikuj nazwę wystąpienia usługi ServiceNow. Nazwę wystąpienia można znaleźć w adresie URL używanym do uzyskiwania dostępu do usługi ServiceNow. W poniższym przykładzie nazwa wystąpienia to dev35214.

   ![Wystąpienie usługi ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Uzyskaj poświadczenia dla administratora w usługi ServiceNow. Przejdź do profilu użytkownika w programie usługi ServiceNow i sprawdź, czy użytkownik ma rolę administratora. 

   ![Rola administratora usługi ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Upewnij się, że następujące ustawienia są **wyłączone** w usługi ServiceNow:

   1. Wybierz pozycję **zabezpieczenia systemu**  >  **Ustawienia wysokie zabezpieczenia**  >  **wymagają uwierzytelniania podstawowego dla przychodzących żądań schematu**.
   2. Wybieranie **Właściwości systemu**  >  **usługi sieci Web**  >  **wymagają podstawowej autoryzacji dla przychodzących żądań SOAP**.
     
   > [!IMPORTANT]
   > Jeśli te ustawienia są *włączone* , aparat aprowizacji nie będzie mógł komunikować się z usługą usługi ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usługi ServiceNow z galerii aplikacji usługi Azure AD

Dodaj usługi ServiceNow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do usługi ServiceNow. Jeśli wcześniej skonfigurowano usługi ServiceNow do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do usługi ServiceNow należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do usługi ServiceNow 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla usługi ServiceNow w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ServiceNow**.

    ![Link do usługi ServiceNow na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź poświadczenia administratora usługi ServiceNow i nazwę użytkownika. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi usługi ServiceNow ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawia stronę aprowizacji usługi, na której można wprowadzić poświadczenia administratora.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do usługi ServiceNow**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie usługi ServiceNow for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API usługi ServiceNow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do usługi ServiceNow**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usługi ServiceNow dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usługi ServiceNow, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić usługi ServiceNow, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* **InvalidLookupReference:** W przypadku inicjowania obsługi niektórych atrybutów, takich jak dział i lokalizacja w usługi ServiceNow, wartości muszą już istnieć w tabeli referencyjnej w usługi ServiceNow. Na przykład możesz mieć dwie lokalizacje (Seattle, Los Angeles) i trzy działy (sprzedaż, finanse, Marketing) w tabeli **Wstaw nazwę tabeli** w usługi ServiceNow. Jeśli próbujesz zainicjować obsługę administracyjną użytkownika, gdzie jego dział jest "sprzedaż", a lokalizacja to "Seattle", zostanie ona zainicjowana pomyślnie. W przypadku próby aprowizacji użytkownika z działem "sprzedaż" i lokalizacją "LA" użytkownik nie zostanie zainicjowany. Lokalizację LA należy dodać do tabeli referencyjnej w usługi ServiceNow lub atrybut użytkownika w usłudze Azure AD musi zostać zaktualizowany w taki sposób, aby był zgodny z formatem w usługi ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Przejrzyj [mapowania atrybutów](../app-provisioning/customize-application-attributes.md) , aby zidentyfikować pasujący atrybut. Ta wartość musi być obecna dla użytkownika lub grupy, którą próbujesz udostępnić. 
* Zapoznaj się z [interfejsem API protokołu SOAP usługi ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) , aby poznać wszelkie wymagania lub ograniczenia (na przykład format, aby określić kod kraju dla użytkownika).
* Żądania aprowizacji są domyślnie wysyłane do protokołu https://{nazwa wystąpienia}. Service-Now. com/{Table-Name}. Jeśli potrzebujesz niestandardowego adresu URL dzierżawy, możesz podać cały adres URL w polu Nazwa wystąpienia.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Ten błąd wskazuje na problem z komunikacją z wystąpieniem usługi ServiceNow. Sprawdź podwójne, aby upewnić się, że następujące ustawienia są *wyłączone* w usługi ServiceNow:
   
   1. Wybierz pozycję **zabezpieczenia systemu**  >  **Ustawienia wysokie zabezpieczenia**  >  **wymagają uwierzytelniania podstawowego dla przychodzących żądań schematu**.
   2. Wybieranie **Właściwości systemu**  >  **usługi sieci Web**  >  **wymagają podstawowej autoryzacji dla przychodzących żądań SOAP**.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)