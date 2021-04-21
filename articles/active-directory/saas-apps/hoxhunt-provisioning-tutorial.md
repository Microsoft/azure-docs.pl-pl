---
title: 'Samouczek: konfigurowanie aplikacji Hoxhunt do automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i deprowizować konta użytkowników z usługi Azure AD do aplikacji Hoxhunt.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 24fbe0a4-ab2d-4e10-93a6-c87d634ffbcf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2021
ms.author: Zhchia
ms.openlocfilehash: db33cc43419b4228ca270d3a69c0e88de2c05638
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752052"
---
# <a name="tutorial-configure-hoxhunt-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie aplikacji Hoxhunt do automatycznego aprowizowania użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w usłudze Hoxhunt, jak i Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje użytkowników i grupy do [hoxhunt](https://www.hoxhunt.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w aplikacji Hoxhunt
> * Usuń użytkowników w hoxhunt, gdy nie wymagają już dostępu
> * Synchronizuj atrybuty użytkownika między usługami Azure AD i Hoxhunt
> * [Logowanie pojedyncze do konta](hoxhunt-tutorial.md) Hoxhunt (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z uprawnieniami do konfigurowania aprowizowania (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). [](../roles/permissions-reference.md) 
* Dzierżawa hoxhunt.
* Klucz interfejsu API standardu SCIM i adres URL punktu końcowego standardu SCIM dla organizacji (skonfigurowane przez obsługę systemu Hoxhunt).
## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, jakie dane mają [być mapowe między usługą Azure AD i usługą Hoxhunt.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-hoxhunt-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie aplikacji Hoxhunt do obsługi aprowizowania w usłudze Azure AD
Skontaktuj się z pomocą techniczną firmy [Hoxhunt](mailto:support@hoxhunt.com) w celu otrzymania klucza interfejsu API SCIM i adresu URL punktu końcowego SCIM w celu skonfigurowania aplikacji Hoxhunt do obsługi aprowności w usłudze Azure AD.
## <a name="step-3-add-hoxhunt-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie aplikacji Hoxhunt z galerii aplikacji usługi Azure AD

Dodaj aplikację Hoxhunt z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizowaniem w usłudze Hoxhunt. Jeśli wcześniej konfigurowano aplikację Hoxhunt dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do aplikacji Hoxhunt należy wybrać rolę inną niż **Dostęp domyślny.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-hoxhunt"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników w hoxhunt 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hoxhunt-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla aplikacji Hoxhunt w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Hoxhunt**.

    ![Link do aplikacji Hoxhunt na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczna karta Aprowizowanie](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora wprowadź** adres URL dzierżawy Hoxhunt i token tajny. Kliknij **pozycję Testuj połączenie,** aby upewnić się, że usługa Azure AD może nawiązać połączenie z platformą Hoxhunt. Jeśli połączenie nie powiedzie się, upewnij się, że konto Hoxhunt ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników z hoxhunt.**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Hoxhunt w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w hoxhunt dla operacji aktualizacji. Jeśli zdecydujesz się zmienić pasujący atrybut [docelowy,](../app-provisioning/customize-application-attributes.md)musisz upewnić się, że interfejs API Hoxhunt obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane filtrowanie|
   |---|---|---|
   |userName|Ciąg|&check;|
   |emails[type eq "work"].value|Ciąg|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Ciąg|
   |addresses[type eq "work"].country|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę aprowizowania Usługi Azure  AD dla platformy Hoxhunt, zmień stan aprowowania na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować w  aplikacji Hoxhunt, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Dziennik zmian
* 2021-04-20 — dodano obsługę atrybutu "preferredLanguage" i rozszerzenia przedsiębiorstwa "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division".

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)