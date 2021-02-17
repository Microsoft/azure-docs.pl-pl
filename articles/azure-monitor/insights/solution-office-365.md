---
title: Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfiguracji i używania rozwiązania pakietu Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordów pakietu Office 365 utworzonych w Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: d031b64b36bd7ff91c64960642cfbf00fb1682ab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587311"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure (wersja zapoznawcza)

![Logo pakietu Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Aktualizacja rozwiązania
> To rozwiązanie zostało zastąpione przez rozwiązanie "ogólne dostępność" [pakietu Office 365](../../sentinel/connect-office-365.md) na [platformie Azure](../../sentinel/overview.md) — dane i [rozwiązanie do monitorowania i raportowania usługi Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Razem z udoskonaloną konfiguracją zapewnią zaktualizowaną wersję poprzedniego rozwiązania Azure Monitor Office 365. Możesz nadal korzystać z istniejącego rozwiązania do 31 października 2020.
> 
> Azure — badanie informacji o zabezpieczeniach i rozwiązania do zarządzania zdarzeniami w chmurze, które pozyskają dzienniki i udostępniają dodatkowe funkcje SIEM, w tym wykrywania, badań, łowiectwa i uczenia maszynowego. Korzystanie z funkcji wskaźnikowej platformy Azure umożliwi teraz pozyskiwanie dzienników zarządzania aktywnością programu Office 365 i programu Exchange.
> 
> Usługa Azure AD Reporting oferuje bardziej obszerny widok dzienników z działania usługi Azure AD w Twoim środowisku, w tym zdarzeń logowania, zdarzeń inspekcji i zmian w katalogu. Aby nawiązać połączenie z dziennikami usługi Azure AD, możesz użyć [łącznika usługi Azure AD wskaźnikowego](../../sentinel/connect-azure-active-directory.md) Azure lub skonfigurować [integrację dzienników usługi Azure ad z usługą Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> W kolekcji dzienników usługi Azure AD podlegają cenniki Azure Monitor.  Aby uzyskać więcej informacji, zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .
>
> Aby korzystać z rozwiązania Azure wskaźnikowego dla pakietu Office 365:
> 1. Korzystanie z łącznika pakietu Office 365 na platformie Azure wskaźnikowej wpływa na ceny obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure wskaźnikowej](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Jeśli korzystasz już z rozwiązania Azure Monitor Office 365, najpierw należy je odinstalować za pomocą skryptu w [poniższej sekcji odinstalowywania](#uninstall).
> 3. [Włącz rozwiązanie Azure](../../sentinel/quickstart-onboard.md) nazwa_obszaru_roboczego w Twoim obszarze roboczym.
> 4. Przejdź do strony **Łączniki danych** na platformie Azure, a następnie Włącz łącznik **pakietu Office 365** .
>
> ## <a name="frequently-asked-questions"></a>Często zadawane pytania
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>P: Czy możliwe jest dołączenie rozwiązania pakietu Office 365 Azure Monitor od razu do 31 października?
> Nie, Azure Monitor skrypty dołączania rozwiązania pakietu Office 365 nie są już dostępne. Rozwiązanie zostanie usunięte 31 października.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: czy tabele i schematy zostaną zmienione?
> Nazwa i schemat tabeli **pakietu Office** będą nadal takie same, jak w bieżącym rozwiązaniu. Można nadal używać tych samych zapytań w nowym rozwiązaniu, wykluczając zapytania, które odwołują się do danych usługi Azure AD.
> 
> Nowe dzienniki [rozwiązań raportowania i monitorowania usługi Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) zostaną pozyskane do tabel [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) i [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) zamiast z **pakietu Office**. Aby uzyskać więcej informacji, zobacz [jak analizować dzienniki usługi Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), które są również odpowiednie dla użytkowników usług Azure wskaźnikowych i Azure monitor.
> 
> Poniżej znajdują się przykłady konwersji zapytań z **pakietu Office** do **SigninLogs**:
> 
> **Nieudane logowania zapytania przez użytkownika:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Wyświetl operacje usługi Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: Jak mogę dołączyć wskaźnik do platformy Azure?
> Azure — Wskaźnikowanie to rozwiązanie, które można włączyć w nowym lub istniejącym Log Analytics obszarze roboczym. Aby dowiedzieć się więcej, zobacz artykuł [dotyczący dokumentacji dotyczącej platformy Azure](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: czy do łączenia dzienników usługi Azure AD jest potrzebny wskaźnik na platformie Azure?
> Można skonfigurować [integrację dzienników usługi Azure AD z usługą Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), która nie jest powiązana z rozwiązaniem wskaźnikowym platformy Azure. Wskaźnik "Azure" obejmuje natywną i wbudowaną zawartość dla dzienników usługi Azure AD. Aby uzyskać więcej informacji, zobacz pytanie poniżej dotyczące nieaktualnej zawartości zorientowanej na zabezpieczenia.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: Jakie są różnice podczas łączenia dzienników usługi Azure AD z usług wskaźnikowych i Azure Monitor platformy Azure?
> Azure — Wskaźnikowanie i Azure Monitor łączenie się z dziennikami usługi Azure AD na podstawie tego samego [rozwiązania do raportowania i monitorowania usługi Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Wskaźnik na platformie Azure obejmuje jeden kliknięcia łącznika natywnego, który łączy te same dane i udostępnia informacje dotyczące monitorowania.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: co należy zmienić podczas przechodzenia do nowych tabel raportowania i monitorowania usługi Azure AD?
> Wszystkie zapytania korzystające z danych usługi Azure AD, w tym zapytania w alertach, pulpity nawigacyjne i zawartość utworzoną przy użyciu danych usługi Office 365 Azure AD, muszą zostać utworzone ponownie przy użyciu nowych tabel.
>
> Usługa Azure — wskaźnik i usługa Azure AD udostępniają wbudowaną zawartość, której można użyć podczas przechodzenia do rozwiązania raportowania i monitorowania usługi Azure AD. Aby uzyskać więcej informacji, zapoznaj się z następnym pytaniem dotyczącym wbudowanej zawartości zorientowanej na zabezpieczenia oraz [jak używać Azure monitor skoroszytów dla raportów Azure Active Directoryowych](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: Jak mogę korzystać z wbudowanej zawartości "wskaźnik" na platformie Azure.
> Funkcja badania platformy Azure obejmuje wbudowane pulpity nawigacyjne oparte na zabezpieczeniach, niestandardowe zapytania dotyczące alertów, zapytania dotyczące łowiectwa, badanie i automatyczne możliwości odpowiedzi na podstawie dzienników pakietu Office 365 i usługi Azure AD. Więcej informacji można znaleźć w witrynie GitHub i samouczków platformy Azure, aby dowiedzieć się więcej:
>
> - [Wykrywaj zagrożenia wbudowane](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Tworzenie niestandardowych reguł analitycznych do wykrywania podejrzanych zagrożeń](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorowanie danych](../../sentinel/tutorial-monitor-your-data.md)
> - [Badanie zdarzeń za pomocą usługi Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Społeczność usługi GitHub na platformie Azure](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: czy wskaźnik "platformy Azure" udostępnia dodatkowe łączniki jako część rozwiązania?
> Tak, zobacz [źródła danych w programie Azure wskaźnikowym Connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>P: co się stanie w dniu 31 października? Czy muszę odłączania wcześniej?
> 
> - Nie będzie można odbierać danych z rozwiązania usługi **Office 365** . Rozwiązanie zostanie usunięte z obszaru roboczego i nie będzie już dostępne w portalu Marketplace.
> - W przypadku klientów z wskaźnikiem na platformie Azure — rozwiązanie obszaru roboczego Log Analytics w usłudze **Office 365** zostanie uwzględnione w rozwiązaniu usługi Azure **SecurityInsights** .
> - Jeśli Twoje rozwiązanie nie zostanie odłączania ręcznie do 31 października, dane zostaną rozłączone automatycznie, a tabela **pakietu Office** zostanie usunięta. Nawet w dalszym ciągu będzie można przywrócić tabelę po włączeniu łącznika pakietu Office 365 na platformie Azure wskaźnikowej, jak wyjaśniono poniżej.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: czy dane zostaną przesłane do nowego rozwiązania?
> Tak. Po usunięciu rozwiązania **pakietu Office 365** z obszaru roboczego jego dane staną się tymczasowo niedostępne, ponieważ schemat został usunięty. Po włączeniu nowego łącznika **pakietu Office 365** w wskaźniku kontrolnym platformy Azure schemat zostanie przywrócony do obszaru roboczego, a wszystkie zebrane dane staną się dostępne. 
 

Rozwiązanie do zarządzania pakietem Office 365 umożliwia monitorowanie środowiska pakietu Office 365 w Azure Monitor.

- Monitoruj działania użytkowników na kontach pakietu Office 365, aby analizować wzorce użycia, a także identyfikować trendy behawioralne. Na przykład można wyodrębnić określone scenariusze użycia, takie jak pliki, które są udostępniane poza organizacją lub najpopularniejszymi witrynami programu SharePoint.
- Monitoruj działania administratora, aby śledzić zmiany konfiguracji lub operacje o wysokim poziomie uprawnień.
- Wykrywaj i badaj niepożądane zachowanie użytkowników, które można dostosować do potrzeb organizacji.
- Zademonstrowanie inspekcji i zgodności. Na przykład można monitorować operacje dostępu do plików dla poufnych plików, co może ułatwić proces inspekcji i zgodności.
- Rozwiązywanie problemów operacyjnych przy użyciu [zapytań dzienników](../logs/log-query-overview.md) w oparciu o dane działania pakietu Office 365 w organizacji.


## <a name="uninstall"></a>Odinstalowanie

Rozwiązanie do zarządzania pakietem Office 365 można usunąć, korzystając z procesu w temacie [usuwanie rozwiązania do zarządzania](solutions.md#remove-a-monitoring-solution). Nie spowoduje to zatrzymania zbierania danych z pakietu Office 365 do Azure Monitor. Wykonaj poniższą procedurę, aby anulować subskrypcję pakietu Office 365 i zatrzymać zbieranie danych.

1. Zapisz następujący skrypt jako *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Uruchom skrypt za pomocą następującego polecenia:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Przykład:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Zostanie wyświetlony monit o podanie poświadczeń. Podaj poświadczenia dla obszaru roboczego Log Analytics.

## <a name="data-collection"></a>Zbieranie danych

Zebranie danych może potrwać kilka godzin. Po rozpoczęciu zbierania pakiet Office 365 wysyła [powiadomienie elementu webhook](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) z szczegółowymi danymi do Azure monitor przy każdym utworzeniu rekordu. Ten rekord jest dostępny w Azure Monitor w ciągu kilku minut od momentu odebrania.

## <a name="using-the-solution"></a>Użycie rozwiązania

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po dodaniu rozwiązania pakietu Office 365 do obszaru roboczego Log Analytics, kafelek **pakietu office 365** zostanie dodany do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
![Kafelek podsumowania pakietu Office 365](media/solution-office-365/tile.png)  

Kliknij kafelek **office 365** , aby otworzyć pulpit nawigacyjny **pakietu Office 365** .

![Pulpit nawigacyjny pakietu Office 365](media/solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera dziesięć najważniejszych alertów według liczby pasujących do kryteriów tej kolumny dla określonego zakresu i zakresu czasu. Możesz uruchomić wyszukiwanie w dzienniku, które udostępnia całą listę, klikając pozycję Zobacz wszystko u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--|:--|
| Operacje | Zawiera informacje o aktywnych użytkownikach ze wszystkich monitorowanych subskrypcji pakietu Office 365. Zobaczysz również liczbę działań, które wystąpiły w czasie.
| Exchange | Pokazuje podział działań programu Exchange Server, takich jak uprawnienia Add-Mailbox lub Set-Mailbox. |
| SharePoint | Przedstawia najważniejsze działania wykonywane przez użytkowników w dokumentach programu SharePoint. Podczas przechodzenia do szczegółów z tego kafelka na stronie wyszukiwania są wyświetlane szczegółowe informacje o tych działaniach, takie jak dokument docelowy i lokalizacja tego działania. Na przykład dla zdarzenia, do którego jest uzyskiwany dostęp do pliku, będzie można wyświetlić dokument, do którego jest uzyskiwany dostęp, jego nazwę skojarzonego konta i adres IP. |
| Azure Active Directory | Obejmuje działania najważniejszych użytkowników, takie jak resetowanie hasła użytkownika i logowania. Po przejściu do szczegółów będzie można zobaczyć szczegóły tych działań, jak w przypadku stanu wyniku. Jest to szczególnie przydatne, jeśli chcesz monitorować podejrzane działania na Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika

Wszystkie rekordy utworzone w obszarze roboczym Log Analytics w Azure Monitor przez rozwiązanie pakietu Office 365 mają **Typ** **biura**.  Właściwość **OfficeWorkload** określa, do której usługi Office 365 odwołuje się rekord — Exchange, usługi azureactivedirectory, SharePoint lub OneDrive.  Właściwość **recordType** określa typ operacji.  Właściwości różnią się w zależności od typu operacji i są wyświetlane w tabelach poniżej.

### <a name="common-properties"></a>Wspólne właściwości

Następujące właściwości są wspólne dla wszystkich rekordów pakietu Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *Pakiet Office* |
| ClientIP | Adres IP urządzenia, które było używane podczas zarejestrowania działania. Adres IP jest wyświetlany w formacie IPv4 lub IPv6. |
| OfficeWorkload | Usługa Office 365, do której odwołuje się rekord.<br><br>Usługi azureactivedirectory<br>Exchange<br>SharePoint|
| Operacja | Nazwa działania użytkownika lub administratora.  |
| OrganizationId | Identyfikator GUID dzierżawy pakietu Office 365 w organizacji. Ta wartość będzie zawsze taka sama dla organizacji, niezależnie od usługi Office 365, w której występuje. |
| RecordType | Typ wykonywanej operacji. |
| ResultStatus | Wskazuje, czy akcja (określona we właściwości Operation) zakończyła się powodzeniem. Możliwe wartości to sukces, PartiallySucceeded lub niepowodzenie. W przypadku działania administracyjnego programu Exchange wartością jest true lub false. |
| UserId | Nazwa UPN użytkownika, który wykonał akcję, która spowodowała zarejestrowanie rekordu; na przykład my_name@my_domain_name . Należy pamiętać, że rekordy dla działania wykonywanego przez konta systemu (takie jak SHAREPOINT\system lub systemowe NTAUTHORITY\SYSTEM) również są uwzględnione. | 
| UserKey | Alternatywny identyfikator użytkownika zidentyfikowany we właściwości UserId.  Na przykład ta właściwość jest wypełniana unikatowym IDENTYFIKATORem (PUID) usługi Passport dla zdarzeń wykonywanych przez użytkowników w usługach SharePoint, OneDrive dla firm i Exchange. Ta właściwość może również określać taką samą wartość jak Właściwość UserID dla zdarzeń występujących w innych usługach i zdarzeń wykonywanych przez konta systemowe|
| UserType | Typ użytkownika, który wykonał operację.<br><br>Administrator<br>Aplikacja<br>DcAdmin<br>Zwykły<br>Zarezerwowany<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Baza Azure Active Directory

Następujące właściwości są wspólne dla wszystkich Azure Active Directory rekordów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi azureactivedirectory |
| RecordType     | Usługi azureactivedirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| ExtendedProperties | Właściwości rozszerzone zdarzenia usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory logowanie do konta

Te rekordy są tworzone, gdy użytkownik Active Directory próbuje się zalogować.

| Właściwość | Opis |
|:--- |:--- |
| `OfficeWorkload` | Usługi azureactivedirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Aplikacja, która wyzwala zdarzenie logowania do konta, takie jak Office 15. |
| `Client` | Szczegóły dotyczące urządzenia klienckiego, systemu operacyjnego urządzenia i przeglądarki, która była używana na potrzeby zdarzenia logowania do konta. |
| `LoginStatus` | Ta właściwość jest bezpośrednio z OrgIdLogon. stanu logowania. Mapowanie różnych niepowodzeń logowania może odbywać się przy użyciu algorytmów alertów. |
| `UserDomain` | Informacje o tożsamości dzierżawy (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Te rekordy są tworzone w przypadku wprowadzenia zmian lub uzupełnień do Azure Active Directory obiektów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi azureactivedirectory |
| RecordType     | Usługi azureactivedirectory |
| AADTarget | Użytkownik wykonał akcję (identyfikowaną przez właściwość operacji). |
| Aktor | Nazwa główna użytkownika lub usługi, która wykonał akcję. |
| ActorContextId | Identyfikator GUID organizacji, do której należy aktor. |
| ActorIpAddress | Adres IP aktora w formacie adresu IPV4 lub IPV6. |
| InterSystemsId | Identyfikator GUID, który śledzi akcje między składnikami w ramach usługi Office 365. |
| IntraSystemId |     Identyfikator GUID, który jest generowany przez Azure Active Directory do śledzenia akcji. |
| SupportTicketId | Identyfikator biletu obsługi klienta dla akcji w sytuacjach "czynność-w imieniu". |
| TargetContextId | Identyfikator GUID organizacji, do której należy użytkownik dokierowany. |


### <a name="data-center-security"></a>Zabezpieczenia centrum danych

Te rekordy są tworzone na podstawie danych inspekcji zabezpieczeń centrum danych.  

| Właściwość | Opis |
|:--- |:--- |
| EffectiveOrganization | Nazwa dzierżawy, w której nastąpiło podniesienie uprawnień/polecenie cmdlet. |
| ElevationApprovedTime | Sygnatura czasowa dla momentu zatwierdzenia podniesienia uprawnień. |
| ElevationApprover | Nazwa programu Microsoft Manager. |
| ElevationDuration | Czas, przez który podniesienie uprawnień było aktywne. |
| ElevationRequestId |     Unikatowy identyfikator żądania podniesienia uprawnień. |
| ElevationRole | Rola, dla której zażądano podniesienia uprawnień. |
| ElevationTime | Godzina rozpoczęcia podniesienia uprawnień. |
| Start_Time | Godzina rozpoczęcia wykonywania polecenia cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian w konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Określa, czy polecenie cmdlet zostało uruchomione przez użytkownika w organizacji, przez personel centrum danych firmy Microsoft lub konto usługi centrum danych, czy przez administratora delegowanego. Wartość false wskazuje, że polecenie cmdlet zostało uruchomione przez kogoś w organizacji. Wartość true wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konto usługi centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |     Jest to przyjazna dla użytkownika nazwa obiektu, który został zmodyfikowany przez polecenie cmdlet. Jest to rejestrowane tylko wtedy, gdy polecenie cmdlet modyfikuje obiekt. |
| OrganizationName | Nazwa dzierżawy. |
| OriginatingServer | Nazwa serwera, z którego zostało wykonane polecenie cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet, które zostały zidentyfikowane we właściwości Operations. |


### <a name="exchange-mailbox"></a>Skrzynka pocztowa programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień do skrzynek pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informacje dotyczące klienta poczty e-mail użytego do wykonania tej operacji, takie jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniu przenośnym. |
| Client_IPAddress | Adres IP urządzenia, który został użyty podczas rejestrowania operacji. Adres IP jest wyświetlany w formacie IPv4 lub IPv6. |
| ClientMachineName | Nazwa komputera, który hostuje klienta programu Outlook. |
| ClientProcessName | Klient poczty e-mail, który był używany do uzyskiwania dostępu do skrzynki pocztowej. |
| ClientVersion | Wersja klienta poczty e-mail. |
| InternalLogonType | Zarezerwowane do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który uzyskał dostęp do skrzynki pocztowej i wykonał operację, która została zarejestrowana. |
| LogonUserDisplayName |     Przyjazna dla użytkownika nazwa użytkownika, który wykonał operację. |
| LogonUserSid | Identyfikator SID użytkownika, który wykonał operację. |
| MailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp. |
| MailboxOwnerMasterAccountSid | Identyfikator SID konta właściciela skrzynki pocztowej. |
| MailboxOwnerSid | Identyfikator SID właściciela skrzynki pocztowej. |
| MailboxOwnerUPN | Adres e-mail osoby będącej właścicielem skrzynki pocztowej, do której uzyskano dostęp. |


### <a name="exchange-mailbox-audit"></a>Inspekcja skrzynek pocztowych programu Exchange

Te rekordy są tworzone podczas tworzenia wpisu inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Element | Reprezentuje element, na którym wykonano operację | 
| SendAsUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp do wysyłania wiadomości e-mail. |
| SendAsUserSmtp | Adres SMTP użytkownika, który jest personifikowany. |
| SendonBehalfOfUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp do wysyłania poczty w imieniu użytkownika. |
| SendOnBehalfOfUserSmtp | Adres SMTP użytkownika, w którym jest wysyłana wiadomość e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupa inspekcji skrzynek pocztowych programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień do grup programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperations | Wskazuje, czy operacja dotyczyła więcej niż jednej skrzynki pocztowej. |
| DestMailboxId | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator GUID docelowej skrzynki pocztowej. |
| DestMailboxOwnerMasterAccountSid | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator SID konta głównego właściciela skrzynki pocztowej. |
| DestMailboxOwnerSid | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator SID docelowej skrzynki pocztowej. |
| DestMailboxOwnerUPN | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa nazwę UPN właściciela docelowej skrzynki pocztowej. |
| DestFolder | Folder docelowy dla operacji takich jak Przenieś. |
| Folder | Folder, w którym znajduje się grupa elementów. |
| Foldery |     Informacje o folderach źródłowych związanych z operacjami; na przykład jeśli foldery są zaznaczone, a następnie usunięte. |


### <a name="sharepoint-base"></a>Baza programu SharePoint

Te właściwości są wspólne dla wszystkich rekordów programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Wskazuje, że zdarzenie wystąpiło w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, do którego uzyskano dostęp lub zmodyfikowany. Zobacz tabelę ItemType, aby uzyskać szczegółowe informacje na temat typów obiektów. |
| MachineDomainInfo | Informacje o operacjach synchronizacji urządzeń. Te informacje są raportowane tylko wtedy, gdy znajdują się w żądaniu. |
| MachineId |     Informacje o operacjach synchronizacji urządzeń. Te informacje są raportowane tylko wtedy, gdy znajdują się w żądaniu. |
| Site_ | Identyfikator GUID witryny, w której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| Source_Name | Jednostka, która wyzwoliła operację poddane inspekcji. Możliwe wartości to SharePoint lub ObjectModel. |
| UserAgent | Informacje na temat klienta lub przeglądarki użytkownika. Te informacje są udostępniane przez klienta lub przeglądarkę. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint

Te rekordy są tworzone po wprowadzeniu zmian w konfiguracji programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Opcjonalny ciąg dla zdarzeń niestandardowych. |
| Event_Data |     Opcjonalny ładunek dla zdarzeń niestandardowych. |
| ModifiedProperties | Właściwość jest uwzględniana dla zdarzeń administracyjnych, takich jak dodanie użytkownika jako członka lokacji lub grupy administratorów kolekcji witryn. Właściwość zawiera nazwę zmodyfikowanej właściwości (na przykład grupy administratorów lokacji), nową wartość zmodyfikowanej właściwości (takiego użytkownika, który został dodany jako administrator lokacji) i poprzednią wartość zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint

Te rekordy są tworzone w odpowiedzi na operacje na plikach w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Rozszerzenie pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| DestinationFileName | Nazwa pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| DestinationRelativeUrl | Adres URL folderu docelowego, do którego plik jest kopiowany lub przenoszony. Kombinacja wartości parametrów SiteURL, DestinationRelativeURL i DestinationFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki dla pliku, który został skopiowany. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| Udostępnianietype | Typ uprawnień do udostępniania, które zostały przypisane do użytkownika, z którym udostępniono zasób. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, w której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| SourceFileExtension | Rozszerzenie pliku, do którego dostęp jest uzyskiwany przez użytkownika. Ta właściwość jest pusta, jeśli obiekt, do którego uzyskano dostęp, jest folderem. |
| SourceFileName |     Nazwa pliku lub folderu, do którego użytkownik uzyskuje dostęp. |
| SourceRelativeUrl | Adres URL folderu, który zawiera plik, do którego użytkownik uzyskuje dostęp. Kombinacja wartości parametrów SiteURL, SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki do pliku, do którego uzyskuje dostęp użytkownik. |
| UserSharedWith |     Użytkownik, do którego udostępniono zasób. |




## <a name="sample-log-queries"></a>Przykładowe zapytania dziennika

Poniższa tabela zawiera przykładowe zapytania dzienników dla rekordów aktualizacji zebranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji pakietu Office 365 |Pakiet Office &#124; sumowanie () według operacji |
|Użycie witryn programu SharePoint|Pakiet Office &#124;, gdzie OfficeWorkload = ~ "SharePoint" &#124; Sumuj liczbę () przez SiteUrl \| Sortuj według liczby ASC|
|Operacje dostępu do plików według typu użytkownika | Pakiet Office &#124; Sumuj liczbę () według użytkownika |
|Monitoruj zewnętrzne działania w programie Exchange|Pakiet Office &#124;, gdzie OfficeWorkload = ~ "Exchange" i ExternalAccess = = true|



## <a name="next-steps"></a>Następne kroki

* Użyj [zapytań dzienników w Azure monitor](../logs/log-query-overview.md) , aby wyświetlić szczegółowe dane aktualizacji.
* [Utwórz własne pulpity nawigacyjne](../visualize/tutorial-logs-dashboards.md) , aby wyświetlić ulubione zapytania wyszukiwania pakietu Office 365.
* [Twórz Alerty](../alerts/alerts-overview.md) , aby otrzymywać powiadomienia o ważnych działaniach związanych z pakietem Office 365.  
