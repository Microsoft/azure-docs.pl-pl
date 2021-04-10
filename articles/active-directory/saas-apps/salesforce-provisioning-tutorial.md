---
title: 'Samouczek: Konfigurowanie usługi Salesforce do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Zapoznaj się z krokami wymaganymi do wykonania w usłudze Salesforce i usłudze Azure AD, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w usłudze Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: c5df0a5fc054a12e3fa2ef1e352645c57c357b01
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798735"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Salesforce do automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności wymaganych do wykonania w usłudze Salesforce i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w usłudze Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa Salesforce.com

> [!Note]
> Role nie powinny być edytowane ręcznie w Azure Active Directory podczas importowania ról.

> [!IMPORTANT]
> Jeśli używasz konta próbnego Salesforce.com, nie będzie można skonfigurować automatycznej aprowizacji użytkowników. Konta próbne nie mają włączonego dostępu do interfejsu API, dopóki nie zostaną zakupione. To ograniczenie można obejść, używając bezpłatnego [konta dewelopera](https://developer.salesforce.com/signup) do wykonania tego samouczka.

Jeśli używasz środowiska piaskownicy Salesforce, zobacz [Samouczek dotyczący integracji z piaskownicą usługi Salesforce](./salesforce-sandbox-tutorial.md).

## <a name="assigning-users-to-salesforce"></a>Przypisywanie użytkowników do usługi Salesforce

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy lub które grupy w usłudze Azure AD potrzebują dostępu do aplikacji w usłudze Salesforce. Po wykonaniu tej decyzji można przypisać tych użytkowników do aplikacji Salesforce, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md) .

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Salesforce

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usług Salesforce w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do usługi Salesforce należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji

    > [!NOTE]
    > Ta aplikacja importuje profile z usługi Salesforce w ramach procesu aprowizacji, który klient może chcieć wybrać podczas przypisywania użytkowników w usłudze Azure AD. Należy pamiętać, że profile zaimportowane z usługi Salesforce są wyświetlane jako role w usłudze Azure AD.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z [interfejsem API aprowizacji usługi Azure AD do konta użytkownika usługi Salesforce — V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm), a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze Salesforce na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!Tip]
> Możesz również włączyć opcję Sign-On Single opartych na protokole SAML dla usługi Salesforce, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi administracyjnej użytkowników Active Directory kont użytkowników w usłudze Salesforce.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

2. Jeśli skonfigurowano już usługi Salesforce do logowania jednokrotnego, Wyszukaj wystąpienie usług Salesforce przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj usługi **Salesforce** w galerii aplikacji. Wybierz pozycję Salesforce z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

3. Wybierz wystąpienie usługi Salesforce, a następnie wybierz kartę **aprowizacji** .

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawia stronę aprowizacji usługi Salesforce, z trybem aprowizacji ustawionym na automatyczne i inne wartości, które można ustawić.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. W sekcji **poświadczenia administratora** podaj następujące ustawienia konfiguracji:

    a. W polu tekstowym **Nazwa użytkownika administratora** wpisz nazwę konta usługi Salesforce z profilem **administratora systemu** w Salesforce.com przypisane.

    b. W polu tekstowym **hasło administratora** wpisz hasło dla tego konta.

6. Aby uzyskać token zabezpieczający usługi Salesforce, Otwórz nową kartę i zaloguj się do tego samego konta administratora usługi Salesforce. W prawym górnym rogu strony kliknij swoją nazwę, a następnie kliknij pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia wybrane łącze ustawienia.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

7. W okienku nawigacji po lewej stronie kliknij pozycję **moje dane osobowe** , aby rozwinąć sekcję powiązaną, a następnie kliknij pozycję **Zresetuj mój token zabezpieczający**.
  
    ![Zrzut ekranu przedstawia Resetowanie mojego tokenu zabezpieczającego wybranego z informacji osobistych.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

8. Na stronie **Resetowanie tokenu zabezpieczeń** kliknij przycisk **Resetuj token zabezpieczający** .

    ![Zrzut ekranu przedstawia stronę token zabezpieczający REST z tekstem wyjaśniającym i opcją resetowania tokenu zabezpieczającego](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

9. Sprawdź skrzynkę odbiorczą wiadomości e-mail skojarzoną z tym kontem administratora. Poszukaj wiadomości e-mail z Salesforce.com, która zawiera nowy token zabezpieczający.

10. Skopiuj token, przejdź do okna usługi Azure AD i wklej go w polu **token tajny** .

11. Należy wprowadzić **adres URL dzierżawy** , jeśli wystąpienie usługi Salesforce znajduje się w chmurze dla instytucji rządowych usługi Salesforce. W przeciwnym razie jest to opcjonalne. Wprowadź adres URL dzierżawy przy użyciu formatu "https:// \<your-instance\> . my.Salesforce.com", zastępując go \<your-instance\> nazwą wystąpienia usługi Salesforce.

12. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z Twoją aplikacją w usłudze Salesforce.

13. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru poniżej.

14. Kliknij przycisk **Zapisz.**  

15. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników w usłudze Salesforce.**

16. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z usługą Salesforce. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w usłudze Salesforce dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

17. Aby włączyć usługę Azure AD Provisioning dla usługi Salesforce, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

18. Kliknij przycisk **Zapisz.**

> [!NOTE]
> Po zainicjowaniu obsługi użytkowników w aplikacji Salesforce administrator musi skonfigurować dla nich ustawienia specyficzne dla języka. Aby uzyskać więcej informacji na temat konfiguracji języka, zobacz [ten](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artykuł.

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do usługi Salesforce w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji usługi Salesforce.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Typowe problemy
* Jeśli masz problemy z autoryzacją dostępu do usługi Salesforce, upewnij się, że:
    * Używane poświadczenia mają dostęp administratora do usługi Salesforce.
    * Używana wersja usługi Salesforce obsługuje Dostęp w sieci Web (np. Deweloper, przedsiębiorstwa, piaskownica i nieograniczona wersja usługi Salesforce).
    * Dostęp do interfejsu API sieci Web jest włączony dla użytkownika.
* Usługa Azure AD Provisioning obsługuje język aprowizacji, ustawienia regionalne i strefę czasową dla użytkownika. Te atrybuty są w domyślnych mapowaniach atrybutów, ale nie mają domyślnego atrybutu źródłowego. Upewnij się, że wybrano domyślny atrybut źródłowy i że atrybut źródłowy jest w formacie oczekiwanym przez usługi SalesForce. Na przykład localeSidKey dla języka angielskiego (Stany Zjednoczone) jest en_US. Zapoznaj się ze wskazówkami podanymi w [tym miejscu](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) , aby określić właściwy format localeSidKey. Formaty languageLocaleKey można znaleźć [tutaj](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Oprócz upewnienia się, że format jest poprawny, może być konieczne upewnienie się, że język jest włączony dla użytkowników, zgodnie z opisem w [tym miejscu](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** Nie można utworzyć użytkownika w aplikacji docelowej, ponieważ nie ma żadnych dostępnych licencji dla tego użytkownika. Uzyskaj dodatkowe licencje dla aplikacji docelowej lub przejrzyj przypisania użytkowników i konfigurację mapowania atrybutów, aby upewnić się, że poprawni użytkownicy są przypisani przy użyciu poprawnych atrybutów.
* **SalesforceDuplicateUserName:** Nie można zainicjować obsługi administracyjnej użytkownika, ponieważ ma on Salesforce.com "username", który jest zduplikowany w innej dzierżawie Salesforce.com.W Salesforce.com wartości atrybutu "username" muszą być unikatowe we wszystkich dzierżawach Salesforce.com.Domyślnie element userPrincipalName użytkownika w Azure Active Directory ma postać "username" w Salesforce.com.Dostępne są dwie opcje.Jedną z opcji jest znalezienie i zmiana nazwy użytkownika ze zduplikowaną nazwą "username" w innej dzierżawie Salesforce.com, Jeśli administrujesz tą inną dzierżawcą.Druga opcja polega na usunięciu dostępu Azure Active Directory użytkownika do dzierżawy Salesforce.com, z którą katalog jest zintegrowany. Ponowimy próbę wykonania tej operacji podczas następnej próby synchronizacji. 
* **SalesforceRequiredFieldMissing:** W celu pomyślnego utworzenia lub zaktualizowania użytkownika usługi Salesforce muszą mieć pewne atrybuty. Ten użytkownik nie ma jednego z wymaganych atrybutów. Upewnij się, że atrybuty, takie jak poczta e-mail i alias, są wypełniane wszystkim użytkownikom, którzy mają być obsługiwani w usłudze Salesforce. Można zasięgać użytkowników, którzy nie mają tych atrybutów, przy użyciu [filtrów określania zakresu atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 
* Domyślne mapowanie atrybutów dla aprowizacji do usługi Salesforce obejmuje wyrażenie SingleAppRoleAssignments służące do mapowania appRoleAssignments w usłudze Azure AD na ProfileName w usłudze Salesforce. Upewnij się, że użytkownicy nie mają wielu przypisań ról aplikacji w usłudze Azure AD, ponieważ mapowanie atrybutu obsługuje tylko jedną rolę. 
* Przed zmianą usługi Salesforce wymagane jest ręczne zatwierdzenie aktualizacji poczty e-mail. W związku z tym w dziennikach aprowizacji może zostać wyświetlona wiele wpisów w celu zaktualizowania wiadomości e-mail użytkownika (do momentu zatwierdzenia zmiany wiadomości e-mail).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](./salesforce-tutorial.md)
