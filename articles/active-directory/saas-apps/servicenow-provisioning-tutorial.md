---
title: 'Samouczek: Konfigurowanie usługi ServiceNow dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z usługi Azure AD do usługi ServiceNow.
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
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539047"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi ServiceNow na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano czynności wykonywane zarówno w usługi ServiceNow, jak i w Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługi Azure AD automatycznie inicjuje i tymczasowo zastrzega użytkownikom i grupom [usługi ServiceNow](https://www.servicenow.com/) za pomocą usługi Azure AD Provisioning. 

Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w usługi ServiceNow
> * Usuń użytkowników w usłudze usługi ServiceNow, gdy nie potrzebują już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i usługi ServiceNow
> * Udostępnianie grup i członkostwa w grupach w usługi ServiceNow
> * Zezwalaj na [Logowanie](servicenow-tutorial.md) jednokrotne do usługi ServiceNow (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny)
* [Wystąpienie usługi ServiceNow](https://www.servicenow.com/) o wartości Calgary lub wyższej
* [Wystąpienie usługi ServiceNow Express](https://www.servicenow.com/) w wersji Helsinki lub nowszej
* Konto użytkownika w usługi ServiceNow z rolą administratora

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i usługi ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi ServiceNow w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zidentyfikuj nazwę wystąpienia usługi ServiceNow. Nazwę wystąpienia można znaleźć w adresie URL używanym do uzyskiwania dostępu do usługi ServiceNow. W poniższym przykładzie nazwa wystąpienia to **dev35214**.

   ![Zrzut ekranu pokazujący wystąpienie usługi ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Uzyskaj poświadczenia dla administratora w usługi ServiceNow. Przejdź do profilu użytkownika w programie usługi ServiceNow i sprawdź, czy użytkownik ma rolę administratora. 

   ![Zrzut ekranu przedstawiający rolę administratora usługi ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usługi ServiceNow z galerii aplikacji usługi Azure AD

Dodaj usługi ServiceNow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do usługi ServiceNow. Jeśli wcześniej skonfigurowano usługi ServiceNow do logowania jednokrotnego (SSO), możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas testowania integracji. [Dowiedz się więcej o dodawaniu aplikacji z galerii](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definiowanie, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz użyć [kroków, aby przypisać użytkowników i grupy do aplikacji](../manage-apps/assign-user-or-group-access-portal.md). Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz [użyć filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Pamiętaj o następujących wskazówkach:

* W przypadku przypisywania użytkowników i grup do usługi ServiceNow należy wybrać rolę inną niż domyślny dostęp. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do usługi ServiceNow 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w programie TestApp. Konfigurację można oprzeć na przypisaniach użytkowników i grup w usłudze Azure AD.

Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla usługi ServiceNow w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **usługi ServiceNow**.

    ![Zrzut ekranu przedstawiający listę aplikacji.](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź poświadczenia administratora usługi ServiceNow i nazwę użytkownika. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi usługi ServiceNow ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Zrzut ekranu przedstawiający stronę aprowizacji usługi, na której można wprowadzać poświadczenia administratora.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu pokazujący pola dla wiadomości e-mail z powiadomieniem.](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do usługi ServiceNow**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie usługi ServiceNow for Updates. 

   Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API usługi ServiceNow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. 
   
   Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do usługi ServiceNow**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do usługi ServiceNow w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usługi ServiceNow dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

12. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usługi ServiceNow, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i grupy, które chcesz udostępnić usługi ServiceNow, wybierając odpowiednie wartości z **zakresu** w sekcji **Ustawienia** .

    ![Zrzut ekranu pokazujący opcje zakresu aprowizacji.](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający przycisk służący do zapisywania konfiguracji aprowizacji.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne cykle. Kolejne cykle są wykonywane co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

- Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
- Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
- Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. [Dowiedz się więcej na temat stanów kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* W przypadku aprowizacji określonych atrybutów (takich jak **dział** i **Lokalizacja**) w usługi ServiceNow wartości muszą już istnieć w tabeli referencyjnej w usługi ServiceNow. Jeśli nie, zostanie wyświetlony błąd **InvalidLookupReference** . 

   Na przykład możesz mieć dwie lokalizacje (Seattle, Los Angeles) i trzy działy (sprzedaż, finanse, Marketing) w pewnej tabeli w usługi ServiceNow. Jeśli spróbujesz zainicjować obsługę użytkownika, którego dział jest "sprzedaż" i której lokalizacją jest "Seattle", ten użytkownik zostanie pomyślnie zainicjowany. Jeśli spróbujesz zainicjować obsługę użytkownika, którego dział jest "sprzedaż" i której lokalizacją jest "LA", użytkownik nie zostanie zainicjowany. Lokalizacja "LA" musi zostać dodana do tabeli referencyjnej w usługi ServiceNow, lub atrybut użytkownika w usłudze Azure AD musi zostać zaktualizowany, aby był zgodny z formatem w usługi ServiceNow. 
* Jeśli wystąpi błąd **EntryJoiningPropertyValueIsMissing** , przejrzyj [mapowania atrybutów](../app-provisioning/customize-application-attributes.md) , aby zidentyfikować pasujący atrybut. Ta wartość musi być obecna dla użytkownika lub grupy, którą próbujesz udostępnić. 
* Aby poznać wymagania lub ograniczenia (na przykład format określający kod kraju dla użytkownika), należy zapoznać się z [interfejsem API protokołu SOAP usługi ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Żądania aprowizacji są domyślnie wysyłane do protokołu https://{nazwa wystąpienia}. Service-Now. com/{Table-Name}. Jeśli potrzebujesz niestandardowego adresu URL dzierżawy, możesz podać cały adres URL jako nazwę wystąpienia.
* Błąd **ServiceNowInstanceInvalid** wskazuje na problem z komunikacją z wystąpieniem usługi ServiceNow. Oto tekst błędu:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Jeśli występują problemy z połączeniem testowym, wybierz pozycję **nie** dla następujących ustawień w programie usługi ServiceNow:
   
  - **Zabezpieczenia systemu**  >  Ustawienia wysokiego poziomu **zabezpieczeń**  >  **Wymagaj uwierzytelniania podstawowego dla przychodzących żądań schematu**
  - **Właściwości systemu**  >  **Usługi**  >  sieci Web **Wymagaj podstawowej autoryzacji dla przychodzących żądań SOAP**

     ![Zrzut ekranu przedstawiający opcję autoryzacji żądań SOAP.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Jeśli nadal nie możesz rozwiązać problemu, skontaktuj się z pomocą techniczną usługi ServiceNow i poproś o włączenie debugowania protokołu SOAP, aby pomóc w rozwiązywaniu problemów. 

* Usługa Azure AD Provisioning aktualnie działa pod określonymi [zakresami adresów IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). W razie potrzeby można ograniczyć inne zakresy adresów IP i dodać te określone zakresy adresów IP do listy dozwolonych aplikacji. Ta technika zezwala na przepływ ruchu z usługi Azure AD Provisioning do aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
