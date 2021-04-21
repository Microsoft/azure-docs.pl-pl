---
title: Rozwiązywanie Azure Automation State Configuration problemów
description: W tym artykule opisano sposób rozwiązywania Azure Automation State Configuration problemów.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91c4780981851b62027fecf18da2c3b78625f272
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831208"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Rozwiązywanie Azure Automation State Configuration problemów

Ten artykuł zawiera informacje na temat rozwiązywania problemów, które występują podczas kompilowania lub wdrażania konfiguracji w Azure Automation State Configuration. Aby uzyskać ogólne informacje na temat State Configuration, zobacz [Azure Automation State Configuration omówienie .](../automation-dsc-overview.md)

## <a name="diagnose-an-issue"></a>Diagnozowanie problemu

Po otrzymaniu błędu kompilacji lub wdrożenia dla konfiguracji poniżej znajdziesz kilka kroków, które pomogą Ci zdiagnozować problem.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja została pomyślnie skompilowana na komputerze lokalnym

Azure Automation State Configuration jest zbudowany na programie PowerShell Desired State Configuration (DSC). Dokumentację języka DSC i składni można znaleźć w dokumentacji [dsc programu PowerShell.](/powershell/scripting/overview)

Kompilowanie konfiguracji DSC na komputerze lokalnym umożliwia odnajdywanie i rozwiązywanie typowych błędów, takich jak:

   - Brak modułów.
   - Błędy składniowe.
   - Błędy logiki.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetlanie dzienników DSC w węźle

Jeśli konfiguracja zostanie skompilowana pomyślnie, ale nie powiedzie się po zastosowaniu do węzła, szczegółowe informacje można znaleźć w dziennikach DSC. Aby uzyskać informacje o tym, gdzie można znaleźć te dzienniki, zobacz Gdzie znajdują się dzienniki zdarzeń [DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizowaniu szczegółowych informacji z dzienników DSC. Jeśli skontaktujemy się z pomocą techniczną, będą oni wymagać tych dzienników do zdiagnozowania problemu.

Moduł można zainstalować na komputerze lokalnym, zgodnie z instrukcjami w temacie `xDscDiagnostics` [Instalowanie modułu stabilnej wersji.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Aby zainstalować moduł `xDscDiagnostics` na maszynie platformy Azure, użyj narzędzia [Invoke-AzVMRunCommand.](/powershell/module/az.compute/invoke-azvmruncommand) Możesz również użyć  opcji uruchom polecenie w instrukcje Azure Portal, korzystając z procedury Uruchamianie skryptów programu [PowerShell](../../virtual-machines/windows/run-command.md)na maszynie wirtualnej z systemem Windows przy użyciu Uruchamianie polecenia .

Aby uzyskać informacje na temat **używania funkcji xDscDiagnostics,** zobacz [Using xDscDiagnostics to analyze DSC logs (Używanie diagnostyki xDscDiagnostics do analizowania dzienników DSC).](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Zobacz również [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły

Konfiguracja DSC zależy od modułów zainstalowanych w węźle. W przypadku korzystania z Azure Automation State Configuration zaimportuj wszystkie wymagane moduły na konto usługi Automation, zgodnie z instrukcjami w te [tematu Import Modules (Importowanie modułów).](../shared-resources/modules.md#import-modules) Konfiguracje mogą również być zależne od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenariusz: Nie można usunąć konfiguracji ze znakami specjalnymi z portalu

### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu zostanie wyświetlony następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowym problemem, który ma zostać rozwiązany.

### <a name="resolution"></a>Rozwiązanie

Użyj polecenia cmdlet [Remove-AzAutomationDscConfiguration,](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) aby usunąć konfigurację.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenariusz: Nie można zarejestrować agenta DSC

### <a name="issue"></a>Problem

W [przypadku polecenia Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lub innego polecenia cmdlet DSC zostanie wyświetlony błąd:

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

Ten błąd jest zwykle spowodowany przez zaporę, maszynę, która znajduje się za serwerem proxy, lub inne błędy sieci.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy maszyna ma dostęp do odpowiednich punktów końcowych dla konfiguracji DSC, i spróbuj ponownie. Aby uzyskać listę wymaganych portów i adresów, zobacz [Network planning (Planowanie sieci).](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenariusz: raporty stanu zwracają kod odpowiedzi Bez autoryzacji

### <a name="issue"></a>Problem

Po zarejestrowaniu węzła w Azure Automation State Configuration zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zły lub wygasły certyfikat. Zobacz [Ponowne rejestrowanie węzła](../automation-dsc-onboarding.md#re-register-a-node).

Ten problem może być również spowodowany tym, że konfiguracja serwera proxy nie zezwala na dostęp do pliku ***.azure-automation.net**. Aby uzyskać więcej informacji, [zobacz Configuration of private networks (Konfiguracja sieci prywatnych).](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Rozwiązanie

Aby ponownie zarejestrować węzeł DSC, który się nie popełni, należy wykonać poniższe kroki.

#### <a name="step-1-unregister-the-node"></a>Krok 1. Wyrejestruj węzeł

1. W Azure Portal przejdź do strony **Konta** usługi Home Automation > (Twoje konto usługi  >   Automation) > State **Configuration (DSC).**
1. Wybierz **pozycję Węzły** i wybierz węzeł, który ma problemy.
1. Wybierz **pozycję Wyrejestruj,** aby wyrejestrować węzeł.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Krok 2. Odinstalowywanie rozszerzenia DSC z węzła

1. W Azure Portal przejdź do **strony** Głównej maszyny  >  **wirtualnej** > (węzeł awarii) > **rozszerzenia**.
1. Wybierz **pozycję Microsoft.Powershell.DSC**, rozszerzenie DSC programu PowerShell.
1. Wybierz pozycję Odinstaluj, aby odinstalować rozszerzenie. 

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Krok 3. Usuwanie wszystkich złych lub wygasłych certyfikatów z węzła

W węźle, który nie działa, w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenia:

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

#### <a name="step-4-reregister-the-failing-node"></a>Krok 4. Ponowne rejestrowanie węzła, który się nie popełni

1. W Azure Portal przejdź do strony **Konta** usługi Home Automation > (Twoje konto usługi  >   Automation) > State **Configuration (DSC).**
1. Wybierz pozycję **Węzły.**
1. Wybierz pozycję **Dodaj**.
1. Wybierz węzeł, który się nie popełni.
1. Wybierz **pozycję** Połącz i wybierz odpowiednie opcje.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenariusz: węzeł jest w stanie niepowodzeniem z błędem "Nie znaleziono"

### <a name="issue"></a>Problem

Węzeł ma raport ze stanem Niepowodzenie i zawiera błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje zwykle, gdy węzeł jest przypisany do nazwy konfiguracji, na przykład **ABC,** zamiast nazwy konfiguracji węzła (pliku MOF), na przykład **ABC. Serwer sieci Web**.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł z nazwą konfiguracji węzła, a nie nazwą konfiguracji.
* Konfigurację węzła można przypisać do węzła przy użyciu Azure Portal lub polecenia cmdlet programu PowerShell.

  * W Azure Portal przejdź do strony **Konta** usługi Home Automation > (Twoje konto usługi  >   Automation) > State **Configuration (DSC).** Następnie wybierz węzeł i wybierz pozycję **Przypisz konfigurację węzła**.
  * Użyj polecenia cmdlet [Set-AzAutomationDscNode.](/powershell/module/Az.Automation/Set-AzAutomationDscNode)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenariusz: podczas kompilowania konfiguracji nie zostały opracowane żadne konfiguracje węzłów (pliki MOF)

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Przyczyna

Gdy wyrażenie następujące po swonie kluczowym w konfiguracji DSC ma `Node` wartość , `$null` konfiguracje węzłów nie są konfiguracyjne.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Upewnij się, że wyrażenie obok słowa kluczowego w definicji konfiguracji nie ocenia `Node` wartości Null.
* W przypadku przekazywania danych [ConfigurationData](../automation-dsc-compile.md) podczas kompilowania konfiguracji upewnij się, że dane konfiguracji są przekazywania wartości, których oczekuje konfiguracja.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC zostaje zablokowany w stanie W toku

### <a name="issue"></a>Problem

Agent DSC wyprowadza dane wyjściowe:

```error
No instance found with given property values
```

### <a name="cause"></a>Przyczyna

Uaktualniono wersję programu Windows Management Framework (WMF) i uszkodzono Instrumentacja zarządzania Windows (WMI).

### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami [w tesłudze DSC known issues and limitations](/powershell/scripting/wmf/known-issues/known-issues-dsc)(Znane problemy i ograniczenia dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenariusz: Nie można użyć poświadczeń w konfiguracji DSC

### <a name="issue"></a>Problem

Zadanie kompilacji DSC zostało zawieszone z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Przyczyna

Poświadczenie było używane w konfiguracji, ale nie zapewniało prawidłowego ustawienia wartości `ConfigurationData` true dla każdej konfiguracji `PSDscAllowPlainTextPassword` węzła.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że dla każdej konfiguracji węzła, która jest wymieniona w konfiguracji, należy przekazać wartość właściwą, aby ustawić wartość `ConfigurationData` `PSDscAllowPlainTextPassword` true. Zobacz [Kompilowanie konfiguracji DSC w programie Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenariusz: błąd "Rozszerzenie przetwarzania błędów" podczas włączania maszyny z rozszerzenia DSC

### <a name="issue"></a>Problem

Po włączeniu maszyny przy użyciu rozszerzenia DSC wystąpi błąd, który zawiera błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy do węzła jest przypisana nazwa konfiguracji węzła, która nie istnieje w usłudze.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypisujesz węzeł o nazwie, która dokładnie odpowiada nazwie w usłudze.
* Można nie uwzględniać nazwy konfiguracji węzła, co powoduje włączenie węzła, ale nie przypisanie konfiguracji węzła.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenariusz: błąd "Wystąpił co najmniej jeden błąd" podczas rejestrowania węzła przy użyciu programu PowerShell

### <a name="issue"></a>Problem

Podczas rejestrowania węzła przy użyciu [narzędzia Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) lub [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)występuje następujący błąd:

```error
One or more errors occurred.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła w oddzielnej subskrypcji niż używana przez konto usługi Automation.

### <a name="resolution"></a>Rozwiązanie

Traktuj węzeł między subskrypcjami tak, jakby był zdefiniowany dla oddzielnej chmury lub dla środowisk lokalnych. Zarejestruj węzeł przy użyciu jednej z tych opcji włączania maszyn:

* Windows: maszyny fizyczne/wirtualne z systemem Windows lokalnie lub [w chmurze innej niż Azure/AWS.](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)
* Linux: maszyny fizyczne/wirtualne z systemem Linux w środowisku lokalnym lub [w chmurze innej niż platforma Azure.](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie "Aprowizowanie nie powiodło się"

### <a name="issue"></a>Problem

Po zarejestrowaniu węzła zostanie wyświetlony błąd:

```error
Provisioning has failed
```

### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem a platformą Azure.

### <a name="resolution"></a>Rozwiązanie

Ustal, czy węzeł znajduje się w wirtualnej sieci prywatnej (VPN), czy ma inne problemy z nawiązywanie połączenia z platformą Azure. Zobacz [Rozwiązywanie problemów z wdrażaniem funkcji.](onboarding.md)

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenariusz: Błąd z ogólnym błędem podczas stosowania konfiguracji w systemie Linux

### <a name="issue"></a>Problem

Po zastosowaniu konfiguracji w systemie Linux wystąpi błąd, który zawiera błąd:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Przyczyna

Jeśli **/tmp** lokalizacja jest ustawiona `noexec` na , bieżąca wersja DSC nie może zastosować konfiguracji.

### <a name="resolution"></a>Rozwiązanie

Usuń `noexec` opcję z **lokalizacji /tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenariusz: Nakładające się nazwy konfiguracji węzłów mogą powodować złe wydanie

### <a name="issue"></a>Problem

Jeśli używasz pojedynczego skryptu konfiguracji do generowania wielu konfiguracji węzłów, a niektóre nazwy konfiguracji węzła są podzbiorem innych nazw, usługa kompilacji może przypisać nieprawidłową konfigurację. Ten problem występuje tylko wtedy, gdy używasz pojedynczego skryptu do generowania konfiguracji z danymi konfiguracji na węzeł i tylko wtedy, gdy nazwa nakłada się na początku ciągu. Przykładem jest pojedynczy skrypt konfiguracji używany do generowania konfiguracji na podstawie danych węzła przekazywanych jako tablica skrótów przy użyciu polecenia cmdlet, a dane węzła obejmują serwery o nazwach **server** i **1server.**

### <a name="cause"></a>Przyczyna

Jest to znany problem z usługą kompilacji.

### <a name="resolution"></a>Rozwiązanie

Najlepszym obejściem jest skompilowanie lokalnie lub w potoku CI/CD i przekazanie plików MOF konfiguracji węzła bezpośrednio do usługi. Jeśli kompilacja w usłudze jest wymagana, następnym najlepszym obejściem jest podzielenie zadań kompilacji tak, aby nazwy nie nakładały się na siebie.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenariusz: błąd limitu czasu bramy podczas przekazywania konfiguracji DSC

#### <a name="issue"></a>Problem

Podczas `GatewayTimeout` przekazywania konfiguracji DSC występuje błąd. 

### <a name="cause"></a>Przyczyna

Konfiguracje DSC, których kompilacja trwa długo, mogą spowodować ten błąd.

### <a name="resolution"></a>Rozwiązanie

Konfiguracje DSC można przyspieszyć, jawnie uwzględniając parametr dla dowolnych wywołań `ModuleName` [Import-DSCResource.](/powershell/scripting/dsc/configurations/import-dscresource)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj swojego problemu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów z platformy Azure za [pośrednictwem forów platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z , oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta. pomoc techniczna platformy Azure łączy społeczność platformy Azure z odpowiedziami, pomocą techniczną i ekspertami.
* Zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
