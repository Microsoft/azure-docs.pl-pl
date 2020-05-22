---
title: Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego
description: W tym artykule opisano sposób konfigurowania istniejącej sieci wirtualnej i podsieci, w której można wdrożyć Azure SQL Database wystąpienie zarządzane.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 4e678edad2c59205e76598991b36d296404a3163
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773625"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzane usługi Azure SQL Database musi zostać wdrożone w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) platformy Azure i podsieci dedykowanej tylko dla wystąpień zarządzanych. Możesz użyć istniejącej sieci wirtualnej i podsieci, jeśli jest skonfigurowana zgodnie z [wymaganiami sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

W przypadku zastosowania jednego z następujących przypadków można sprawdzić poprawność sieci i zmodyfikować ją przy użyciu skryptu opisanego w tym artykule:

- Masz nową podsieć, która nie jest jeszcze skonfigurowana.
- Nie masz pewności, że podsieć jest zgodna z [wymaganiami](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Należy sprawdzić, czy podsieć nadal spełnia [wymagania sieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po wprowadzeniu zmian.

> [!Note]
> Wystąpienie zarządzane można utworzyć tylko w sieciach wirtualnych utworzonych za pomocą modelu wdrażania Azure Resource Manager. Sieci wirtualne platformy Azure utworzone za pomocą klasycznego modelu wdrażania nie są obsługiwane. Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi w artykule [Określanie rozmiaru podsieci dla wystąpień zarządzanych](sql-database-managed-instance-determine-size-vnet-subnet.md) . Nie można zmienić rozmiaru podsieci po wdrożeniu zasobów wewnątrz programu.
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Weryfikowanie i modyfikowanie istniejącej sieci wirtualnej

Jeśli chcesz utworzyć wystąpienie zarządzane w ramach istniejącej podsieci, zalecamy wykonanie następującego skryptu programu PowerShell w celu przygotowania podsieci:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skrypt przygotowuje podsieć w trzech krokach:

1. Weryfikuj: sprawdza poprawność wybranej sieci wirtualnej i podsieci pod kątem wymagań dotyczących sieci wystąpień zarządzanych.
2. Potwierdź: pokazuje użytkownika zestaw zmian, które należy wykonać w celu przygotowania podsieci do wdrożenia wystąpienia zarządzanego. Prosi również o zgodę.
3. Przygotuj: poprawnie konfiguruje sieć wirtualną i podsieć.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [tworzenie Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego serwera DNS](sql-database-managed-instance-custom-dns.md).
