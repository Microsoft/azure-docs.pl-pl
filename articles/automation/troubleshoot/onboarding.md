---
title: Rozwiązywanie problemów z wdrażaniem funkcji Azure Automation
description: W tym artykule opisano sposób rozwiązywania problemów, które powstają podczas wdrażania Azure Automation funkcji.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651123"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Rozwiązywanie problemów z wdrażaniem funkcji

Podczas wdrażania funkcji Azure Automation Update Management lub funkcji Change Tracking i spisu na maszynach wirtualnych mogą pojawić się komunikaty o błędach. W tym artykule opisano błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="known-issues"></a>Znane problemy

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenariusz: zmiana nazwy zarejestrowanego węzła wymaga wyrejestrowania lub ponownego zarejestrowania

#### <a name="issue"></a>Problem

Węzeł jest zarejestrowany w Azure Automation, a następnie zmieniono nazwę komputera systemu operacyjnego. Raporty z węzła nadal pojawiają się z oryginalną nazwą.

#### <a name="cause"></a>Przyczyna

Zmiana nazwy zarejestrowanych węzłów nie aktualizuje nazwy węzła w Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Wyrejestruj węzeł z konfiguracji stanu Azure Automation, a następnie zarejestruj go ponownie. Raporty publikowane w usłudze przed upływem tego czasu nie będą już dostępne.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Scenariusz: certyfikaty ponownego podpisywania za pośrednictwem serwera proxy HTTPS nie są obsługiwane

#### <a name="issue"></a>Problem

Po nawiązaniu połączenia za pośrednictwem serwera proxy, który kończy ruch HTTPS, a następnie ponownie szyfruje ruch przy użyciu nowego certyfikatu, usługa nie zezwala na połączenie.

#### <a name="cause"></a>Przyczyna

Azure Automation nie obsługuje certyfikatów ponownego podpisywania używanych do szyfrowania ruchu.

#### <a name="resolution"></a>Rozwiązanie

Obecnie nie istnieje obejście tego problemu.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenariusz: Wdrażanie funkcji kończy się niepowodzeniem z komunikatem "nie można włączyć rozwiązania"

#### <a name="issue"></a>Problem

Podczas próby włączenia funkcji na maszynie wirtualnej zostanie wyświetlony jeden z następujących komunikatów:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przez nieprawidłowe lub brakujące uprawnienia do maszyny wirtualnej lub obszaru roboczego lub dla użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz poprawne [uprawnienia wdrażania funkcji](../automation-role-based-access-control.md#feature-setup-permissions), a następnie spróbuj ponownie wdrożyć tę funkcję. Jeśli zostanie wyświetlony komunikat o błędzie `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , zobacz następujące [informacje dotyczące rozwiązywania problemów](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenariusz: Wdrażanie funkcji kończy się niepowodzeniem z komunikatem "nie można skonfigurować konta automatyzacji dla rejestrowania diagnostycznego"

#### <a name="issue"></a>Problem

Podczas próby włączenia funkcji na maszynie wirtualnej zostanie wyświetlony następujący komunikat:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany tym, że warstwa cenowa nie jest zgodna z modelem rozliczania subskrypcji. Aby uzyskać więcej informacji, zobacz [monitorowanie użycia i szacowane koszty w Azure monitor](../../azure-monitor//usage-estimated-costs.md).

#### <a name="resolution"></a>Rozwiązanie

Utwórz ręcznie obszar roboczy Log Analytics i powtórz proces wdrażania funkcji w celu wybrania utworzonego obszaru roboczego.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenariusz: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że zapisane zapytanie grupy komputerów wyszukiwania używane jako docelowe funkcji nie jest prawidłowo sformatowane. 

#### <a name="cause"></a>Przyczyna

Być może zmieniono zapytanie lub system mógł go zmienić.

#### <a name="resolution"></a>Rozwiązanie

Możesz usunąć zapytanie dla tej funkcji, a następnie ponownie włączyć tę funkcję, co spowoduje ponowne utworzenie zapytania. Zapytanie można znaleźć w obszarze roboczym w obszarze **zapisane wyszukiwania**. Nazwa zapytania to **MicrosoftDefaultComputerGroup**, a kategoria zapytania to nazwa skojarzonej funkcji. Jeśli włączono wiele funkcji, zapytanie **MicrosoftDefaultComputerGroup** wyświetla wiele razy w obszarze **zapisane wyszukiwania**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenariusz: PolicyViolation

#### <a name="issue"></a>Problem

Ten kod błędu wskazuje, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jednej zasady.

#### <a name="cause"></a>Przyczyna 

Zasady blokują wykonywanie operacji.

#### <a name="resolution"></a>Rozwiązanie

Aby pomyślnie wdrożyć tę funkcję, należy rozważyć zmianę wskazanych zasad. Ponieważ istnieje wiele różnych typów zasad, które można zdefiniować, wymagane zmiany są zależne od zasad, które zostały naruszone. Na przykład jeśli zasady są zdefiniowane w grupie zasobów, która nie zezwala na zmianę zawartości niektórych zawartych zasobów, można wybrać jedną z następujących poprawek:

* Usuń zasady całkowicie.
* Spróbuj włączyć funkcję dla innej grupy zasobów.
* Przekieruj zasady do określonego zasobu, na przykład konto usługi Automation.
* Popraw zestaw zasobów, dla których zasady są skonfigurowane do odrzucania.

Sprawdź powiadomienia w prawym górnym rogu Azure Portal lub przejdź do grupy zasobów zawierającej konto usługi Automation, a następnie wybierz pozycję **wdrożenia** w obszarze **Ustawienia** , aby wyświetlić niepowodzenie wdrożenia. Aby dowiedzieć się więcej na temat Azure Policy, zobacz [omówienie Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenariusz: Błędy podczas próby odłączenia obszaru roboczego

#### <a name="issue"></a>Problem

Podczas próby odłączenia obszaru roboczego pojawia się następujący komunikat o błędzie:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy nadal masz aktywne funkcje w obszarze roboczym Log Analytics, które zależą od konta usługi Automation i połączonego obszaru roboczego Log Analytics.

### <a name="resolution"></a>Rozwiązanie

Usuń zasoby dla następujących funkcji z obszaru roboczego, jeśli są używane:

* Zarządzanie aktualizacjami
* Śledzenie zmian i spis
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

Po usunięciu zasobów funkcji można odłączyć obszar roboczy. Ważne jest, aby wyczyścić wszystkie istniejące artefakty z tych funkcji z obszaru roboczego i konta usługi Automation:

* W przypadku Update Management Usuń **wdrożenia aktualizacji (harmonogramy)** z konta usługi Automation.
* W przypadku Start/Stop VMS during off-hours Usuń wszystkie blokady składników funkcji na koncie usługi Automation w obszarze **Ustawienia**  >  **blokady**. Aby uzyskać więcej informacji, zobacz [usuwanie funkcji](../automation-solution-vm-management-remove.md).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics błędów rozszerzenia systemu Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Instalacja agenta Log Analytics dla rozszerzenia systemu Windows może zakończyć się niepowodzeniem z różnych powodów. W poniższej sekcji opisano problemy dotyczące wdrażania funkcji, które mogą powodować błędy podczas wdrażania rozszerzenia Agent Log Analytics dla systemu Windows.

>[!NOTE]
>Agent Log Analytics dla systemu Windows to nazwa używana obecnie w Azure Automation dla Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenariusz: Wystąpił wyjątek podczas żądania WebClient

Rozszerzenie Log Analytics dla systemu Windows na maszynie wirtualnej nie może komunikować się z zasobami zewnętrznymi, a wdrożenie kończy się niepowodzeniem.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikatów o błędach, które są zwracane:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Przyczyna

Niektóre potencjalne przyczyny tego błędu to:

* Serwer proxy skonfigurowany na maszynie wirtualnej zezwala tylko na określone porty.
* Ustawienie zapory blokuje dostęp do wymaganych portów i adresów.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie porty i adresy otwarte do komunikacji. Lista portów i adresów znajduje się w temacie [Planning The Network](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenariusz: instalacja nie powiodła się z powodu przejściowych problemów ze środowiskiem

Instalacja rozszerzenia Log Analytics for Windows nie powiodła się podczas wdrażania z powodu innej instalacji lub akcji blokującej instalację.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikatów o błędach, które mogą zostać zwrócone:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Przyczyna

Niektóre potencjalne przyczyny tego błędu to:

* Inna instalacja jest w toku.
* System jest wyzwalany w celu ponownego uruchomienia podczas wdrażania szablonu.

#### <a name="resolution"></a>Rozwiązanie

Ten błąd jest przejściowy. Ponów wdrożenie, aby zainstalować rozszerzenie.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenariusz: limit czasu instalacji

Instalacja rozszerzenia Log Analytics for Windows nie została ukończona z powodu przekroczenia limitu czasu.

#### <a name="issue"></a>Problem

Poniżej znajduje się przykładowy komunikat o błędzie, który może zostać zwrócony:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Przyczyna

Ten typ błędu występuje, ponieważ maszyna wirtualna jest w dużym obciążeniu podczas instalacji.

### <a name="resolution"></a>Rozwiązanie

Spróbuj zainstalować rozszerzenie Agent Log Analytics dla systemu Windows, gdy maszyna wirtualna jest w niższym obciążeniu.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.