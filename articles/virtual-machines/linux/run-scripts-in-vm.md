---
title: Uruchamianie skryptów na maszynie wirtualnej systemu Azure z systemem Linux
description: W tym temacie opisano sposób uruchamiania skryptów na maszynie wirtualnej
services: automation
ms.service: virtual-machines-linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
ms.openlocfilehash: 9dbfb3e76fe1c0e80dd8020b79f4edd6d32a24a3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758586"
---
# <a name="run-scripts-in-your-linux-vm"></a>Uruchamianie skryptów na maszynie Wirtualnej systemu Linux

Aby zautomatyzować zadania lub rozwiązać problemy, może być konieczne uruchomienie poleceń na maszynie Wirtualnej. Poniższy artykuł zawiera krótkie omówienie funkcji, które są dostępne do uruchamiania skryptów i poleceń w ramach maszyn wirtualnych.

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

[Rozszerzenie skryptów niestandardowych](../extensions/custom-script-linux.md) jest używane głównie do konfiguracji po wdrożeniu i instalacji oprogramowania.

* Pobieranie i uruchamianie skryptów na maszynach wirtualnych platformy Azure.
* Można uruchomić przy użyciu szablonów usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell lub witryny Azure portal.
* Pliki skryptów można pobrać z usługi Azure Storage lub GitHub lub dostarczone z komputera po uruchomieniu z witryny Azure portal.
* Uruchom skrypt programu PowerShell na komputerach z systemem Windows i skrypt Bash na komputerach z systemem Linux.
* Przydatne do konfiguracji po wdrożeniu, instalacji oprogramowania i innych zadań konfiguracyjnych lub zarządzania.

## <a name="run-command"></a>Uruchom polecenie

Funkcja [Uruchom polecenie](run-command.md) umożliwia zarządzanie maszyną wirtualną i aplikacjami oraz rozwiązywanie problemów przy użyciu skryptów i jest dostępna nawet wtedy, gdy komputer jest niedostępny, na przykład jeśli zapora gościa nie ma otwartego portu RDP lub SSH.

* Uruchamianie skryptów na maszynach wirtualnych platformy Azure.
* Można uruchomić przy użyciu [witryny Azure portal,](run-command.md) [interfejsu API REST,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)lub programu [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Szybko uruchom skrypt i wyświetl dane wyjściowe i powtórz zgodnie z potrzebami w witrynie Azure portal.
* Skrypt można wpisać bezpośrednio lub można uruchomić jeden z wbudowanych skryptów.
* Uruchom skrypt programu PowerShell na komputerach z systemem Windows i skrypt Bash na komputerach z systemem Linux.
* Przydatne do zarządzania maszynami wirtualnymi i aplikacjami oraz do uruchamiania skryptów na maszynach wirtualnych, które są nieosiągalne.

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

[Hybrydowy proces roboczy systemu runbook](../../automation/automation-hybrid-runbook-worker.md) zapewnia ogólne zarządzanie maszynami, aplikacjami i środowiskami za pomocą skryptów niestandardowych użytkownika przechowywanych na koncie automatyzacji.

* Uruchamianie skryptów na komputerach platformy Azure i innych niż azure.
* Można uruchomić przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, interfejsu API REST, programu PowerShell, elementu webhook.
* Skrypty przechowywane i zarządzane na koncie automatyzacji.
* Uruchamianie eksbry programów PowerShell, przepływu pracy programu PowerShell, języka Python lub yartów graficznych
* Brak limitu czasu na czas wykonywania skryptu.
* Wiele skryptów można uruchomić jednocześnie.
* Pełne dane wyjściowe skryptu są zwracane i przechowywane.
* Historia pracy dostępna przez 90 dni.
* Skrypty mogą być uruchamiane jako system lokalny lub z poświadczeniami dostarczonymi przez użytkownika.
* Wymaga [ręcznej instalacji](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konsola szeregowa

Konsola [szeregowa](serial-console.md) zapewnia bezpośredni dostęp do maszyny Wirtualnej, podobnie jak posiadanie klawiatury podłączonej do maszyny Wirtualnej.

* Uruchamianie poleceń na maszynach wirtualnych platformy Azure.
* Można uruchomić przy użyciu konsoli tekstowej do komputera w witrynie Azure portal.
* Zaloguj się do urządzenia za pomocą lokalnego konta użytkownika.
* Przydatne, gdy potrzebny jest dostęp do maszyny wirtualnej niezależnie od stanu sieci lub systemu operacyjnego komputera.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych funkcjach, które są dostępne do uruchamiania skryptów i poleceń w maszynach wirtualnych.

* [Rozszerzenie niestandardowego skryptu](../extensions/custom-script-linux.md)
* [Uruchamianie polecenia](run-command.md)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md)
* [Konsola szeregowa](serial-console.md)