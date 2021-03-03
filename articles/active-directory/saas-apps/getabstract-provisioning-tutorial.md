---
title: 'Samouczek: Konfigurowanie getAbstract dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do metody getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 25253e9a302a34fb473da63ad4cad562d6302a8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651749"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie getAbstract dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach metod getAbstract i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup [przy użyciu usługi](https://www.getabstract.com) Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Utwórz użytkowników w getAbstract.
> * Usuń użytkowników z metody getAbstract, gdy nie wymagają już dostępu.
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i getAbstract.
> * Inicjowanie obsługi administracyjnej grup i członkostw w grupach w programie getAbstract.
> * [Logowanie](getabstract-tutorial.md) jednokrotne do getAbstract (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Dzierżawa getAbstract (licencja getAbstract Corporate).
* Logowanie jednokrotne włączone w dzierżawie usługi Azure AD i w dzierżawie getAbstract.
* Zatwierdzenie i Standard scim Włączanie dla elementu getAbstract (Wyślij wiadomość e-mail do b2b.itsupport@getabstract.com ).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ dane, które mają być [mapowane między usługą Azure AD i getAbstract](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie funkcji getAbstract w celu obsługi aprowizacji za pomocą usługi Azure AD
1. Zaloguj się do getAbstract
2. Kliknij ikonę Ustawienia znajdującą się w prawym górnym rogu, a następnie kliknij pozycję **My Central administrator** ,
 
    ![getAbstract my administrator centralny](media/getabstract-provisioning-tutorial/my-account.png)

3. Znajdź i kliknij opcję **administrator Standard scim**
 
    ![Standard scim administrator getAbstract](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. Kliknij przycisk **Przejdź** 

    ![Identyfikator klienta getAbstract Standard scim](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. Kliknij pozycję **Generuj nowy token**

    ![getAbstract Standard scim token 1](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. Jeśli na pewno chcesz kliknąć przycisk **Generuj nowy token** . W przeciwnym razie kliknij przycisk **Anuluj**

    ![getAbstract Standard scim token 2](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. Na koniec możesz kliknąć ikonę Kopiuj do Schowka lub wybrać cały token i skopiować go. Należy również pamiętać, że adres URL dzierżawy/podstawowy to `https://www.getabstract.com/api/scim/v2` . Te wartości zostaną wprowadzone w polu **Secret token** * i **adres URL dzierżawy** * na karcie aprowizacji aplikacji getAbstract w Azure Portal.

    ![getAbstract Standard scim token 3](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj getAbstract z galerii aplikacji usługi Azure AD

Dodaj getAbstract z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do elementu getAbstract. Jeśli wcześniej skonfigurowano getAbstract dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do metody getAbstract należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w celu getAbstract 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej dla elementu getAbstract w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **getAbstract**.

    ![Link getAbstract na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL i token klucza tajnego dla dzierżawy. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z serwerem getAbstract Jeśli połączenie nie powiedzie się, upewnij się, że konto getAbstract ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby usunąć abstrakcyjne**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do getAbstract w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w getAbstract dla operacji Update. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API getAbstract obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |externalId|Ciąg|
   |preferredLanguage|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do getAbstract**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do getAbstract w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w getAbstract dla operacji Update. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
    |---|---|---|
    |displayName|Ciąg|&check;|
    |externalId|Ciąg|
    |elementy członkowskie|Tematy pomocy|
12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla elementu getAbstract, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby getAbstract, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)