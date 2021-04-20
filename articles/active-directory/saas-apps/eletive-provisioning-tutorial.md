---
title: 'Samouczek: konfigurowanie aplikacji Eletive do automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i cofać aprowizować konta użytkowników z usługi Azure AD do aplikacji Eletive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8a775422-e6d7-4cd5-b8d1-cc8a2db24c4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2021
ms.author: Zhchia
ms.openlocfilehash: 393629be5c30eb3d97dae1de1c06e2d4cf7af94d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741944"
---
# <a name="tutorial-configure-eletive-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie aplikacji Eletive na celu automatyczną aprowizowanie użytkowników

W tym samouczku opisano kroki, które należy wykonać w usłudze Eletive i Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje i cofa aprowizowanie użytkowników i grup w [usłudze Eletive](https://app.eletive.com/) przy użyciu usługi Azure AD Provisioning Service. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w programie Eletive
> * Usuń użytkowników z programu Eletive, gdy nie wymagają już dostępu
> * Utrzymuj synchronizację atrybutów użytkownika między usługą Azure AD i usługą Eletive
> * Logowanie pojedynczej do konta Eletive (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z uprawnieniami do konfigurowania aprowizowania (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). [](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 
* Konto użytkownika w programie Eletive z dostępem do administracji.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowe między usługą Azure AD i usługą Eletive.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-eletive-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie aplikacji Eletive do obsługi aprowowania w usłudze Azure AD

1. Zaloguj się do [aplikacji Eletive](https://app.eletive.com/). Przejdź do **ustawień**  ->  **Funkcje.**

    ![Funkcje](media/eletive-provisioning-tutorial/settings.png)

2.  Włącz **integracje** i **standard SCIM 2.0.**

    ![Integracje](media/eletive-provisioning-tutorial/scim.png)

3.  Przejdź do **menu Settings**  ->  **Integrations (Integracje ustawień).**

4.  Kliknij pozycję **User Provisioning (Aprowizowanie użytkowników).**

    ![Tab](media/eletive-provisioning-tutorial/user.png)

5.  Kliknij pozycję **Połącz**.

    ![Przycisk](media/eletive-provisioning-tutorial/connect.png)

6.  Skopiuj i zapisz adres URL standardu SCIM 2.0 oraz token bearer. Te wartości zostaną wprowadzone w polach Adres URL dzierżawy i Token tajny na karcie Aprowizowanie aplikacji Eletive w Azure Portal.


## <a name="step-3-add-eletive-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie aplikacji Eletive z galerii aplikacji usługi Azure AD

Dodaj aplikację Eletive z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizością w aplikacji Eletive. Jeśli aplikacja Eletive była wcześniej konfigurowana dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do aplikacji Eletive należy wybrać rolę inną niż **Domyślny dostęp.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, możesz zaktualizować [manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać role. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. Jeśli zakres aprowizowania jest ustawiony na przypisanych użytkowników i grupy, możesz kontrolować zakres, przypisując do aplikacji jednego lub dwóch użytkowników lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-eletive"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników w aplikacji Eletive 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-eletive-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla aplikacji Eletive w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Eletive**.

    ![Link aplikacji Eletive na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczna karta Aprowizowanie](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora wprowadź** adres URL dzierżawy Eletive i token tajny. Kliknij **pozycję Testuj połączenie,** aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Eletive. Jeśli połączenie nie powiedzie się, upewnij się, że konto Eletive ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników z Eletive.**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Eletive w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako dopasowania **właściwości** są używane do dopasowania kont użytkowników w Eletive operacji aktualizacji. Jeśli zdecydujesz się zmienić pasujący [atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), musisz upewnić się, że interfejs API Eletive obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane w przypadku filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |emails[type eq "work"].value|Ciąg|
   |externalId|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |preferredLanguage|Ciąg|
   |userType|Ciąg|
   |urn:ietf:params:scim:schemas:extension:eletive:2.0:User:participateInSurvey|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę aprowizowania Azure  AD dla aplikacji Eletive, zmień stan aprowności na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować  w aplikacji Eletive, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="more-resources"></a>Więcej zasobów

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
