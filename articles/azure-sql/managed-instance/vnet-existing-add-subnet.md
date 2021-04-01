---
title: Konfigurowanie istniejącej sieci wirtualnej
titleSuffix: Azure SQL Managed Instance
description: W tym artykule opisano sposób konfigurowania istniejącej sieci wirtualnej i podsieci, w której można wdrożyć wystąpienie zarządzane usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617659"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Konfigurowanie istniejącej sieci wirtualnej dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL musi być wdrożone w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) platformy Azure i podsieć dedykowana tylko dla wystąpień zarządzanych. Możesz użyć istniejącej sieci wirtualnej i podsieci, jeśli są skonfigurowane zgodnie z [wymaganiami sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md#network-requirements).

W przypadku zastosowania jednego z następujących przypadków można sprawdzić poprawność sieci i zmodyfikować ją przy użyciu skryptu opisanego w tym artykule:

- Masz nową podsieć, która nie jest jeszcze skonfigurowana.
- Nie masz pewności, że podsieć jest zgodna z [wymaganiami](connectivity-architecture-overview.md#network-requirements).
- Należy sprawdzić, czy podsieć nadal spełnia [wymagania sieci](connectivity-architecture-overview.md#network-requirements) po wprowadzeniu zmian.

> [!Note]
> Wystąpienie zarządzane można utworzyć tylko w sieciach wirtualnych utworzonych za pomocą modelu wdrażania Azure Resource Manager. Sieci wirtualne platformy Azure utworzone za pomocą klasycznego modelu wdrażania nie są obsługiwane. Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi w artykule [Określanie rozmiaru podsieci dla wystąpienia zarządzanego SQL](vnet-subnet-determine-size.md) . Nie można zmienić rozmiaru podsieci po wdrożeniu zasobów.
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Weryfikowanie i modyfikowanie istniejącej sieci wirtualnej

Jeśli chcesz utworzyć wystąpienie zarządzane wewnątrz istniejącej podsieci, zalecamy wykonanie następującego skryptu programu PowerShell w celu przygotowania podsieci:

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

1. Weryfikuj: sprawdza poprawność wybranej sieci wirtualnej i podsieci dla wymagań sieci wystąpień zarządzanych przez SQL.
2. Potwierdź: pokazuje użytkownika zestaw zmian, które należy wykonać w celu przygotowania podsieci do wdrożenia wystąpienia zarządzanego SQL. Prosi również o zgodę.
3. Przygotuj: poprawnie konfiguruje sieć wirtualną i podsieć.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane SQL?](sql-managed-instance-paas-overview.md)
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego serwera DNS](custom-dns-configure.md).
