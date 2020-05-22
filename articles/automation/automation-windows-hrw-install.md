---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows w Azure Automation
description: W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook, który służy do uruchamiania elementów Runbook na komputerach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 3a27cee7a94ee6f33c399d10f90e47ec574e7380
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744174"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednego wyznaczeniu komputerów. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Windows.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalacja i konfiguracja hybrydowego procesu roboczego elementu Runbook systemu Windows

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, można użyć jednej z następujących metod.

* W przypadku maszyn wirtualnych platformy Azure Zainstaluj agenta Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym Log Analytics przy użyciu szablonu Azure Resource Manager lub programu PowerShell. Po zainstalowaniu agenta można dodać maszynę wirtualną do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation. Zapoznaj się z krokami 3 i 4 w sekcji [wdrażanie ręczne](#manual-deployment) .

* Użyj elementu Runbook usługi Automation, aby całkowicie zautomatyzować proces konfigurowania komputera z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.

* Postępuj zgodnie z procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej spoza platformy Azure.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać serwery jako węzły DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows to:

* Windows Server 2012 lub nowszy
* Windows PowerShell 5,1 lub nowszy ([pobieranie plików WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać więcej wymagań sieci dla hybrydowego procesu roboczego elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Włączanie serwerów do zarządzania przy użyciu konfiguracji stanu Azure Automation

Aby uzyskać informacje na temat włączania serwerów do zarządzania przy użyciu konfiguracji stanu Azure Automation, zobacz [Konfiguracja stanu komputerów do zarządzania przez Azure Automation](automation-dsc-onboarding.md).

Włączenie Azure Automation [Update Management](automation-update-management.md) automatycznie konfiguruje dowolny komputer z systemem Windows połączony z obszarem roboczym log Analytics jako hybrydowy proces roboczy elementu Runbook do obsługi aktualizacji elementu Runbook. Jednak ten proces roboczy nie jest zarejestrowany w żadnych grup hybrydowych procesów roboczych elementu Runbook zdefiniowanych już na koncie usługi Automation.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowych procesów roboczych elementu Runbook

Komputer roboczy można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation. Należy pamiętać, że musisz obsługiwać elementy Runbook usługi Automation, o ile używasz tego samego konta zarówno dla funkcji Azure Automation, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

## <a name="automated-deployment"></a>Wdrożenie automatyczne

Na maszynie docelowej wykonaj następujące kroki, aby zautomatyzować instalację i konfigurację roli hybrydowego procesu roboczego systemu Windows.

### <a name="step-1---download-the-powershell-script"></a>Krok 1. Pobieranie skryptu programu PowerShell

Pobierz skrypt **New-OnPremiseHybridWorker. ps1** z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Pobieranie powinno być bezpośrednio z komputera z uruchomioną rolą hybrydowego procesu roboczego elementu Runbook lub z innego komputera w środowisku. Po pobraniu skryptu skopiuj go do procesu roboczego. Skrypt **New-OnPremiseHybridWorker. ps1** używa parametrów opisanych poniżej podczas wykonywania.

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

> [!NOTE]
> Podczas włączania funkcji Program Azure Automation obsługuje tylko niektóre regiony do łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2. Otwieranie powłoki wiersza polecenia programu Windows PowerShell

Otwórz program **Windows PowerShell** na ekranie **startowym** w trybie administratora.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 — Uruchamianie skryptu programu PowerShell

W powłoce wiersza polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` i `WorkspaceName` . Następnie uruchom skrypt.

Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Musisz zalogować się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Krok 4 — Instalowanie narzędzia NuGet

Zostanie wyświetlony monit o zgodę na zainstalowanie narzędzia NuGet i uwierzytelnienie przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji pakietu NuGet, możesz uzyskać ją z [dostępnych wersji dystrybucji NuGet](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Krok 5 — Weryfikowanie wdrożenia

Po zakończeniu działania skryptu na stronie grupy hybrydowych procesów roboczych zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie grupy hybrydowych procesów roboczych i wybrać kafelek **hybrydowe procesy** robocze. Na stronie hybrydowe procesy robocze można zobaczyć wszystkich członków tej grupy.

## <a name="manual-deployment"></a>Wdrażanie ręczne

Na maszynie docelowej wykonaj dwa pierwsze kroki w środowisku automatyzacji. Następnie wykonaj pozostałe kroki dla każdego komputera podrzędnego.

### <a name="step-1---create-a-log-analytics-workspace"></a>Krok 1. Tworzenie obszaru roboczego Log Analytics

Jeśli nie masz jeszcze obszaru roboczego Log Analytics, zapoznaj się ze [wskazówkami dotyczącymi projektu dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) przed utworzeniem obszaru roboczego.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Krok 2. Dodawanie funkcji Azure Automation do obszaru roboczego Log Analytics

Funkcja automatyzacji dodaje funkcjonalność dla Azure Automation, w tym obsługę hybrydowego procesu roboczego elementu Runbook. Po włączeniu funkcji Azure Automation w obszarze roboczym Log Analytics składniki procesu roboczego są automatycznie wypychane do komputera agenta.

Aby dodać funkcję Azure Automation, na przykład Update Management, do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Krok 3 — Instalowanie agenta Log Analytics dla systemu Windows

Agent Log Analytics dla systemu Windows łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na komputerze i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Aby zainstalować agenta na komputerze, postępuj zgodnie z instrukcjami na stronie [łączenie komputerów z systemem Windows w celu Azure monitor dzienników](../log-analytics/log-analytics-windows-agent.md). Można powtórzyć ten proces dla wielu komputerów, aby dodać wielu procesów roboczych do środowiska.

Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera, wskazujące, że są one połączone i raportowane do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego. Aby ukończyć instalację i konfigurację agenta, wykonaj następujące czynności.

1. Włącz tę funkcję, aby dodać maszynę agenta. Zobacz [Włączanie maszyn w obszarze roboczym](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Sprawdź, czy Agent prawidłowo pobrał funkcję Azure Automation. 
3. Aby potwierdzić wersję hybrydowego procesu roboczego elementu Runbook, przejdź do **katalogu C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation** i zanotuj podfolder **Version** .

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 4. Instalowanie środowiska Runbook i nawiązywanie połączenia z usługą Azure Automation

W przypadku skonfigurowania agenta w celu raportowania do obszaru roboczego Log Analytics funkcja Azure Automation wypchnięcie `HybridRegistration` modułu programu PowerShell, który zawiera `Add-HybridRunbookWorker` polecenie cmdlet. To polecenie cmdlet służy do instalowania środowiska Runbook na komputerze i rejestrowania go w Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Teraz uruchom `Add-HybridRunbookWorker` polecenie cmdlet, używając następującej składni.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informacje wymagane dla tego polecenia cmdlet można uzyskać ze strony Zarządzanie kluczami w Azure Portal. Otwórz Tę stronę, wybierając pozycję **klucze** na stronie Ustawienia na koncie usługi Automation.

![Strona zarządzania kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Dla `GroupName` parametru Użyj nazwy grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, jest dodawana.
* Dla `EndPoint` parametru Użyj wpisu **adresu URL** na stronie Zarządzanie kluczami.
* Dla `Token` parametru należy użyć wpisu **podstawowego klucza dostępu** na stronie Zarządzanie kluczami.
* W razie potrzeby ustaw `Verbose` parametr, aby otrzymać szczegółowe informacje o instalacji.

### <a name="step-5----install-powershell-modules"></a>Krok 5. Instalowanie modułów programu PowerShell

Elementy Runbook mogą korzystać z dowolnych działań i poleceń cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation. Ponieważ te moduły nie są automatycznie wdrażane na komputerach lokalnych, należy je zainstalować ręcznie. Wyjątek jest modułem platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem hybrydowego procesu roboczego elementu Runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły obsługujące te zasoby, w szczególności `PowerShellGet` moduł. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Zainstalowane moduły muszą znajdować się w lokalizacji, do której odwołuje się `PSModulePath` zmienna środowiskowa, dzięki czemu hybrydowy proces roboczy może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnego komputera z systemem Windows

1. W Azure Portal przejdź do konta usługi Automation.
2. W obszarze **Ustawienia konta**wybierz pozycję **klucze** i zanotuj wartości **adresu URL** i **podstawowego klucza dostępu**.

3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie przy użyciu adresu URL i podstawowych wartości klucza dostępu. Użyj `Verbose` parametru, aby zapoznać się ze szczegółowym dziennikiem procesu usuwania. Aby usunąć przestarzałe maszyny z grupy hybrydowych procesów roboczych, użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy. Następnie wykonaj następujące kroki, aby usunąć grupę:

1. Otwórz konto usługi Automation w Azure Portal.
2. Wybierz **grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów**. Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stronie właściwości wybranej grupy wybierz pozycję **Usuń**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie tej akcji. Wybierz opcję **tak** , jeśli na pewno chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ukończenie tego procesu może potrwać kilka sekund. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md)
* [Rozwiązywanie problemów z hybrydowym procesem roboczym elementu Runbook](troubleshoot/hybrid-runbook-worker.md#windows)