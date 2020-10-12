---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows w Azure Automation
description: W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na maszynach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74657743d14b9365f66ed3373592b708a07e11dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88660516"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczeniu maszyn. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Windows, sposobu usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych elementu Runbook.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy.

### <a name="a-log-analytics-workspace"></a>Obszar roboczy Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook zależy od obszaru roboczego Log Analytics Azure Monitor, aby zainstalować i skonfigurować rolę. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Jeśli nie masz obszaru roboczego Log Analytics Azure Monitor, zapoznaj się ze [wskazówkami dotyczącymi projektu dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) przed utworzeniem obszaru roboczego.

Jeśli masz obszar roboczy, ale nie jest on połączony z kontem usługi Automation, włączenie funkcji automatyzacji powoduje dodanie funkcji dla Azure Automation, w tym obsługę hybrydowego procesu roboczego elementu Runbook. Po włączeniu jednej z Azure Automation funkcji w obszarze roboczym Log Analytics, w odróżnieniu od [Update Management](update-management/update-mgmt-overview.md) lub [Change Tracking i spisu](change-tracking.md), składniki procesu roboczego są automatycznie wypychane do komputera agenta.

> [!NOTE]
> Podczas włączania funkcji Update Management lub Change Tracking i spisu Azure Automation obsługuje tylko niektóre regiony do łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md). Przed włączeniem którejkolwiek z tych funkcji Przejrzyj informacje o [cenach platformy Azure](https://azure.microsoft.com/pricing/details/automation/) dotyczące Azure Automation.

   Aby dodać funkcję Update Management do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Aby dodać funkcję Change Tracking i spisu do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook wymaga [agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Windows.

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane dla hybrydowego procesu roboczego elementu Runbook systemu Windows:

* Windows Server 2019
* Windows Server 2016, wersja 1709 i 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 z dodatkiem SP2 (x64), 2008 R2
* Windows 10 Enterprise (w tym wiele sesji) i Pro
* Windows 8 Enterprise i Pro
* Windows 7 z dodatkiem SP1

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows to:

* Windows PowerShell 5,1 lub nowszy ([pobieranie plików WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać więcej wymagań sieci dla hybrydowego procesu roboczego elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowych procesów roboczych elementu Runbook

Możesz dodać maszynę procesu roboczego do grupy hybrydowych procesów roboczych elementu Runbook na Twoim koncie usługi Automation. Należy pamiętać, że musisz obsługiwać elementy Runbook usługi Automation, o ile używasz tego samego konta zarówno dla funkcji Azure Automation, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

>[!NOTE]
>Włączenie Azure Automation [Update Management](update-management/update-mgmt-overview.md) automatycznie skonfiguruje wszystkie maszyny z systemem Windows, które są połączone z obszarem roboczym log Analytics jako hybrydowy proces roboczy elementu Runbook w celu obsługi zarządzania aktualizacjami systemu operacyjnego. Jednak ten proces roboczy nie jest zarejestrowany w żadnych grup hybrydowych procesów roboczych elementu Runbook zdefiniowanych już na koncie usługi Automation.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Włączanie maszyn na potrzeby zarządzania przy użyciu konfiguracji stanu Azure Automation

Informacje o włączaniu maszyn do zarządzania przy użyciu konfiguracji stanu Azure Automation można znaleźć w temacie [enable Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać maszyny jako węzły DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Opcje instalacji hybrydowego procesu roboczego elementu Runbook systemu Windows

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, można użyć jednej z następujących metod.

* W przypadku maszyn wirtualnych platformy Azure Zainstaluj agenta Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym Log Analytics przy użyciu szablonu Azure Resource Manager lub programu PowerShell. Po zainstalowaniu agenta można dodać maszynę wirtualną do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.

* W przypadku maszyn wirtualnych spoza platformy Azure Zainstaluj agenta Log Analytics dla systemu Windows, korzystając z opcji wdrażania opisanych w artykule [łączenie komputerów z systemem Windows do Azure monitor](../azure-monitor/platform/agent-windows.md) . Można powtórzyć ten proces dla wielu maszyn, aby dodać wielu procesów roboczych do środowiska. Po zainstalowaniu agenta maszyny wirtualne można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.

* Za pomocą dostarczonego skryptu programu PowerShell można całkowicie [zautomatyzować](#automated-deployment) proces konfigurowania co najmniej jednej maszyny z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.

## <a name="automated-deployment"></a>Wdrożenie automatyczne

Na maszynie docelowej wykonaj następujące kroki, aby zautomatyzować instalację i konfigurację roli hybrydowego procesu roboczego systemu Windows przy użyciu skryptu programu PowerShell **New-OnPremiseHybridWorker.ps1**. Skrypt wykonuje następujące kroki:

* Instaluje niezbędne moduły
* Zaloguj się przy użyciu konta platformy Azure
* Weryfikuje istnienie określonej grupy zasobów i konta usługi Automation
* Tworzy odwołania do atrybutów konta usługi Automation
* Tworzy obszar roboczy Log Analytics Azure Monitor, jeśli nie zostanie określony
* Włączanie rozwiązania Azure Automation w obszarze roboczym
* Pobierz i Zainstaluj agenta Log Analytics dla systemu Windows
* Zarejestruj maszynę jako hybrydowy proces roboczy elementu Runbook

### <a name="step-1---download-the-powershell-script"></a>Krok 1. Pobieranie skryptu programu PowerShell

Pobierz skrypt **New-OnPremiseHybridWorker.ps1** z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Po pobraniu skryptu Skopiuj lub uruchom go na maszynie docelowej. Skrypt **New-OnPremiseHybridWorker.ps1** używa parametrów opisanych poniżej podczas wykonywania.

| Parametr | Stan | Opis |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obowiązkowy | Nazwa grupy zasobów skojarzonej z kontem usługi Automation. |
| `AutomationAccountName` | Obowiązkowy | Nazwa konta usługi Automation.
| `Credential` | Opcjonalne | Poświadczenia, które mają być używane podczas logowania w środowisku platformy Azure. |
| `HybridGroupName` | Obowiązkowy | Nazwa grupy hybrydowych procesów roboczych elementu Runbook, która została określona jako element docelowy dla elementów Runbook, które obsługują ten scenariusz. |
| `OMSResourceGroupName` | Opcjonalne | Nazwa grupy zasobów dla obszaru roboczego Log Analytics. Jeśli ta grupa zasobów nie jest określona, `AAResourceGroupName` zostanie użyta wartość. |
| `SubscriptionID` | Obowiązkowy | Identyfikator subskrypcji platformy Azure skojarzonej z kontem usługi Automation. |
| `TenantID` | Opcjonalne | Identyfikator organizacji dzierżawy skojarzonej z kontem usługi Automation. |
| `WorkspaceName` | Opcjonalne | Nazwa obszaru roboczego Log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, skrypt tworzy i konfiguruje go. |

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2. Otwieranie powłoki wiersza polecenia programu Windows PowerShell

W **menu Start** kliknij przycisk **Start**, wpisz **PowerShell**, kliknij prawym przyciskiem myszy program **Windows PowerShell**, a następnie kliknij polecenie **Uruchom jako administrator**.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 — Uruchamianie skryptu programu PowerShell

W powłoce wiersza polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` i `WorkspaceName` . Następnie uruchom skrypt.

Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Musisz zalogować się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

```powershell-interactive
$NewOnPremiseHybridWorkerParameters = @{
  AutomationAccountName = <nameOfAutomationAccount>
  AAResourceGroupName   = <nameOfResourceGroup>
  OMSResourceGroupName  = <nameOfResourceGroup>
  HybridGroupName       = <nameOfHRWGroup>
  SubscriptionID        = <subscriptionId>
  WorkspaceName         = <nameOfLogAnalyticsWorkspace>
}
.\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
```

### <a name="step-4---install-nuget"></a>Krok 4 — Instalowanie narzędzia NuGet

Zostanie wyświetlony monit o zgodę na zainstalowanie narzędzia NuGet i uwierzytelnienie przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji pakietu NuGet, możesz pobrać ją z [dostępnych wersji dystrybucji NuGet](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Krok 5 — Weryfikowanie wdrożenia

Po zakończeniu działania skryptu na stronie grupy hybrydowych procesów roboczych na koncie usługi Automation zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie grupy hybrydowych procesów roboczych i wybrać kafelek **hybrydowe procesy** robocze. Na stronie hybrydowe procesy robocze można zobaczyć wszystkich członków tej grupy.

## <a name="manual-deployment"></a>Wdrożenie ręczne

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, wykonaj następujące czynności.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Krok 1. sprawdzenie, czy Agent zgłasza do obszaru roboczego

Agent Log Analytics dla systemu Windows łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na maszynie i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla maszyny, co oznacza, że jest ona podłączona i zgłaszana do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego. Aby ukończyć instalację i konfigurację agenta, wykonaj następujące czynności.

1. Włącz tę funkcję, aby dodać maszynę agenta. Aby uzyskać Update Management i maszyn wirtualnych platformy Azure, zobacz [włączanie Update Management na podstawie konta usługi Automation](update-management/update-mgmt-enable-automation-account.md), [Włączanie Update Management przez przeglądanie Azure Portal](update-management/update-mgmt-enable-portal.md), [Włączanie Update Management z elementu Runbook](update-management/update-mgmt-enable-runbook.md)lub [Włączanie Update Management z maszyny wirtualnej platformy Azure](update-management/update-mgmt-enable-vm.md). Aby uzyskać Change Tracking i maszyn wirtualnych platformy Azure, zobacz [Włączanie maszyn wirtualnych platformy Azure](automation-enable-changes-from-auto-acct.md#enable-azure-vms)oraz maszyn wirtualnych spoza platformy Azure, zobacz [Włączanie maszyn w obszarze roboczym](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Aby potwierdzić wersję hybrydowego procesu roboczego elementu Runbook, przejdź do `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` podfolderu **wersji** i zanotuj go.

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 2. Instalowanie środowiska Runbook i nawiązywanie połączenia z usługą Azure Automation

W przypadku skonfigurowania agenta w celu raportowania do obszaru roboczego Log Analytics funkcja Azure Automation wypchnięcie `HybridRegistration` modułu programu PowerShell, który zawiera `Add-HybridRunbookWorker` polecenie cmdlet. To polecenie cmdlet służy do instalowania środowiska Runbook na komputerze i rejestrowania go w Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Teraz uruchom `Add-HybridRunbookWorker` polecenie cmdlet, używając następującej składni.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Możesz uzyskać informacje wymagane do parametrów `Url` i `Key` ze strony **klucze** na koncie usługi Automation. Wybierz pozycję **klucze** w sekcji **Ustawienia konta** w lewej części strony.

![Strona zarządzania kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Dla `Url` parametru skopiuj wartość **adresu URL**.

* Dla `Key` parametru skopiuj wartość **podstawowy klucz dostępu**.

* Dla `GroupName` parametru Użyj nazwy grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, jest dodawana.

* W razie potrzeby ustaw `Verbose` parametr, aby otrzymać szczegółowe informacje o instalacji.

### <a name="step-3----install-powershell-modules"></a>Krok 3 — Instalowanie modułów programu PowerShell

Elementy Runbook mogą korzystać z dowolnych działań i poleceń cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation. Ponieważ te moduły nie są automatycznie wdrażane na maszynach lokalnych, należy je zainstalować ręcznie. Wyjątek jest modułem platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem hybrydowego procesu roboczego elementu Runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły obsługujące te zasoby, w szczególności `PowerShellGet` moduł. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Zainstalowane moduły muszą znajdować się w lokalizacji, do której odwołuje się `PSModulePath` zmienna środowiskowa, dzięki czemu hybrydowy proces roboczy może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Install modules in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnej maszyny z systemem Windows

1. W Azure Portal przejdź do konta usługi Automation.

2. W obszarze **Ustawienia konta**wybierz pozycję **klucze** i zanotuj wartości **adresu URL** i **podstawowego klucza dostępu**.

3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie przy użyciu adresu URL i podstawowych wartości klucza dostępu. Użyj `Verbose` parametru, aby zapoznać się ze szczegółowym dziennikiem procesu usuwania. Aby usunąć przestarzałe maszyny z grupy hybrydowych procesów roboczych, użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdej maszyny, która jest członkiem grupy. Następnie wykonaj następujące kroki, aby usunąć grupę:

1. Otwórz konto usługi Automation w Azure Portal.

2. Wybierz **grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów**. Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stronie właściwości wybranej grupy wybierz pozycję **Usuń**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie tej akcji. Wybierz opcję **tak** , jeśli na pewno chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ukończenie tego procesu może potrwać kilka sekund. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](troubleshoot/hybrid-runbook-worker.md#general)
