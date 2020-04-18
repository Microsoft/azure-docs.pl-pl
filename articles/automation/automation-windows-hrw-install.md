---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje dotyczące instalowania procesu roboczego hybrydowego śunku usługi Azure Automation, którego można używać do uruchamiania śmiób ekwolniczej na komputerach z systemem Windows w lokalnym centrum danych lub środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617337"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego systemu Windows

Za pomocą funkcji Hybrydowy proces roboczy elementu runbook usługi Azure Automation można uruchamiać elementy runbook bezpośrednio na komputerze obsługującym rolę i przeciwko zasobom w środowisku do zarządzania tymi zasobami lokalnymi. Usługa Azure Automation przechowuje i zarządza systemami runbook, a następnie dostarcza je do jednego lub więcej wyznaczonych komputerów. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego żyła na komputerze z systemem Windows.

Po pomyślnym wdrożeniu procesu roboczego żyłajnik uruchom [runbook na hybrydowym układzie roboczym żyli,](automation-hrw-run-runbooks.md) aby dowiedzieć się, jak skonfigurować programy runbook do automatyzacji procesów w lokalnym centrum danych lub innym środowisku chmury.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalacja i konfiguracja procesu roboczego hybrydowego systemu Windows

Aby zainstalować i skonfigurować hybrydowy proces roboczy systemu Windows, można użyć jednej z następujących metod.

* W przypadku maszyn wirtualnych platformy Azure zainstaluj agenta analizy dzienników dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym usługi Log Analytics przy użyciu szablonu usługi Azure Resource Manager lub programu PowerShell. Po zainstalowaniu agenta maszynę wirtualną można dodać do grupy hybrydowego pracownika systemu runbook na koncie automatyzacji. Zapoznaj się z krokami 3 i 4 w sekcji [Ręczne wdrażanie.](#manual-deployment)

* Użyj systemu runbook automatyzacji, aby całkowicie zautomatyzować proces konfigurowania komputera z systemem Windows. Jest to zalecana metoda dla komputerów w centrum danych lub innym środowisku chmury.

* Postępuj zgodnie z procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę hybrydowego procesu roboczego podręcznika na maszynie wirtualnej nienawiązywać się do platformy Azure.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego niuansów z konfiguracją żądanego stanu (DSC), należy dodać serwery jako węzły DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimalne wymagania dla hybrydowego procesu roboczego systemu Windows

Minimalne wymagania dla hybrydowego procesu roboczego systemu Windows są następujące:

* Windows Server 2012 lub nowszy
* Windows PowerShell 5.1 lub nowsze[(pobierz WMF 5.1)](https://www.microsoft.com/download/details.aspx?id=54616)
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać więcej wymagań dotyczących sieci dla hybrydowego procesu roboczego uruchomieniu, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Dołączanie do serwera do zarządzania za pomocą automatyzacji DSC

Aby uzyskać informacje na temat dołączania serwerów do zarządzania za pomocą dsc, zobacz [Maszyny dołączania do zarządzania przez usługę Azure Automation DSC](automation-dsc-onboarding.md).

Włączenie [rozwiązania do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md) powoduje automatyczne skonfigurowanie dowolnego komputera z systemem Windows podłączonego do obszaru roboczego usługi Log Analytics jako hybrydowego procesu roboczego żyjącego w celu obsługi śmięty umnienia zawartych w rozwiązaniu. Jednak ten proces roboczy nie jest zarejestrowany w żadnych grupach hybrydowych procesów roboczych ujedkw.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowego roboczego podręcznika runbook

Komputer roboczy można dodać do grupy hybrydowego pracownika uruchomieniu na koncie automatyzacji. Należy zauważyć, że należy obsługiwać elementy runbook automatyzacji tak długo, jak używasz tego samego konta dla rozwiązania i członkostwa w grupie hybrydowego systemu runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

## <a name="automated-deployment"></a>Wdrożenie automatyczne

Na komputerze docelowym wykonaj następujące czynności, aby zautomatyzować instalację i konfigurację roli hybrydowy proces pracy systemu Windows.

### <a name="step-1---download-the-powershell-script"></a>Krok 1 - Pobierz skrypt programu PowerShell

Pobierz skrypt **New-OnPremiseHybridWorker.ps1** z [Galerii Programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Pobieranie powinno pochodzić bezpośrednio z komputera z rolą hybrydowego procesu roboczego lub z innego komputera w twoim środowisku. Po pobraniu skryptu skopiuj go do pracownika. Skrypt **New-OnPremiseHybridWorker.ps1** używa parametrów opisanych poniżej podczas wykonywania.

| Parametr | Stan | Opis |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obowiązkowy | Nazwa grupy zasobów skojarzonej z kontem automatyzacji. |
| `AutomationAccountName` | Obowiązkowy | Nazwa konta Automatyzacja.
| `Credential` | Optional (Opcjonalność) | Poświadczenia do użycia podczas logowania do środowiska platformy Azure. |
| `HybridGroupName` | Obowiązkowy | Nazwa grupy hybrydowego procesu roboczego niu go określanego jako miejsce docelowe dla kreśleń runbook obsługujących ten scenariusz. |
| `OMSResourceGroupName` | Optional (Opcjonalność) | Nazwa grupy zasobów dla obszaru roboczego usługi Log Analytics. Jeśli ta grupa zasobów nie jest `AAResourceGroupName` określona, używana jest wartość. |
| `SubscriptionID` | Obowiązkowy | Identyfikator subskrypcji platformy Azure skojarzony z kontem automatyzacji. |
| `TenantID` | Optional (Opcjonalność) | Identyfikator organizacji dzierżawcy skojarzonej z kontem automatyzacji. |
| `WorkspaceName` | Optional (Opcjonalność) | Nazwa obszaru roboczego usługi Log Analytics. Jeśli nie masz obszaru roboczego usługi Log Analytics, skrypt tworzy i konfiguruje jeden. |

> [!NOTE]
> Podczas włączania rozwiązań usługa Azure Automation obsługuje tylko niektóre regiony do łączenia obszaru roboczego usługi Log Analytics i konta automatyzacji. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionu dla konta automatyzacji i obszaru roboczego Usługi Analizy dzienników](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2 - Otwórz powłokę wiersza polecenia programu Windows PowerShell

Otwórz **program Windows PowerShell** na ekranie **startowym** w trybie administratora.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 - Uruchamianie skryptu programu PowerShell

W powłoce wiersza polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów `AutomationAccountName` `AAResourceGroupName`, `OMSResourceGroupName` `HybridGroupName`, `SubscriptionID`, `WorkspaceName`, i . Następnie uruchom skrypt.

Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie się przy użyciu platformy Azure. Musisz zalogować się przy za pomocą konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Krok 4 - Zainstaluj NuGet

Zostanie wyświetlony monit o wyrażenie zgody na zainstalowanie usługi NuGet i uwierzytelnienie się przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji NuGet, możesz ją uzyskać w [dostępnych wersjach dystrybucji NuGet.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Krok 5 - Sprawdź wdrożenie

Po zakończeniu skryptu na stronie Hybrydowe grupy robocze są wyświetlane nowa grupa i liczba członków. Jeśli jest to istniejąca grupa, liczba członków jest zwiększana. Grupę można wybrać z listy na stronie Hybrydowe grupy robocze i wybrać kafelek **Hybrydowych pracowników.** Na stronie Hybrydowe pracowników można wyświetlić każdego członka grupy na liście.

## <a name="manual-deployment"></a>Wdrożenie ręczne

Na komputerze docelowym wykonaj dwa pierwsze kroki raz dla środowiska automatyzacji. Następnie wykonaj pozostałe kroki dla każdego komputera roboczego.

### <a name="step-1---create-a-log-analytics-workspace"></a>Krok 1 - Tworzenie obszaru roboczego usługi Log Analytics

Jeśli nie masz jeszcze obszaru roboczego usługi Log Analytics, przed utworzeniem obszaru roboczego zapoznaj [się ze wskazówkami dotyczącymi projektu dziennika monitora platformy Azure.](../azure-monitor/platform/design-logs-deployment.md)

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Krok 2 — dodawanie rozwiązania automatyzacji do obszaru roboczego usługi Log Analytics

Rozwiązanie automatyzacji dodaje funkcje usługi Azure Automation, w tym obsługę procesu roboczego hybrydowego systemu runbook. Po dodaniu rozwiązania do obszaru roboczego usługi Log Analytics automatycznie wypycha do komputera agenta składniki procesu roboczego, które można zainstalować zgodnie z opisem w następnym kroku.

Aby dodać rozwiązanie automatyzacji do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Krok 3 - Instalowanie agenta analizy dzienników dla systemu Windows

Agent analizy dzienników dla systemu Windows łączy komputery z obszarem roboczym usługi Azure Monitor Log Analytics. Po zainstalowaniu agenta na komputerze i podłączeniu go do obszaru roboczego automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego systemu runbook.

Aby zainstalować agenta na komputerze, postępuj zgodnie z instrukcjami podanymi w aplikacji [Podłączanie komputerów z systemem Windows do dzienników usługi Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Można powtórzyć ten proces dla wielu komputerów, aby dodać wiele pracowników do środowiska.

Po pomyślnym nawiązaniu połączenia agenta z obszarem roboczym usługi Log Analytics po kilku minutach można uruchomić następującą kwerendę, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera, wskazujące, że jest połączony i raportowania do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego obszaru roboczego. 

Wykonaj następujące kroki, aby zakończyć instalację i konfigurację agenta.

1. Włącz rozwiązanie do wbudowanego komputera agenta. Zobacz [Maszyny pokładowe w obszarze roboczym](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Sprawdź, czy agent poprawnie pobrał rozwiązanie automatyzacji. 
3. Aby potwierdzić wersję hybrydowego procesu roboczego systemu runbook, przejdź do **folderu C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** i zanotuj podfolder **wersji.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 4 — Instalowanie środowiska runbook i łączenie się z usługą Azure Automation

Po skonfigurowaniu agenta do raportowania do obszaru roboczego usługi `HybridRegistration` Log Analytics rozwiązanie automatyzacji `Add-HybridRunbookWorker` wypycha moduł programu PowerShell, który zawiera polecenie cmdlet. Użyj tego polecenia cmdlet, aby zainstalować środowisko zestawu runbook na komputerze i zarejestrować go w usłudze Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Teraz uruchom `Add-HybridRunbookWorker` polecenie cmdlet przy użyciu następującej składni.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informacje wymagane dla tego polecenia cmdlet można uzyskać ze strony Zarządzanie kluczami w witrynie Azure portal. Otwórz tę stronę, wybierając **pozycję Klawisze** na stronie Ustawienia na koncie automatyzacji.

![Strona Zarządzanie kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* W `GroupName` przypadku tego parametru należy użyć nazwy grupy Hybrydowy proces roboczy żytnika. Jeśli ta grupa już istnieje na koncie automatyzacji, bieżący komputer jest dodawany do niego. Jeśli ta grupa nie istnieje, jest dodawana.
* W `EndPoint` przypadku tego parametru użyj wpisu **adresu URL** na stronie Zarządzanie kluczami.
* W `Token` przypadku tego parametru należy użyć wpisu **KLUCZ DOSTĘPU PODSTAWOWEGO** na stronie Zarządzanie kluczami.
* W razie potrzeby `Verbose` ustaw parametr, aby otrzymywać szczegółowe informacje o instalacji.

### <a name="step-5----install-powershell-modules"></a>Krok 5 - Instalowanie modułów programu PowerShell

Elementy runbooks można użyć dowolnego działania i polecenia cmdlet zdefiniowane w modułach zainstalowanych w środowisku usługi Azure Automation. Ponieważ te moduły nie są automatycznie wdrażane na komputerach lokalnych, należy zainstalować je ręcznie. Wyjątkiem jest moduł platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług platformy Azure i działań dla usługi Azure Automation.

Ponieważ głównym celem funkcji hybrydowy proces roboczy elementu runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły, które obsługują te zasoby, w szczególności `PowerShellGet` moduł. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Program Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Zainstalowane moduły muszą znajdować się w lokalizacji, do których odwołuje się zmienna `PSModulePath` środowiskowa, aby proces roboczy hybrydowy mógł je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Instalowanie modułów w psmodulepath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować programy runbook do automatyzacji procesów w lokalnym centrum danych lub innym środowisku chmury, zobacz [Runbooks w hybrydowym usłudze Runbook Worker](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące usuwania hybrydowych procesów ceł likwidujących, zobacz [Usuwanie hybrydowych workers ceł ceł systemu Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi pracownikami żyłazy, zobacz [Rozwiązywanie problemów z hybrydowymi pracownikami żyłagodników funkcjonowania systemu Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Aby uzyskać dodatkowe kroki dotyczące rozwiązywania problemów z zarządzaniem aktualizacjami, zobacz [Zarządzanie aktualizacjami: rozwiązywanie problemów](troubleshoot/update-management.md).
