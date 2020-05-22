---
title: Skalowanie hostów sesji Azure Automation — Azure
description: Jak automatycznie skalować hosty sesji pulpitu wirtualnego systemu Windows przy użyciu Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745712"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skalowanie hostów sesji przy użyciu Azure Automation

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows.

Możesz zmniejszyć łączny koszt wdrożenia pulpitu wirtualnego systemu Windows, przeskalując maszyny wirtualne. Oznacza to wyłączenie i cofnięcie przydziału maszyn wirtualnych hosta sesji w godzinach użycia poza szczytem, a następnie włączenie ich z powrotem i ich ponowne przypisanie w godzinach szczytu.

Ten artykuł zawiera informacje na temat narzędzia do skalowania utworzonego przy użyciu Azure Automation i Azure Logic Apps, które automatycznie skaluje maszyny wirtualne hosta sesji w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak korzystać z narzędzia do skalowania, przejdź do sekcji [wymagania wstępne](#prerequisites).

## <a name="report-issues"></a>Zgłaszanie problemów

Raporty o problemach narzędzia do skalowania są obecnie obsługiwane w usłudze GitHub, a nie pomoc techniczna firmy Microsoft. Jeśli wystąpią problemy z narzędziem skalowania, możesz zgłosić je komp otwarcie problemu z usługą GitHub o nazwie "4a-WVD-skalować-logicapps" na [stronie usług pulpitu zdalnego](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)w witrynie GitHub.

## <a name="how-the-scaling-tool-works"></a>Jak działa narzędzie skalowania

Narzędzie skalowania udostępnia opcję automatyzacji o niskich kosztach dla klientów, którzy chcą zoptymalizować koszty maszyn wirtualnych hosta sesji.

Możesz użyć narzędzia skalowania, aby:
 
- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w oparciu o szczytową i nieszczytową godzinę biznesową.
- Skalowanie maszyn wirtualnych na podstawie liczby sesji na rdzeń procesora CPU.
- Skalowanie maszyn wirtualnych poza godzinami szczytu, pozostawiając minimalną liczbę maszyn wirtualnych hosta sesji z systemem.

Narzędzie do skalowania używa Azure Automation kombinacji elementów Runbook programu PowerShell, webhook i Azure Logic Apps do działania. Po uruchomieniu narzędzia Azure Logic Apps wywoła element webhook w celu uruchomienia Azure Automation elementu Runbook. Następnie element Runbook utworzy zadanie.

W czasie szczytowego użycia zadanie sprawdza bieżącą liczbę sesji i pojemność maszyny wirtualnej aktualnie działającego hosta sesji dla każdej puli hostów. Te informacje służą do obliczenia, czy uruchomione maszyny wirtualne hosta sesji mogą obsługiwać istniejące sesje na podstawie parametru *SessionThresholdPerCPU* zdefiniowanego dla pliku **createazurelogicapp. ps1** . Jeśli maszyny wirtualne hosta sesji nie mogą obsługiwać istniejących sesji, zadanie uruchamia dodatkowe maszyny wirtualne hosta sesji w puli hostów.

>[!NOTE]
>*SessionThresholdPerCPU* nie ogranicza liczby sesji na maszynie wirtualnej. Ten parametr określa tylko, kiedy należy uruchomić nowe maszyny wirtualne w celu równoważenia obciążenia połączeń. Aby ograniczyć liczbę sesji, należy postępować zgodnie z instrukcjami [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) , aby odpowiednio skonfigurować parametr *MaxSessionLimit* .

W czasie użytkowania poza szczytem zadanie Określa, które maszyny wirtualne hosta sesji powinny zostać zamknięte na podstawie parametru *MinimumNumberOfRDSH* . Zadanie skonfiguruje maszyny wirtualne hosta sesji do trybu opróżniania, aby zapobiec łączeniu się nowych sesji z hostami. Jeśli parametr *LimitSecondsToForceLogOffUser* zostanie ustawiony na wartość różną od zera, zadanie powiadomi wszystkie aktualnie zalogowanych użytkowników, aby zaoszczędzić swoją pracę, zaczekać skonfigurowany czas, a następnie wymusić wylogowanie się użytkowników. Po wyrejestrowaniu wszystkich sesji użytkownika na maszynie wirtualnej hosta sesji zadanie zostanie zamknięte.

Jeśli parametr *LimitSecondsToForceLogOffUser* zostanie ustawiony na wartość zero, zadanie zezwoli na ustawienie konfiguracji sesji w określonych zasadach grupy do obsługi wylogowywania sesji użytkowników. Aby wyświetlić te zasady grupy, przejdź do pozycji **Konfiguracja komputera**  >  **zasady**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi terminalowych**  >  **Terminal Server**  >  **limity czasu sesji**serwera terminali. W przypadku aktywnych sesji na maszynie wirtualnej hosta sesji zadanie pozostawi maszynę wirtualną hosta sesji uruchomioną. Jeśli nie ma aktywnych sesji, zadanie zamknie maszynę wirtualną hosta sesji.

Zadanie jest uruchamiane okresowo na podstawie interwału ustawionego cyklu. Ten interwał można zmienić na podstawie rozmiaru środowiska pulpitu wirtualnego systemu Windows, ale należy pamiętać, że uruchamianie i zamykanie maszyn wirtualnych może zająć trochę czasu, więc pamiętaj, aby uwzględnić opóźnienie. Zalecamy ustawienie interwału cyklu na co 15 minut.

Jednak narzędzie ma również następujące ograniczenia:

- To rozwiązanie ma zastosowanie tylko do maszyn wirtualnych hosta sesji w puli.
- To rozwiązanie służy do zarządzania maszynami wirtualnymi w dowolnym regionie, ale mogą być używane tylko w tej samej subskrypcji, co konto Azure Automation i Azure Logic Apps.

>[!NOTE]
>Narzędzie skalowania kontroluje tryb równoważenia obciążenia puli hostów, która jest skalowana. Ustawia je na funkcję równoważenia obciążenia w pierwszej kolejności zarówno w godzinach szczytu, jak i poza szczytem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania narzędzia do skalowania upewnij się, że masz następujące elementy:

- [Dzierżawa i Pula hostów systemu Windows Virtual Desktop](create-host-pools-arm-template.md)
- Maszyny wirtualne puli hostów sesji skonfigurowane i zarejestrowane w usłudze pulpitu wirtualnego systemu Windows
- Użytkownik z [dostępem współautora](../../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji platformy Azure

Komputer używany do wdrożenia narzędzia musi mieć następujące: 

- Windows PowerShell 5,1 lub nowszy
- Moduł Microsoft AZ PowerShell

Jeśli wszystko jest gotowe, zacznijmy.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Najpierw musisz mieć konto Azure Automation, aby uruchomić element Runbook programu PowerShell. Poniżej przedstawiono sposób konfigurowania konta:

1. Uruchom program Windows PowerShell jako administrator.
2. Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Twoje konto musi mieć uprawnienia współautora w ramach subskrypcji platformy Azure, w której chcesz wdrożyć narzędzie do skalowania.

3. Uruchom następujące polecenie cmdlet, aby pobrać skrypt służący do tworzenia konta Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Uruchom następujące polecenie cmdlet, aby wykonać skrypt i utworzyć konto Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Dane wyjściowe polecenia cmdlet będą zawierać identyfikator URI elementu webhook. Pamiętaj, aby zachować rekord identyfikatora URI, ponieważ będzie on używany jako parametr podczas konfigurowania harmonogramu wykonywania dla usługi Azure Logic Apps.

6. Po skonfigurowaniu konta Azure Automation Zaloguj się do subskrypcji platformy Azure i sprawdź, czy konto Azure Automation i odpowiedni element Runbook pojawiły się w określonej grupie zasobów, jak pokazano na poniższej ilustracji:

![Obraz strony Przegląd platformy Azure przedstawiający nowo utworzone konto usługi Automation i element Runbook.](../media/automation-account.png)

  Aby sprawdzić, czy element webhook jest w tym miejscu, wybierz nazwę elementu Runbook. Następnie przejdź do sekcji zasobów elementu Runbook i wybierz pozycję elementy **webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Tworzenie Azure Automation konta Uruchom jako

Teraz, gdy masz konto Azure Automation, musisz również Azure Automation utworzyć konto Uruchom jako w celu uzyskania dostępu do zasobów platformy Azure.

[Konto Uruchom jako Azure Automation](../../automation/manage-runas-account.md) zapewnia uwierzytelnianie na potrzeby zarządzania zasobami na platformie Azure za pomocą poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako jest tworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory i przypisuje rolę współautor do użytkownika jednostki usługi na poziomie subskrypcji, konto Uruchom jako platformy Azure jest doskonałym sposobem na bezpieczne uwierzytelnianie z użyciem certyfikatów i nazwy głównej usługi bez konieczności przechowywania nazwy użytkownika i hasła w obiekcie Credential. Aby dowiedzieć się więcej o uwierzytelnianiu Uruchom jako, zobacz [ograniczanie uprawnień konta Uruchom jako](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Każdy użytkownik, który jest członkiem roli Administratorzy subskrypcji i administrator subskrypcji, może utworzyć konto Uruchom jako, postępując zgodnie z instrukcjami w następnej sekcji.

Aby utworzyć konto Uruchom jako na koncie platformy Azure:

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wprowadź i wybierz pozycję **konta usługi Automation**.

2. Na stronie **konta usługi Automation** wybierz nazwę konta usługi Automation.

3. W okienku po lewej stronie okna wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

4. Wybierz pozycję **konto Uruchom jako platformy Azure**. Po wyświetleniu okienka **Dodawanie konta Uruchom jako platformy Azure** Przejrzyj informacje przeglądowe, a następnie wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia konta.

5. Zaczekaj kilka minut na utworzenie konta Uruchom jako przez platformę Azure. Postęp tworzenia można śledzić w menu w obszarze powiadomienia.

6. Po zakończeniu procesu zostanie utworzony element zawartości o nazwie AzureRunAsConnection na określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, Identyfikator subskrypcji i odcisk palca certyfikatu. Zapamiętaj identyfikator aplikacji, ponieważ będzie on używany później.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Tworzenie przypisania roli na pulpicie wirtualnym systemu Windows

Następnie należy utworzyć przypisanie roli, aby AzureRunAsConnection mogły współdziałać z pulpitem wirtualnym systemu Windows. Upewnij się, że używasz programu PowerShell, aby zalogować się przy użyciu konta, które ma uprawnienia do tworzenia przypisań ról.

Najpierw pobierz i zaimportuj [moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawców.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Po znalezieniu dzierżawy z pulami hosta, które chcesz skalować, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Azure Automation](#create-an-azure-automation-account) i użyj nazwy dzierżawy uzyskanej z poprzedniego polecenia cmdlet w poniższym poleceniu cmdlet, aby utworzyć przypisanie roli:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Tworzenie aplikacji logiki platformy Azure i harmonogramu wykonywania

Na koniec należy utworzyć aplikację logiki platformy Azure i skonfigurować harmonogram wykonywania dla nowego narzędzia do skalowania.

1.  Otwórz program Windows PowerShell jako administrator

2.  Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

     ```powershell
     Login-AzAccount
     ```

3. Uruchom następujące polecenie cmdlet, aby pobrać plik skryptu createazurelogicapp. ps1 na komputerze lokalnym.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows przy użyciu konta, które ma uprawnienia właściciela usług pulpitu zdalnego lub współautor pulpitu zdalnego.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Uruchom następujący skrypt programu PowerShell, aby utworzyć aplikację logiki platformy Azure i harmonogram wykonywania.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Po uruchomieniu skryptu aplikacja logiki powinna pojawić się w grupie zasobów, jak pokazano na poniższej ilustracji.

     ![Obraz strony przegląd dla przykładowej aplikacji logiki platformy Azure.](../media/logic-app.png)

Aby wprowadzić zmiany do harmonogramu wykonywania, takie jak zmiana interwału cyklu lub strefy czasowej, przejdź do harmonogramu automatycznego skalowania i wybierz pozycję **Edytuj** , aby przejść do projektanta Logic Apps.

![Obraz projektanta Logic Apps. Menu cykl i element webhook umożliwiające użytkownikowi edytowanie czasów cyklu i pliku elementu webhook.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Zarządzaj narzędziem do skalowania

Po utworzeniu narzędzia do skalowania możesz uzyskać dostęp do jego danych wyjściowych. W tej sekcji opisano kilka funkcji, które mogą być przydatne.

### <a name="view-job-status"></a>Wyświetlanie stanu zadania

Można wyświetlić podsumowanie stanu wszystkich zadań elementu Runbook lub wyświetlić bardziej szczegółowy stan określonego zadania elementu Runbook w Azure Portal.

Po prawej stronie wybranego konta usługi Automation w obszarze "statystyki zadania" można wyświetlić listę podsumowań wszystkich zadań elementu Runbook. Otwarcie strony **zadania** po lewej stronie okna powoduje wyświetlenie bieżących stanów zadań, godzin rozpoczęcia i czasu zakończenia.

![Zrzut ekranu przedstawiający stronę stanu zadania.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Wyświetlanie dzienników i narzędzi do skalowania danych wyjściowych

Możesz wyświetlić dzienniki operacji skalowania w poziomie i skalowania przez otwarcie elementu Runbook i wybranie nazwy zadania.

Przejdź do elementu Runbook (nazwa domyślna to WVDAutoScaleRunbook) w grupie zasobów hostującym konto Azure Automation i wybierz pozycję **Przegląd**. Na stronie Przegląd wybierz zadanie w obszarze ostatnie zadania, aby wyświetlić dane wyjściowe narzędzia do skalowania, jak pokazano na poniższej ilustracji.

![Obraz okna dane wyjściowe narzędzia do skalowania.](../media/tool-output.png)

