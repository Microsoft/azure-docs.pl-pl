---
title: Archiwizowanie raportu & za pomocą usługi Azure Monitor — Zarządzanie prawami w usłudze Azure AD
description: Dowiedz się, jak archiwizować dzienniki i tworzyć raporty z Azure Monitor w Azure Active Directory zarządzania uprawnieniami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c5ab92fcc1d70d12e37ae351e768514b4e7522f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501706"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiwizuj dzienniki i raporty dotyczące zarządzania prawami usługi Azure AD w Azure Monitor

Usługa Azure AD przechowuje zdarzenia inspekcji przez maksymalnie 30 dni w dzienniku inspekcji. Dane inspekcji mogą jednak być przechowywane dłużej niż domyślny okres przechowywania, który [jest określany przez czas, w jakim usługa Azure AD przechowuje dane raportowania?](../reports-monitoring/reference-reports-data-retention.md), przez kierowanie go do konta usługi Azure Storage lub przy użyciu Azure monitor. Następnie można używać skoroszytów i zapytań niestandardowych i raportów dotyczących tych danych.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurowanie usługi Azure AD do używania Azure Monitor
Przed użyciem skoroszytów Azure Monitor należy skonfigurować usługę Azure AD w celu wysyłania kopii jej dzienników inspekcji do Azure Monitor.

Archiwizowanie dzienników inspekcji usługi Azure AD wymaga, aby Azure Monitor w ramach subskrypcji platformy Azure. Więcej informacji na temat wymagań wstępnych i szacowanych kosztów używania Azure Monitor w [dziennikach aktywności usługi Azure AD można znaleźć w Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Rola wymagana wstępnie**: Administrator globalny

1. Zaloguj się do Azure Portal jako użytkownik będący administratorem globalnym. Upewnij się, że masz dostęp do grupy zasobów zawierającej obszar roboczy Azure Monitor.
 
1. Wybierz **Azure Active Directory** następnie kliknij pozycję **Ustawienia diagnostyczne** w obszarze monitorowanie w menu nawigacji po lewej stronie. Sprawdź, czy istnieje już ustawienie wysyłania dzienników inspekcji do tego obszaru roboczego.

1. Jeśli nie ma jeszcze ustawienia, kliknij pozycję **Dodaj ustawienie diagnostyczne**. Instrukcje zawarte w tym artykule umożliwiają [integrację dzienników usługi Azure AD z dziennikami Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) w celu wysłania dziennika inspekcji usługi Azure AD do obszaru roboczego Azure monitor.

    ![Okienko ustawień diagnostycznych](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Po wysłaniu dziennika do Azure Monitor wybierz pozycję **log Analytics obszary robocze**, a następnie wybierz obszar roboczy zawierający dzienniki inspekcji usługi Azure AD.

1. Wybierz pozycję **użycie i szacowane koszty** , a następnie kliknij pozycję **przechowywanie danych**. Zmień suwak na liczbę dni, w ciągu których dane mają spełniać wymagania dotyczące inspekcji.

    ![Okienko obszarów roboczych Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Później, aby zobaczyć zakres dat przechowywanych w obszarze roboczym, możesz użyć skoroszytu *zakresu daty zarchiwizowanego dziennika* :  
    
    1. Wybierz **Azure Active Directory** a następnie kliknij pozycję **skoroszyty**. 
    
    1. Rozwiń sekcję **Azure Active Directory Rozwiązywanie problemów** i kliknij **zarchiwizowany zakres dat dzienników**. 


## <a name="view-events-for-an-access-package"></a>Wyświetlanie zdarzeń dla pakietu dostępu  

Aby wyświetlić zdarzenia dotyczące pakietu dostępu, musisz mieć dostęp do odpowiedniego obszaru roboczego usługi Azure monitor (zobacz [Zarządzanie dostępem do danych dziennika i obszarów roboczych w Azure monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) informacji) i w jednej z następujących ról: 

- Administrator globalny  
- Administrator zabezpieczeń  
- Czytelnik zabezpieczeń  
- Czytelnik raportu  
- Administrator aplikacji  

Aby wyświetlić zdarzenia, należy wykonać następującą procedurę: 

1. W Azure Portal wybierz pozycję **Azure Active Directory** , a następnie kliknij pozycję **skoroszyty**. Jeśli masz tylko jedną subskrypcję, przejdź do kroku 3. 

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, która zawiera obszar roboczy.  

1. Wybierz skoroszyt o nazwie *działanie pakietu dostępu*. 

1. W tym skoroszycie wybierz zakres czasu **(jeśli nie** jest to konieczne, Zmień na nie), a następnie wybierz identyfikator pakietu dostępu z listy rozwijanej wszystkich pakietów dostępu, które miały działanie w tym zakresie czasu. Zostaną wyświetlone zdarzenia związane z pakietem dostępu, które wystąpiły w wybranym zakresie czasu.  

    ![Wyświetl zdarzenia pakietu dostępu](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Każdy wiersz zawiera godzinę, identyfikator pakietu dostępu, nazwę operacji, identyfikator obiektu, UPN i nazwę wyświetlaną użytkownika, który uruchomił operację.  Dodatkowe szczegóły znajdują się w formacie JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Tworzenie niestandardowych zapytań Azure Monitor przy użyciu Azure Portal
Możesz tworzyć własne zapytania dotyczące zdarzeń inspekcji usługi Azure AD, w tym zdarzeń związanych z zarządzaniem uprawnieniami.  

1. W Azure Active Directory Azure Portal kliknij pozycję **dzienniki** w sekcji monitorowanie w menu nawigacji po lewej stronie, aby utworzyć nową stronę zapytania.

1. Obszar roboczy powinien być pokazywany w lewym górnym rogu strony zapytania. Jeśli masz wiele obszarów roboczych Azure Monitor i obszar roboczy, którego używasz do przechowywania zdarzeń inspekcji usługi Azure AD, nie jest wyświetlany, kliknij przycisk **Wybierz zakres**. Następnie wybierz odpowiednią subskrypcję i obszar roboczy.

1. Następnie w obszarze tekstu zapytania Usuń ciąg "Search *" i zastąp go następującym zapytaniem:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Następnie kliknij przycisk **Uruchom**. 

    ![Kliknij przycisk Uruchom, aby rozpocząć zapytanie](./media/entitlement-management-logs-and-reporting/run-query.png)

W tabeli będą domyślnie wyświetlane zdarzenia dziennika inspekcji dla zarządzania uprawnieniami z ostatniej godziny. Można zmienić ustawienie "zakres czasu", aby wyświetlić starsze zdarzenia. Jednak zmiana tego ustawienia spowoduje wyświetlenie tylko zdarzeń, które wystąpiły po skonfigurowaniu usługi Azure AD do wysyłania zdarzeń do Azure Monitor.

Jeśli chcesz znać najstarsze i najnowsze zdarzenia inspekcji przechowywane w Azure Monitor, użyj następującego zapytania:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Aby uzyskać więcej informacji na temat kolumn przechowywanych dla zdarzeń inspekcji w Azure Monitor, zobacz [interpretowanie schematu dzienników inspekcji usługi Azure AD w programie Azure monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Tworzenie niestandardowych zapytań Azure Monitor przy użyciu Azure PowerShell

Możesz uzyskać dostęp do dzienników za pomocą programu PowerShell po skonfigurowaniu usługi Azure AD w celu wysyłania dzienników do Azure Monitor. Następnie należy wysyłać zapytania ze skryptów lub wiersza polecenia programu PowerShell, bez konieczności być administratorem globalnym w dzierżawie. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Upewnij się, że nazwa główna użytkownika lub usługi ma poprawne przypisanie roli

Upewnij się, że nazwa główna użytkownika lub usługi, która będzie uwierzytelniana w usłudze Azure AD, znajduje się w odpowiedniej roli platformy Azure w obszarze roboczym Log Analytics. Opcje roli są Log Analytics Reader lub współautorem Log Analytics. Jeśli jesteś już w jednej z tych ról, Pomiń, aby [pobrać identyfikator log Analytics z jedną subskrypcją platformy Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Aby ustawić przypisanie roli i utworzyć zapytanie, wykonaj następujące czynności:

1. W Azure Portal odszukaj [obszar roboczy log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Wybierz pozycję **Access Control (IAM)**.

1. Następnie kliknij przycisk **Dodaj** , aby dodać przypisanie roli.

    ![Dodaj przypisanie roli](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Zainstaluj moduł Azure PowerShell

Gdy masz odpowiednie przypisanie roli, uruchom program PowerShell i [Zainstaluj moduł Azure PowerShell](/powershell/azure/install-az-ps) (jeśli jeszcze tego nie zrobiono), wpisując:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Teraz możesz przystąpić do uwierzytelniania w usłudze Azure AD i pobrać identyfikator obszaru roboczego, z którego korzystasz Log Analytics.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Pobierz identyfikator Log Analytics z jedną subskrypcją platformy Azure
Jeśli masz tylko jedną subskrypcję platformy Azure i jeden obszar roboczy Log Analytics, wpisz następujące polecenie, aby uwierzytelnić się w usłudze Azure AD, nawiązać połączenie z tą subskrypcją i pobrać ten obszar roboczy:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Pobieranie identyfikatora Log Analytics z wieloma subskrypcjami platformy Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) działa w jednej subskrypcji w danym momencie. Dlatego jeśli masz wiele subskrypcji platformy Azure, upewnij się, że masz połączenie z tym obszarem roboczym Log Analytics z dziennikami usługi Azure AD. 
 
 Następujące polecenia cmdlet wyświetlają listę subskrypcji i znajdują Identyfikator subskrypcji z obszarem roboczym Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Możesz ponownie uwierzytelnić i skojarzyć sesję programu PowerShell z tą subskrypcją za pomocą polecenia, takiego jak `Connect-AzAccount –Subscription $subs[0].id` . Aby dowiedzieć się więcej o sposobie uwierzytelniania na platformie Azure za pomocą programu PowerShell, w tym nieinteraktywnie, zobacz artykuł [Logowanie przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps).

Jeśli masz wiele obszarów roboczych Log Analytics w tej subskrypcji, polecenie cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) zwraca listę obszarów roboczych. Następnie można znaleźć taki, który zawiera dzienniki usługi Azure AD. `CustomerId`Pole zwrócone przez to polecenie cmdlet jest takie samo jak wartość "identyfikator obszaru roboczego" wyświetlaną w Azure Portal w obszarze roboczym log Analytics Omówienie.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Wyślij zapytanie do obszaru roboczego Log Analytics
Na koniec po zidentyfikowaniu obszaru roboczego można użyć [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) do wysłania zapytania Kusto do tego obszaru roboczego. Te zapytania są zapisywane w [języku zapytań Kusto](/azure/kusto/query/).
 
Na przykład możesz pobrać zakres dat dla rekordów zdarzeń inspekcji z obszaru roboczego Log Analytics, za pomocą poleceń cmdlet programu PowerShell, aby wysłać zapytanie, takie jak:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Możesz również pobrać zdarzenia zarządzania uprawnieniami za pomocą zapytania, takie jak:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Następne kroki:
- [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md)