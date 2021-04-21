---
title: Uruchamianie skryptów na maszynie wirtualnej platformy Azure z systemem Windows
description: W tym temacie opisano sposób uruchamiania skryptów na maszynie wirtualnej z systemem Windows
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7bf62eb2ab8d2ce82ce73e3e8ae26cf303b8ba67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765885"
---
# <a name="run-scripts-in-your-windows-vm"></a>Uruchamianie skryptów na maszynie wirtualnej z systemem Windows

Aby zautomatyzować zadania lub rozwiązać problemy, może być konieczne uruchomienie poleceń na maszynie wirtualnej. Poniższy artykuł zawiera krótkie omówienie funkcji, które są dostępne do uruchamiania skryptów i poleceń w ramach maszyn wirtualnych.

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

Rozszerzenie [niestandardowego skryptu jest](../extensions/custom-script-windows.md) używane głównie do konfigurowania po wdrożeniu i instalowania oprogramowania.

* Pobieranie i uruchamianie skryptów na maszynach wirtualnych platformy Azure.
* Można uruchamiać przy użyciu Azure Resource Manager, interfejsu wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell lub Azure Portal.
* Pliki skryptów można pobrać z usługi Azure Storage lub GitHub albo dostarczone z komputera podczas uruchamiania z Azure Portal.
* Uruchom skrypt programu PowerShell na maszynach z systemem Windows i skrypt powłoki Bash na maszynach z systemem Linux.
* Przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania i innych zadań konfiguracji lub zarządzania.

## <a name="run-command"></a>Uruchom polecenie 

Funkcja [Uruchamianie polecenia](run-command.md) umożliwia zarządzanie maszyną wirtualną i aplikacją oraz rozwiązywanie problemów za pomocą skryptów i jest dostępna nawet wtedy, gdy maszyna jest nieodpowiedna, na przykład jeśli zapora gościa nie ma otwartego portu RDP lub SSH.

* Uruchamianie skryptów na maszynach wirtualnych platformy Azure.
* Można go uruchamiać przy [użyciu Azure Portal](run-command.md), [interfejsu API REST,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/run-command#az_vm_run_command_invoke)lub programu [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)
* Szybkie uruchamianie skryptu i wyświetlanie danych wyjściowych oraz powtarzanie w razie potrzeby w Azure Portal.
* Skrypt można wpisać bezpośrednio lub uruchomić jeden z wbudowanych skryptów.
* Uruchom skrypt programu PowerShell na maszynach z systemem Windows i skrypt powłoki Bash na maszynach z systemem Linux.
* Przydatne do zarządzania maszynami wirtualnymi i aplikacji oraz do uruchamiania skryptów na maszynach wirtualnych, które są nieosiągalne.

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

Hybrydowy [proces roboczy runbook zapewnia](../../automation/automation-hybrid-runbook-worker.md) ogólne zarządzanie maszyną, aplikacją i środowiskiem przy użyciu niestandardowych skryptów użytkownika przechowywanych na koncie usługi Automation.

* Uruchamianie skryptów na platformie Azure i maszynach spoza platformy Azure.
* Można go uruchamiać przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell i webhook.
* Skrypty przechowywane i zarządzane na koncie usługi Automation.
* Uruchamianie programu PowerShell, przepływu pracy programu PowerShell, języka Python lub graficznych elementów Runbook
* Brak limitu czasu w czasie wykonywania skryptu.
* Wiele skryptów można uruchamiać jednocześnie.
* Zwracane i przechowywane są pełne dane wyjściowe skryptu.
* Historia zadań dostępna przez 90 dni.
* Skrypty mogą być uruchamiane jako system lokalny lub z poświadczeniami dostarczonymi przez użytkownika.
* Wymaga [instalacji ręcznej](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konsola szeregowa

Maszyna [Konsola szeregowa](/troubleshoot/azure/virtual-machines/serial-console-windows) bezpośredni dostęp do maszyny wirtualnej, podobnie jak klawiatura podłączona do maszyny wirtualnej.

* Uruchamianie poleceń na maszynach wirtualnych platformy Azure.
* Można go uruchomić przy użyciu konsoli tekstowej na maszynie w Azure Portal.
* Zaloguj się do maszyny przy użyciu konta użytkownika lokalnego.
* Przydatne, gdy wymagany jest dostęp do maszyny wirtualnej niezależnie od stanu sieci lub systemu operacyjnego maszyny.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych funkcjach, które są dostępne do uruchamiania skryptów i poleceń w ramach maszyn wirtualnych.

* [Rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md)
* [Uruchamianie polecenia](run-command.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md)
* [Konsola szeregowa](/troubleshoot/azure/virtual-machines/serial-console-windows)