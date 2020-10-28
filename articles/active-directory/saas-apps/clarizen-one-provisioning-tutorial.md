---
title: 'Samouczek: Konfigurowanie Clarizen jeden dla automatycznej aprowizacji użytkowników w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w celu Clarizen jednego.
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
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795376"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Clarizen jeden dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach obu Clarizen i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup, aby [Clarizen je](https://www.clarizen.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Clarizen jeden
> * Usuń użytkowników w Clarizen jeden, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkownika między usługą Azure AD a Clarizen
> * Udostępnianie grup i członkostwa w grupach w Clarizen jeden
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) jednokrotne do Clarizen (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto użytkownika w Clarizen jednym z uprawnieniami **użytkownika integracji** i **administratora uproszczonego** [permissions](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i Clarizen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Krok 2. Skonfiguruj usługę Clarizen, aby obsługiwać obsługę administracyjną w usłudze Azure AD
1. Wybierz jeden z czterech następujących adresów URL dzierżawców według Clarizen jednego środowiska i centrum danych:
      * Centrum danych produkcyjnych USA: https://servicesapp2.clarizen.com/scim/v2
      * Produkcyjne centrum danych w Unii Europejskiej: https://serviceseu1.clarizen.com/scim/v2
      * Centrum danych w piaskownicy USA: https://servicesapp.clarizentb.com/scim/v2
      * Centrum danych w piaskownicy UE: https://serviceseu.clarizentb.com/scim/v2

2. Generuj [klucz interfejsu API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji Clarizen jednej aplikacji w Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj Clarizen jeden z galerii aplikacji usługi Azure AD

Dodaj Clarizen jeden z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Clarizen. Jeśli wcześniej skonfigurowano Clarizen jeden dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do Clarizen jednej, należy wybrać rolę inną niż **domyślny dostęp** . Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w celu Clarizen jednego 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla Clarizen w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw** , a następnie **Wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Clarizen jeden** .

    ![Łącze Clarizen 1 na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja** .

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny** .

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL Clarizen jednego dzierżawy i token tajny. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Clarizen. Jeśli połączenie nie powiedzie się, upewnij się, że Clarizen jedno konto ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu** .

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz** .

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Clarizen jeden** .

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby Clarizen je w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Clarizen jeden dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że Clarizen jeden interfejs API obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |title|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |wiadomości e-mail [Type EQ "Home"]. Value|String|
   |wiadomości e-mail [Type EQ "Other"]. Value|String|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |name.honorificPrefix|Ciąg|
   |Name. honorificSuffix|String|
   |adresy [Type EQ "Other"]. sformatowane|String|
   |adresy [typ EQ "Work"]. sformatowane|String|
   |addresss [Type EQ "Work"]. Country|String|
   |addresss [Type EQ "Work"]. region|String|
   |adresy [typ EQ "Work"]. locale|String|
   |addresss [Type EQ "Work"]. KodPocztowy|String|
   |adresy [typ EQ "Work"]. streetAddress|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|String|
   |numer telefonu [typ EQ "Home"]. Value|String|
   |braky [Type EQ "Other"]. Value|String|
   |numer telefonu [typ EQ "pager"]. wartość|String|
   |externalId|Ciąg|
   |nickName|Ciąg|
   |locale|Ciąg|
   |role [podstawowa EQ "true". Type]|String|
   |role [podstawowa EQ "true". Value]|Ciąg|
   |timezone|Ciąg|
   |userType|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|String|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: dzielenie|String|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Clarizen jeden** .

11. Przejrzyj atrybuty grup, które są synchronizowane z usługą Azure AD, aby Clarizen je w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Clarizen jeden dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning for Clarizen, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby Clarizen je, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz** .

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
Podczas przypisywania użytkownika do aplikacji Clarizen z galerii wybierz opcję tylko rola **użytkownika** . Podane poniżej role są nieprawidłowe.

* Administrator (Administrator)
* Użytkownik raportu wiadomości e-mail
* Użytkownik zewnętrzny
* Użytkownik finansowy
* Użytkownik społecznościowy
* Administrator
* Czas & użytkownika wydatków



## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
