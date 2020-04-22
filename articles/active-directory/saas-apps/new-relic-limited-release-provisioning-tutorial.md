---
title: 'Samouczek: Konfigurowanie nowej wersji (ograniczona wersja) do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i usuwać aprowizyję kont użytkowników z usługi Azure AD na nową wersję (wersja ograniczona).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727348"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie nowej relikwii (ograniczona wersja) do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w nowej wersji(ograniczona wersja) i usługi Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania przepisów użytkowników i grup do [nowej relikwii](https://newrelic.com/) przy użyciu usługi Azure AD inicjowania obsługi administracyjnej usługi. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w nowej relikwii (wersja ograniczona)
> * Usuń użytkowników w New Relic (Limited Release), gdy nie wymagają już dostępu
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i nową reliktem (wersja ograniczona)
> * Aprowizuj grupy i członkostwa w grupach w New Relic (Limited Release)
> * [Logowanie jednokrotne do](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) nowej relikwii (wydanie ograniczone) (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). 
* Co najmniej jedno konto w new relikt (ograniczona wersja), do którego użytkownicy mają mieć dostęp. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a nową wersją (ograniczona wersja).](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>Krok 2. Configure New Relic(Limited Release) to support provisioning with Azure AD

Współpracuj z przedstawicielem konta lub uzyskaj pomoc techniczną w support.newrelic.com, aby skonfigurować scm i jednokrotne dla swojej organizacji. Musisz podać swojego przedstawiciela konta:

- Nazwa organizacji
- Lista identyfikatorów nowych kont reliktów do skojarzenia z organizacją

Dzięki tym informacjom przedstawiciel twojego konta tworzy rekord organizacji w naszym nowym systemie i kojarzy twoje konta z organizacją.

Przedstawiciel twojego konta udostępnia następujące informacje, które należy skonfigurować aplikację New Relic SCIM/SSO dla dostawcy tożsamości:

- Punkt końcowy SCIM (adres URL dzierżawy)
- Token na okaziciela SCIM (tajny token)

Token nośnika SCIM umożliwia inicjowanie obsługi administracyjnej użytkowników w New Relic, więc należy zachować wartość zabezpieczoną. Przedstawiciel Twojego konta przekaże Ci token na okaziciela SCIM w bezpieczny sposób.

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie nowej relikwii (ograniczonej wersji) z galerii aplikacji usługi Azure AD

Dodaj nową relikt (wersja ograniczona) z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej do nowej wersji(ograniczona wersja). Jeśli wcześniej skonfigurowałeś nową relikt (limitowana wersja) dla aplikacji jednolitowej, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do nowej relikwii (wersja ograniczona) należy wybrać rolę inną niż **Dostęp domyślny**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>Krok 5. Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla nowej wersji (wersja ograniczona) 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla nowej wersji (ograniczona wersja) w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Nowa relikwia (wersja ograniczona).**

    ![Łącze Nowa relikwia na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres URL `https://scim-provisioning.service.newrelic.com/scim/v2` dzierżawy. Wprowadź wartość tokenu uwierzytelniania SCIM pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z nową reliktem. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto New Relic ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowania obsługi](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z nową reliktem (wersja ograniczona).**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do nowej wersji (ograniczona wersja) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w new relic (Limited Release) dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API Nowa relikwia (ograniczona wersja) obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |identyfikator zewnętrzny|Ciąg|
   |aktywne|Wartość logiczna|
   |wiadomości e-mail[wpisz eq "praca"].wartość|Ciąg|
   |name.givenName|Ciąg|
   |nazwa.sformatowany|Ciąg|
   |Strefa czasowa|Ciąg|

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z nową reliktem (wersja ograniczona).**

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do nowej wersji (ograniczona wersja) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w new relikt (ograniczona wersja) dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |identyfikator zewnętrzny|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla nowej wersji(ograniczona wersja), zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić new relikwii (ograniczona wersja), wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Początkowy cykl trwa dłużej niż kolejne cykle, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu inicjowania obsługi administracyjnej użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) aby określić, którzy użytkownicy zostali pomyślnie lub bezskutecznie udostępnieni
* Sprawdź [pasek postępu,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) aby zobaczyć stan cyklu inicjowania obsługi administracyjnej i jak blisko jest do zakończenia
* Jeśli konfiguracja inicjowania obsługi administracyjnej wydaje się być w stanie złej kondycji, aplikacja przejdzie do kwarantanny. Dowiedz się więcej o stanach kwarantanny [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../manage-apps/check-status-user-account-provisioning.md)
