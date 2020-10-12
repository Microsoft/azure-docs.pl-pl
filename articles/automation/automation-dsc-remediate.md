---
title: Koryguj niezgodne serwery konfiguracji stanu Azure Automation
description: W tym artykule opisano, jak ponownie zastosować konfiguracje na żądanie do serwerów, na których stan konfiguracji został przeliczona.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 4430b8cdfe9414ddbfd7aad3c3fe7827adbc8705
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186371"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Koryguj niezgodne serwery konfiguracji stanu Azure Automation

Gdy serwery są zarejestrowane w Azure Automation konfiguracji stanu, tryb konfiguracji jest ustawiony na `ApplyOnly` , `ApplyandMonitor` lub `ApplyAndAutoCorrect` . Jeśli tryb nie jest ustawiony na `ApplyAndAutoCorrect` , serwery, które przestają ze stanu zgodnego z dowolnego powodu, pozostają niezgodne do momentu ich ręcznego skorygowania.

Usługa Azure COMPUTE oferuje funkcję o nazwie Run, która umożliwia klientom uruchamianie skryptów wewnątrz maszyn wirtualnych.
Ten dokument zawiera przykładowe skrypty dla tej funkcji podczas ręcznego korygowania dryfu konfiguracji.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Poprawianie oddryfu maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell

Można poprawić dryf maszyn wirtualnych z systemem Windows przy użyciu `Run` funkcji polecenia. Zobacz [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../virtual-machines/windows/run-command.md).

Aby wymusić pobranie najnowszej konfiguracji przez węzeł Konfiguracja stanu Azure Automation i zastosować go, należy użyć polecenia cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Poprawianie dryfu maszyn wirtualnych z systemem Linux

W przypadku maszyn wirtualnych z systemem Linux nie masz opcji używania `Run` polecenia. Można tylko poprawić dryf dla tych maszyn przez powtórzenie procesu rejestracji. 

W przypadku węzłów platformy Azure można poprawić dryf z Azure Portal lub przy użyciu poleceń cmdlet AZ module. Szczegółowe informacje o tym procesie zostały udokumentowane w [włączyć maszynę wirtualną przy użyciu Azure Portal](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

W przypadku węzłów hybrydowych można poprawić dryf przy użyciu skryptów języka Python. Zobacz [wykonywanie operacji DSC na komputerze z systemem Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
