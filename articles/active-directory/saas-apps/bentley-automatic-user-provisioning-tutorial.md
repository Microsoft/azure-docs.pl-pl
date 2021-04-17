---
title: 'Samouczek: konfigurowanie rozwiązania Doma — automatyczne aprowizowanie użytkowników w celu automatycznego aprowizowania użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i co wyłączać aprowizowanie kont użytkowników z usługi Azure AD do aplikacji Automatyczne aprowizowanie użytkowników.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 08778fff-f252-45c2-95d4-cc640c288af3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2021
ms.author: Zhchia
ms.openlocfilehash: 0d5aaee56606abcc886310049ad5a087f2aedb5e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590325"
---
# <a name="tutorial-configure-bentley---automatic-user-provisioning-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie rozwiązania Doma — automatyczne aprowizowanie użytkowników na celu automatyczne aprowizowanie użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w rozwiązaniu Do automatycznego aprowizowania użytkowników, jak i w usłudze Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje użytkowników i grupy do [funkcji Automatyczne](https://www.bentley.com) aprowizowanie użytkowników przy użyciu usługi Azure AD Provisioning i co umożliwia jej coprowizowanie. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w rozwiązaniu Doma — automatyczna aprowzowanie użytkowników
> * Usuwanie użytkowników w 2018 r. — automatyczne aprowizowanie użytkowników, którzy nie wymagają już dostępu
> * Utrzymuj synchronizację atrybutów użytkownika między usługą Azure AD i pakietem Funkcji automatycznego aprowizowania użytkowników
> * Aprowizowanie grup i członkoseń w grupach w rozwiązaniu Doma — automatyczne aprowizowanie użytkowników

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z uprawnieniami do konfigurowania aprowizowania (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). [](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 
* Konto federacyjna z programem AIMS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowe między usługą Azure AD a usługą Automatyczne aprowizowanie użytkowników.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-bentley---automatic-user-provisioning-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie rozwiązania Rozwiązania — automatyczne aprowizowanie użytkowników w celu obsługi aprowności w usłudze Azure AD

W celu informacji o adresie URL dzierżawy i [tokenie](https://communities.bentley.com/communities/other_communities/licensing_cloud_and_web_services/w/wiki/52836/microsoft-azure-ad-automatic-user-provisioning-configuration) tajnym należy się sprowizować do zespołu pomocy technicznej usługi User Provisioning firmy Aprowizowanie użytkowników w Firmie. Te wartości zostaną wprowadzone na karcie Aprowizowanie w aplikacji Rozwiązania w Azure Portal.

## <a name="step-3-add-bentley---automatic-user-provisioning-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie aplikacjiJące — automatyczne aprowizowanie użytkowników z galerii aplikacji usługi Azure AD

Dodaj pozycję Rozwiązanie — automatyczne aprowizowanie użytkowników z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizowaniem w aplikacji Do usługi Automatyczne aprowizowanie użytkowników. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do funkcji Automatyczne aprowizowanie użytkowników należy wybrać rolę inną niż **Dostęp domyślny.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bentley---automatic-user-provisioning"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników w rozwiązaniu Doma — automatyczne aprowizowanie użytkowników 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bentley---automatic-user-provisioning-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników w usłudze Azure AD— automatyczne aprowizowanie użytkowników:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Rozwiązania — automatyczne aprowizowanie użytkowników.**

    ![Link Do aplikacji — Automatyczne aprowizowanie użytkowników na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczna karta Aprowizowanie](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora wprowadź** swój adres URL dzierżawy automatycznego aprowizowania użytkowników i token tajny. Kliknij **pozycję Testuj połączenie,** aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Domi - Automatic User Provisioning. Jeśli połączenie nie powiedzie się, upewnij się, że konto Rozwiązania — automatyczne aprowizowanie użytkowników ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania wybierz** pozycję Synchronizuj użytkowników Azure Active Directory **z użytkownikiem — automatyczna aprownia użytkowników.**

9. W sekcji Mapowanie atrybutów przejrzyj atrybuty użytkownika synchronizowane z usługi  Azure AD do usługi One - Automatic User Provisioning. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w funkcji Automatyczne aprowizowanie użytkowników dla operacji aktualizacji. Jeśli zdecydujesz się na zmianę pasującego [atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)docelowego, musisz upewnić się, że interfejs API Funkcji — automatyczna aprownia użytkowników obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane w przypadku filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |title|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |preferredLanguage|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |addresses[type eq "work"].streetAddress|Ciąg|
   |addresses[type eq "work"].locality|Ciąg|
   |addresses[type eq "work"].region|Ciąg|
   |addresses[type eq "work"].postalCode|Ciąg|
   |addresses[type eq "work"].country|Ciąg|
   |phoneNumbers[type eq "work"].value|Ciąg|
   |externalId|Ciąg|
   |urn:ietf:params:scim:schemas:extension:Formatuj:2.0:User:isSoftDeleted|Ciąg|

10. W sekcji **Mapowania wybierz** pozycję Synchronizuj grupy Azure Active Directory do opcji **Automatyczne aprowizowanie użytkowników.**

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi Automatyczne inicjowanie obsługi użytkowników w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako właściwości **dopasowywania** są używane do dopasowywania grup w funkcji Automatyczne aprowizowanie użytkowników dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

      |Atrybut|Typ|Obsługiwane w przypadku filtrowania|
      |---|---|---|
      |displayName|Ciąg|&check;|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|
      |urn:ietf:params:scim:schemas:extension:Formatuj:2.0:Group:description|Ciąg|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę aprowizowania Usługi Azure AD dla funkcji Automatycznego aprowizowania użytkowników, zmień ustawienie **Stan** aprowności na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować w aplikacji  Centrum — automatyczne aprowizowanie użytkowników, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Ograniczenia łącznika
* Atrybut rozszerzenia przedsiębiorstwa "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager" nie jest obsługiwany i zostanie usunięty.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
