---
title: Rozwiązywanie problemów z dołączaniem rozwiązań do zarządzania automatyzacją usługi Azure
description: Dowiedz się, jak rozwiązywać problemy z błędami dołączania za pomocą rozwiązań Zarządzanie aktualizacjami, Śledzenie zmian i Zapasy
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536968"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Rozwiązywanie problemów z błędami podczas dołączania do zarządzania aktualizacjami, śledzenia zmian i zapasów

Podczas dołączania rozwiązań, takich jak zarządzanie aktualizacjami lub śledzenie zmian i spis, mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i jak je rozwiązać.

## <a name="known-issues"></a>Znane problemy

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scenariusz: Zmiana nazwy zarejestrowanego węzła wymaga ponownego wyrejestrowania / zarejestrowania

#### <a name="issue"></a>Problem

Węzeł jest zarejestrowany w usłudze Azure Automation, a następnie nazwa komputera systemu operacyjnego zostanie zmieniona.  Raporty z węzła nadal pojawiają się z oryginalną nazwą.

#### <a name="cause"></a>Przyczyna

Zmiana nazwy zarejestrowanych węzłów nie aktualizuje nazwy węzła w usłudze Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Wyrejestruj węzeł z konfiguracji stanu automatyzacji platformy Azure, a następnie zarejestruj go ponownie.  Raporty opublikowane w usłudze przed tym czasem nie będą już dostępne.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenariusz: Ponowne podpisywanie certyfikatów za pośrednictwem serwera proxy https nie jest obsługiwane

#### <a name="issue"></a>Problem

Klienci zgłaszali, że podczas łączenia się za pośrednictwem rozwiązania proxy, które kończy ruch https, a następnie ponownie szyfruje ruch przy użyciu nowego certyfikatu, usługa nie zezwala na połączenie.

#### <a name="cause"></a>Przyczyna

Usługa Azure Automation nie obsługuje ponownego podpisywania certyfikatów używanych do szyfrowania ruchu.

#### <a name="resolution"></a>Rozwiązanie

Nie jest dostępne żadne obejście tego problemu.

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

Ten błąd jest spowodowany przez nieprawidłowe lub brakujące uprawnienia na maszynie wirtualnej, obszarze roboczym lub dla użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz poprawne uprawnienia do dołączania maszyny wirtualnej. Przejrzyj [uprawnienia potrzebne do komputerów pokładowych](../automation-role-based-access-control.md#onboarding-permissions) i spróbuj ponownie przydzielić rozwiązanie. Jeśli zostanie wyświetlony `The solution cannot be enabled on this VM because the permission to read the workspace is missing`błąd, upewnij `Microsoft.OperationalInsights/workspaces/read` się, że masz uprawnienia, aby móc znaleźć, czy maszyna wirtualna jest przywdziewiona do obszaru roboczego.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenariusz: Dołączanie kończy się niepowodzeniem z komunikatem — nie można skonfigurować konta automatyzacji do rejestrowania diagnostycznego

#### <a name="issue"></a>Problem

Podczas próby wprowadzenia maszyny wirtualnej do rozwiązania zostanie wyświetlony następujący komunikat:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany, jeśli warstwa cenowa nie jest zgodna z modelem rozliczeń subskrypcji. Aby uzyskać więcej informacji, zobacz [Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Rozwiązanie

Utwórz obszar roboczy usługi Log Analytics ręcznie i powtórz proces dołączania, aby wybrać utworzony obszar roboczy.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenariusz: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że zapisane zapytanie grupy komputera wyszukiwania używane do kierowania rozwiązania nie zostało poprawnie sformatowane. 

#### <a name="cause"></a>Przyczyna

Być może kwerenda została zmieniona lub została zmieniona przez system.

#### <a name="resolution"></a>Rozwiązanie

Można usunąć kwerendę dla tego rozwiązania i ponownie zaokrągć rozwiązanie, które odtwarza kwerendę. Kwerendę można znaleźć w obszarze roboczym w obszarze **Zapisane wyszukiwania**. Nazwa kwerendy to **MicrosoftDefaultComputerGroup**, a kategoria kwerendy to nazwa rozwiązania skojarzonego z tą kwerendą. Jeśli jest włączonych wiele rozwiązań, **grupa MicrosoftDefaultComputerGroup** jest wyświetlana wiele razy w obszarze **Zapisane wyszukiwania**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenariusz: PolicyViolation

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jednej zasady.

#### <a name="cause"></a>Przyczyna 

Zasady są w miejscu, który blokuje operację z ukończenia.

#### <a name="resolution"></a>Rozwiązanie

Aby pomyślnie wdrożyć rozwiązanie, należy rozważyć zmianę wskazanych zasad. Ponieważ istnieje wiele różnych typów zasad, które można zdefiniować, określone zmiany wymagane zależą od zasad, które zostały naruszone. Na przykład jeśli zasada została zdefiniowana w grupie zasobów, która odmówiła uprawnień do zmiany zawartości niektórych typów zasobów w tej grupie zasobów, można na przykład wykonać dowolną z następujących czynności:

* Całkowicie usuń zasady.
* Spróbuj przydzielić do innej grupy zasobów.
* Zmiana polityki, na przykład poprzez:
  * Ponowne kierowanie zasad na określony zasób (na przykład na określone konto automatyzacji).
  * Zmiana zestawu zasobów, które zasady zostały skonfigurowane do odmowy.

Sprawdź powiadomienia w prawym górnym rogu witryny Azure portal lub przejdź do grupy zasobów, która zawiera konto automatyzacji i wybierz **wdrożenia** w obszarze **Ustawienia,** aby wyświetlić wdrożenie nie powiodło się. Aby dowiedzieć się więcej o usłudze Azure Policy, odwiedź stronę: [Omówienie zasad platformy Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenariusz: błędy podczas próby odłączenia obszaru roboczego

#### <a name="issue"></a>Problem

Podczas próby odłączenia obszaru roboczego pojawia się następujący błąd:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy nadal masz aktywne rozwiązania w obszarze roboczym usługi Log Analytics, które zależą od konta automatyzacji i obszaru roboczego usługi Log Analytics są połączone.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć następujące rozwiązania z obszaru roboczego, jeśli ich używasz:

* Zarządzanie aktualizacjami
* Śledzenie zmian
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

Po usunięciu rozwiązań można odłączyć obszar roboczy. Ważne jest, aby oczyścić wszelkie istniejące artefakty z tych rozwiązań z obszaru roboczego i konta automatyzacji, jak również.  

* Zarządzanie aktualizacjami
  * Usuwanie wdrożeń aktualizacji (harmonogramów) z konta automatyzacji
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy
  * Usuń wszelkie blokady składników rozwiązania na koncie automatyzacji w obszarze**Blokady** **ustawień** > .
  * Aby uzyskać dodatkowe kroki w celu usunięcia maszyn wirtualnych Start/Stop podczas rozwiązania poza godzinami pracy, [zobacz, Usuń maszynę wirtualną Start/Stop podczas rozwiązania poza godzinami pracy.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Błędy rozszerzenia MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Podczas wdrażania rozwiązania są wdrażane różne powiązane zasoby. Jednym z tych zasobów jest rozszerzenie agenta monitorowania firmy Microsoft lub agent analizy dzienników dla systemu Linux. Są to rozszerzenia maszyny wirtualnej zainstalowane przez agenta gościa maszyny wirtualnej, który jest odpowiedzialny za komunikowanie się ze skonfigurowanym obszarem roboczym usługi Log Analytics w celu późniejszej koordynacji pobierania plików binarnych i innych plików, od których zależy rozwiązanie, od którego jest onboarding, gdy rozpocznie się wykonywanie.
Zazwyczaj najpierw dowiedzieć się o MMA lub agenta usługi Log Analytics dla awarii instalacji systemu Linux z powiadomienia pojawiającego się w Centrum powiadomień. Kliknięcie tego powiadomienia zawiera dalsze informacje o konkretnym niepowodzeniu. Nawigacja do zasobu Grupy zasobów, a następnie do wdrożenia element w nim również zawiera szczegółowe informacje na temat błędów wdrażania, które wystąpiły.
Instalacja agenta MMA lub usługi Log Analytics dla systemu Linux może zakończyć się niepowodzeniem z różnych powodów, a kroki, które należy podjąć w celu rozwiązania tych błędów, różnią się w zależności od problemu. Należy wykonać szczegółowe kroki rozwiązywania problemów.

W poniższej sekcji opisano różne problemy, które można natknąć podczas dołączania, które powodują błąd we wdrażaniu rozszerzenia MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenariusz: Wystąpił wyjątek podczas żądania WebClient

Rozszerzenie MMA na maszynie wirtualnej nie może komunikować się z zasobami zewnętrznymi i wdrażanie kończy się niepowodzeniem.

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

* Istnieje serwer proxy skonfigurowany w maszynie Wirtualnej, który zezwala tylko na określone porty.

* Ustawienie zapory zablokowało dostęp do wymaganych portów i adresów.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie porty i adresy otwarte dla komunikacji. Aby uzyskać listę portów i adresów, zobacz [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenariusz: Instalacja nie powiodła się z powodu problemów ze środowiskiem przejściowym

Instalacja rozszerzenia programu Microsoft Monitoring Agent nie powiodła się podczas wdrażania z powodu innej instalacji lub akcji blokującej instalację

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

* Trwa kolejna instalacja
* System jest wyzwalany do ponownego uruchomienia podczas wdrażania szablonu

#### <a name="resolution"></a>Rozwiązanie

Ten błąd jest błąd przejściowy w przyrodzie. Ponów próbę wdrożenia, aby zainstalować rozszerzenie.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenariusz: Limit czasu instalacji

Instalacja rozszerzenia MMA nie została ukończona z powodu limitu czasu.

#### <a name="issue"></a>Problem

Poniższy przykład jest komunikat o błędzie, który może być zwrócony:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, ponieważ maszyna wirtualna jest pod dużym obciążeniem podczas instalacji.

### <a name="resolution"></a>Rozwiązanie

Spróbuj zainstalować rozszerzenie MMA, gdy maszyna wirtualna jest pod mniejszym obciążeniem.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
