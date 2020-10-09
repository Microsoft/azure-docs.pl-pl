---
title: Wdrażanie aplikacji w klastrze w programie PowerShell
description: Przykładowy skrypt Azure PowerShell — wdrażanie aplikacji w klastrze Service Fabric.
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
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 145372fa872c481ec1a7c3de016c35fdc0f9d960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083807"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Ten przykładowy skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji w klastrze, usuwa zbędny pakiet aplikacji i tworzy wystąpienie aplikacji z typu aplikacji.  Jeśli jakieś usługi domyślne zostały zdefiniowane w manifeście aplikacji typu aplikacji docelowej, to te usługi są tworzone w tym momencie. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowego skryptu skrypt w obszarze [usuwanie aplikacji](service-fabric-powershell-remove-application.md) może służyć do usuwania wystąpienia aplikacji, wyrejestrowania typu aplikacji i usunięcia pakietu aplikacji z magazynu obrazów.

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Tworzy połączenie z klastrem Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopiuje pakiet aplikacji do magazynu obrazów klastra.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Rejestruje typ i wersję aplikacji w klastrze. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Tworzy aplikację z zarejestrowanego typu aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji Service Fabric z magazynu obrazów.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Service Fabric module programu PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/service-fabric/overview?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
