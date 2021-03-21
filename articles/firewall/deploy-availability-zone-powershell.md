---
title: Wdrażanie zapory platformy Azure za pomocą Strefy dostępności przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zaporę platformy Azure z Strefy dostępności przy użyciu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656109"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Wdrażanie usługi Azure Firewall ze strefami dostępności przy użyciu programu Azure PowerShell

Zaporę platformy Azure można skonfigurować podczas wdrażania w celu rozdzielenia wielu Strefy dostępności w celu zwiększenia dostępności.

Ta funkcja włącza następujące scenariusze:

- Możesz zwiększyć dostępność do 99,99% czasu pracy. Aby uzyskać więcej informacji, zobacz Umowa dotycząca poziomu usług zapory platformy Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Umowa SLA na czas działania na 99,99% jest oferowana w przypadku wybrania co najmniej dwóch Strefy dostępności.
- Można także skojarzyć zaporę platformy Azure z określoną strefą tylko z przyczyn bliskości przy użyciu standardowej umowy SLA usługi 99,95%.

Aby uzyskać więcej informacji na temat Strefy dostępności zapory platformy Azure, zobacz [co to jest Zapora systemu Azure?](overview.md)

Poniższy przykład Azure PowerShell pokazuje, jak można wdrożyć zaporę platformy Azure z Strefy dostępności.

## <a name="create-a-firewall-with-availability-zones"></a>Tworzenie zapory za pomocą Strefy dostępności

Ten przykład umożliwia utworzenie zapory w strefach 1, 2 i 3.

Po utworzeniu standardowego publicznego adresu IP nie określono konkretnej strefy. Spowoduje to utworzenie domyślnie nadmiarowego adresu IP. Standardowe publiczne adresy IP można skonfigurować we wszystkich strefach lub w jednej strefie.

Należy pamiętać, że nie można mieć zapory w strefie 1 i adres IP w strefie 2. Można jednak mieć zaporę w strefie 1 i adres IP we wszystkich strefach lub zaporę i adres IP w tej samej pojedynczej strefie na potrzeby sąsiedztwa.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: monitorowanie dzienników usługi Azure Firewall](./firewall-diagnostics.md)