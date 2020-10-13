---
title: Rozwiązywanie problemów z konfiguracją stanu Azure Automation
description: W tym artykule opisano sposób rozwiązywania problemów z konfiguracją stanu Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8043369ebfef23ed84ccff8e7428fbd2048e10b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187221"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Rozwiązywanie problemów z konfiguracją stanu Azure Automation

Ten artykuł zawiera informacje o rozwiązywaniu problemów i rozwiązywaniu problemów, które pojawiają się podczas kompilowania lub wdrażania konfiguracji w Azure Automation konfiguracji stanu. Aby uzyskać ogólne informacje na temat funkcji konfiguracja stanu, zobacz [Omówienie konfiguracji stanu Azure Automation](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnozuj problem

Po otrzymaniu błędu kompilacji lub wdrożenia dla konfiguracji należy wykonać kilka kroków, aby ułatwić zdiagnozowanie problemu.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja została pomyślnie skompilowana na komputerze lokalnym

Konfiguracja stanu Azure Automation jest oparta na konfiguracji żądanego stanu programu PowerShell (DSC). Dokumentację dotyczącą języka i składni DSC można znaleźć w dokumentacji [DSC programu PowerShell](/powershell/scripting/overview).

Przez skompilowanie konfiguracji DSC na komputerze lokalnym, można odnajdywać i rozwiązywać typowe błędy, takie jak:

   - Brak modułów.
   - Błędy składniowe.
   - Błędy logiki.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetl dzienniki DSC w Twoim węźle

Jeśli konfiguracja została pomyślnie skompilowana, ale nie powiedzie się w przypadku zastosowania do węzła, można znaleźć szczegółowe informacje w dziennikach DSC. Informacje o tym, gdzie znaleźć te dzienniki, można znaleźć w temacie [gdzie znajdują się dzienniki zdarzeń DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizie szczegółowych informacji z dzienników DSC. Jeśli skontaktujesz się z pomocą techniczną, wymagają one tych dzienników do zdiagnozowania problemu.

Moduł można zainstalować `xDscDiagnostics` na komputerze lokalnym, postępując zgodnie z instrukcjami w temacie [Instalowanie stabilnej wersji modułu](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Aby zainstalować `xDscDiagnostics` moduł na maszynie platformy Azure, użyj polecenie [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Można również użyć opcji **Uruchom polecenie** w Azure Portal, wykonując czynności opisane w temacie [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../../virtual-machines/windows/run-command.md).

Aby uzyskać informacje na temat korzystania z programu **xDscDiagnostics**, zobacz [Używanie xDscDiagnostics do analizowania dzienników DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zobacz również [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły

Konfiguracja DSC zależy od modułów zainstalowanych w węźle. W przypadku korzystania z konfiguracji stanu Azure Automation należy zaimportować wszystkie wymagane moduły do konta usługi Automation, wykonując czynności opisane w sekcji [Importowanie modułów](../shared-resources/modules.md#import-modules). Konfiguracje mogą również mieć zależność od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenariusz: nie można usunąć konfiguracji z użyciem znaków specjalnych z portalu

### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu zostanie wyświetlony następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowym problemem, który został zaplanowany do rozwiązania.

### <a name="resolution"></a>Rozwiązanie

Użyj polecenia cmdlet [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0) , aby usunąć konfigurację.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenariusz: nie można zarejestrować agenta DSC

### <a name="issue"></a>Problem

Po [ustawieniu-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lub innym poleceniu cmdlet DSC zostanie wyświetlony komunikat o błędzie:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Przyczyna

Ten błąd jest zwykle spowodowany przez zaporę, komputer za serwerem proxy lub inne błędy sieciowe.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp do odpowiednich punktów końcowych dla DSC, i spróbuj ponownie. Aby uzyskać listę wymaganych portów i adresów, zobacz [Planowanie sieci](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenariusz: raporty o stanie zwracają kod odpowiedzi bez autoryzacji

### <a name="issue"></a>Problem

Po zarejestrowaniu węzła z konfiguracją stanu Azure Automation zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zły lub wygasły certyfikat. Zobacz [Ponowne rejestrowanie węzła](../automation-dsc-onboarding.md#re-register-a-node).

Przyczyną tego problemu może być również konfiguracja serwera proxy, która nie zezwala na dostęp do ***. Azure-Automation.NET**. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci prywatnych](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby ponownie zarejestrować węzeł niepowodzenia konfiguracji DSC.

#### <a name="step-1-unregister-the-node"></a>Krok 1. Wyrejestruj węzeł

1. W Azure Portal przejdź do pozycji **Home**  >  **konta automatyzacji** głównej > (konto usługi Automation) > **Konfiguracja stanu (DSC)**.
1. Wybierz **węzły**i wybierz węzeł z problemami.
1. Wybierz pozycję **Wyrejestruj** , aby wyrejestrować węzeł.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Krok 2. odinstalowanie rozszerzenia DSC z węzła

1. W Azure Portal przejdź do **strony głównej**  >  **maszyny wirtualnej** > (węzeł z niepowodzeniem) > **rozszerzenia**.
1. Wybierz pozycję **Microsoft. PowerShell. DSC**, rozszerzenie DSC programu PowerShell.
1. Wybierz pozycję **Odinstaluj** , aby odinstalować rozszerzenie.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Krok 3. usunięcie wszystkich nieprawidłowych lub wygasłych certyfikatów z węzła

W przypadku niepowodzenia węzła w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenia:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Krok 4. ponowne zarejestrowanie węzła, który kończy się niepowodzeniem

1. W Azure Portal przejdź do pozycji **Home**  >  **konta automatyzacji** głównej > (konto usługi Automation) > **Konfiguracja stanu (DSC)**.
1. Wybierz **węzły**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz węzeł z niepowodzeniem.
1. Wybierz pozycję **Połącz**, a następnie wybierz odpowiednie opcje.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenariusz: węzeł jest w stanie niepowodzenia z powodu błędu "nie znaleziono"

### <a name="issue"></a>Problem

Węzeł zawiera raport z nieprawidłowym stanem i zawiera błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy węzeł jest przypisany do nazwy konfiguracji, na przykład **ABC**, zamiast nazwy węzła konfiguracji (pliku MOF), na przykład **ABC. Serwer**Web.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł z nazwą konfiguracji węzła, a nie nazwą konfiguracji.
* Konfigurację węzła można przypisać do węzła przy użyciu Azure Portal lub polecenia cmdlet programu PowerShell.

  * W Azure Portal przejdź do pozycji **Home**  >  **konta automatyzacji** głównej > (konto usługi Automation) > **Konfiguracja stanu (DSC)**. Następnie wybierz węzeł i wybierz pozycję **Przypisz konfigurację węzła**.
  * Użyj polecenia cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenariusz: podczas kompilowania konfiguracji nie zostały utworzone żadne konfiguracje węzłów (pliki MOF)

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Przyczyna

Gdy wyrażenie `Node` zgodne ze słowem kluczowym w konfiguracji DSC ma wartość `$null` , nie są generowane konfiguracje węzłów.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Upewnij się, że wyrażenie obok `Node` słowa kluczowego w definicji konfiguracji nie jest zgodne z wartością null.
* Jeśli przekazujesz [ConfigurationData](../automation-dsc-compile.md) podczas kompilowania konfiguracji, upewnij się, że przekazujesz wartości, których konfiguracja oczekuje od danych konfiguracyjnych.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC zostanie zablokowany w stanie w toku

### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Przyczyna

Uaktualniono wersję środowiska Windows Management Framework (WMF) i masz uszkodzone Instrumentacja zarządzania Windows (WMI).

### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami podanymi w temacie [znane problemy i ograniczenia DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenariusz: nie można użyć poświadczenia w konfiguracji DSC

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zostało zawieszone z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Przyczyna

W konfiguracji użyto poświadczeń, ale nie wprowadzono dla `ConfigurationData` `PSDscAllowPlainTextPassword` nich odpowiedniej wartości true dla każdej konfiguracji węzła.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że w `ConfigurationData` `PSDscAllowPlainTextPassword` każdej konfiguracji węzła, która jest wymieniona w konfiguracji, określono wartość true. Zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenariusz: "błąd przetwarzania rozszerzenia" podczas włączania maszyny z poziomu rozszerzenia DSC

### <a name="issue"></a>Problem

Gdy komputer zostanie włączony przy użyciu rozszerzenia DSC, wystąpi błąd, który zawiera błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy do węzła jest przypisana nazwa konfiguracji węzła, która nie istnieje w usłudze.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł o nazwie, która dokładnie pasuje do nazwy w usłudze.
* Można zrezygnować z uwzględnienia nazwy konfiguracji węzła, co spowoduje włączenie węzła, ale nie przypisanie konfiguracji węzła.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenariusz: "wystąpił co najmniej jeden błąd" podczas rejestrowania węzła przy użyciu programu PowerShell

### <a name="issue"></a>Problem

Po zarejestrowaniu węzła przy użyciu polecenia [register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) lub [register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)pojawia się następujący błąd:

```error
One or more errors occurred.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła w oddzielnej subskrypcji, który jest używany przez konto usługi Automation.

### <a name="resolution"></a>Rozwiązanie

Traktuj węzeł między subskrypcjami, tak jakby został zdefiniowany dla oddzielnej chmury lub lokalnie. Zarejestruj węzeł przy użyciu jednej z następujących opcji włączania maszyn:

* Windows: [fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [fizyczne/wirtualne maszyny z systemem Linux w środowisku lokalnym lub w chmurze innej niż Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie "Inicjowanie obsługi nie powiodło się"

### <a name="issue"></a>Problem

Po zarejestrowaniu węzła zostanie wyświetlony komunikat o błędzie:

```error
Provisioning has failed
```

### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem i platformą Azure.

### <a name="resolution"></a>Rozwiązanie

Ustal, czy węzeł znajduje się w wirtualnej sieci prywatnej (VPN), czy też ma inne problemy z nawiązywaniem połączenia z platformą Azure. Zobacz [Rozwiązywanie problemów z wdrażaniem funkcji](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenariusz: Wystąpił błąd ogólny podczas stosowania konfiguracji w systemie Linux

### <a name="issue"></a>Problem

W przypadku zastosowania konfiguracji w systemie Linux Wystąpił błąd, który zawiera następujący komunikat:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Przyczyna

Jeśli lokalizacja **/tmp** jest ustawiona na `noexec` , bieżąca wersja DSC nie będzie mogła zastosować konfiguracji.

### <a name="resolution"></a>Rozwiązanie

Usuń `noexec` opcję z lokalizacji **/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenariusz: nazwy konfiguracji węzłów, które pokrywają się, mogą spowodować nieprawidłową wersję

### <a name="issue"></a>Problem

W przypadku wygenerowania wielu konfiguracji węzłów za pomocą pojedynczego skryptu konfiguracji, a niektóre nazwy konfiguracji węzłów są podzbiorami innych nazw, usługa kompilacji może zakończyć przypisywanie niewłaściwej konfiguracji. Ten problem występuje tylko wtedy, gdy używasz pojedynczego skryptu do generowania konfiguracji z danymi konfiguracyjnymi na węzeł i tylko wtedy, gdy nastąpi odchodzenie nazw na początku ciągu. Przykładem jest pojedynczy skrypt konfiguracyjny używany do generowania konfiguracji na podstawie danych węzła przekazaną jako tablicę skrótów za pomocą poleceń cmdlet, a dane węzła obejmują serwery o nazwach **Server** i **1server**.

### <a name="cause"></a>Przyczyna

Jest to znany problem dotyczący usługi kompilacji.

### <a name="resolution"></a>Rozwiązanie

Najlepszym rozwiązaniem jest skompilowanie lokalnie lub w potoku ciągłej integracji/ciągłego wdrażania i przekazanie plików MOF konfiguracji węzła bezpośrednio do usługi. Jeśli kompilacja w usłudze jest wymagana, następnym najlepszym obejściem jest podzielenie zadań kompilacji tak, aby nie zachodziły na nazwy.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenariusz: błąd limitu czasu bramy w przekazywaniu konfiguracji DSC

#### <a name="issue"></a>Problem

`GatewayTimeout`Wystąpił błąd podczas przekazywania konfiguracji DSC. 

### <a name="cause"></a>Przyczyna

W przypadku konfiguracji DSC, które mogą być czasochłonne, może to spowodować wystąpienie tego błędu.

### <a name="resolution"></a>Rozwiązanie

Konfiguracje DSC można szybko przeanalizować przez jawne dołączenie `ModuleName` parametru do dowolnego wywołania [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
