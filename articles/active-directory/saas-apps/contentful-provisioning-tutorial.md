---
title: 'Samouczek: Konfigurowanie zawartości dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: 4ff08e51f6e3b2ae72da43052c25046be8bb5397
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352154"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zawartości dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w przypadku zawartości, jak i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługi Azure AD automatycznie inicjuje i usuwają [zawartość](https://www.contentful.com/) użytkowników i grup przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w zawartości
> * Usuń użytkowników w zawartości, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i zawartością
> * Udostępnianie grup i członkostwa w grupach w zawartości
> * [Logowanie](./contentful-tutorial.md) jednokrotne do zawartości (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto organizacji z zawartością z subskrypcją, która obsługuje Inicjowanie obsługi Standard scim. Uzyskaj dostęp do zawartości za pośrednictwem [support@contentful.com](mailto:support@contentful.com) usługi, jeśli masz pytania dotyczące subskrypcji organizacji.
 
## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ dane, które mają być [mapowane między usługą Azure AD i zawartością](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie zawartości w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Utwórz konto **użytkownika usługi** w zawartości. Wszystkie uprawnienia do aprowizacji na platformie Azure zostaną udostępnione za pomocą tego konta. Zalecane jest wybranie **właściciela** jako roli organizacji dla tego konta.

2. Zaloguj się do zawartości jako **użytkownik usługi** utworzony w poprzednim kroku.

3. Przejdź do **lewego suwaka**  ->  **Ustawienia organizacji opcje**  ->  **dostępu narzędzia**  ->  **udostępniania użytkowników**.

    ![Menu](media/contentful-provisioning-tutorial/access.png)

4. Skopiuj i Zapisz **adres URL Standard scim**. Ta wartość zostanie wprowadzona na karcie aprowizacji aplikacji zawartości w Azure Portal.

5. Kliknij pozycję **Generuj osobisty token dostępu**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. W oknie modalnym Nadaj swojemu osobistemu tokenowi dostępu nazwę i kliknij pozycję "Generuj".
    
7. Zostanie wygenerowany **adres URL Standard scim** i **token tajny** . Skopiuj i Zapisz te wartości. Te wartości zostaną wprowadzone na karcie aprowizacji aplikacji zawartości w Azure Portal.

    ![access](media/contentful-provisioning-tutorial/token.png)


Skontaktuj się z Tobą, [support@contentful.com](mailto:support@contentful.com) Jeśli masz pytania dotyczące konfigurowania aprowizacji w konsoli administracyjnej z zawartością.


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie zawartości z galerii aplikacji usługi Azure AD

Dodaj zawartość z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą zawartości. Jeśli wcześniej skonfigurowano zawartość dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do zawartości należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do zawartości 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną zawartości w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zawartość**.

    ![Link zawartość na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź swój zawartość adres URL dzierżawy i token tajny. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z zawartością. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto zawartości ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do zawartości**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do zawartości w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w zawartości dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API zawartości obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do zawartości**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do zawartości w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w zawartości dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |displayName|Ciąg|&check;|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla zawartości, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, dla których chcesz udostępnić zawartość, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)