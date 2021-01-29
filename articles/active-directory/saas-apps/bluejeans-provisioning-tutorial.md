---
title: 'Samouczek: Konfigurowanie BlueJeans dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d029f033a3c452587dbeeadf69c46cc99f604031
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053859"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BlueJeans na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie BlueJeans i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługi Azure AD automatycznie inicjuje i usuwają użytkowników, aby [BlueJeans](https://www.bluejeans.com/pricing) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w BlueJeans
> * Usuń użytkowników w BlueJeans, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i BlueJeans
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/bluejeans-tutorial) jednokrotne do BlueJeans (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Dzierżawa BlueJeans z planem [firmy](https://www.bluejeans.com/pricing) lub lepsza.
* Konto użytkownika w BlueJeans z uprawnieniami administratora.
* Inicjowanie obsługi administracyjnej Standard scim jest włączone w przedsiębiorstwie BlueJeans.

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API BlueJeans](https://BlueJeans.github.io/developer), który jest dostępny dla zespołów BlueJeans w planie standardowym lub lepszym.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie BlueJeans w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do konsoli administracyjnej BlueJeans. Przejdź do ustawień grupy > zabezpieczenia.
2. Wybierz pozycję **Logowanie jednokrotne** i **Skonfiguruj teraz**.

    ![teraz](./media/bluejeans-provisioning-tutorial/configure.png)

3. W oknie **udostępnianie & Integration** wybierz pozycję **Utwórz konta użytkowników i zarządzaj nimi za pomocą dostawcy tożsamości** , a następnie kliknij pozycję **Generuj token**.

    ![generuj](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Skopiuj i Zapisz token. 
5. Adres URL dzierżawy BlueJeans to `https://api.bluejeans.com/v2/scim` . **Adres URL dzierżawy** i **token tajny** z poprzedniego kroku zostaną wprowadzone na karcie aprowizacji aplikacji BlueJeans w Azure Portal.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie BlueJeans z galerii aplikacji usługi Azure AD

Dodaj BlueJeans z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do BlueJeans. Jeśli wcześniej skonfigurowano BlueJeans do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany w oparciu o atrybuty użytkownika, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników do BlueJeans należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przetestuj się z małym zestawem użytkowników przed wprowadzeniem ich do wszystkich osób. Jeśli zakres dla aprowizacji jest ustawiony na przypisanych użytkowników, można kontrolować ten sposób, przypisując jeden lub dwóch użytkowników do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników, można określić [Filtr określania zakresu atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do BlueJeans

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników w programie TestApp na podstawie przypisań użytkowników w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla BlueJeans w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BlueJeans**.

    ![Link rozwiązania BlueJeans na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy BlueJeans i token tajny, który został pobrany w kroku 2. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą BlueJeans. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi BlueJeans ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby, która powinna odebrać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do BlueJeans**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do BlueJeans w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie BlueJeans for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API BlueJeans obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

|Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
|---|---|---|
|userName|Ciąg|&check;|
|aktywne|Wartość logiczna|
|title|Ciąg|
|emails[type eq "work"].value|Ciąg|
|name.givenName|Ciąg|
|name.familyName|Ciąg|
|phoneNumbers[type eq "work"].value|Ciąg|
|urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla BlueJeans, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników, którym chcesz udostępnić BlueJeans, wybierając odpowiednie wartości z **zakresu** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

* BlueJeans nie zezwala na nazwy użytkowników, których długość przekracza 30 znaków.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)