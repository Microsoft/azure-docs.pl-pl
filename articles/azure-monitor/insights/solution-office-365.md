---
title: Rozwiązanie do zarządzania usługą Office 365 na platformie Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje na temat konfiguracji i korzystania z rozwiązania usługi Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordów usługi Office 365 utworzonych w usłudze Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 2c6eb5407ec62b6e9e771ce257b66fca2a91e0a5
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023609"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania usługą Office 365 na platformie Azure (wersja zapoznawcza)

![Logo usługi Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Aktualizacja rozwiązania
> To rozwiązanie zostało zastąpione przez rozwiązanie do ogólnej dostępności [usługi Office 365](../../sentinel/connect-office-365.md) w [usłudze Azure Sentinel](../../sentinel/overview.md) oraz rozwiązanie do [raportowania i monitorowania usługi Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Razem zapewniają zaktualizowaną wersję poprzedniego rozwiązania usługi Azure Monitor Office 365 z ulepszonym doświadczeniem konfiguracji. Z istniejącego rozwiązania można korzystać do 30 lipca 2020 roku.
> 
> Usługa Azure Sentinel to natywne rozwiązanie do zarządzania informacjami i zdarzeniami w chmurze, które posyła dzienniki i zapewnia dodatkowe funkcje SIEM, w tym wykrywanie, badania, polowania i analizy oparte na uczeniu maszynowym. Korzystanie z usługi Azure Sentinel zapewni teraz pozyskiwania dzienników aktywności programu SharePoint i programu Exchange w usłudze Office 365.
> 
> Raportowanie usługi Azure AD zapewnia bardziej kompleksowy widok dzienników z działania usługi Azure AD w twoim środowisku, w tym zdarzenia logowania, zdarzenia inspekcji i zmiany w katalogu. Aby połączyć dzienniki usługi Azure AD, można użyć [łącznika usługi Azure Sentinel Azure AD](../../sentinel/connect-azure-active-directory.md) lub skonfigurować [integrację dzienników usługi Azure AD z usługą Azure Monitor.](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 
>
> Kolekcja dziennika usługi Azure AD podlega cennikom usługi Azure Monitor.  Więcej informacji można znaleźć w [cenniku usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)
>
> Aby użyć rozwiązania usługi Azure Sentinel Office 365:
> 1. Korzystanie z łącznika usługi Office 365 w usłudze Azure Sentinel wpływa na ceny dla obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Cennik wartowania platformy Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Jeśli korzystasz już z rozwiązania Usługi Office 365 usługi Azure Monitor, należy je najpierw odinstalować za pomocą skryptu w [poniższej sekcji Odinstaluj](#uninstall).
> 3. [Włącz rozwiązanie Azure Sentinel](../../sentinel/quickstart-onboard.md) w obszarze roboczym.
> 4. Przejdź do strony **Łączniki danych** w usłudze Azure Sentinel i włącz łącznik **usługi Office 365.**
>
> ## <a name="frequently-asked-questions"></a>Często zadawane pytania
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-july-30th"></a>Pyt.: Czy możliwe jest wbudowane rozwiązanie usługi Azure Monitor usługi Office 365 od teraz do 30 lipca?
> Nie, skrypty dołączania rozwiązania usługi Azure Monitor Office 365 nie są już dostępne. Rozwiązanie zostanie usunięte 30 lipca.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>Pyt.: Czy tabele i schematy zostaną zmienione?
> Nazwa i schemat tabeli **Aktywności pakietu Office** pozostaną takie same, jak w bieżącym rozwiązaniu. Można kontynuować przy użyciu tych samych zapytań w nowym rozwiązaniu, z wyłączeniem kwerend, które odwołują się do danych usługi Azure AD.
> 
> Nowe dzienniki [raportowania i monitorowania usługi Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) będą wychowywać do tabel [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) i [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) zamiast **officeactivity**. Aby uzyskać więcej informacji, zobacz [jak analizować dzienniki usługi Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), co jest również istotne dla użytkowników usługi Azure Sentinel i Usługi Azure Monitor.
> 
> Poniżej przedstawiono przykłady do konwersji zapytań z **OfficeActivity** na **Logi logowania:**
> 
> **Kwerenda nie powiodło się logowania, przez użytkownika:**
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
> **Wyświetlanie operacji usługi Azure AD:**
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
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>Pyt.: Jak mogę na pokładzie usługi Azure Sentinel?
> Usługa Azure Sentinel to rozwiązanie, które można włączyć w nowym lub istniejącym obszarze roboczym usługi Log Analytics. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Sentinel na pokładzie.](../../sentinel/quickstart-onboard.md)
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>Pyt.: Czy potrzebuję usługi Azure Sentinel, aby połączyć dzienniki usługi Azure AD?
> Można skonfigurować [integrację dzienników usługi Azure AD z usługą Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), która nie jest powiązana z rozwiązaniem Azure Sentinel. Usługa Azure Sentinel udostępnia natywną zawartość łącznika i zawartości wyjętej z pudełka dla dzienników usługi Azure AD. Aby uzyskać więcej informacji, zobacz poniższe pytanie dotyczące zawartości zorientowanej na zabezpieczenia.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>Pyt.: Jakie są różnice podczas łączenia dzienników usługi Azure AD z usługi Azure Sentinel i Azure Monitor?
> Usługa Azure Sentinel i usługa Azure Monitor łączą się z dziennikami usługi Azure AD na podstawie tego samego [rozwiązania do raportowania i monitorowania usługi Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Usługa Azure Sentinel udostępnia łącznik natywny jednym kliknięciem, który łączy te same dane i udostępnia informacje o monitorowaniu.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>Pyt.: Co należy zmienić podczas przenoszenia do nowych tabel raportowania i monitorowania usługi Azure AD?
> Wszystkie kwerendy korzystające z danych usługi Azure AD, w tym zapytania w alertach, pulpitach nawigacyjnych i dowolnej zawartości utworzonej przy użyciu danych usługi Azure AD usługi Office 365, muszą zostać ponownie utworzone przy użyciu nowych tabel.
>
> Usługa Azure Sentinel i usługa Azure AD zapewniają wbudowaną zawartość, której można użyć podczas przenoszenia do rozwiązania do raportowania i monitorowania usługi Azure AD. Aby uzyskać więcej informacji, zobacz następne pytanie dotyczące zawartości zorientowanej na zabezpieczenia wyjętej z pudełka oraz [jak używać skoroszytów usługi Azure Monitor dla raportów usługi Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>Pyt.: Jak mogę korzystać z zawartości usługi Azure Sentinel out-of-the-box zorientowanych zabezpieczeń?
> Usługa Azure Sentinel udostępnia gotowe do użycia pulpity nawigacyjne zorientowane na zabezpieczenia, niestandardowe zapytania alertów, zapytania myśliwskie, funkcje dochodzeń i automatycznej odpowiedzi oparte na dziennikach usługi Office 365 i usłudze Azure AD. Zapoznaj się z usługą Azure Sentinel GitHub i samouczkami, aby dowiedzieć się więcej:
>
> - [Wykrywanie zagrożeń gotowych do użycia](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Tworzenie niestandardowych reguł analitycznych do wykrywania podejrzanych zagrożeń](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorowanie danych](../../sentinel/tutorial-monitor-your-data.md)
> - [Badanie incydentów za pomocą usługi Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Konfigurowanie automatycznych odpowiedzi na zagrożenia w usłudze Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Społeczność usługi Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>Pyt.: Czy usługa Azure Sentinel udostępnia dodatkowe łączniki w ramach rozwiązania?
> Tak, zobacz [Źródło danych usługi Azure Sentinel connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-july-30-do-i-need-to-offboard-beforehand"></a>P: Co się stanie 30 lipca? Czy muszę wcześniej wyłączyć pokład?
> 
> - Nie będzie można odbierać danych z rozwiązania **usługi Office365.** Rozwiązanie nie będzie już dostępne w marketplace
> - Dla klientów usługi Azure Sentinel rozwiązanie obszaru roboczego usługi **Office365** zostanie uwzględnione w rozwiązaniu Usługi Azure Sentinel **SecurityInsights.**
> - Jeśli nie wyłączysz rozwiązania ręcznie, dane zostaną automatycznie rozłączone 30 lipca.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: Czy mój transfer danych do nowego rozwiązania?
> Tak. Po usunięciu rozwiązania **usługi Office 365** z obszaru roboczego jego dane staną się tymczasowo niedostępne, ponieważ schemat zostanie usunięty. Po włączeniu nowego łącznika **usługi Office 365** w sentinel schemat zostanie przywrócony do obszaru roboczego i wszystkie dane już zebrane staną się dostępne. 
 

Rozwiązanie do zarządzania usługą Office 365 umożliwia monitorowanie środowiska usługi Office 365 w usłudze Azure Monitor.

- Monitoruj działania użytkowników na kontach usługi Office 365, aby analizować wzorce użycia, a także identyfikować trendy behawioralne. Na przykład można wyodrębnić określone scenariusze użycia, takie jak pliki udostępnione poza organizacją lub najpopularniejsze witryny programu SharePoint.
- Monitorowanie działań administratora w celu śledzenia zmian konfiguracji lub operacji o wysokich uprawnieniach.
- Wykrywanie i badanie niechcianych zachowań użytkowników, które można dostosować do potrzeb organizacji.
- Zademonstruj inspekcję i zgodność. Na przykład można monitorować operacje dostępu do plików na poufnych plikach, co może pomóc w procesie inspekcji i zgodności.
- Rozwiązywanie problemów operacyjnych przy użyciu [zapytań dziennika](../log-query/log-query-overview.md) na podstawie danych aktywności usługi Office 365 w organizacji.


## <a name="uninstall"></a>Dezinstalacja

Rozwiązanie do zarządzania usługą Office 365 można usunąć za pomocą procesu [w pliku Usuń rozwiązanie do zarządzania](solutions.md#remove-a-monitoring-solution). Nie spowoduje to jednak zatrzymania pobierania danych z usługi Office 365 do usługi Azure Monitor. Postępuj zgodnie z poniższą procedurą, aby anulować subskrypcję usługi Office 365 i przestać zbierać dane.

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

Zostanie wyświetlony monit o podanie poświadczeń. Podaj poświadczenia dla obszaru roboczego usługi Log Analytics.

## <a name="data-collection"></a>Zbieranie danych

Zbieranie danych może potrwać kilka godzin. Po rozpoczęciu zbierania usługa Office 365 wysyła [powiadomienie elementu webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) ze szczegółowymi danymi do usługi Azure Monitor za każdym razem, gdy tworzony jest rekord. Ten rekord jest dostępny w usłudze Azure Monitor w ciągu kilku minut po odebraniu.

## <a name="using-the-solution"></a>Użycie rozwiązania

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po dodaniu rozwiązania usługi Office 365 do obszaru roboczego usługi Log Analytics kafelek **usługi Office 365** zostanie dodany do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
![Kafelek podsumowania usługi Office 365](media/solution-office-365/tile.png)  

Kliknij kafelek **usługi Office 365,** aby otworzyć pulpit nawigacyjny **usługi Office 365.**

![Pulpit nawigacyjny usługi Office 365](media/solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę dziesięciu pierwszych alertów według liczby odpowiadających kryteriom tej kolumny dla określonego zakresu i zakresu czasu. Wyszukiwanie w dzienniku, które zawiera całą listę, można uruchomić, klikając pozycję Zobacz wszystko u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--|:--|
| Operacje | Zawiera informacje o aktywnych użytkownikach ze wszystkich monitorowanych subskrypcji usługi Office 365. Będziesz również mógł zobaczyć liczbę działań, które zdarzają się w czasie.
| Exchange | Pokazuje podział działań programu Exchange Server, takich jak Uprawnienie skrzynki pocztowej dodawania lub Skrzynka pocztowa. |
| Program SharePoint | Pokazuje najważniejsze działania wykonywane przez użytkowników w dokumentach programu SharePoint. Podczas przechodzenia do szczegółów z tego kafelka strona wyszukiwania zawiera szczegóły tych działań, takie jak dokument docelowy i lokalizacja tego działania. Na przykład w przypadku zdarzenia dostęp do pliku będzie można zobaczyć dokument, do którego uzyskuje się dostęp, nazwę skojarzonego konta i adres IP. |
| Usługa Azure Active Directory | Obejmuje najważniejsze działania użytkowników, takie jak resetowanie hasła użytkownika i próby logowania. Podczas przechodzenia do szczegółów będzie można zobaczyć szczegóły tych działań, takich jak stan wyniku. Jest to szczególnie przydatne, jeśli chcesz monitorować podejrzane działania w usłudze Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Rekordy dziennika usługi Azure Monitor

Wszystkie rekordy utworzone w obszarze roboczym analizy dzienników w usłudze Azure Monitor przez rozwiązanie usługi Office 365 mają **typ** **aktywności office.**  Właściwość **pakietu OfficeWorkload** określa, do której usługi Office 365 odwołuje się rekord — Exchange, AzureActiveDirectory, SharePoint lub OneDrive.  **Właściwość RecordType** określa typ operacji.  Właściwości będą się różnić dla każdego typu operacji i są wyświetlane w tabelach poniżej.

### <a name="common-properties"></a>Wspólne właściwości

Następujące właściwości są wspólne dla wszystkich rekordów usługi Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *Aktywności pakietu Office* |
| KlientIP | Adres IP urządzenia, które było używane podczas zarejestrowania działania. Adres IP jest wyświetlany w formacie IPv4 lub IPv6. |
| Pakiet OfficeWorkload | Usługa Office 365, do których odwołuje się rekord.<br><br>Usługa AzureActiveDirectory<br>Exchange<br>Program SharePoint|
| Operacja | Nazwa działania użytkownika lub administratora.  |
| OrganizationId | Identyfikator GUID dla dzierżawy usługi Office 365 w organizacji. Ta wartość będzie zawsze taka sama dla twojej organizacji, niezależnie od usługi Office 365, w której występuje. |
| RecordType | Rodzaj wykonywanej operacji. |
| ResultStatus | Wskazuje, czy akcja (określona we właściwości Operation) zakończyła się powodzeniem. Możliwe wartości to Powodnie, CzęściowoSucceeded lub Failed. W przypadku działania administratora programu Exchange wartość jest true lub false. |
| UserId | Nazwa UPN (nazwa główna użytkownika) użytkownika, który wykonał akcję, która spowodowała zarejestrowanie rekordu; na przykład my_name@my_domain_name. Należy zauważyć, że rekordy dla działań wykonywanych przez konta systemowe (takie jak SHAREPOINT\system lub NTAUTHORITY\SYSTEM) są również uwzględniane. | 
| UserKey | Alternatywny identyfikator użytkownika identyfikowanego we właściwości Identyfikator użytkownika.  Na przykład ta właściwość jest wypełniona unikatowym identyfikatorem paszportu (PUID) dla zdarzeń wykonywanych przez użytkowników w programie SharePoint, OneDrive dla Firm i Exchange. Ta właściwość może również określać taką samą wartość jak UserID właściwości dla zdarzeń występujących w innych usługach i zdarzeń wykonywanych przez konta systemowe|
| UserType | Typ użytkownika, który wykonał operację.<br><br>Administrator<br>Aplikacja<br>DcAdmin ( DcAdmin )<br>Zwykłe<br>Zarezerwowano<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Baza usługi Azure Active Directory

Następujące właściwości są wspólne dla wszystkich rekordów usługi Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Usługa AzureActiveDirectory |
| RecordType     | Usługa AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| Extendedproperties | Rozszerzone właściwości zdarzenia usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logowanie konta usługi Azure Active Directory

Te rekordy są tworzone, gdy użytkownik usługi Active Directory próbuje się zalogować.

| Właściwość | Opis |
|:--- |:--- |
| `OfficeWorkload` | Usługa AzureActiveDirectory |
| `RecordType`     | Usługa AzureActiveDirectoryNalogo count |
| `Application` | Aplikacja, która wyzwala zdarzenie logowania do konta, takie jak Office 15. |
| `Client` | Szczegółowe informacje o urządzeniu klienckim, uzywu urządzenia i przeglądarce urządzeń, które zostały użyte do zdarzenia logowania do konta. |
| `LoginStatus` | Ta właściwość jest z OrgIdLogon.LoginStatus bezpośrednio. Mapowanie różnych interesujących błędów logowania można wykonać, ostrzegając algorytmy. |
| `UserDomain` | Informacje o tożsamości dzierżawy (TII). | 


### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Te rekordy są tworzone podczas zmiany lub uzupełnienia obiektów usługi Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Usługa AzureActiveDirectory |
| RecordType     | Usługa AzureActiveDirectory |
| AADTarget | Użytkownik, na który została wykonana akcja (identyfikowana przez właściwość Operation). |
| Aktor | Użytkownik lub podmiot usługi, który wykonał akcję. |
| AktorContextId | Identyfikator GUID organizacji, do której należy aktor. |
| AktorIpAddress | Adres IP aktora w formacie adresu IPV4 lub IPV6. |
| Międzysystemsid | Identyfikator GUID, który śledzi akcje między składnikami w usłudze Office 365. |
| IntraSystemId |     Identyfikator GUID, który jest generowany przez usługę Azure Active Directory do śledzenia akcji. |
| SupportTicketId (Bilet pomocniczy) | Identyfikator biletu obsługi klienta dla akcji w sytuacjach "act-on-behalf-of". |
| Obiekt DocelowyContextId | Identyfikator GUID organizacji, do której należy użytkownik docelowy. |


### <a name="data-center-security"></a>Zabezpieczenia centrum danych

Te rekordy są tworzone na podstawie danych inspekcji data center security.  

| Właściwość | Opis |
|:--- |:--- |
| Skuteczna organizacja | Nazwa dzierżawy, do którego została skierowana elewacja/polecenie cmdlet. |
| ElevationApprovedCzas | Sygnatura czasowa dla momentu zatwierdzenia elewacji. |
| ElewacjaApprover | Nazwa menedżera firmy Microsoft. |
| WysokośćDuration | Czas trwania, dla którego elewacja była aktywna. |
| ElevationRequestId (Poziom elewacji |     Unikatowy identyfikator żądania podniesienia uprawnień. |
| ElevationRole (ElevationRole) | Rola, dla jaką żądano podniesienia. |
| Czas elewacji | Czas rozpoczęcia elewacji. |
| Start_time | Godzina rozpoczęcia wykonania polecenia cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian w konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Exchange |
| RecordType     | WymianaAdmin |
| Zewnętrzneakcesy |     Określa, czy polecenie cmdlet zostało uruchomione przez użytkownika w organizacji, przez personel centrum danych firmy Microsoft, konto usługi centrum danych, czy przez administratora delegowanego. Wartość False wskazuje, że polecenie cmdlet zostało uruchomione przez inną osobę w organizacji. Wartość True wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konto usługi centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |     Jest to przyjazna dla użytkownika nazwa obiektu, który został zmodyfikowany przez polecenie cmdlet. Jest to rejestrowane tylko wtedy, gdy polecenie cmdlet modyfikuje obiekt. |
| OrganizationName | Nazwa dzierżawy. |
| Serwer źródłowy | Nazwa serwera, z którego wykonano polecenie cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet, który jest identyfikowany we właściwości Operations. |


### <a name="exchange-mailbox"></a>Skrzynka pocztowa programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień w skrzynkach pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Exchange |
| RecordType     | ExchangeItem (Wymiana) |
| ClientInfoString (KlientInfoString) | Informacje o kliencie poczty e-mail, który został użyty do wykonania operacji, takie jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniu przenośnym. |
| Client_IPAddress | Adres IP urządzenia, które było używane podczas rejestrowania operacji. Adres IP jest wyświetlany w formacie IPv4 lub IPv6. |
| Nazwa klienta | Nazwa komputera, na którym znajduje się klient programu Outlook. |
| Nazwa klienta | Klient poczty e-mail, który został użyty do uzyskania dostępu do skrzynki pocztowej. |
| ClientVersion (Wersja klienta) | Wersja klienta poczty e-mail . |
| InternalLogonType (Typ wewnętrzny) | Zarezerwowane do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który uzyskał dostęp do skrzynki pocztowej i wykonał operację, która została zarejestrowana. |
| Nazwa funkcji LognUserDisplayname |     Przyjazna dla użytkownika nazwa użytkownika, który wykonał operację. |
| Identyfikator logowaniauuuusyd | Identyfikator SID użytkownika, który wykonał operację. |
| Skrzynka pocztowaGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do który był uzyskiwał dostęp. |
| Adres pocztowyOwnerMasterAccountSid | Identyfikator SID konta głównego konta właściciela skrzynki pocztowej. |
| Identyfikator skrzynki pocztowej | Identyfikator SID właściciela skrzynki pocztowej. |
| Skrzynka pocztowaOwnerUPN | Adres e-mail osoby, która jest właścicielem skrzynki pocztowej, do której dostęp uzyskał dostęp. |


### <a name="exchange-mailbox-audit"></a>Inspekcja skrzynek pocztowych programu Exchange

Te rekordy są tworzone podczas tworzenia wpisu inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Exchange |
| RecordType     | ExchangeItem (Wymiana) |
| Element | Reprezentuje element, na którym operacja została wykonana | 
| SendAsUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do który był dostępny w celu wysłania wiadomości e-mail jako. |
| SendAsUserSmtp | Adres SMTP użytkownika, który jest personifikowany. |
| SendonBehalfOfUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do który był uzyskiwał dostęp do wysyłania poczty w imieniu. |
| SendOnBehalfOfUserSmtp | Adres SMTP użytkownika, w imieniu którego jest wysyłana wiadomość e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupa inspekcji skrzynek pocztowych programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień w grupach programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Exchange |
| Pakiet OfficeWorkload | Grupa ExchangeItem |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperacje | Wskazuje, czy operacja dotyczyła więcej niż jednej skrzynki pocztowej. |
| DestMailboxId ( DestMailboxId ) | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa docelowy identyfikator GUID skrzynki pocztowej. |
| DestMailboxOwnerMasterAccountSid | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa identyfikator SID dla identyfikatora SID konta głównego docelowego właściciela skrzynki pocztowej. |
| DestMailboxOwnerSyd | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa identyfikator SID docelowej skrzynki pocztowej. |
| DestMailboxOwnerUPN | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa pozycję UPN właściciela docelowej skrzynki pocztowej. |
| DestFolder ( DestFolder ) | Folder docelowy dla operacji, takich jak Move. |
| Folder | Folder, w którym znajduje się grupa elementów. |
| Foldery |     Informacje o folderach źródłowych zaangażowanych w operację; na przykład, jeśli foldery są zaznaczone, a następnie usunięte. |


### <a name="sharepoint-base"></a>Baza programu SharePoint

Te właściwości są wspólne dla wszystkich rekordów programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Program SharePoint |
| Pakiet OfficeWorkload | Program SharePoint |
| EventSource | Identyfikuje zdarzenie, które wystąpiło w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, który był dostępny lub modyfikowany. Zobacz ItemType tabeli, aby uzyskać szczegółowe informacje na temat typów obiektów. |
| MachineDomainInfo | Informacje o operacjach synchronizacji urządzeń. Te informacje są zgłaszane tylko wtedy, gdy są obecne w żądaniu. |
| Ida maszyny |     Informacje o operacjach synchronizacji urządzeń. Te informacje są zgłaszane tylko wtedy, gdy są obecne w żądaniu. |
| Site_ | Identyfikator GUID witryny, w której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| Source_name | Jednostka, która wyzwoliła operację skontrolowana. Możliwe wartości to SharePoint lub ObjectModel. |
| Useragent | Informacje o kliencie lub przeglądarce użytkownika. Informacje te są dostarczane przez klienta lub przeglądarkę. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint

Te rekordy są tworzone po wprowadzeniu zmian konfiguracji w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Program SharePoint |
| Pakiet OfficeWorkload | Program SharePoint |
| Niestandardowevent | Opcjonalny ciąg dla zdarzeń niestandardowych. |
| Event_Data |     Opcjonalny ładunek dla zdarzeń niestandardowych. |
| Właściwości zmodyfikowane | Właściwość jest uwzględniona dla zdarzeń administratora, takich jak dodawanie użytkownika jako członka witryny lub grupy administratorów zbioru witryn. Właściwość zawiera nazwę właściwości, która została zmodyfikowana (na przykład grupa Administrator witryny), nową wartość zmodyfikowanej właściwości (taką jak użytkownik, który został dodany jako administrator witryny) i poprzednią wartość zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint

Te rekordy są tworzone w odpowiedzi na operacje plików w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| Pakiet OfficeWorkload | Program SharePoint |
| Pakiet OfficeWorkload | Działanie programu SharePointFileOperation |
| DestinationFileExtension (Plik docelowy) | Rozszerzenie pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| Destinationfilename | Nazwa pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| DestinationRelativeUrl (Przeznaczenie) | Adres URL folderu docelowego, w którym plik jest kopiowany lub przenoszony. Kombinacja wartości dla parametrów SiteURL, DestinationRelativeURL i DestinationFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki dla pliku, który został skopiowany. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| Rodzaj udostępniania | Typ uprawnień udostępniania, które zostały przypisane do użytkownika, który został udostępniony zasobu. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, do której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| ŹródłoFileRozcięknięcie | Rozszerzenie pliku, do który uzyskał dostęp użytkownik. Ta właściwość jest pusta, jeśli obiekt, do który został uzyskiwał dostęp, jest folderem. |
| Nazwa pliku źródłowego |     Nazwa pliku lub folderu, do który użytkownik uzyskuje dostęp. |
| ŹródłoRelativeUrl | Adres URL folderu zawierającego plik, do który użytkownik uzyskuje dostęp. Kombinacja wartości dla parametrów SiteURL, SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki dla pliku, do którego użytkownik uzyskuje dostęp. |
| UserSharedWith |     Użytkownik, który został udostępniony zasobu. |




## <a name="sample-log-queries"></a>Przykładowe kwerendy dziennika

Poniższa tabela zawiera przykładowe kwerendy dziennika dla rekordów aktualizacji zebranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji usługi Office 365 |OfficeActivity &#124; podsumowują liczbę() według operacji |
|Korzystanie z witryn programu SharePoint|OfficeActivity &#124; gdzie Pakiet OfficeWorkload =~ "sharepoint" &#124; podsumować count() według sortowania SiteUrl \| według liczby asc|
|Operacje dostępu do plików według typu użytkownika | OfficeActivity &#124; podsumowują liczbę() według typu użytkownika |
|Monitorowanie działań zewnętrznych w programie Exchange|OfficeActivity &#124; gdzie Pakiet OfficeWorkload =~ "exchange" i ExternalAccess == true|



## <a name="next-steps"></a>Następne kroki

* Użyj [zapytań dziennika w usłudze Azure Monitor,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe dane aktualizacji.
* [Tworzenie własnych pulpitów nawigacyjnych](../learn/tutorial-logs-dashboards.md) w celu wyświetlania ulubionych zapytań wyszukiwania usługi Office 365.
* [Tworzenie alertów,](../platform/alerts-overview.md) które będą proaktywnie powiadamiane o ważnych działaniach usługi Office 365.  
