---
title: Automatyczne stosowanie poprawek dla maszyn wirtualnych SQL Server (klasyczny) | Microsoft Docs
description: W tym artykule wyjaśniono funkcję zautomatyzowanego stosowania poprawek dla SQL Server Virtual Machines działających na platformie Azure przy użyciu klasycznego trybu wdrażania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978080"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne stosowanie poprawek dla SQL Server na platformie Azure Virtual Machines (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-patching.md)
> 
> 

Automatyczne stosowanie poprawek ustanawia okno konserwacji dla maszyny wirtualnej platformy Azure z programem SQL Server. Automatyczne aktualizacje można instalować tylko w tym oknie konserwacji. W przypadku SQL Server gwarantuje to, że aktualizacje systemu i wszelkie skojarzone ponowne uruchomienia będą wykonywane z najlepszymi możliwymi momentami dla bazy danych. 

> [!IMPORTANT]
> Instalowane są tylko aktualizacje systemu Windows oznaczone jako **Ważne**. Pozostałe aktualizacje programu SQL Server, na przykład aktualizacje zbiorcze, należy instalować ręcznie. 

Automatyczne stosowanie poprawek zależy od [rozszerzenia agenta IaaS w programie SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżer zasobów tego artykułu, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server na platformie Azure Virtual Machines Menedżer zasobów](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby używać zautomatyzowanej poprawki, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia Azure PowerShell](/powershell/azure/overview).

**SQL Server rozszerzenie IaaS**:

* [Zainstaluj rozszerzenie SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego stosowania poprawek. W przypadku klasycznych maszyn wirtualnych należy skonfigurować te ustawienia przy użyciu programu PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram obsługi** |Codzienne, poniedziałek, wtorek, środa, czwartek, piątek, Sobota, niedziela |Harmonogram pobierania i instalowania aktualizacji systemu Windows, SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia aktualizowania maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut, przez jaką można ukończyć pobieranie i instalowanie aktualizacji. |
| **Kategoria poprawek** |Ważne |Kategoria aktualizacji do pobrania i zainstalowania. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie program PowerShell służy do konfigurowania automatycznej poprawki na istniejącej maszynie wirtualnej SQL Server. Polecenie **New-AzureVMSqlServerAutoPatchingConfig** konfiguruje nowe okno obsługi dla aktualizacji automatycznych.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Na podstawie tego przykładu w poniższej tabeli opisano praktyczny wpływ na docelową maszynę wirtualną platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Poprawki są instalowane w każdym czwartek. |
| **MaintenanceWindowStartingHour** |Rozpocznij aktualizacje o 11:10:00. |
| **MaintenanceWindowDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu rozpoczęcia muszą one zostać wykonane przez 1:13:00. |
| **PatchCategory** |Jedynym możliwym ustawieniem dla tego parametru jest "ważne". |

Zainstalowanie i skonfigurowanie agenta SQL Server IaaS może potrwać kilka minut.

Aby wyłączyć automatyczne stosowanie poprawek, Uruchom ten sam skrypt bez parametru-Enable dla New-AzureVMSqlServerAutoPatchingConfig. Tak jak w przypadku instalacji, automatyczne stosowanie poprawek może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [SQL Server rozszerzenia agenta IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

