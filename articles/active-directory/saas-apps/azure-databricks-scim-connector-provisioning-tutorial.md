---
title: 'Samouczek: Konfigurowanie łącznika usługi Azure Databricks Standard scim dla automatycznej aprowizacji użytkowników z Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD, Azure Databricks łącznik Standard scim.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370523"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie łącznika Azure Databricks Standard scim na potrzeby automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w Azure Databricks łącznika Standard scim i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. W przypadku skonfigurowania usługi Azure AD automatycznie inicjuje i usuwają użytkowników i grupy [Azure Databricks łącznik Standard scim](https://databricks.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Azure Databricks łącznika Standard scim
> * Usuń użytkowników w łączniku Azure Databricks Standard scim, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkownika między usługą Azure AD a Azure Databricks łącznika Standard scim
> * Udostępnianie grup i członkostw w grupach w Azure Databricks łącznika Standard scim

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto Azure Databricks z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ dane, które mają być [mapowane między usługą Azure AD a Azure Databricks łącznika Standard scim](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie łącznika Azure Databricks Standard scim w celu obsługi aprowizacji w usłudze Azure AD

1. Aby skonfigurować Azure Databricks Standard scim, Dodaj go jako zasób w dzierżawie Azure Active Directory i skonfiguruj go przy użyciu poniższych ustawień.

    ![Konfiguracja Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. W celu wygenerowania osobistego tokenu dostępu w Azure Databricks zapoznaj się z [tym](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)tematem.

3. Skopiuj **token**. Ta wartość zostanie wprowadzona w polu token tajny na karcie inicjowanie aplikacji łącznika Azure Databricks Standard scim w Azure Portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie łącznika Azure Databricks Standard scim z galerii aplikacji usługi Azure AD

Dodaj łącznik Azure Databricks Standard scim z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Azure Databricks łącznika Standard scim. Jeśli wcześniej skonfigurowano Azure Databricks łącznik Standard scim dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do Azure Databricks łącznika Standard scim należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników do Azure Databricks łącznika Standard scim 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej na temat Azure Databricks "punkt końcowy Standard scim", zapoznaj się z [tym](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej dla Azure Databricks łącznika Standard scim w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Azure Databricks łącznik Standard scim**.

    ![Łącze Azure Databricks łącznika Standard scim na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość punktu końcowego Standard scim w **adresie URL dzierżawy**. Adres URL dzierżawy powinien mieć format `https://<region>.azuredatabricks.net/api/2.0/preview/scim` , w którym **region** można znaleźć w adresie URL strony głównej Azure Databricks. Na przykład punkt końcowy Standard scim dla regionu **zachodniego** będzie `https://westus.azuredatabricks.net/api/2.0/preview/scim`. Wprowadź wartość tokenu pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z łącznikiem Azure Databricks Standard scim. Jeśli połączenie nie powiedzie się, upewnij się, że konto łącznika usługi Azure Databricks Standard scim ma uprawnienia administratora i spróbuj ponownie.

    ![aprowizacji](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Azure Databricks łącznik Standard scim**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, do Azure Databricks łącznik Standard scim w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w Azure Databricks łącznika Standard scim dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API łącznika Azure Databricks Standard scim obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|String|
   |displayName|String|
   |aktywne|Boolean|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Azure Databricks łącznik Standard scim**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługą Azure AD, aby Azure Databricks łącznik Standard scim w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w łączniku Azure Databricks Standard scim dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

     |Atrybut|Typ|
     |---|---|
     |displayName|String|
     |elementy członkowskie|Dokumentacja|

11. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby Azure Databricks łącznik Standard scim**.

12. Aby włączyć usługę Azure AD aprowizacji dla Azure Databricks łącznika Standard scim, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Azure Databricks łącznik Standard scim, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

14. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
* Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
* Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* W przypadku zapisywania do katalogu datakostek zawsze konwertuje wartości nazwy użytkownika na małe litery, niezależnie od wielkości liter, które wysyłamy do nich za pośrednictwem Standard scim.
* Obecnie żądania dotyczące interfejsu API Standard scim "Azure Databricks" dla użytkowników są rozróżniane wielkością liter, dlatego USER@contoso.com Jeśli wyślemy do niej zapytanie z wartością 0 w miarę ich user@contoso.comprzechowywania.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
