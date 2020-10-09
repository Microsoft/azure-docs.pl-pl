---
title: Aktualizowanie nazwy użytkownika i hasła protokołu RDP w programie PowerShell
description: Przykładowy skrypt programu Azure PowerShell — aktualizowanie nazwy użytkownika i hasła protokołu RDP dla wszystkich węzłów klastra usługi Service Fabric określonego typu węzła.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: bcf619e2251f5c1b641190549da45f721835ce0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076157"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aktualizowanie nazwy użytkownika i hasła administratora maszyn wirtualnych w klastrze

Każdy [typ węzła](../service-fabric-cluster-nodetypes.md) w klastrze usługi Service Fabric jest zestawem skalowania maszyn wirtualnych. Ten przykładowy skrypt aktualizuje nazwę użytkownika i hasło dla maszyn wirtualnych klastra w określonym typie węzła.  Dodaj rozszerzenie VMAccessAgent do zestawu skalowania, ponieważ hasło administratora nie jest wartością zestawu skalowania, którą można modyfikować.  Zmiany nazwy użytkownika i hasła dotyczą wszystkich węzłów w zestawie skalowania. Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji dostępnej w [przewodniku programu Azure PowerShell](/powershell/azure/). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Pobiera właściwości typu węzła klastra (zestaw skalowania maszyn wirtualnych).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Dodaje rozszerzenie do zestawu skalowania maszyn wirtualnych.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Aktualizuje stan zestawu skalowania maszyn wirtualnych do stanu obiektu lokalnego VMSS.|

## <a name="duration"></a>Czas trwania

Pojedynczy typ węzła z pięcioma węzłami, na przykład, ma czas trwania od 45 do 60 minut, aby zmienić nazwę użytkownika lub hasło. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
