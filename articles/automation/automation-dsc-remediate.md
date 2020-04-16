---
title: Korygowanie niezgodnych serwerów konfiguracji stanu automatyzacji platformy Azure
description: Jak ponownie zastosować konfiguracje na żądanie do serwerów, na których stan konfiguracji się zmienił
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406081"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Korygowanie niezgodnych serwerów DSC

Gdy serwery są zarejestrowane w usłudze Azure `ApplyOnly` `ApplyandMonitor`Automation `ApplyAndAutoCorrect`State Configuration, tryb konfiguracji jest ustawiony na , lub . Jeśli tryb nie jest `ApplyAndAutoCorrect`ustawiony na , serwery, które dryfują ze stanu zgodnego z jakiegokolwiek powodu pozostają niezgodne, dopóki nie są ręcznie poprawione.

Dane obliczeniowe platformy Azure oferuje funkcję o nazwie Uruchom polecenie, która umożliwia klientom uruchamianie skryptów wewnątrz maszyn wirtualnych.
Ten dokument zawiera przykładowe skrypty dla tej funkcji podczas ręcznego korygowania dryfu konfiguracji.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Prawidłowe dryfowanie maszyn wirtualnych systemu Windows przy użyciu programu PowerShell

Aby uzyskać instrukcje krok po kroku przy użyciu funkcji Uruchom polecenie na maszynach wirtualnych systemu Windows, zobacz stronę dokumentacji [Uruchamianie skryptów programu PowerShell w maszynie Wirtualnej systemu Windows za pomocą polecenia Uruchom polecenie](/azure/virtual-machines/windows/run-command).

Aby wymusić węzeł konfiguracji stanu automatyzacji platformy Azure, aby pobrać najnowszą konfigurację i zastosować ją, użyj polecenia cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Prawidłowy dryf maszyn wirtualnych systemu Linux

Podobna funkcjonalność nie jest obecnie dostępna dla serwerów Linuksa.
Jedyną opcją jest powtórzenie procesu rejestracji.

W przypadku węzłów platformy Azure można poprawić dryf z witryny Azure portal lub przy użyciu poleceń cmdlet modułu Az. Szczegółowe informacje na temat tego procesu są dokumentowane w [komputerach dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
W przypadku węzłów hybrydowych można poprawić dryf przy użyciu dołączonych skryptów Języka Python.
Zobacz [PowerShell DSC dla repozytorium Linuksa](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md).