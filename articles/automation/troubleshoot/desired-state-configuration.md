---
title: Rozwiązywanie problemów z konfiguracją żądanego stanu automatyzacji usługi Azure (DSC)
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją żądanego stanu (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f0034d06c852d4d71284093879a456d296d98715
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457471"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Rozwiązywanie problemów z konfiguracją żądanego stanu usługi Azure Automation (DSC)

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją żądanego stanu (DSC).

## <a name="diagnosing-an-issue"></a>Diagnozowanie problemu

Jeśli masz błędy podczas kompilowania lub wdrażania konfiguracji w konfiguracji stanu platformy Azure, oto kilka kroków ułatwiających zdiagnozowanie problemu.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja kompiluje się pomyślnie na komputerze lokalnym

Usługa Azure State Configuration jest oparta na programie PowerShell DSC. Dokumentację dotyczącą języka i składni DSC można znaleźć w [dokumentach dsc programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Kompilując konfigurację DSC na komputerze lokalnym, można wykrywać i rozwiązywać typowe błędy, takie jak:

   - Brakujące moduły
   - Błędy składniowe
   - Błędy logiczne

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetlanie dzienników DSC w węźle

Jeśli konfiguracja kompiluje się pomyślnie, ale kończy się niepowodzeniem po zastosowaniu do węzła, można znaleźć szczegółowe informacje w dziennikach DSC. Aby uzyskać informacje o tym, gdzie można znaleźć te dzienniki, zobacz [Gdzie są dzienniki zdarzeń DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizowaniu szczegółowych informacji z dzienników DSC. Jeśli skontaktujesz się z pomocą techniczną, wymagają one tych dzienników, aby zdiagnozować problem.

Moduł xDscDiagnostics można zainstalować na komputerze lokalnym, korzystając z instrukcji znalezionych w [instrukcji Zainstaluj moduł stabilnej wersji](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Aby zainstalować moduł xDscDiagnostics na komputerze platformy Azure, użyj [invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Można również użyć opcji **polecenia Uruchom** z portalu, wykonując kroki znalezione w [poleceniach Uruchom program PowerShell w maszynie Wirtualnej systemu Windows z poleceniem Uruchom](../../virtual-machines/windows/run-command.md)polecenie .

Aby uzyskać informacje na temat używania xDscDiagnostics, zobacz [Używanie diagnostyki xDscDiagnostics do analizowania dzienników DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zobacz też [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy automatyzacji mają wymagane moduły

DSC zależy od modułów zainstalowanych w węźle. Korzystając z konfiguracji stanu automatyzacji platformy Azure, zaimportuj wszystkie wymagane moduły do konta automatyzacji, wykonując kroki wymienione w module [importu](../shared-resources/modules.md#importing-modules). Konfiguracje mogą również mieć zależność od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Typowe błędy podczas pracy z DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenariusz: Nie można usunąć konfiguracji ze znakami specjalnymi z portalu

#### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu pojawia się następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowy problem, który jest planowany do rozwiązania.

#### <a name="resolution"></a>Rozwiązanie

* Użyj polecenia cmdlet Az "Remove-AzAutomationDscConfiguration", aby usunąć konfigurację.
* Dokumentacja dla tego polecenia cmdlet nie została jeszcze zaktualizowana.  Do tego czasu zapoznaj się z dokumentacją modułu AzureRM.
  * [Konfiguracja usuń usługę AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenariusz: nie można zarejestrować agenta dsc

#### <a name="issue"></a>Problem

Podczas próby uruchomienia `Set-DscLocalConfigurationManager` lub innego polecenia cmdlet DSC pojawia się błąd:

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

#### <a name="cause"></a>Przyczyna

Ten błąd jest zwykle spowodowany przez zaporę, komputer znajduje się za serwerem proxy lub inne błędy sieciowe.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp do odpowiednich punktów końcowych dla usługi Azure Automation DSC i spróbuj ponownie. Aby uzyskać listę potrzebnych portów i adresów, zobacz [planowanie sieci](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenariusz: Raporty o stanie zwraca kod odpowiedzi "Nieautoryzowane"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła z konfiguracją stanu (DSC) pojawia się jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez uszkodzony lub wygasły certyfikat.  Aby uzyskać więcej informacji, zobacz [Wygaśnięcie certyfikatu i ponowna rejestracja](../automation-dsc-onboarding.md#re-registering-a-node).

Ten problem może być również spowodowany przez konfigurację serwera proxy nie zezwalającą na dostęp do ***.azure-automation.net**. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci prywatnych](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Rozwiązanie

Wykonaj kroki wymienione poniżej, aby ponownie zarejestrować węzeł DSC, który się nie powiódł.

Najpierw odseić rejestrację węzła, wykonując następujące kroki.

1. Z witryny Azure portal w obszarze**Konta automatyzacji** **domowej** -> —> {Your Automation account} -> **State configuration (DSC)**
2. Kliknij "Węzły" i kliknij węzeł ma problemy.
3. Kliknij przycisk "Wyrejestruj", aby odebrać rejestrację węzła.

Po drugie odinstaluj rozszerzenie DSC z węzła.

1. Z witryny Azure portal w obszarze **Extensions** **Domowa** -> **maszyna wirtualna** -> {Węzeł awarii} - rozszerzenia >
2. Kliknij przycisk "Microsoft.Powershell.DSC".
3. Kliknij przycisk "Odinstaluj", aby odinstalować rozszerzenie DSC programu PowerShell.

Po trzecie, usuń wszystkie uszkodzone lub wygasłe certyfikaty z węzła.

W węźle w przypadku awarii z monitu programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące czynności:

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

Na koniec ponownie zarejestrować węzeł w sieci wada przy użyciu następujących kroków.

1. Z witryny Azure portal w obszarze**Konta automatyzacji** **domowej** -> —> {Your Automation account} -> **State configuration (DSC)**
2. Kliknij "Węzły".
3. Kliknij przycisk "Dodaj".
4. Wybierz węzeł w stanie awarii.
5. Kliknij przycisk "Połącz" i wybierz żądane opcje.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenariusz: Węzeł jest w stanie awarii z błędem "Nie znaleziono"

#### <a name="issue"></a>Problem

Węzeł ma raport o stanie Niepowodzenie i **zawierający** błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy węzeł jest przypisany do nazwy konfiguracji (na przykład ABC) zamiast nazwy konfiguracji węzła (na przykład ABC. WebServer).

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł z "nazwą konfiguracji węzła", a nie "nazwą konfiguracji".
* Konfigurację węzła można przypisać do węzła za pomocą portalu Azure lub polecenia cmdlet programu PowerShell.

  * Aby przypisać konfigurację węzła do węzła za pomocą portalu Azure portal, otwórz stronę **Węzły DSC,** a następnie wybierz węzeł i kliknij przycisk **Przypisz konfigurację węzła.**
  * Aby przypisać konfigurację węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj polecenia cmdlet **Set-AzureRmAutomationDscNode**

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Scenariusz: Podczas kompilowania konfiguracji nie zostały wyprodukowane żadne konfiguracje węzłów (pliki MOF).

#### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Przyczyna

Gdy wyrażenie następujące **node** słowa kluczowego w `$null`konfiguracji DSC ocenia do , następnie nie konfiguracje węzła są produkowane.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, które rozwiązań:

* Upewnij się, że wyrażenie obok **node** słowa kluczowego w definicji konfiguracji nie jest oceny $null.
* Jeśli przekazujesz ConfigurationData podczas kompilowania konfiguracji, upewnij się, że są przekazywanie oczekiwanych wartości, które wymaga konfiguracji z [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC utknie w stanie "w toku"

#### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Przyczyna

Uaktualniłeś wersję WMF i uszkodziłeś w uduśnym umi.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami w artykule [Znane problemy i ograniczenia DSC.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenariusz: nie można użyć poświadczenia w konfiguracji DSC

#### <a name="issue"></a>Problem

Zadanie kompilacji DSC zostało zawieszone z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Przyczyna

Użyto poświadczenia w konfiguracji, ale nie zapewniają odpowiednie **ConfigurationData** ustawić **PSDscAllowPlainTextPassword** true dla każdej konfiguracji węzła.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, aby przekazać w odpowiedniej **ConfigurationData,** aby ustawić **PSDscAllowPlainTextPassword** true dla każdej konfiguracji węzła, który jest wymieniony w konfiguracji. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure](../automation-dsc-compile.md).

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Scenariusz: Dołączanie z rozszerzenia dsc, błąd "Rozszerzenie przetwarzania awarii"

#### <a name="issue"></a>Problem

Podczas dołączania przy użyciu rozszerzenia DSC występuje błąd zawierający błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy węzeł jest przypisany nazwę konfiguracji węzła, który nie istnieje w usłudze.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł o nazwie konfiguracji węzła, która dokładnie odpowiada nazwie w usłudze.
* Można wybrać opcję nie dołączania nazwy konfiguracji węzła, co spowoduje dołączanie węzła, ale nie przypisywanie konfiguracji węzła

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Scenariusz: Rejestrowanie węzła w programie PowerShell zwraca błąd "Wystąpił jeden lub więcej błędów"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła przy użyciu `Register-AzAutomationDSCNode` lub `Register-AzureRMAutomationDSCNode`, pojawia się następujący błąd.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła, który mieszka w oddzielnej subskrypcji niż konto automatyzacji.

#### <a name="resolution"></a>Rozwiązanie

Potraktuj węzeł między subskrypcją tak, jakby działa w oddzielnej chmurze lub lokalnie.

Wykonaj poniższe czynności, aby zarejestrować węzeł.

* Windows — [fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux — [fizyczne/wirtualne maszyny z systemem Linux lokalnie lub w chmurze innej niż Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie — "Nie można zainicjować obsługi administracyjnej"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła pojawia się błąd:

```error
Provisioning has failed
```

#### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem a platformą Azure.

#### <a name="resolution"></a>Rozwiązanie

Określ, czy węzeł znajduje się w prywatnej sieci wirtualnej lub ma inne problemy z łączeniem się z platformą Azure.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami podczas dołączania rozwiązań](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Scenariusz: Zastosowanie konfiguracji w systemie Linux występuje błąd z ogólnym błędem

#### <a name="issue"></a>Problem

Podczas stosowania konfiguracji w systemie Linux występuje błąd zawierający błąd:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Przyczyna

Klienci zidentyfikowali, że `/tmp` jeśli lokalizacja `noexec`jest ustawiona na , bieżąca wersja DSC nie będzie zastosować konfiguracji.

#### <a name="resolution"></a>Rozwiązanie

* Usuń `noexec` tę opcję `/tmp` z lokalizacji.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenariusz: nazwy konfiguracji węzła, które nakładają się na siebie, mogą spowodować nieprawidłowe zwolnienie

#### <a name="issue"></a>Problem

Jeśli skrypt konfiguracji jest używany do generowania konfiguracji wielu węzłów, a niektóre konfiguracje węzłów mają nazwę, która jest podzbiorem innych, problem w usłudze kompilacji może spowodować przypisanie niewłaściwej konfiguracji.  Dzieje się tak tylko wtedy, gdy przy użyciu pojedynczego skryptu do generowania konfiguracji z danymi konfiguracji na węzeł i tylko wtedy, gdy nakładanie się nazwy występuje na początku ciągu.

Przykład, jeśli skrypt konfiguracji jest używany do generowania konfiguracji na podstawie danych węzła przekazywane jako mieszadłowe przy użyciu poleceń cmdlet, a dane węzła zawiera serwer o nazwie "serwer" i "1server".

#### <a name="cause"></a>Przyczyna

Znany problem z usługą kompilacji.

#### <a name="resolution"></a>Rozwiązanie

Najlepszym rozwiązaniem byłoby skompilowanie lokalnie lub w potoku ciągłej integracji/ciągłego wdrażania i przekazywanie plików MOF bezpośrednio do usługi.  Jeśli kompilacja w usłudze jest wymaganiem, następnym najlepszym rozwiązaniem byłoby podzielenie zadań kompilacji, aby nie nakładać się w nazwy.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenariusz: błąd limitu czasu bramy podczas przekazywania konfiguracji DSC

#### <a name="issue"></a>Problem

Podczas przekazywania `GatewayTimeout` konfiguracji DSC pojawia się błąd. 

#### <a name="cause"></a>Przyczyna

Konfiguracje DSC, które zajmują dużo czasu, aby skompilować może spowodować ten błąd.

#### <a name="resolution"></a>Rozwiązanie

Konfiguracje DSC można przyspieszyć, jawnie dołączając `ModuleName` parametr dla `Import-DscResource` wszystkich wywołań. Aby uzyskać więcej informacji, zobacz [Korzystanie z importu dscresource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
