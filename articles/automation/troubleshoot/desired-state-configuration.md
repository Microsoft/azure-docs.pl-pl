---
title: Rozwiązywanie problemów z konfiguracją stanu Azure Automation (DSC)
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją stanu Azure Automation (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679322"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Rozwiązywanie problemów z konfiguracją stanu Azure Automation (DSC)

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów występujących podczas kompilowania lub wdrażania konfiguracji w programie Azure Automation State Configuration (DSC).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnozowanie problemu

Po otrzymaniu błędu kompilacji lub wdrożenia dla konfiguracji należy wykonać kilka kroków, aby ułatwić zdiagnozowanie problemu.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja została pomyślnie skompilowana na komputerze lokalnym

Konfiguracja stanu Azure Automation (DSC) jest oparta na konfiguracji żądanego stanu programu PowerShell (DSC). Dokumentację dotyczącą języka i składni DSC można znaleźć w dokumentacji [DSC programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Przez skompilowanie konfiguracji DSC na komputerze lokalnym, można odnajdywać i rozwiązywać typowe błędy, takie jak:

   - Brakujące moduły
   - Błędy składniowe
   - Błędy logiki

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetl dzienniki DSC w Twoim węźle

Jeśli konfiguracja została pomyślnie skompilowana, ale nie powiedzie się w przypadku zastosowania do węzła, można znaleźć szczegółowe informacje w dziennikach DSC. Informacje o tym, gdzie znaleźć te dzienniki, można znaleźć w temacie [gdzie znajdują się dzienniki zdarzeń DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizie szczegółowych informacji z dzienników DSC. Jeśli skontaktujesz się z pomocą techniczną, wymagają one tych dzienników do zdiagnozowania problemu.

`xDscDiagnostics` Moduł można zainstalować na maszynie lokalnej, korzystając z instrukcji dostępnych w temacie [Instalowanie stabilnej wersji modułu](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Aby zainstalować `xDscDiagnostics` moduł na maszynie platformy Azure, użyj polecenie [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Można również użyć opcji **Uruchom polecenie** w Azure Portal, wykonując czynności opisane w sekcji [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../../virtual-machines/windows/run-command.md).

Aby uzyskać informacje na temat korzystania z programu **xDscDiagnostics**, zobacz [Używanie xDscDiagnostics do analizowania dzienników DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zobacz również [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły

Konfiguracja DSC zależy od modułów zainstalowanych w węźle. W przypadku korzystania z konfiguracji stanu Azure Automation należy zaimportować wszystkie wymagane moduły do konta usługi Automation, wykonując kroki z sekcji [Importowanie modułów](../shared-resources/modules.md#importing-modules). Konfiguracje mogą również mieć zależność od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenariusz: nie można usunąć konfiguracji z użyciem znaków specjalnych z portalu

### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu zostanie wyświetlony następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowym problemem, który został zaplanowany do rozwiązania.

### <a name="resolution"></a>Rozwiązanie

Aby usunąć konfigurację, użyjhttps://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 polecenia cmdlet [Remove-AzAutomationDscConfiguration].

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenariusz: nie można zarejestrować agenta DSC

### <a name="issue"></a>Problem

Po [ustawieniu-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lub innym poleceniu cmdlet DSC zostanie wyświetlony komunikat o błędzie:

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

Sprawdź, czy komputer ma dostęp do odpowiednich punktów końcowych dla DSC, i spróbuj ponownie. Aby uzyskać listę wymaganych portów i adresów, zobacz [Planowanie sieci](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenariusz: raporty o stanie zwracają kod odpowiedzi nieautoryzowany

### <a name="issue"></a>Problem

Podczas rejestrowania węzła z konfiguracją stanu Azure Automation zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zły lub wygasły certyfikat. Zobacz [wygaśnięcie i rejestrację certyfikatu](../automation-dsc-onboarding.md#re-registering-a-node).

Przyczyną tego problemu może być również konfiguracja serwera proxy, która nie zezwala na dostęp do ***. Azure-Automation.NET**. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci prywatnych](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Rozwiązanie

Wykonaj poniższe kroki, aby ponownie zarejestrować węzeł DSC zakończony niepowodzeniem.

Krok 1. Wyrejestruj węzeł.

1. W Azure Portal przejdź do**konta usługi Automation** **Home** -> — > (konto usługi Automation) — > **Konfiguracja stanu (DSC)**.
2. Wybierz **węzły**i kliknij węzeł z problemami.
3. Kliknij pozycję **Wyrejestruj** , aby wyrejestrować węzeł.

Krok 2. Odinstaluj rozszerzenie DSC z węzła.

1. W Azure Portal przejdź do **strony głównej** -> **maszyny wirtualnej** — > (węzeł z niepowodzeniem) — **rozszerzenia**>.
2. Wybierz pozycję **Microsoft. PowerShell. DSC**, rozszerzenie DSC programu PowerShell.
3. Kliknij przycisk **Odinstaluj** , aby odinstalować rozszerzenie.

Krok 3. usunięcie wszystkich nieprawidłowych lub wygasłych certyfikatów z węzła.

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

Krok 4. Zarejestruj ponownie węzeł, który się nie powiodło.

1. W Azure Portal przejdź do**konta usługi Automation** **Home** -> — > (konto usługi Automation) — **Konfiguracja stanu > (DSC)**
2. Wybierz **węzły**.
3. Kliknij pozycję **Dodaj**.
4. Wybierz węzeł z niepowodzeniem.
5. Kliknij przycisk **Połącz** i wybierz odpowiednie opcje.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenariusz: węzeł jest w stanie niepowodzenia z powodu błędu "nie znaleziono"

### <a name="issue"></a>Problem

Węzeł zawiera raport ze stanem niepowodzenie i zawierający błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy węzeł jest przypisany do nazwy konfiguracji, na przykład **ABC**, zamiast nazwy węzła konfiguracji (pliku MOF), na przykład **ABC. Serwer**Web.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł z nazwą konfiguracji węzła, a nie nazwą konfiguracji.
* Konfigurację węzła można przypisać do węzła przy użyciu Azure Portal lub za pomocą polecenia cmdlet programu PowerShell.

  * W Azure Portal przejdź do opcji **Główne** -> **konta usługi Automation** — > (konto usługi Automation) — > **Konfiguracja stanu (DSC)**, a następnie wybierz węzeł, a następnie kliknij pozycję **Przypisz konfigurację węzła**.
  * Użyj polecenia cmdlet [Set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenariusz: podczas kompilowania konfiguracji nie zostały utworzone żadne konfiguracje węzłów (pliki MOF)

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Przyczyna

Gdy wyrażenie zgodne ze `Node` słowem kluczowym w konfiguracji DSC ma `$null`wartość, nie są generowane konfiguracje węzłów.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Upewnij się, że wyrażenie obok `Node` słowa kluczowego w definicji konfiguracji nie jest zgodne z wartością null.
* Jeśli przekazujesz [ConfigurationData](../automation-dsc-compile.md) podczas kompilowania konfiguracji, upewnij się, że przekazujesz wartości, których konfiguracja oczekuje od danych konfiguracyjnych.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC zostanie zablokowany w stanie w toku

### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Przyczyna

Uaktualniono wersję środowiska Windows Management Framework (WMF) i masz uszkodzone Instrumentacja zarządzania Windows (WMI).

### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami podanymi w temacie [znane problemy i ograniczenia DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenariusz: nie można użyć poświadczenia w konfiguracji DSC

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zostało zawieszone z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Przyczyna

W konfiguracji użyto poświadczeń, ale nie wprowadzono dla nich odpowiedniej `ConfigurationData` `PSDscAllowPlainTextPassword` wartości true dla każdej konfiguracji węzła.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że w każdej konfiguracji `ConfigurationData` węzła, `PSDscAllowPlainTextPassword` która została określona w konfiguracji, zostanie podane odpowiednie ustawienie true. Zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenariusz: "błąd przetwarzania rozszerzenia" podczas dołączania z rozszerzenia DSC

### <a name="issue"></a>Problem

Podczas dołączania przy użyciu rozszerzenia DSC wystąpi błąd zawierający błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy do węzła jest przypisana nazwa konfiguracji węzła, która nie istnieje w usłudze.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł o nazwie, która dokładnie pasuje do nazwy w usłudze.
* Można zrezygnować z dołączania nazwy konfiguracji węzła, która powoduje dołączenie węzła, ale nie przypisanie konfiguracji węzła.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenariusz: "wystąpił co najmniej jeden błąd podczas rejestrowania węzła przy użyciu programu PowerShell

### <a name="issue"></a>Problem

Podczas rejestrowania węzła przy użyciu polecenia [register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) lub [register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)pojawia się następujący błąd:

```error
One or more errors occurred.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła w oddzielnej subskrypcji, który jest używany przez konto usługi Automation.

### <a name="resolution"></a>Rozwiązanie

Traktuj węzeł między subskrypcjami, tak jakby został zdefiniowany dla oddzielnej chmury lub lokalnie. Zarejestruj węzeł przy użyciu jednej z następujących opcji dołączania:

* Windows — [fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux — [fizyczne/wirtualne maszyny z systemem Linux w środowisku lokalnym lub w chmurze innej niż Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie — "Inicjowanie obsługi nie powiodło się"

### <a name="issue"></a>Problem

Podczas rejestrowania węzła zostanie wyświetlony komunikat o błędzie:

```error
Provisioning has failed
```

### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem i platformą Azure.

### <a name="resolution"></a>Rozwiązanie

Ustal, czy węzeł znajduje się w wirtualnej sieci prywatnej (VPN), czy też ma inne problemy z nawiązywaniem połączenia z platformą Azure. Zobacz [Rozwiązywanie problemów podczas](onboarding.md)dołączania rozwiązań.

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenariusz: Wystąpił błąd ogólny podczas stosowania konfiguracji w systemie Linux

### <a name="issue"></a>Problem

W przypadku stosowania konfiguracji w systemie Linux Wystąpił błąd z powodu błędu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Przyczyna

Jeśli lokalizacja **/tmp** jest ustawiona na `noexec`, bieżąca wersja DSC nie będzie mogła zastosować konfiguracji.

### <a name="resolution"></a>Rozwiązanie

Usuń `noexec` opcję z lokalizacji **/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenariusz: nazwy konfiguracji węzłów, które pokrywają się, mogą spowodować utratę prawidłowej wersji

### <a name="issue"></a>Problem

W przypadku wygenerowania wielu konfiguracji węzłów za pomocą pojedynczego skryptu konfiguracji, a niektóre nazwy konfiguracji węzłów są podzbiorami innych nazw, usługa kompilacji może zakończyć przypisywanie niewłaściwej konfiguracji. Ten problem występuje tylko w przypadku użycia pojedynczego skryptu do generowania konfiguracji z danymi konfiguracyjnymi na węzeł i tylko wtedy, gdy nazwa się pokrywa na początku ciągu. Przykładem jest pojedynczy skrypt konfiguracyjny używany do generowania konfiguracji na podstawie danych węzła przekazaną jako tablicę skrótów za pomocą poleceń cmdlet, a dane węzła obejmują serwery o nazwach **Server** i **1server**.

### <a name="cause"></a>Przyczyna

Jest to znany problem dotyczący usługi kompilacji.

### <a name="resolution"></a>Rozwiązanie

Najlepszym rozwiązaniem jest skompilowanie lokalnie lub w potoku ciągłej integracji/ciągłego wdrażania i przekazanie plików MOF konfiguracji węzła bezpośrednio do usługi. Jeśli kompilacja w usłudze jest wymagana, następnym najlepszym obejściem jest podzielenie zadań kompilacji tak, aby nie nakładały się nazw.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenariusz: błąd limitu czasu bramy w przekazywaniu konfiguracji DSC

#### <a name="issue"></a>Problem

`GatewayTimeout` Wystąpił błąd podczas przekazywania konfiguracji DSC. 

### <a name="cause"></a>Przyczyna

W przypadku konfiguracji DSC, które mogą być czasochłonne, może to spowodować wystąpienie tego błędu.

### <a name="resolution"></a>Rozwiązanie

Konfiguracje DSC można szybko przeanalizować przez jawne dołączenie `ModuleName` parametru do dowolnego wywołania [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz Twojego problemu powyżej lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.