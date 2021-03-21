---
title: Konfigurowanie Kreatora usługi log Analytics w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę log Analytics.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574387"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Samouczek: Konfigurowanie Kreatora usługi log Analytics


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego usługi log Analytics na potrzeby inspekcji i dzienników logowania
> * Uruchamianie zapytań przy użyciu języka zapytań Kusto (KQL)
> * Tworzenie reguły alertu, która wysyła alerty w przypadku użycia określonego konta
> * Tworzenie skoroszytu niestandardowego przy użyciu szablonu szybkiego startu
> * Dodawanie zapytania do istniejącego szablonu skoroszytu

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure z co najmniej jednym administratorem licencjonowanym w punkcie P1. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

- Dzierżawa usługi Azure AD.

- Użytkownik będący administratorem globalnym lub administratorem zabezpieczeń dla tej dzierżawy usługi Azure AD.


Zapoznaj się z następującymi artykułami:

- [Samouczek: zbieranie i analizowanie dzienników zasobów z zasobów platformy Azure](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Jak zintegrować dzienniki aktywności z Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Zarządzanie kontem dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md)

- [Krótki przewodnik dla języka KQL](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor skoroszyty](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Konfigurowanie obszaru roboczego 

W tej procedurze opisano sposób konfigurowania obszaru roboczego usługi log Analytics na potrzeby dzienników inspekcji i logowania.
Konfigurowanie obszaru roboczego usługi log Analytics składa się z dwóch głównych kroków:
 
1. Tworzenie obszaru roboczego usługi log Analytics
2. Ustawianie ustawień diagnostycznych

**Aby skonfigurować obszar roboczy:** 


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wyszukaj **obszary robocze usługi log Analytics**.

    ![Wyszukaj usługi zasobów i dokumenty](./media/tutorial-log-analytics-wizard/search-services.png)

3. Na stronie obszary robocze usługi log Analytics kliknij pozycję **Dodaj**.

    ![Zrzut ekranu przedstawia przycisk Dodaj na stronie obszary robocze usługi log Analytics.](./media/tutorial-log-analytics-wizard/add.png)

4.  Na stronie **tworzenie log Analytics obszaru roboczego** wykonaj następujące czynności:

    ![Tworzenie obszaru roboczego usługi Log Analytics](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Wybierz subskrypcję.

    2. Wybierz grupę zasobów.
 
    3. W polu tekstowym **Nazwa** wpisz nazwę (np.: MytestWorkspace1).

    4. Wybierz region.

5. Kliknij pozycję **Przejrzyj i utwórz**.

    ![Przegląd i tworzenie](./media/tutorial-log-analytics-wizard/review-create.png)

6. Kliknij przycisk **Utwórz** i poczekaj, aż wdrożenie zakończyło się pomyślnie. Może być konieczne odświeżenie strony, aby wyświetlić nowy obszar roboczy.

    ![Utwórz](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Wyszukaj usługę **Azure Active Directory**.

    ![Zrzut ekranu przedstawia Azure Active Directory w usłudze Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. W sekcji **monitorowanie** kliknij pozycję **ustawienie diagnostyczne**.

    ![Zrzut ekranu przedstawia ustawienia diagnostyczne wybrane z monitorowania.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Na stronie **Ustawienia diagnostyczne** kliknij pozycję **Dodaj ustawienie diagnostyczne**.

    ![Dodaj ustawienie diagnostyczne](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Na stronie **Ustawienia diagnostyczne** wykonaj następujące czynności:

    ![Wybieranie ustawień diagnostycznych](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. W obszarze **szczegóły kategorii** wybierz pozycję **AuditLogs** i **SigninLogs**.

    2. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics** a następnie wybierz nowy obszar roboczy usługi log Analytics. 
   
    3. Kliknij pozycję **Zapisz**. 

## <a name="run-queries"></a>Uruchamianie zapytań  

Ta procedura pokazuje, jak uruchamiać zapytania przy użyciu **języka zapytań Kusto (KQL)**.


**Aby uruchomić zapytanie:**


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wyszukaj usługę **Azure Active Directory**.

    ![Zrzut ekranu przedstawia Azure Active Directory w usłudze Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. W sekcji **monitorowanie** kliknij pozycję **dzienniki**.

4. Na stronie **dzienniki** kliknij pozycję **Rozpocznij**.

5. W polu tekstowym *wyszukiwania* * wpisz swoje zapytanie.

6. Kliknij przycisk **Uruchom**.  


### <a name="kql-query-examples"></a>Przykłady zapytań KQL

Pobieraj 10 losowych wpisów z danych wejściowych:

`SigninLogs | take 10`

Przyjrzyj się logowaniem, gdzie dostęp warunkowy był sukcesem

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Liczba sukcesów

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Łączna liczba pomyślnych logowań według użytkownika dziennie:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Wyświetl informacje o tym, ile razy użytkownik wykonuje określoną operację w określonym przedziale czasu:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Przestawianie wyników na nazwę operacji

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Scal razem inspekcję i logowanie dzienników przy użyciu sprzężenia wewnętrznego:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Wyświetl liczbę logowań według typu aplikacji klienta:

`SigninLogs | summarize count() by ClientAppUsed`

Liczba logowań dziennie:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Zrób 5 losowych wpisów i Zaprojektuj kolumny, które chcesz zobaczyć w wynikach:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Wypełnij 5 najważniejszych w kolejności malejącej i Zaprojektuj kolumny, które chcesz zobaczyć.

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Utwórz nową kolumnę, łącząc wartości z dwiema innymi kolumnami:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu  

Ta procedura pokazuje, jak wysyłać alerty w przypadku używania konta breakglass.

**Aby utworzyć regułę alertu:**


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wyszukaj usługę **Azure Active Directory**.

    ![Zrzut ekranu przedstawia Azure Active Directory w usłudze Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. W sekcji **monitorowanie** kliknij pozycję **dzienniki**.

4. Na stronie **dzienniki** kliknij pozycję **Rozpocznij**.

5. W polu tekstowym **Wyszukaj** wpisz: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Kliknij przycisk **Uruchom**.  

7. Na pasku narzędzi kliknij pozycję **Nowa reguła alertu**.

    ![Nowa reguła alertu](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Na stronie **Tworzenie reguły alertu** upewnij się, że zakres jest prawidłowy.

9. W obszarze **warunek** kliknij pozycję: **zawsze, gdy średnie wyszukiwanie w dzienniku niestandardowym jest większe niż <logic undefined> Liczba**

    ![Warunek domyślny](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Na stronie **Konfiguruj logikę sygnału** w sekcji **logika alertów** wykonaj następujące czynności:

    ![Logika alertu](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Zgodnie **z** opisem wybierz **liczbę wyników**.

    2. Jako **operator**, wybierz opcję **większe niż**.

    3. Jako **wartość progowa** wybierz **0**. 

11. Na stronie **Konfiguruj logikę sygnału** w sekcji **oceniane na podstawie** wykonaj następujące czynności:

    ![Oceniane na podstawie](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Jako **czas (w minutach)** wybierz pozycję **5**.

    2. Jako **częstotliwość (w minutach)** wybierz pozycję **5**.

    3. Kliknij przycisk **Gotowe**. 

12. W obszarze **Grupa akcji** kliknij pozycję **Wybierz grupę akcji**. 

    ![Grupa akcji](./media/tutorial-log-analytics-wizard/action-group.png)

13. Na stronie **Wybierz grupę akcji do dołączenia do tej reguły alertu** kliknij pozycję **Utwórz grupę akcji**. 

    ![Tworzenie grupy akcji](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Na stronie **Tworzenie grupy akcji** wykonaj następujące czynności:

    ![Szczegóły wystąpienia](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. W polu tekstowym **Nazwa grupy akcji** wpisz **moją grupę akcji**.

    2. W polu tekstowym **Nazwa wyświetlana** wpisz **Moje działanie**.

    3. Kliknij pozycję **Przejrzyj i utwórz**. 

    4. Kliknij pozycję **Utwórz**.


15. W obszarze **Dostosowywanie akcji** wykonaj następujące czynności:

    ![Dostosuj akcje](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Wybierz pozycję **temat wiadomości e-mail**.

    2. W polu tekstowym **wiersz tematu** wpisz: `Breakglass account has been used`

16. W obszarze **szczegóły reguły alertu** wykonaj następujące czynności:

    ![Szczegóły reguły alertu](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. W polu tekstowym **Nazwa reguły alertu** wpisz: `Breakglass account`

    2. W polu tekstowym **Opis** wpisz: `Your emergency access account has been used`

17. Kliknij pozycję **Utwórz regułę alertu**.   


## <a name="create-a-custom-workbook"></a>Tworzenie skoroszytu niestandardowego

Ta procedura pokazuje, jak utworzyć nowy skoroszyt przy użyciu szablonu szybkiego startu.




1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wyszukaj usługę **Azure Active Directory**.

    ![Zrzut ekranu przedstawia Azure Active Directory w usłudze Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. W sekcji **monitorowanie** kliknij pozycję **skoroszyty**.

    ![Zrzut ekranu przedstawia monitorowanie w menu Azure Portal z wybranymi skoroszytami.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. W sekcji **Szybki Start** kliknij przycisk **pusty**.

    ![Szybki start](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Kliknij pozycję **Dodaj**.

    ![Dodaj skoroszyt](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Kliknij przycisk **Dodaj tekst**.

    ![Dodaj tekst](./media/tutorial-log-analytics-wizard/add-text.png)


7. W polu tekstowym wpisz: `# Client apps used in the past week` , a następnie kliknij pozycję **Zakończono edycję**.

    ![Tekst skoroszytu](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. W nowym skoroszycie kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Dodaj zapytanie**.

    ![Dodaj zapytanie](./media/tutorial-log-analytics-wizard/add-query.png)

9. W polu tekstowym zapytania wpisz: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Kliknij przycisk **Uruchom zapytanie**.

    ![Zrzut ekranu przedstawia przycisk Uruchom zapytanie.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Na pasku narzędzi w obszarze **Wizualizacja** kliknij pozycję **Wykres kołowy**.

    ![Wykres kołowy](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Kliknij pozycję **Zakończono edycję**.

    ![Zakończono edycję](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Dodawanie zapytania do szablonu skoroszytu  

Ta procedura pokazuje, jak dodać zapytanie do istniejącego szablonu skoroszytu. Przykład jest oparty na zapytaniu, które pokazuje rozkład powodzenia dostępu warunkowego do błędów.


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wyszukaj usługę **Azure Active Directory**.

    ![Zrzut ekranu przedstawia Azure Active Directory w usłudze Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. W sekcji **monitorowanie** kliknij pozycję **skoroszyty**.

    ![Zrzut ekranu przedstawia monitorowanie w menu z wybranymi skoroszytami.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. W sekcji **dostęp warunkowy** kliknij pozycję **szczegółowe informacje i raportowanie dostępu warunkowego**.

    ![Zrzut ekranu przedstawia opcję uzyskiwania dostępu warunkowego i raportowania.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Na pasku narzędzi kliknij przycisk **Edytuj**.

    ![Zrzut ekranu przedstawia przycisk Edytuj.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Na pasku narzędzi kliknij trzy kropki, a następnie **Dodaj**, a następnie **Dodaj zapytanie**.

    ![Dodaj zapytanie skoroszytu](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. W polu tekstowym zapytania wpisz: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Kliknij przycisk **Uruchom zapytanie**.

    ![Zrzut ekranu przedstawia przycisk Uruchom zapytanie, aby uruchomić to zapytanie.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Kliknij pozycję **zakres czasu**, a następnie wybierz pozycję **Ustaw w kwerendzie**.

10. Kliknij pozycję **Wizualizacja**, a następnie wybierz pozycję **Wykres słupkowy**. 

11. Kliknij pozycję **Ustawienia zaawansowane**, jako tytuł wykresu, typ `Conditional Access status over the last 20 days` , a następnie kliknij pozycję **Zakończono edycję**. 

    ![Ustaw tytuł wykresu](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać tożsamościami urządzeń za pomocą Azure Portal.
> [!div class="nextstepaction"]
> [Monitorowanie](overview-monitoring.md)