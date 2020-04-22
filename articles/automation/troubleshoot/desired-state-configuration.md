---
title: Rozwiązywanie problemów z konfiguracją stanu automatyzacji platformy Azure (DSC)
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją stanu automatyzacji platformy Azure (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679322"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Rozwiązywanie problemów z konfiguracją stanu automatyzacji platformy Azure (DSC)

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów, które pojawiają się podczas kompilowania lub wdrażania konfiguracji w konfiguracji stanu automatyzacji platformy Azure (DSC).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="diagnosing-an-issue"></a>Diagnozowanie problemu

Po otrzymaniu błędu kompilacji lub wdrożenia dla konfiguracji, oto kilka kroków, które pomogą Ci zdiagnozować problem.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja kompiluje się pomyślnie na komputerze lokalnym

Konfiguracja stanu usługi Azure Automation State (DSC) jest oparta na konfiguracji żądanego stanu programu PowerShell (DSC). Dokumentację dotyczącą języka i składni DSC można znaleźć w [dokumentach dsc programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Kompilując konfigurację DSC na komputerze lokalnym, można wykrywać i rozwiązywać typowe błędy, takie jak:

   - Brakujące moduły
   - Błędy składniowe
   - Błędy logiczne

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetlanie dzienników DSC w węźle

Jeśli konfiguracja kompiluje się pomyślnie, ale kończy się niepowodzeniem po zastosowaniu do węzła, można znaleźć szczegółowe informacje w dziennikach DSC. Aby uzyskać informacje o tym, gdzie można znaleźć te dzienniki, zobacz [Gdzie są dzienniki zdarzeń DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizowaniu szczegółowych informacji z dzienników DSC. Jeśli skontaktujesz się z pomocą techniczną, wymagają one tych dzienników, aby zdiagnozować problem.

Moduł można zainstalować na komputerze lokalnym, korzystając z instrukcji podanych w instrukcji [Zainstaluj moduł stabilnej wersji](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module). `xDscDiagnostics`

Aby zainstalować `xDscDiagnostics` moduł na komputerze platformy Azure, należy użyć [invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Polecenia **Uruchom** można również użyć w portalu Azure, wykonując czynności opisane w [poleceniach uruchom program PowerShell w maszynie Wirtualnej systemu Windows z poleceniem Uruchom](../../virtual-machines/windows/run-command.md)polecenie .

Aby uzyskać informacje na temat **używania xDscDiagnostics**, zobacz [Używanie diagnostyki xDscDiagnostics do analizowania dzienników DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zobacz też [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy automatyzacji mają wymagane moduły

DSC zależy od modułów zainstalowanych w węźle. Korzystając z konfiguracji stanu automatyzacji platformy Azure, zaimportuj do konta automatyzacji wszystkie wymagane moduły, wykonując czynności opisane w module [importu](../shared-resources/modules.md#importing-modules). Konfiguracje mogą również mieć zależność od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenariusz: Nie można usunąć konfiguracji ze znakami specjalnymi z portalu

### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu pojawia się następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowy problem, który jest planowany do rozwiązania.

### <a name="resolution"></a>Rozwiązanie

Użyj polecenia cmdlet [Usuń-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 aby usunąć konfigurację.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenariusz: nie można zarejestrować agenta dsc

### <a name="issue"></a>Problem

Podczas [set-dsclocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lub innego polecenia cmdlet DSC pojawia się błąd:

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

Ten błąd jest zwykle spowodowany przez zaporę, komputer znajduje się za serwerem proxy lub inne błędy sieciowe.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp do odpowiednich punktów końcowych dla DSC i spróbuj ponownie. Aby uzyskać listę potrzebnych portów i adresów, zobacz [planowanie sieci](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenariusz: Raporty o stanie kodu odpowiedzi zwrotnej Nieautoryzowane

### <a name="issue"></a>Problem

Podczas rejestrowania węzła w konfiguracji stanu automatyzacji platformy Azure pojawia się jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez uszkodzony lub wygasły certyfikat. Zobacz [Wygaśnięcie i ponowna rejestracja certyfikatu](../automation-dsc-onboarding.md#re-registering-a-node).

Ten problem może być również spowodowany przez konfigurację serwera proxy nie zezwalającą na dostęp do ***.azure-automation.net**. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci prywatnych](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Rozwiązanie

Skorzystaj z poniższych czynności, aby ponownie zarejestrować nieudany węzeł DSC.

Krok 1 - Wyrejestrować węzeł.

1. W witrynie Azure portal przejdź do**pozycji Konta automatyzacji** **>** -> (konto automatyzacji) — konfiguracja stanu > **(DSC).**
2. Wybierz **węzły**i kliknij węzeł, który ma problemy.
3. Kliknij przycisk **Wyrejestruj,** aby wyrejestrować węzeł.

Krok 2 - Odinstaluj rozszerzenie DSC z węzła.

1. W witrynie Azure portal przejdź do **home** -> **virtual machine** -> (węzeł w razie awarii) - rozszerzenia > . **Extensions**
2. Wybierz **pozycję Microsoft.Powershell.DSC**— rozszerzenie dsc programu PowerShell.
3. Kliknij **przycisk Odinstaluj,** aby odinstalować rozszerzenie.

Krok 3 - Usuń wszystkie uszkodzone lub wygasłe certyfikaty z węzła.

W węźle w przypadku awarii z wiersza programu PowerShell uruchom następujące polecenia:

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

Krok 4 - Ponownie zarejestrować węzeł w sieci wada.

1. W witrynie Azure portal przejdź do**pozycji Konta automatyzacji** **>** -> (konto automatyzacji) — konfiguracja stanu > **(DSC)**
2. Wybierz **węzły**.
3. Kliknij pozycję **Add** (Dodaj).
4. Wybierz węzeł w stanie awarii.
5. Kliknij **pozycję Połącz** i wybierz żądane opcje.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenariusz: Węzeł jest w stanie awarii z błędem "Nie znaleziono"

### <a name="issue"></a>Problem

Węzeł ma raport o stanie Niepowodzenie i zawierający błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy węzeł jest przypisany do nazwy konfiguracji, na przykład **ABC**, zamiast nazwy konfiguracji węzła (plik MOF), na przykład **ABC. Serwer www**.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł z nazwą konfiguracji węzła, a nie nazwą konfiguracji.
* Konfigurację węzła można przypisać do węzła za pomocą portalu Azure lub polecenia cmdlet programu PowerShell.

  * W witrynie Azure portal przejdź do**pozycji Konta automatyzacji** **>** -> (konto automatyzacji) — konfiguracja stanu > **(DSC),** a następnie wybierz węzeł i kliknij pozycję **Przypisz konfigurację węzła**.
  * Użyj polecenia cmdlet [Set-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenariusz: Podczas kompilowania konfiguracji nie zostały wyprodukowane żadne konfiguracje węzłów (pliki MOF).

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Przyczyna

Gdy wyrażenie następujące `Node` po słowie kluczowym `$null`w konfiguracji DSC ocenia , nie są tworzone żadne konfiguracje węzłów.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Upewnij się, że wyrażenie `Node` obok słowa kluczowego w definicji konfiguracji nie jest oceny null.
* Jeśli przekazujesz [ConfigurationData](../automation-dsc-compile.md) podczas kompilowania konfiguracji, upewnij się, że przekazujesz wartości, których oczekuje konfiguracja od danych konfiguracji.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC utknie w stanie W toku

### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Przyczyna

Uaktualniono wersję programu Windows Management Framework (WMF) i uszkodzono instrumentację zarządzania windowsem (WMI).

### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami w [DSC znanych problemów i ograniczeń](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenariusz: nie można użyć poświadczenia w konfiguracji DSC

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawieszone z błędem:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Przyczyna

Poświadczenia zostały użyte w konfiguracji, ale nie `ConfigurationData` zapewniają `PSDscAllowPlainTextPassword` właściwe, aby ustawić true dla każdej konfiguracji węzła.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, aby `ConfigurationData` przekazać `PSDscAllowPlainTextPassword` w odpowiedniej, aby ustawić true dla każdej konfiguracji węzła, który jest wymieniony w konfiguracji. Zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenariusz: Błąd "Rozszerzenie przetwarzania awarii" podczas dołączania z rozszerzenia DSC

### <a name="issue"></a>Problem

Podczas dołączania przy użyciu rozszerzenia DSC występuje błąd zawierający błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy węzeł jest przypisany nazwę konfiguracji węzła, który nie istnieje w usłudze.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł o nazwie, która dokładnie pasuje do nazwy w usłudze.
* Można wybrać opcję nie dołączania nazwy konfiguracji węzła, co powoduje dołączanie węzła, ale nie przypisywanie konfiguracji węzła.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenariusz: błąd "Wystąpił co najmniej jeden błąd" podczas rejestrowania węzła przy użyciu programu PowerShell

### <a name="issue"></a>Problem

Podczas rejestrowania węzła przy użyciu [funkcji Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) lub [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)pojawia się następujący błąd:

```error
One or more errors occurred.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła w oddzielnej subskrypcji z tej używanej przez konto automatyzacji.

### <a name="resolution"></a>Rozwiązanie

Potraktuj węzeł między subskrypcjami tak, jakby został zdefiniowany dla oddzielnej chmury lub lokalnie. Zarejestruj węzeł przy użyciu jednej z następujących opcji dołączania:

* Windows — [fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux — [fizyczne/wirtualne maszyny z systemem Linux lokalnie lub w chmurze innej niż Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie — "Nie można zainicjować obsługi administracyjnej"

### <a name="issue"></a>Problem

Podczas rejestrowania węzła pojawia się błąd:

```error
Provisioning has failed
```

### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem a platformą Azure.

### <a name="resolution"></a>Rozwiązanie

Określ, czy węzeł znajduje się w wirtualnej sieci prywatnej (VPN) lub ma inne problemy z łączeniem się z platformą Azure. Zobacz [Rozwiązywanie problemów z błędami podczas dołączania rozwiązań](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenariusz: Błąd z ogólnym błędem podczas stosowania konfiguracji w systemie Linux

### <a name="issue"></a>Problem

Podczas stosowania konfiguracji w systemie Linux występuje błąd zawierający błąd:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Przyczyna

Jeśli **/tmp** lokalizacja jest `noexec`ustawiona na , bieżącej wersji DSC nie można zastosować konfiguracje.

### <a name="resolution"></a>Rozwiązanie

Usuń `noexec` tę opcję z lokalizacji **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenariusz: nazwy konfiguracji węzła, które nakładają się na siebie, mogą spowodować nieprawidłowe zwolnienie

### <a name="issue"></a>Problem

Jeśli używasz skryptu konfiguracji do generowania wielu konfiguracji węzłów, a niektóre nazwy konfiguracji węzłów są podzbiorami innych nazw, usługa kompilacji może w końcu przypisać niewłaściwą konfigurację. Ten problem występuje tylko wtedy, gdy przy użyciu pojedynczego skryptu do generowania konfiguracji z danymi konfiguracji na węzeł i tylko wtedy, gdy nakładanie się nazwy występuje na początku ciągu. Przykładem jest pojedynczy skrypt konfiguracyjny używany do generowania konfiguracji na podstawie danych węzła przekazywanych jako mieszadłowe przy użyciu poleceń cmdlet, a dane węzła obejmują serwery o nazwie **serwer** i **serwer 1 serwera**.

### <a name="cause"></a>Przyczyna

Jest to znany problem z usługą kompilacji.

### <a name="resolution"></a>Rozwiązanie

Najlepszym rozwiązaniem jest skompilowanie lokalnie lub w potoku ciągłej integracji/ciągłego wdrażania i przekazywanie plików MOF konfiguracji węzła bezpośrednio do usługi. Jeśli kompilacja w usłudze jest wymaganiem, następnym najlepszym rozwiązaniem jest podzielenie zadań kompilacji, tak aby nie nakładać się w nazwy.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenariusz: błąd limitu czasu bramy podczas przekazywania konfiguracji DSC

#### <a name="issue"></a>Problem

Podczas przekazywania `GatewayTimeout` konfiguracji DSC pojawia się błąd. 

### <a name="cause"></a>Przyczyna

Konfiguracje DSC, które zajmują dużo czasu, aby skompilować może spowodować ten błąd.

### <a name="resolution"></a>Rozwiązanie

Konfiguracje DSC można przyspieszyć, jawnie dołączając `ModuleName` parametr dla wszystkich wywołań [Import-DSCResource.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.