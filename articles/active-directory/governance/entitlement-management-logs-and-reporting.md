---
title: Archiwizowanie & za pomocą Azure Monitor — zarządzanie uprawnieniami usługi Azure AD
description: Dowiedz się, jak archiwizować dzienniki i tworzyć raporty za Azure Monitor w Azure Active Directory zarządzania uprawnieniami.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764391"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiwizowanie dzienników i raportowanie dotyczące zarządzania uprawnieniami usługi Azure AD w usłudze Azure Monitor

Usługa Azure AD przechowuje zdarzenia inspekcji przez maksymalnie 30 dni w dzienniku inspekcji. Można jednak przechowywać dane inspekcji dłużej niż domyślny okres przechowywania, opisany w tece Jak długo usługa Azure AD przechowuje dane [raportowania?](../reports-monitoring/reference-reports-data-retention.md), przekieruj je do konta usługi Azure Storage lub przy użyciu Azure Monitor. Następnie można używać skoroszytów oraz niestandardowych zapytań i raportów dotyczących tych danych.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurowanie usługi Azure AD do używania Azure Monitor
Przed użyciem Azure Monitor należy skonfigurować usługę Azure AD do wysyłania kopii dzienników inspekcji do usługi Azure Monitor.

Archiwizowanie dzienników inspekcji usługi Azure AD wymaga Azure Monitor subskrypcji platformy Azure. Więcej informacji na temat wymagań wstępnych i szacowanych kosztów korzystania z usługi Azure Monitor można znaleźć w dziennikach aktywności usługi [Azure AD w Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Rola wymagań wstępnych:** Administrator globalny

1. Zaloguj się do Azure Portal jako użytkownik, który jest administratorem globalnym. Upewnij się, że masz dostęp do grupy zasobów zawierającej Azure Monitor obszaru roboczego.
 
1. Wybierz **Azure Active Directory** następnie kliknij pozycję **Ustawienia diagnostyczne w** obszarze Monitorowanie w menu nawigacji po lewej stronie. Sprawdź, czy istnieje już ustawienie wysyłania dzienników inspekcji do tego obszaru roboczego.

1. Jeśli jeszcze nie istnieje ustawienie, kliknij pozycję **Dodaj ustawienie diagnostyczne.** Skorzystaj z instrukcji w artykule Integrowanie dzienników usługi Azure AD z [Azure Monitor,](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) aby wysłać dziennik inspekcji usługi Azure AD do Azure Monitor roboczego.

    ![Okienko ustawień diagnostycznych](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Po wysłaniu dziennika do usługi Azure Monitor obszarów roboczych usługi **Log Analytics** i wybierz obszar roboczy zawierający dzienniki inspekcji usługi Azure AD.

1. Wybierz **pozycję Użycie i szacowane koszty, a** następnie kliknij pozycję Przechowywanie **danych.** Zmień suwak na liczbę dni, przez które chcesz zachować dane w celu spełnienia wymagań inspekcji.

    ![Okienko obszarów roboczych usługi Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Później, aby wyświetlić zakres dat w obszarze roboczym, możesz użyć skoroszytu zarchiwizowany zakres *dat* dziennika:  
    
    1. Wybierz **Azure Active Directory** a następnie kliknij **pozycję Skoroszyty.** 
    
    1. Rozwiń **sekcję Azure Active Directory rozwiązywanie problemów,** a następnie kliknij pozycję Zakres dat **zarchiwizowanego dziennika.** 


## <a name="view-events-for-an-access-package"></a>Wyświetlanie zdarzeń dla pakietu dostępu  

Aby wyświetlić zdarzenia dla pakietu dostępu, musisz mieć dostęp do bazowego obszaru roboczego usługi Azure Monitor (zobacz Zarządzanie dostępem do danych dziennika i obszarów roboczych w usłudze [Azure Monitor,](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) aby uzyskać informacje) i w jednej z następujących ról: 

- Administrator globalny  
- Administrator zabezpieczeń  
- Czytelnik zabezpieczeń  
- Czytelnik raportów  
- administrator aplikacji  

Aby wyświetlić zdarzenia, użyj następującej procedury: 

1. W oknie Azure Portal wybierz pozycję **Azure Active Directory** a następnie kliknij **pozycję Skoroszyty.** Jeśli masz tylko jedną subskrypcję, przejdź do kroku 3. 

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję zawierającą obszar roboczy.  

1. Wybierz skoroszyt o nazwie Access Package Activity ( *Działanie pakietu dostępu).* 

1. W tym skoroszycie wybierz  zakres czasu (zmień na Wszystkie, jeśli nie masz pewności) i wybierz identyfikator pakietu dostępu z listy rozwijanej wszystkich pakietów dostępu, które miały aktywność w tym zakresie czasu. Zostaną wyświetlone zdarzenia związane z pakietem dostępu, które wystąpiły w wybranym zakresie czasu.  

    ![Wyświetlanie zdarzeń pakietu dostępu](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Każdy wiersz zawiera czas, identyfikator pakietu dostępu, nazwę operacji, identyfikator obiektu, nazwę UPN i nazwę wyświetlaną użytkownika, który uruchomił operację.  Dodatkowe szczegóły są zawarte w danych JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Tworzenie niestandardowych Azure Monitor zapytań przy użyciu Azure Portal
Możesz tworzyć własne zapytania dotyczące zdarzeń inspekcji usługi Azure AD, w tym zdarzeń zarządzania uprawnieniami.  

1. W Azure Active Directory okienku Azure Portal pozycję Dzienniki  w sekcji Monitorowanie w menu nawigacji po lewej stronie, aby utworzyć nową stronę zapytania.

1. Obszar roboczy powinien być wyświetlany w lewym górnym rogu strony zapytania. Jeśli masz wiele Azure Monitor roboczych, a obszar roboczy, który jest wyświetlany do przechowywania zdarzeń inspekcji usługi Azure AD, nie jest wyświetlany, kliknij pozycję **Wybierz zakres.** Następnie wybierz poprawną subskrypcję i obszar roboczy.

1. Następnie w obszarze tekstowym zapytania usuń ciąg "wyszukaj *" i zastąp go następującym zapytaniem:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Następnie kliknij pozycję **Uruchom**. 

    ![Kliknij pozycję Uruchom, aby uruchomić zapytanie](./media/entitlement-management-logs-and-reporting/run-query.png)

W tabeli będą domyślnie wyświetlane zdarzenia dziennika inspekcji na temat zarządzania uprawnieniami z ostatniej godziny. Możesz zmienić ustawienie "Zakres czasu", aby wyświetlić starsze zdarzenia. Jednak zmiana tego ustawienia spowoduje pokazanie tylko zdarzeń, które wystąpiły po skonfigurowaniu usługi Azure AD do wysyłania zdarzeń do Azure Monitor.

Jeśli chcesz poznać najstarsze i najnowsze zdarzenia inspekcji przechowywane w Azure Monitor, użyj następującego zapytania:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Aby uzyskać więcej informacji na temat kolumn, które są przechowywane dla zdarzeń inspekcji w usłudze Azure Monitor, zobacz Interpretowanie schematu dzienników inspekcji usługi Azure AD w [usłudze Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Tworzenie niestandardowych zapytań Azure Monitor przy użyciu Azure PowerShell

Dostęp do dzienników można uzyskać za pośrednictwem programu PowerShell po skonfigurowaniu usługi Azure AD do wysyłania dzienników do Azure Monitor. Następnie wysyłaj zapytania ze skryptów lub wiersza polecenia programu PowerShell bez konieczności być administratorem globalnym w dzierżawie. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Upewnij się, że użytkownik lub nazwa główna usługi ma poprawne przypisanie roli

Upewnij się, że użytkownik lub nazwa główna usługi, która będzie uwierzytelniana w usłudze Azure AD, należy do odpowiedniej roli platformy Azure w obszarze roboczym usługi Log Analytics. Dostępne są opcje roli Czytelnik usługi Log Analytics lub Współautor usługi Log Analytics. Jeśli masz już jedną z tych ról, przejdź do strony Pobieranie identyfikatora [usługi Log Analytics z jedną subskrypcją platformy Azure.](#retrieve-log-analytics-id-with-one-azure-subscription)

Aby ustawić przypisanie roli i utworzyć zapytanie, wykonaj następujące czynności:

1. Na stronie Azure Portal obszar roboczy [usługi Log Analytics.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. Wybierz **Access Control (IAM)**.

1. Następnie kliknij **przycisk Dodaj,** aby dodać przypisanie roli.

    ![Dodaj przypisanie roli](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalowanie Azure PowerShell modułu

Po przypisaniu odpowiedniej roli uruchom program PowerShell i zainstaluj moduł [Azure PowerShell](/powershell/azure/install-az-ps) (jeśli jeszcze nie został), wpisując:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Teraz możesz rozpocząć uwierzytelnianie w usłudze Azure AD i pobrać identyfikator obszaru roboczego usługi Log Analytics, dla których są zapytania.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Pobieranie identyfikatora usługi Log Analytics z jedną subskrypcją platformy Azure
Jeśli masz tylko jedną subskrypcję platformy Azure i pojedynczy obszar roboczy usługi Log Analytics, wpisz następujące dane, aby uwierzytelnić się w usłudze Azure AD, nawiązać połączenie z subskrypcją i pobrać ten obszar roboczy:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Pobieranie identyfikatora usługi Log Analytics z wieloma subskrypcjami platformy Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) działa w ramach jednej subskrypcji na raz. Dlatego jeśli masz wiele subskrypcji platformy Azure, upewnij się, że łączysz się z obszarem roboczym usługi Log Analytics z dziennikami usługi Azure AD. 
 
 Następujące polecenia cmdlet wyświetlają listę subskrypcji i znajdują identyfikator subskrypcji, która ma obszar roboczy usługi Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Możesz ponownie uwierzytelnić i skojarzyć sesję programu PowerShell z subskrypcją za pomocą polecenia, takiego jak `Connect-AzAccount –Subscription $subs[0].id` . Aby dowiedzieć się więcej na temat sposobu uwierzytelniania na platformie Azure z programu PowerShell, w tym nieinterakcyjnie, zobacz Logowanie za [pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).

Jeśli w ramach tej subskrypcji masz wiele obszarów roboczych usługi Log Analytics, polecenie cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) zwraca listę obszarów roboczych. Następnie możesz znaleźć ten, który ma dzienniki usługi Azure AD. Pole zwrócone przez to polecenie cmdlet jest takie samo jak wartość "Identyfikator obszaru roboczego" wyświetlana w Azure Portal `CustomerId` w przeglądzie obszaru roboczego usługi Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Wysyłanie zapytania do obszaru roboczego usługi Log Analytics
Po zidentyfikowaniu obszaru roboczego możesz użyć [invoke-AzOperationalInsightsQuery,](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) aby wysłać zapytanie Kusto do tego obszaru roboczego. Te zapytania są pisane w [języku zapytań Kusto](/azure/kusto/query/).
 
Na przykład zakres dat rekordów zdarzeń inspekcji można pobrać z obszaru roboczego usługi Log Analytics, a polecenia cmdlet programu PowerShell wysyłają zapytanie, takie jak:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Zdarzenia zarządzania uprawnieniami można również pobrać przy użyciu zapytania, takiego jak:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Następne kroki:
- [Tworzenie interaktywnych raportów przy Azure Monitor skoroszytów](../../azure-monitor/visualize/workbooks-overview.md)
