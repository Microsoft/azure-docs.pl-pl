---
title: 'Samouczek: Konfigurowanie GroupTalk dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735762"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie GroupTalk na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie GroupTalk i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [GroupTalk](https://www.grouptalk.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w GroupTalk
> * Usuń użytkowników w GroupTalk, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i GroupTalk
> * Udostępnianie grup i członkostwa w grupach w GroupTalk

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto użytkownika w GroupTalk z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie GroupTalk w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Skontaktuj się z pomocą techniczną support@grouptalk.com usługi GroupTalk pod **nazwą dzierżawy** i **identyfikatorem** , który chcesz zintegrować z usługą Azure AD.
2. Po otrzymaniu powiadomienia o gotowości niezbędnej konfiguracji integracji z usługą Azure AD Zaloguj się do GroupTalk administratora i przejdź do widoku organizacji. 
3. Element konfiguracji integracji usługi Azure AD powinien być widoczny. Kliknij go, aby zweryfikować nazwę i **Identyfikator** **dzierżawy** w celu uzyskania **tokenu JWT (klucz tajny)**. 
4. Adres URL dzierżawy GroupTalk to `https://api.grouptalk.com/api/scim/` . **Adres URL dzierżawy** i **token tajny** , pobrane w poprzednim kroku, zostaną wprowadzone na karcie aprowizacji aplikacji GroupTalk w Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie GroupTalk z galerii aplikacji usługi Azure AD

Dodaj **GroupTalk** z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do GroupTalk.

1. Kliknij przycisk **zarejestruj się w usłudze GroupTalk** , który spowoduje przekierowanie do aplikacji administracyjnej GroupTalk.
2. Jeśli użytkownik jest już zalogowany w usłudze GroupTalk, Wyloguj się, aby przejść do ekranu logowania. Wybierz kartę Azure AD, a następnie kliknij przycisk **Zaloguj się** .

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Zaloguj się przy użyciu konta administratora usługi AD i zaakceptuj prawa dostępu do aplikacji GroupTalk. Po wykonaniu tej czynności zostanie wyświetlony komunikat o błędzie informujący o tym, że użytkownik nie jest obecny. Jest to oczekiwane, ponieważ użytkownik nie ma jeszcze GroupTalk, ale teraz dodaliśmy GroupTalk do dzierżawy.
4. Wróć do Azure Portal i sprawdź, czy **GroupTalk** jest teraz dodany do aplikacji dla przedsiębiorstw.

Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do GroupTalk należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do GroupTalk 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla GroupTalk w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **GroupTalk**.

    ![Link GroupTalk na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy usługi GroupTalk i token tajny pobrany wcześniej z kroku 2. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą GroupTalk. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi GroupTalk ma uprawnienia administratora, a następnie spróbuj ponownie. Zawsze możesz uzyskać nowy token tajny, zgodnie z opisem w sekcji Krok 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do GroupTalk**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do GroupTalk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie GroupTalk for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API GroupTalk obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |phoneNumbers[type eq "mobile"].value|Ciąg|&check;|
   |emails[type eq "work"].value|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |externalId|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Organization|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: User: Label1|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: User: etykiety 2|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: User: etykiety 3|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: User: Label4|Ciąg|
   |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: User: Label5|Ciąg|


10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do GroupTalk**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do GroupTalk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w GroupTalk dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |displayName|Ciąg|&check;|
      |elementy członkowskie|Tematy pomocy|
      |externalId|Ciąg|      
      |urn: IETF: params: Standard scim: schematy: Extension: grouptalk: 2.0: Group: Description|Ciąg|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla GroupTalk, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić GroupTalk, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).
4. Można skontaktować się z pomocą techniczną usługi GroupTalk w celu uzyskania dodatkowych dzienników aprowizacji skonfigurowanych jako raporty niestandardowe w Administratorze GroupTalk. Mogą to być dodatkowe wskazówki, w przypadku których nie można prawidłowo zainicjować obsługi administracyjnej użytkowników i grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)