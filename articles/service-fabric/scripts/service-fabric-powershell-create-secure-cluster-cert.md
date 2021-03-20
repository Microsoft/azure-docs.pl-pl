---
title: Tworzenie klastra Service Fabric w programie PowerShell
description: Przykładowy skrypt Azure PowerShell — utwórz klaster Service Fabric zabezpieczony za pomocą certyfikatu X. 509.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: bae42c9ec8643da1563fb6b566d8db4af6039225
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078955"
---
# <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric

Ten przykładowy skrypt tworzy klaster usługi Service Fabric z pięcioma węzłami zabezpieczony za pomocą certyfikatu X.509.  Polecenie tworzy certyfikat z podpisem własnym i przekazuje go do nowego magazynu kluczy. Certyfikat jest też kopiowany do katalogu lokalnego.  Ustaw parametr *-OS* , aby wybrać wersję systemu Windows lub Linux uruchamianą w węzłach klastra.  Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, klaster i wszystkie powiązane zasoby.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Tworzy nowy klaster usługi Service Fabric. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
