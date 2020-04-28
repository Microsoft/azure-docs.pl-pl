---
title: 'Samouczek: Konfigurowanie workplace by Facebook do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603212"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie workplace by Facebook do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać w workplace by Facebook i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania przepisów użytkowników i grup do [miejsca pracy przez Facebook](https://work.workplace.com/) przy użyciu usługi azure ad inicjowania obsługi administracyjnej. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migracja do nowej aplikacji Workplace by Facebook
Jeśli masz już integrację z Workplace by Facebook, zapoznaj się z poniższą sekcją dotyczącą nadchodzących zmian. Jeśli po raz pierwszy konfigurujesz Workplace by Facebook, możesz pominąć tę sekcję i przejść do obsługiwanych funkcji. 

#### <a name="whats-changing"></a>Co się zmienia?
* Zmiany po stronie usługi Azure AD: Metoda autoryzacji do inicjowania obsługi administracyjnej użytkowników w workplace historycznie był długo trwały tajny token. Wkrótce zobaczysz, że metoda autoryzacji została zmieniona na udzielenie autoryzacji OAuth. 
* Zmiany po stronie Miejsca pracy: Wcześniej aplikacja Usługi Azure AD była niestandardową integracją w Workplace przez Facebooka. Teraz zobaczysz usługę Azure AD w katalogu integracji w miejscu pracy jako aplikację innej firmy. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co należy zrobić, aby przeprowadzić migrację istniejącej integracji niestandardowej do nowej aplikacji?
Jeśli masz istniejącą integrację workplace z prawidłowym tokenem, **nie jest wymagana żadna akcja.** Automatycznie migrujemy klientów co tydzień do nowej aplikacji. Odbywa się to całkowicie za kulisami. Jeśli nie możesz się doczekać i chcesz ręcznie przejść do nowej aplikacji, możesz dodać nowe wystąpienie Workplace z galerii i ponownie skonfigurować inicjowanie obsługi administracyjnej. Wszystkie nowe wystąpienia Workplace będą automatycznie używać nowej wersji aplikacji. 

 
Jeśli integracja w miejscu pracy jest poddawana kwarantannie, musisz ponownie podać ważny token, abyśmy mogli Cię przeprowadzić. Sekcja poświadczeń administratora będzie wyszarzona, ale można dołączyć następujące (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)** do adresu URL, aby ponownie zapisać poświadczenia. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak sprawdzić, czy moja aplikacja została zmigrowana? 
Podczas migracji aplikacji baner w sekcji autoryzacji dotyczący zmian upcomming zostanie usunięty, a pole tajnego tokenu zostanie zastąpione niebieskim przyciskiem autoryzacji. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>Sekcja poświadczeń administratora jest wyszarzona w mojej aplikacji i nie mogę zapisać. Dlaczego?
Zablokowaliśmy sekcję poświadczeń administratora dla istniejących klientów Workplace. Po migracji dzierżawy do nowej aplikacji Workplace będzie można ponownie zaktualizować sekcję poświadczeń administratora. Jeśli nie możesz się doczekać, możesz użyć powyższego adresu URL, aby edytować aplikację. 

 
#### <a name="when-will-these-changes-happen"></a>Kiedy nastąpią te zmiany?
Wszystkie nowe wystąpienia Workplace będą już korzystać z nowej metody integracji / autoryzacji. Istniejące integracje będą stopniowo migrowane do maja. Zespół pracy zapewnił przedłużenie terminu od lutego-28 do 1 maja. 

## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Workplace przez Facebook
> * Usuwanie użytkowników w Workplace przez Facebooka, gdy nie wymagają już dostępu
> * Synchronizacja atrybutów użytkowników między usługą Azure AD a Workplace przez Facebooka
> * [Logowanie jednokrotne na](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) Workplace przez Facebooka (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny)
* A Workplace by Facebook jednokrotnego podpisania włączona subskrypcja

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska testowego usługi Azure AD, możesz uzyskać miesięczną wersję próbną [tutaj.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a Workplace przez Facebooka](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Krok 2. Configure Workplace by Facebook to support provisioning with Azure AD

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Workplace by Facebook. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji Workplace by Facebook, postępując zgodnie z instrukcjami tutaj:

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Workplace przez Facebooka w celu przetestowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do Workplace przez Facebooka musisz wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie workplace przez Facebooka z galerii aplikacji usługi Azure AD

Dodaj workplace przez Facebook z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej do Workplace przez Facebook. Jeśli wcześniej skonfigurowałeś Workplace by Facebook dla aplikacji SSO, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do Workplace przez Facebooka należy wybrać rolę inną niż **Domyślny dostęp**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Workplace by Facebook**.

    ![Link aplikacji Workplace by Facebook na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Zostaniesz przekierowany do Workplace na stronie autoryzacji Facebooka. Wprowadź nazwę użytkownika workplace według Facebooka i kliknij przycisk **Kontynuuj.** Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z Workplace przez Facebooka. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Workplace by Facebook ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowania obsługi](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoryzacja](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z workplace przez Facebooka**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Workplace przez Facebook w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Workplace przez Facebooka do operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API Workplace by Facebook obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |title|Wartość logiczna|
   |wiadomości e-mail[wpisz eq "praca"].wartość|Ciąg|
   |name.givenName|Ciąg|
   |nazwa.familyName|Ciąg|
   |nazwa.sformatowany|Ciąg|
   |adresy[wpisz eq "praca"].sformatowany|Ciąg|
   |adresy[wpisz eq "praca"].streetAddress|Ciąg|
   |adresy[wpisz eq "praca"].miejscowość|Ciąg|
   |adresy[wpisz eq "praca"].region|Ciąg|
   |adresy[wpisz eq "praca"].kraj|Ciąg|
   |adresy[wpisz eq "praca"].postalCode|Ciąg|
   |adresy[wpisz eq "inne"].sformatowany|Ciąg|
   |phoneNumbers[wpisz eq "praca"].wartość|Ciąg|
   |phoneNumbers[wpisz eq "mobile"].wartość|Ciąg|
   |phoneNumbers[wpisz eq "fax"].wartość|Ciąg|
   |identyfikator zewnętrzny|Ciąg|
   |preferowany Język|Ciąg|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Ciąg|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Ciąg|

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla workplace przez Facebooka, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Workplace by Facebook, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Początkowy cykl trwa dłużej niż kolejne cykle, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu inicjowania obsługi administracyjnej użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) aby określić, którzy użytkownicy zostali pomyślnie lub bezskutecznie udostępnieni
2. Sprawdź [pasek postępu,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) aby zobaczyć stan cyklu inicjowania obsługi administracyjnej i jak blisko jest do zakończenia
3. Jeśli konfiguracja inicjowania obsługi administracyjnej wydaje się być w stanie złej kondycji, aplikacja przejdzie do kwarantanny. Dowiedz się więcej o stanach kwarantanny [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
*  Jeśli użytkownik zostanie utworzony bezpowodzenia i występuje zdarzenie dziennika inspekcji o kodzie "1789003", oznacza to, że użytkownik pochodzi z domeny niezweryfikowanej.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../manage-apps/check-status-user-account-provisioning.md)
