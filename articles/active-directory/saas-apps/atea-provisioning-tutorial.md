---
title: 'Samouczek: Konfigurowanie Atea dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do Atea.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 51410bd86fa9679aea76f6d5c48f267ddec79026
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430700"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Atea na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie Atea i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup [Atea](https://www.atea.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje dotyczące działania tej usługi, jak to działa i często zadawanych pytań, zapoznaj się [z automatyzacją aprowizacji użytkowników i cofaj obsługę administracyjną, aby SaaS aplikacje za pomocą Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w Atea
> * Usuń użytkowników w Atea, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Atea

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto użytkownika w Atea z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i Atea](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie Atea w celu obsługi aprowizacji za pomocą usługi Azure AD

Aby skonfigurować intranet tęczówki do obsługi aprowizacji za pomocą usługi Azure AD, należy uzyskać **adres URL dzierżawy** i **token tajny** przez upuszczenie poczty do [zespołu pomocy technicznej Atea](mailto:servicedesk@atea.dk). Te wartości zostaną wprowadzone w polu **token tajny** i **adres URL dzierżawy** na karcie aprowizacji aplikacji Atea w Azure Portal.

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Atea z galerii aplikacji usługi Azure AD

Dodaj Atea z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Atea. Jeśli wcześniej skonfigurowano Atea na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika i grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do Atea należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać inne role. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować je, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do Atea 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w programie Atea na podstawie przypisań użytkowników i grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Atea w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Atea**.

    ![Link Atea na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wybierz pozycję **Autoryzuj**. Otwiera okno dialogowe logowania Atea w nowym oknie przeglądarki.

     ![Autoryzuj Atea](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. W oknie dialogowym logowania do Atea Zaloguj się do dzierżawy Atea i sprawdź swoją tożsamość.
       
      ![Okno dialogowe logowania Atea](media/atea-provisioning-tutorial/atea-login.png)

7. Po wykonaniu kroków 5 i 6 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Atea. Jeśli połączenie nie powiedzie się, upewnij się, że Atea ma uprawnienia administratora i spróbuj ponownie.
        
      ![Połączenie testowe Atea](media/atea-provisioning-tutorial/test-connection.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. A następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Atea**.

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Atea w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Atea for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API Atea obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
      |---|---|---|
      |userName|Ciąg|&check;|
      |aktywne|Wartość logiczna|
      |emails[type eq "work"].value|Ciąg|
      |name.givenName|Ciąg|
      |name.familyName|Ciąg|
      |Nazwa. sformatowana|Ciąg|
      |phoneNumbers[type eq "mobile"].value|Ciąg|
      |locale|Ciąg|
      |nickName|Ciąg|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Atea, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i grupy, które chcesz udostępnić Atea, wybierając odpowiednią wartość z **zakresu** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
* Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji i zakończenie jego kończenia.
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
