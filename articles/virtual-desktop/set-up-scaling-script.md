---
title: Skalowanie hostów sesji Azure Automation — Azure
description: Jak automatycznie skalować hosty sesji pulpitu wirtualnego systemu Windows przy użyciu Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12a15ab1a4c7369c448e9f65862121b03ca05bba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078558"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skalowanie hostów sesji przy użyciu Azure Automation

Możesz zmniejszyć łączny koszt wdrożenia pulpitu wirtualnego systemu Windows, przeskalując maszyny wirtualne. Oznacza to wyłączenie i cofnięcie przydziału maszyn wirtualnych hosta sesji w godzinach użycia poza szczytem, a następnie włączenie ich z powrotem i ich ponowne przypisanie w godzinach szczytu.

Ten artykuł zawiera informacje na temat narzędzia do skalowania utworzonego za pomocą konta Azure Automation i aplikacji logiki platformy Azure, która automatycznie skaluje maszyny wirtualne hosta sesji w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak korzystać z narzędzia do skalowania, przejdź do sekcji [wymagania wstępne](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Jak działa narzędzie skalowania

Narzędzie skalowania udostępnia opcję automatyzacji o niskich kosztach dla klientów, którzy chcą zoptymalizować koszty maszyn wirtualnych hosta sesji.

Możesz użyć narzędzia skalowania, aby:

- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w oparciu o szczytową i Off-Peak godziny pracy.
- Skalowanie maszyn wirtualnych na podstawie liczby sesji na rdzeń procesora CPU.
- Skalowanie maszyn wirtualnych w Off-Peak godzinach, pozostawiając minimalną liczbę maszyn wirtualnych hosta sesji z systemem.

Narzędzie do skalowania używa kombinacji konta Azure Automation, elementu Runbook programu PowerShell, elementów webhook i aplikacji logiki platformy Azure do działania. Po uruchomieniu narzędzia Aplikacja logiki Azure wywołuje element webhook w celu uruchomienia Azure Automation elementu Runbook. Następnie element Runbook utworzy zadanie.

W czasie szczytowego użycia zadanie sprawdza bieżącą liczbę sesji i pojemność maszyny wirtualnej aktualnie działającego hosta sesji dla każdej puli hostów. Te informacje służą do obliczenia, czy uruchomione maszyny wirtualne hosta sesji mogą obsługiwać istniejące sesje na podstawie parametru *SessionThresholdPerCPU* zdefiniowanego dla pliku **CreateOrUpdateAzLogicApp.ps1** . Jeśli maszyny wirtualne hosta sesji nie mogą obsługiwać istniejących sesji, zadanie uruchamia dodatkowe maszyny wirtualne hosta sesji w puli hostów.

>[!NOTE]
>*SessionThresholdPerCPU* nie ogranicza liczby sesji na maszynie wirtualnej. Ten parametr określa tylko, kiedy należy uruchomić nowe maszyny wirtualne w celu równoważenia obciążenia połączeń. Aby ograniczyć liczbę sesji, należy postępować zgodnie z instrukcjami [Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing) , aby odpowiednio skonfigurować parametr *MaxSessionLimit* .

W czasie użytkowania poza szczytem zadanie Określa, ile maszyn wirtualnych hosta sesji powinno być zamkniętych na podstawie parametru *MinimumNumberOfRDSH* . Jeśli ustawisz parametr *LimitSecondsToForceLogOffUser* na wartość różną od zera, zadanie skonfiguruje maszyny wirtualne hosta sesji do trybu opróżniania, aby uniemożliwić nowym sesjach łączenie się z hostami. To zadanie spowoduje powiadomienie wszystkich aktualnie zalogowanych użytkowników w celu zapisania ich pracy, zaczekaj na skonfigurowanie czasu, a następnie wymusić wylogowanie się użytkowników. Po wyrejestrowaniu wszystkich sesji użytkownika na maszynie wirtualnej hosta sesji zadanie zostanie zamknięte. Po zamknięciu maszyny wirtualnej zadanie spowoduje zresetowanie jego trybu opróżniania hosta sesji.

>[!NOTE]
>Jeśli ręcznie ustawisz maszynę wirtualną hosta sesji do trybu opróżniania, zadanie nie będzie zarządzać maszyną wirtualną hosta sesji. Jeśli maszyna wirtualna hosta sesji jest uruchomiona i ma ustawiony tryb opróżniania, będzie traktowana jako niedostępna, co spowoduje uruchomienie dodatkowych maszyn wirtualnych do obsługi obciążenia. Zalecamy oznaczanie maszyn wirtualnych platformy Azure przed ręcznym ustawieniem ich na tryb opróżniania. Można nazwać tag przy użyciu parametru *MaintenanceTagName* podczas późniejszego tworzenia usługi Azure Logic App Scheduler. Tagi ułatwiają odróżnienie tych maszyn wirtualnych od tych, które są zarządzane przez narzędzie do skalowania. Ustawienie tagu obsługi zapobiega także wprowadzeniu zmian w maszynie wirtualnej do momentu usunięcia znacznika.

Jeśli parametr *LimitSecondsToForceLogOffUser* zostanie ustawiony na wartość zero, zadanie zezwala na ustawienie konfiguracji sesji w określonych zasadach grupy do obsługi wylogowywania sesji użytkowników. Aby wyświetlić te zasady grupy, przejdź do pozycji **Konfiguracja komputera**  >  **zasady**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **limity czasu sesji**hosta sesji. W przypadku aktywnych sesji na maszynie wirtualnej hosta sesji zadanie pozostawi maszynę wirtualną hosta sesji uruchomioną. Jeśli nie ma żadnych aktywnych sesji, zadanie zamknie maszynę wirtualną hosta sesji.

W dowolnym momencie zadanie przyjmuje również *MaxSessionLimit* puli hostów, aby określić, czy bieżąca liczba sesji przekracza 90% maksymalnej wydajności. Jeśli tak jest, zadanie rozpocznie dodatkowe maszyny wirtualne hosta sesji.

Zadanie jest uruchamiane okresowo na podstawie interwału ustawionego cyklu. Ten interwał można zmienić w zależności od rozmiaru środowiska pulpitu wirtualnego systemu Windows, ale pamiętaj, że uruchamianie i zamykanie maszyn wirtualnych może zająć trochę czasu, więc pamiętaj, aby uwzględnić opóźnienie. Zalecamy ustawienie interwału cyklu na co 15 minut.

Jednak narzędzie ma również następujące ograniczenia:

- To rozwiązanie dotyczy tylko maszyn wirtualnych hosta sesji wielosesyjnych w puli.
- To rozwiązanie służy do zarządzania maszynami wirtualnymi w dowolnym regionie, ale mogą być używane tylko w tej samej subskrypcji, co konto Azure Automation i aplikacja logiki platformy Azure.
- Maksymalne środowisko uruchomieniowe zadania w elemencie Runbook to 3 godziny. Jeśli uruchamianie lub zatrzymywanie maszyn wirtualnych w puli hostów trwa dłużej niż to, zadanie zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [zasoby udostępnione](../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>Narzędzie skalowania kontroluje tryb równoważenia obciążenia puli hostów, który jest obecnie skalowany. Narzędzie używa trybu o szerokim zakresie równoważenia obciążenia dla godzin szczytu i godziny poza szczytem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania narzędzia do skalowania upewnij się, że masz następujące elementy:

- [Pula hostów pulpitów wirtualnych systemu Windows](create-host-pools-azure-marketplace.md)
- Maszyny wirtualne puli hostów sesji skonfigurowane i zarejestrowane w usłudze pulpitu wirtualnego systemu Windows
- Użytkownik z [dostępem współautora](../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji platformy Azure

Komputer używany do wdrożenia narzędzia musi mieć następujące:

- Windows PowerShell 5,1 lub nowszy
- Moduł Microsoft AZ PowerShell

Jeśli wszystko jest gotowe, zacznijmy.

## <a name="create-or-update-an-azure-automation-account"></a>Utwórz lub zaktualizuj konto Azure Automation

>[!NOTE]
>Jeśli masz już konto Azure Automation z elementem Runbook, na którym działa Starsza wersja skryptu skalowania, należy postępować zgodnie z poniższymi instrukcjami, aby upewnić się, że jest ona aktualizowana.

Najpierw musisz mieć konto Azure Automation, aby uruchomić element Runbook programu PowerShell. Proces opisany w tym temacie jest prawidłowy, nawet jeśli masz istniejące konto Azure Automation, którego chcesz użyć do skonfigurowania elementu Runbook programu PowerShell. Aby skonfigurować tę funkcję:

1. Otwórz program Windows PowerShell.

2. Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Twoje konto musi mieć uprawnienia współautora w ramach subskrypcji platformy Azure, w której ma zostać wdrożone narzędzie do skalowania.

3. Uruchom następujące polecenie cmdlet, aby pobrać skrypt służący do tworzenia konta Azure Automation:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Uruchom następujące polecenie cmdlet, aby wykonać skrypt i utworzyć konto Azure Automation. Możesz podać wartości parametrów lub dodać do nich komentarz, aby używać ich wartości domyślnych.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Dane wyjściowe polecenia cmdlet będą zawierać identyfikator URI elementu webhook. Pamiętaj, aby zachować rekord identyfikatora URI, ponieważ będzie on używany jako parametr podczas konfigurowania harmonogramu wykonywania dla aplikacji logiki platformy Azure.

6. Jeśli określono parametr **WorkspaceName** dla log Analytics, w danych wyjściowych polecenia cmdlet zostanie również uwzględniony identyfikator obszaru roboczego log Analytics i jego klucz podstawowy. Pamiętaj, aby zapamiętać identyfikator URI, ponieważ będzie on potrzebny później jako parametr podczas konfigurowania harmonogramu wykonywania dla aplikacji logiki platformy Azure.

7. Po skonfigurowaniu konta Azure Automation Zaloguj się do subskrypcji platformy Azure i sprawdź, czy konto Azure Automation i odpowiedni element Runbook pojawiły się w określonej grupie zasobów, jak pokazano na poniższej ilustracji:

    >[!div class="mx-imgBorder"]
    >![Obraz strony Przegląd platformy Azure przedstawiający nowo utworzone konto Azure Automation i element Runbook.](media/automation-account.png)

    Aby sprawdzić, czy element webhook jest w tym miejscu, wybierz nazwę elementu Runbook. Następnie przejdź do sekcji zasobów elementu Runbook i wybierz pozycję elementy **webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Tworzenie Azure Automation konta Uruchom jako

Teraz, gdy masz konto Azure Automation, musisz również utworzyć Azure Automation konto Uruchom jako, jeśli jeszcze go nie masz. To konto pozwoli narzędziu na dostęp do zasobów platformy Azure.

[Konto Uruchom jako Azure Automation](../automation/manage-runas-account.md) zapewnia uwierzytelnianie na potrzeby zarządzania zasobami na platformie Azure za pomocą poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako jest tworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory i przypisuje rolę współautor do użytkownika jednostki usługi na poziomie subskrypcji. Konto Uruchom jako platformy Azure to doskonałe rozwiązanie bezpiecznego uwierzytelniania przy użyciu certyfikatów i nazwy głównej usługi bez konieczności przechowywania nazwy użytkownika i hasła w obiekcie Credential. Aby dowiedzieć się więcej o uwierzytelnianiu konta Uruchom jako, zobacz [ograniczanie uprawnień konta Uruchom jako](../automation/manage-runas-account.md#limit-run-as-account-permissions).

Każdy użytkownik, który jest członkiem roli Administratorzy subskrypcji i administrator subskrypcji, może utworzyć konto Uruchom jako.

Aby utworzyć konto Uruchom jako na koncie Azure Automation:

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wprowadź i wybierz pozycję **konta usługi Automation**.

2. Na stronie **konta usługi Automation** wybierz nazwę konta Azure Automation.

3. W okienku po lewej stronie okna wybierz pozycję **konta Uruchom jako** w sekcji **Ustawienia konta** .

4. Wybierz pozycję **konto Uruchom jako platformy Azure**. Po wyświetleniu okienka **Dodawanie konta Uruchom jako platformy Azure** Przejrzyj informacje przeglądowe, a następnie wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia konta.

5. Zaczekaj kilka minut na utworzenie konta Uruchom jako przez platformę Azure. Postęp tworzenia można śledzić w menu w obszarze powiadomienia.

6. Po zakończeniu procesu zostanie utworzony element zawartości o nazwie **AzureRunAsConnection** na określonym koncie Azure Automation. Wybierz pozycję **konto Uruchom jako platformy Azure**. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, Identyfikator subskrypcji i odcisk palca certyfikatu. Te same informacje można również znaleźć na stronie **połączenia** . Aby przejść do tej strony, w okienku po lewej stronie okna wybierz pozycję **połączenia** w sekcji **udostępnione zasoby** , a następnie kliknij pozycję zasób połączenia o nazwie **AzureRunAsConnection**.

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Tworzenie aplikacji logiki platformy Azure i harmonogramu wykonywania

Na koniec należy utworzyć aplikację logiki platformy Azure i skonfigurować harmonogram wykonywania dla nowego narzędzia do skalowania. Najpierw pobierz i zaimportuj [moduł programu PowerShell wirtualizacji pulpitu](powershell-module.md) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

1. Otwórz program Windows PowerShell.

2. Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

    ```powershell
    Login-AzAccount
    ```

3. Uruchom następujące polecenie cmdlet, aby pobrać skrypt służący do tworzenia aplikacji logiki platformy Azure.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Uruchom następujący skrypt programu PowerShell, aby utworzyć aplikację logiki platformy Azure i harmonogram wykonywania dla puli hostów

    >[!NOTE]
    >Należy uruchomić ten skrypt dla każdej puli hostów, która ma być automatycznie skalowana, ale wymagane jest tylko jedno konto Azure Automation.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Po uruchomieniu skryptu aplikacja logiki Azure powinna pojawić się w grupie zasobów, jak pokazano na poniższej ilustracji.

    >[!div class="mx-imgBorder"]
    >![Obraz strony przegląd dla przykładowej aplikacji logiki platformy Azure.](media/logic-app.png)

    Aby wprowadzić zmiany do harmonogramu wykonywania, takie jak zmiana interwału cyklu lub strefy czasowej, przejdź do harmonogramu automatycznego skalowania aplikacji logiki platformy Azure i wybierz pozycję **Edytuj** , aby przejść do projektanta aplikacji logiki Azure.

    >[!div class="mx-imgBorder"]
    >![Obraz projektanta aplikacji logiki platformy Azure. Menu cykl i element webhook umożliwiające użytkownikowi edytowanie czasów cyklu i pliku elementu webhook.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Zarządzaj narzędziem do skalowania

Po utworzeniu narzędzia do skalowania możesz uzyskać dostęp do jego danych wyjściowych. W tej sekcji opisano kilka funkcji, które mogą być przydatne.

### <a name="view-job-status"></a>Wyświetlanie stanu zadania

Można wyświetlić podsumowanie stanu wszystkich zadań elementu Runbook lub wyświetlić bardziej szczegółowy stan określonego zadania elementu Runbook w Azure Portal.

Po prawej stronie wybranego konta Azure Automation w obszarze "statystyki zadania" można wyświetlić listę podsumowań wszystkich zadań elementu Runbook. Otwarcie strony **zadania** po lewej stronie okna powoduje wyświetlenie bieżących stanów zadań, godzin rozpoczęcia i czasu zakończenia.

>[!div class="mx-imgBorder"]
>![Zrzut ekranu przedstawiający stronę stanu zadania.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Wyświetlanie dzienników i narzędzi do skalowania danych wyjściowych

Możesz wyświetlić dzienniki operacji skalowania w poziomie i skalowania w poziomie, otwierając element Runbook i wybierając zadanie.

Przejdź do elementu Runbook w grupie zasobów hostującym konto Azure Automation i wybierz pozycję **Przegląd**. Na stronie Przegląd wybierz zadanie w obszarze **ostatnie zadania** , aby wyświetlić dane wyjściowe narzędzia do skalowania, jak pokazano na poniższej ilustracji.

>[!div class="mx-imgBorder"]
>![Obraz okna dane wyjściowe narzędzia do skalowania.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Sprawdź numer wersji skryptu elementu Runbook

Możesz sprawdzić, która wersja skryptu elementu Runbook jest używana, otwierając plik Runbook na koncie Azure Automation i wybierając pozycję **Widok**. Skrypt dla elementu Runbook zostanie wyświetlony po prawej stronie ekranu. W skrypcie zobaczysz numer wersji w formacie `v#.#.#` poniżej `SYNOPSIS` sekcji. Numer najnowszej wersji można znaleźć [tutaj](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1). Jeśli nie widzisz numeru wersji w skrypcie elementu Runbook, oznacza to, że uruchomiono wcześniejszą wersję skryptu i należy od razu ją zaktualizować. Jeśli musisz zaktualizować skrypt elementu Runbook, postępuj zgodnie z instrukcjami w temacie [Tworzenie lub aktualizowanie konta Azure Automation](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Raportowanie problemów

Gdy zgłaszasz problem, musisz podać poniższe informacje, aby pomóc nam w rozwiązywaniu problemów:

- Pełny dziennik z karty **wszystkie dzienniki** w zadaniu, które spowodowało problem. Aby dowiedzieć się, jak uzyskać dziennik, postępuj zgodnie z instrukcjami wyświetlanymi w temacie [Wyświetlanie dzienników i narzędzia do skalowania](#view-logs-and-scaling-tool-output). Jeśli w dzienniku znajdują się poufne lub prywatne informacje, możesz je usunąć przed przesłaniem do nas problemu.

- Wersja skryptu elementu Runbook, którego używasz. Aby dowiedzieć się, jak uzyskać numer wersji, zobacz [Sprawdzanie numeru wersji skryptu elementu Runbook](#check-the-runbook-script-version-number)

- Numer wersji każdego z następujących modułów programu PowerShell zainstalowanych na koncie Azure Automation. Aby znaleźć te moduły, Otwórz Azure Automation konto, wybierz pozycję **moduły** w sekcji **zasoby udostępnione** w okienku po lewej stronie okna, a następnie wyszukaj nazwę modułu.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- Data wygaśnięcia [konta Uruchom jako](#create-an-azure-automation-run-as-account). Aby to sprawdzić, Otwórz konto Azure Automation, a następnie wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta** w okienku po lewej stronie okna. Data wygaśnięcia powinna być **uwzględniona w ramach konta Uruchom jako platformy Azure**.

### <a name="log-analytics"></a>Usługa Log Analytics

Jeśli zamierzasz używać Log Analytics, możesz wyświetlić wszystkie dane dziennika w dzienniku niestandardowym o nazwie **WVDTenantScale_CL** w obszarze **dzienniki niestandardowe** w widoku **dzienniki** w obszarze roboczym log Analytics. Wystawiłeś przykładowe zapytania, które mogą być przydatne.

- Aby wyświetlić wszystkie dzienniki dla puli hostów, wprowadź następujące zapytanie

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Aby wyświetlić łączną liczbę aktualnie uruchomionych maszyn wirtualnych hosta sesji i aktywnych sesji użytkownika w puli hostów, wprowadź następujące zapytanie

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Aby wyświetlić stan wszystkich maszyn wirtualnych hosta sesji w puli hostów, wprowadź następujące zapytanie

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Aby wyświetlić błędy i ostrzeżenia, wprowadź następujące zapytanie

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Zgłaszanie problemów

Raporty o problemach narzędzia do skalowania są obecnie obsługiwane przez pomoc techniczna firmy Microsoft. Podczas tworzenia raportu o problemie upewnij się, że postępuj zgodnie z instrukcjami w temacie [problemy z raportowaniem](#reporting-issues). Jeśli masz opinię na temat tego narzędzia lub chcesz zażądać nowych funkcji, Otwórz problem z usługą GitHub o nazwie "4-WVD-skalowanie-narzędzie" na [stronie usług pulpitu zdalnego](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)w serwisie GitHub.
