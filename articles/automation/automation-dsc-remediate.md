---
title: Koryguj niezgodne serwery konfiguracji stanu Azure Automation
description: Jak ponownie zastosować konfiguracje na żądanie do serwerów, na których nastąpiło przetworzenie stanu konfiguracji
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406081"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Koryguj niezgodne serwery DSC

Gdy serwery są zarejestrowane w Azure Automation konfiguracji stanu, tryb konfiguracji jest ustawiony na `ApplyOnly`, `ApplyandMonitor`lub. `ApplyAndAutoCorrect` Jeśli tryb nie jest ustawiony na `ApplyAndAutoCorrect`, serwery, które przestają ze stanu zgodnego z dowolnego powodu, pozostają niezgodne do momentu ich ręcznego skorygowania.

Usługa Azure COMPUTE oferuje funkcję o nazwie Run, która umożliwia klientom uruchamianie skryptów wewnątrz maszyn wirtualnych.
Ten dokument zawiera przykładowe skrypty dla tej funkcji podczas ręcznego korygowania dryfu konfiguracji.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Poprawianie oddryfu maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell

Aby uzyskać instrukcje krok po kroku przy użyciu funkcji Uruchom polecenie na maszynach wirtualnych z systemem Windows, zobacz stronę dokumentacji [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](/azure/virtual-machines/windows/run-command).

Aby wymusić pobranie najnowszej konfiguracji przez węzeł Konfiguracja stanu Azure Automation i zastosować go, należy użyć polecenia cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Poprawianie dryfu maszyn wirtualnych z systemem Linux

Podobne funkcje nie są obecnie dostępne dla serwerów z systemem Linux.
Jedyną opcją jest powtórzenie procesu rejestracji.
W przypadku węzłów platformy Azure można poprawić dryf z Azure Portal lub przy użyciu poleceń cmdlet AZ module. Szczegółowe informacje o tym procesie zostały udokumentowane w obszarze dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
W przypadku węzłów hybrydowych można poprawić dryf przy użyciu dołączonych skryptów języka Python.
Zobacz program [POWERSHELL DSC dla repozytorium systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md).