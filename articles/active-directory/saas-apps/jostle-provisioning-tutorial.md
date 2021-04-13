---
title: 'Samouczek: konfigurowanie usługi Jostle do automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i deprowizować konta użytkowników z usługi Azure AD do rozwiązania Jostle.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368672"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi Jostle do automatycznego aprowizowania użytkowników

W tym samouczku opisano kroki, które należy wykonać w rozwiązaniach Jostle i Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje użytkowników i grupy do [rozwiązania Jostle](https://www.jostle.me/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w jostle
> * Usuwanie użytkowników z jostle, gdy nie wymagają już dostępu
> * Utrzymuj synchronizację atrybutów użytkownika między usługą Azure AD i rozwiązaniami Jostle
> * [Logowanie pojedyncze do jostle](jostle-tutorial.md) (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md) 
* Konto użytkownika w usłudze Azure AD z uprawnieniami do konfigurowania aprowizowania (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). [](../roles/permissions-reference.md) 
* Dzierżawa [Jostle.](https://www.jostle.me/)
* Konto użytkownika w programie Jostle z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ustal, jakie dane mają [być mapowe między usługą Azure AD i rozwiązaniami Jostle.](../app-provisioning/customize-application-attributes.md)

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie rozwiązania Jostle do obsługi aprowizowania w usłudze Azure AD

### <a name="automation-account"></a>Konto usługi Automation

Przed rozpoczęciem należy utworzyć użytkownika usługi **Automation w intranecie** rozwiązania Jostle. Będzie to konto, które zostanie skonfigurowane na platformie Azure. Użytkowników usługi Automation można tworzyć w ustawieniach administratora > kontach użytkowników i danych **> zarządzanie użytkownikami usługi Automation.**

Aby uzyskać więcej informacji na temat użytkowników usługi Automation i sposobu jej tworzenia, zobacz [ten artykuł.](https://forum.jostle.us/hc/en-us/articles/360057364073)

Po utworzeniu należy  aktywować konto użytkownika usługi Automation (tj. zalogować się do intranetu co najmniej raz), aby można było użyć go do skonfigurowania platformy Azure.

### <a name="manage-user-provisioning"></a>Zarządzanie aprowizowanie użytkowników

Przed rozpoczęciem upewnij się, że subskrypcja konta obejmuje funkcje **logowania jednokrotnego/aprowizowania użytkowników.** Jeśli tak nie jest, możesz skontaktować się z menedżerem ds. sukcesu klientów i pomóc Ci w <success@jostle.me> dodaniu go do twojego konta.

Następnym krokiem jest uzyskanie adresu URL interfejsu **API** i klucza **interfejsu API z** usługi Jostle:

1. Przejdź do pozycji Nawigacja główna i kliknij **pozycję Ustawienia administratora.**
1. W **obszarze Dane użytkownika do/z innych systemów kliknij** pozycję Zarządzaj **aprowizowanie użytkowników.** Jeśli nie widzisz pozycji "Zarządzaj aprowizowanie użytkowników" w tym miejscu i masz sprawdzoną, że Twoje konto obejmuje aprowizowanie logowania jednokrotnego/użytkowników, skontaktuj się z pomocą techniczną, aby ta strona została włączona w <support@jostle.me> ustawieniach administratora).
1. W sekcji **User Provisioning API details** (Szczegóły interfejsu API aprowowania użytkowników) przejdź do pola Your Base URL (Twój podstawowy adres **URL),** kliknij przycisk Copy (Kopiuj) i zapisz adres URL w miejscu, w który będzie można później łatwo uzyskać do niego dostęp.                                                           

   ![Inicjowanie obsługi](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Następnie kliknij przycisk **Dodaj nowy klucz...** Przycisk
1. Na poniższym ekranie przejdź do pola Użytkownik usługi **Automation** i użyj menu rozwijanego, aby wybrać konto użytkownika usługi Automation. 

   ![Konto integracji](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. W polu **Provisioning API key description** (Opis klucza interfejsu API aprowizowania) nadaj kluczowi nazwę (tj. "Azure"), a następnie kliknij **przycisk Add (Dodaj).**

1. Po wygenerowaniu klucza **pamiętaj,** aby skopiować go od razu i zapisać w miejscu, w którym zapisano adres URL (ponieważ będzie to jedyny raz, gdy pojawi się klucz).                                                               
1. Następnie użyjesz adresu **URL** interfejsu API i klucza **interfejsu API,** aby skonfigurować integrację na platformie Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie rozwiązania Jostle z galerii aplikacji usługi Azure AD

Dodaj narzędzie Jostle z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizowanie w usłudze Jostle. Jeśli wcześniej konfigurowano program Jostle na użytek logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas początkowego testowania integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do jostle należy wybrać rolę inną niż **Domyślny dostęp.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, możesz zaktualizować [manifest aplikacji,](../develop/howto-add-app-roles-in-azure-ad-apps.md) aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników dla jostle 

Ta sekcja zawiera instrukcje konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w aplikacji Jostle na podstawie przypisań użytkowników i grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla rozwiązania Jostle w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **Jostle**.

    ![Link do rozwiązania Jostle na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

1.  Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczna karta Aprowizowanie](common/provisioning-automatic.png)

1. W sekcji **Poświadczenia administratora wprowadź** adres **URL** dzierżawy Jostle i informacje o **tokenie wpisów tajnych.** Wybierz **pozycję Testuj połączenie,** aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Jostle. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Jostle ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **Wiadomość e-mail** z powiadomieniem wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach aprowiwizowania. Zaznacz pole **wyboru Wyślij powiadomienie e-mail w przypadku wystąpienia** błędu.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **Mapowania wybierz** pozycję Synchronizuj użytkowników Azure Active Directory **z jostle.**

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do rozwiązania Jostle w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako właściwości **dopasowania** są używane do dopasowywania kont użytkowników w jostle dla operacji aktualizacji. W przypadku zmiany [pasującego atrybutu docelowego](../app-provisioning/customize-application-attributes.md)należy upewnić się, że interfejs API Jostle obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane filtrowanie|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |emails[type eq "personal"].value|Ciąg|
   |emails[type eq "alternate1"].value|Ciąg|
   |emails[type eq "alternate2"].value|Ciąg|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|Ciąg|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |Ciąg|  

1. Aby skonfigurować filtry zakresu, zobacz instrukcje podane w [samouczku dotyczącym filtrowania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę aprowizowania usługi Azure AD dla rozwiązania Jostle, zmień ustawienie **Stan aprowowania** na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, które chcesz aprowizować  w jostle, wybierając odpowiednie wartości w sekcji Zakres **w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do aprowizować, wybierz pozycję **Zapisz.**

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Początkowy cykl trwa dłużej niż kolejne cykle, które odbywają się co około 40 minut, o ile usługa aprowności Azure AD jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizowania użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizowania,](../reports-monitoring/concept-provisioning-logs.md) aby określić, którzy użytkownicy zostali aprowizowany pomyślnie lub niepomyślnie.
* Sprawdź pasek [postępu,](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) aby zobaczyć stan cyklu aprowizowania i jego zamknięcie do ukończenia.
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej o stanach kwarantanny, zobacz [Stan aprowowania aplikacji kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Więcej zasobów

* [Zarządzanie aprowizowanie konta użytkownika dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)