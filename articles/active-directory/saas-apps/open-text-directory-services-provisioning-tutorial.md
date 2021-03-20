---
title: 'Samouczek: Konfigurowanie usług katalogowych OpenText dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do usług katalogowych OpenText.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 2f31eddab1070d073d3fd5a4761dad597e42a2e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181893"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usług OpenText Directory dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach usług katalogowych OpenText i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługi Azure AD automatycznie inicjuje i usuwają użytkownikom i grupom OpenText usług katalogowych przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w usługach katalogowych OpenText
> * Usuń użytkowników w usługach katalogowych OpenText, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługami Azure AD i OpenText Directory
> * Udostępnianie grup i członkostw w grupach w usługach OpenText Directory
> * [Logowanie](./opentext-directory-services-tutorial.md) jednokrotne do usług katalogowych OpenText (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* OTDSa instalacja dostępna w usłudze Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługami Azure AD i OpenText Directory](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usług katalogowych OpenText w celu obsługi aprowizacji za pomocą usługi Azure AD

> [!NOTE]
> Poniższe kroki dotyczą instalacji usług katalogowych OpenText. Nie dotyczą one dzierżaw OpenText CoreShare ani OpenText OT2.

1. Tworzenie dedykowanego poufnego **klienta OAuth**.
2. Ustaw długi **okres istnienia tokenu dostępu**.

      ![Okres istnienia tokenu dostępu](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Nie określaj żadnych adresów URL przekierowań. Nie są one wymagane. 
4. OTDS wygeneruje i wyświetli **klucz tajny klienta**. Zapisz **Identyfikator klienta** i **klucz tajny klienta** w bezpiecznej lokalizacji.

      ![Klucz tajny klienta](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Utwórz partycję dla użytkowników i grup, które mają być synchronizowane z usługi Azure AD.

      ![Strona partycji](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Przyznaj prawa administracyjne do klienta OAuth utworzonego na partycji, która będzie używana dla synchronizowanych użytkowników i grup usługi Azure AD.    
      * Akcje > partycji — > edytowanie administratorów

      ![Strona administratora](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Należy pobrać i skonfigurować token tajny w usłudze Azure AD. W tym celu można użyć dowolnej aplikacji klienckiej HTTP. Poniżej przedstawiono kroki do pobrania przy użyciu aplikacji interfejsu API struktury Swagger zawartej w OTDS.
      * W przeglądarce internetowej przejdź do pozycji {OTDS URL}/otdsws/OAuth2
      * Przejdź do/token i kliknij ikonę kłódki w prawym górnym rogu. Wprowadź identyfikator klienta OAuth i wpis tajny pobrane wcześniej jako nazwę użytkownika i hasło. Kliknij przycisk Autoryzuj.

      ![Przycisk autoryzacji](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Wybierz **client_credentials** dla grant_type i kliknij przycisk **Execute (wykonaj**).

      ![Wykonaj Buton](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Token dostępu w odpowiedzi powinien być używany w polu **token tajny** w usłudze Azure AD.

      ![Token dostępu](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usług katalogowych OpenText z galerii aplikacji usługi Azure AD

Dodaj usługi katalogowe OpenText z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą administracyjną do usług katalogowych OpenText. Jeśli wcześniej skonfigurowano usługi OpenText Directory dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do usług katalogowych OpenText należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w usługach OpenText Directory 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla usług katalogowych OpenText w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **OpenText usługi katalogowej**.

    ![Link usług katalogowych OpenText na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy usług katalogowych OpenText
   * Adres URL dzierżawy innej niż specifc: {OTDS URL}/scim/{partitionName}
   * Określony adres URL dzierżawy: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Wprowadź token tajny pobrany z kroku 2. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługami OpenText Directory. Jeśli połączenie nie powiedzie się, upewnij się, że konto usług katalogowych OpenText ma uprawnienia administratora, a następnie spróbuj ponownie.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby OpenText usługi katalogowe**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do usług katalogowych OpenText w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w usługach OpenText Directory dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API usług OpenText Directory obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |title|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |adresy [typ EQ "Work"]. sformatowane|Ciąg|
   |adresy [typ EQ "Work"]. streetAddress|Ciąg|
   |adresy [typ EQ "Work"]. locale|Ciąg|
   |addresss [Type EQ "Work"]. region|Ciąg|
   |addresss [Type EQ "Work"]. KodPocztowy|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|
   |numer telefonu [typ EQ "Fax"]. wartość|Ciąg|
   |externalId|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Dokumentacja| 

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do usług katalogowych OpenText**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do usług katalogowych OpenText w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usługach OpenText Directory dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usług katalogowych OpenText, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić OpenText usług katalogowych, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)