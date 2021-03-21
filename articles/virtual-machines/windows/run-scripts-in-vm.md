---
title: Uruchamianie skryptów na maszynie wirtualnej z systemem Windows Azure
description: W tym temacie opisano sposób uruchamiania skryptów w maszynie wirtualnej z systemem Windows
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: ab7d20a83c4cb8cb7a36c4603a5b0b2e69b0662f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560671"
---
# <a name="run-scripts-in-your-windows-vm"></a>Uruchamianie skryptów na maszynie wirtualnej z systemem Windows

Aby zautomatyzować zadania lub rozwiązać problemy, może być konieczne uruchomienie poleceń na maszynie wirtualnej. Poniższy artykuł zawiera krótkie omówienie funkcji, które są dostępne do uruchamiania skryptów i poleceń na maszynach wirtualnych.

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

[Rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md) jest używane przede wszystkim do konfiguracji po wdrożeniu i instalacji oprogramowania.

* Pobieranie i uruchamianie skryptów w usłudze Azure Virtual Machines.
* Można uruchamiać przy użyciu szablonów Azure Resource Manager, interfejsu wiersza polecenia platformy Azure, usługi API REST, programu PowerShell lub Azure Portal.
* Pliki skryptów można pobrać z usługi Azure Storage lub GitHub lub z komputera, gdy jest on uruchamiany z Azure Portal.
* Uruchom skrypt programu PowerShell na maszynach z systemem Windows i skrypcie bash na maszynach z systemem Linux.
* Przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania i innych zadań związanych z konfiguracją lub zarządzaniem.

## <a name="run-command"></a>Uruchom polecenie

Funkcja [Run command](run-command.md) umożliwia zarządzanie maszynami wirtualnymi i aplikacjami oraz Rozwiązywanie problemów przy użyciu skryptów i jest dostępna nawet wtedy, gdy komputer jest nieosiągalny, na przykład jeśli Zapora gościa nie ma otwartego portu RDP lub SSH.

* Uruchamianie skryptów w usłudze Azure Virtual Machines.
* Mogą być uruchamiane przy użyciu [Azure Portal](run-command.md), [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [wiersza polecenia platformy Azure](/cli/azure/vm/run-command#az-vm-run-command-invoke)lub [programu PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) .
* Szybkie uruchamianie skryptu i wyświetlanie danych wyjściowych i powtarzanie w razie konieczności w Azure Portal.
* Skrypt można wpisać bezpośrednio lub można uruchomić jeden z wbudowanych skryptów.
* Uruchom skrypt programu PowerShell na maszynach z systemem Windows i skrypcie bash na maszynach z systemem Linux.
* Przydatne do zarządzania maszynami wirtualnymi i aplikacjami oraz do uruchamiania skryptów w maszynach wirtualnych, które są nieosiągalne.

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

[Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md) zapewnia ogólne zarządzanie maszynami, aplikacjami i środowiskami przy użyciu niestandardowych skryptów użytkownika przechowywanych na koncie usługi Automation.

* Uruchamiaj skrypty na platformie Azure i na maszynach spoza platformy Azure.
* Można uruchamiać przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, usługi API REST, programu PowerShell i elementu webhook.
* Skrypty przechowywane i zarządzane na koncie usługi Automation.
* Uruchamianie programu PowerShell, przepływu pracy programu PowerShell, środowiska Python lub graficznych elementów Runbook
* Czas wykonywania skryptu nie jest limitem czasu.
* Jednocześnie można uruchamiać wiele skryptów.
* Pełne dane wyjściowe skryptu są zwracane i przechowywane.
* Historia zadań dostępna przez 90 dni.
* Skrypty mogą działać jako system lokalny lub z poświadczeniami dostarczonymi przez użytkownika.
* Wymaga [instalacji ręcznej](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konsola szeregowa

[Konsola szeregowa](../troubleshooting/serial-console-windows.md) zapewnia bezpośredni dostęp do maszyny wirtualnej, podobnie jak przy użyciu klawiatury podłączonej do maszyny wirtualnej.

* Uruchom polecenia w usłudze Azure Virtual Machines.
* Można uruchomić za pomocą konsoli tekstowej na maszynie w Azure Portal.
* Zaloguj się na komputerze przy użyciu konta użytkownika lokalnego.
* Przydatne, gdy dostęp do maszyny wirtualnej jest konieczny niezależnie od stanu sieci lub systemu operacyjnego maszyny.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych funkcjach, które są dostępne do uruchamiania skryptów i poleceń na maszynach wirtualnych.

* [Rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md)
* [Uruchamianie polecenia](run-command.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md)
* [Konsola szeregowa](../troubleshooting/serial-console-windows.md)
