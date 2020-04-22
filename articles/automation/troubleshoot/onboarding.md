---
title: Rozwiązywanie problemów z dołączaniem rozwiązań do zarządzania automatyzacją usługi Azure
description: Dowiedz się, jak rozwiązywać problemy z błędami dołączania rozwiązania.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679232"
---
# <a name="troubleshoot-solution-onboarding"></a>Rozwiązywanie problemów z dołączaniem rozwiązania

Mogą pojawić się błędy podczas dołączania rozwiązania do zarządzania aktualizacjami lub rozwiązania śledzenia zmian i zapasów. W tym artykule opisano różne błędy, które mogą wystąpić i jak je rozwiązać.

## <a name="known-issues"></a>Znane problemy

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenariusz: Zmiana nazwy zarejestrowanego węzła wymaga ponownego zarejestrowania lub zarejestrowania się

#### <a name="issue"></a>Problem

Węzeł jest zarejestrowany w usłudze Azure Automation, a następnie nazwa komputera systemu operacyjnego zostanie zmieniona. Raporty z węzła nadal pojawiają się z oryginalną nazwą.

#### <a name="cause"></a>Przyczyna

Zmiana nazwy zarejestrowanych węzłów nie aktualizuje nazwy węzła w usłudze Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Wyrejestruj węzeł z konfiguracji stanu automatyzacji platformy Azure, a następnie zarejestruj go ponownie. Raporty opublikowane w usłudze przed tym czasem nie będą już dostępne.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenariusz: Ponowne podpisywanie certyfikatów za pośrednictwem serwera proxy https nie jest obsługiwane

#### <a name="issue"></a>Problem

Podczas łączenia się za pośrednictwem rozwiązania proxy, które kończy ruch HTTPS, a następnie ponownie szyfruje ruch przy użyciu nowego certyfikatu, usługa nie zezwala na połączenie.

#### <a name="cause"></a>Przyczyna

Usługa Azure Automation nie obsługuje ponownego podpisywania certyfikatów używanych do szyfrowania ruchu.

#### <a name="resolution"></a>Rozwiązanie

Obecnie nie ma obejścia tego problemu.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenariusz: Dołączanie kończy się niepowodzeniem z komunikatem — nie można włączyć rozwiązania

#### <a name="issue"></a>Problem

Podczas próby dołączania maszyny wirtualnej do rozwiązania jest odbierany jeden z następujących komunikatów:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przez nieprawidłowe lub brakujące uprawnienia na maszynie Wirtualnej lub obszarze roboczym lub dla użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie [uprawnienia potrzebne do komputerów pokładowych,](../automation-role-based-access-control.md#onboarding-permissions) a następnie spróbuj ponownie przywdzielić rozwiązanie. Jeśli zostanie wyświetlony `The solution cannot be enabled on this VM because the permission to read the workspace is missing`błąd, upewnij `Microsoft.OperationalInsights/workspaces/read` się, że masz uprawnienia, aby móc znaleźć, czy maszyna wirtualna jest przywdziewiona do obszaru roboczego.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenariusz: Dołączanie kończy się niepowodzeniem z komunikatem: Nie można skonfigurować konta automatyzacji do rejestrowania diagnostycznego

#### <a name="issue"></a>Problem

Podczas próby dołączania maszyny wirtualnej do rozwiązania zostanie wyświetlony następujący komunikat:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany, jeśli warstwa cenowa nie jest zgodna z modelem rozliczeń subskrypcji. Zobacz [Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Rozwiązanie

Utwórz obszar roboczy usługi Log Analytics ręcznie i powtórz proces dołączania, aby wybrać utworzony obszar roboczy.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenariusz: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że zapisane zapytanie grupy komputera wyszukiwania używane do kierowania rozwiązania nie jest poprawnie sformatowany. 

#### <a name="cause"></a>Przyczyna

Być może kwerenda została zmieniona lub system mógł ją zmienić.

#### <a name="resolution"></a>Rozwiązanie

Można usunąć kwerendę dla rozwiązania, a następnie ponownie za pomocą rozwiązania, który odtwarza kwerendę. Kwerendę można znaleźć w obszarze roboczym w obszarze **Zapisane wyszukiwania**. Nazwa kwerendy to **MicrosoftDefaultComputerGroup**, a kategoria kwerendy to nazwa skojarzonego rozwiązania. Jeśli włączono wiele rozwiązań, kwerenda **MicrosoftDefaultComputerGroup** jest wyświetlana wiele razy w obszarze **Zapisane wyszukiwania**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenariusz: PolicyViolation

#### <a name="issue"></a>Problem

Ten kod błędu wskazuje, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jednej zasady.

#### <a name="cause"></a>Przyczyna 

Zasady blokuje zakończenie operacji.

#### <a name="resolution"></a>Rozwiązanie

Aby pomyślnie wdrożyć rozwiązanie, należy rozważyć zmianę wskazanych zasad. Ponieważ istnieje wiele różnych typów zasad, które można zdefiniować, wymagane zmiany zależą od zasad, które zostały naruszone. Jeśli na przykład zasada jest zdefiniowana w grupie zasobów, która odmawia uprawnień do zmiany zawartości niektórych zawartych zasobów, można wybrać jedną z następujących poprawek:

* Całkowicie usuń zasady.
* Spróbuj przykłować rozwiązanie do innej grupy zasobów.
* Ponownie kierować zasady do określonego zasobu, na przykład konto automatyzacji.
* Popraw zestaw zasobów, które zasady są skonfigurowane do odmowy.

Sprawdź powiadomienia w prawym górnym rogu witryny Azure portal lub przejdź do grupy zasobów, która zawiera konto automatyzacji i wybierz **wdrożenia** w obszarze **Ustawienia,** aby wyświetlić wdrożenie nie powiodło się. Aby dowiedzieć się więcej o zasadach platformy Azure, zobacz [Omówienie zasad platformy Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenariusz: błędy podczas próby odłączenia obszaru roboczego

#### <a name="issue"></a>Problem

Podczas próby odłączenia obszaru roboczego pojawia się następujący błąd:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy nadal masz aktywne rozwiązania w obszarze roboczym usługi Log Analytics, które zależą od konta automatyzacji i obszaru roboczego usługi Log Analytics są połączone.

### <a name="resolution"></a>Rozwiązanie

Usuń następujące rozwiązania z obszaru roboczego, jeśli ich używasz:

* Zarządzanie aktualizacjami
* Śledzenie zmian i spis
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

Po usunięciu rozwiązań można odłączyć obszar roboczy. Ważne jest, aby wyczyścić istniejące artefakty z tych rozwiązań z obszaru roboczego i konta automatyzacji 

* W przypadku zarządzania aktualizacjami usuń wdrożenia aktualizacji (harmonogramy) z konta automatyzacji.
* W przypadku maszyn wirtualnych Start/Stop poza godzinami pracy usuń wszelkie blokady składników rozwiązania na koncie automatyzacji w obszarze**Blokady** **ustawień** > . Zobacz [Usuwanie maszyn wirtualnych start/stop podczas pracy poza godzinami pracy.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Usługa Log Analytics dla błędów rozszerzeń systemu Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Instalacja agenta usługi Log Analytics dla rozszerzenia systemu Windows może zakończyć się niepowodzeniem z różnych powodów. W poniższej sekcji opisano problemy z dołączaniem, które mogą powodować błędy podczas wdrażania agenta usługi Log Analytics dla rozszerzenia systemu Windows.

>[!NOTE]
>Agent analizy dzienników dla systemu Windows to nazwa używana obecnie w usłudze Azure Automation dla programu Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenariusz: Wystąpił wyjątek podczas żądania WebClient

Usługa Log Analytics dla systemu Windows rozszerzenie na maszynie Wirtualnej nie może komunikować się z zasobów zewnętrznych i wdrożenie nie powiedzie się.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady zwracanych komunikatów o błędach:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Przyczyna

Niektóre potencjalne przyczyny tego błędu to:

* Serwer proxy skonfigurowany w maszynie wirtualnej zezwala tylko na określone porty.
* Ustawienie zapory zablokowało dostęp do wymaganych portów i adresów.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie porty i adresy otwarte dla komunikacji. Aby uzyskać listę portów i adresów, zobacz [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenariusz: Instalacja nie powiodła się z powodu problemów ze środowiskiem przejściowym

Instalacja rozszerzenia Log Analytics dla systemu Windows nie powiodła się podczas wdrażania z powodu innej instalacji lub akcji blokującej instalację

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikatów o błędach:

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

* Trwa kolejna instalacja.
* System jest wyzwalany do ponownego uruchomienia podczas wdrażania szablonu.

#### <a name="resolution"></a>Rozwiązanie

Ten błąd ma charakter przejściowy. Ponów próbę wdrożenia, aby zainstalować rozszerzenie.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenariusz: Limit czasu instalacji

Instalacja rozszerzenia usługi Log Analytics dla systemu Windows nie została ukończona z powodu przesulenia limitu czasu.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykład komunikatu o błędzie, który może zostać zwrócony:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Przyczyna

Ten typ błędu występuje, ponieważ maszyna wirtualna jest pod dużym obciążeniem podczas instalacji.

### <a name="resolution"></a>Rozwiązanie

Spróbuj zainstalować agenta analizy dzienników dla rozszerzenia systemu Windows, gdy maszyna wirtualna jest pod mniejszym obciążeniem.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
