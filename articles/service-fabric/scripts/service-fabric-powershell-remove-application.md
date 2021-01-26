---
title: Usuwanie aplikacji z klastra w programie PowerShell
description: Przykładowy skrypt Azure PowerShell — usuwanie aplikacji z klastra Service Fabric.
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
ms.openlocfilehash: 686afa791df88382e3e5e1b2d233317c36bf1dd6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791309"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Usuwanie aplikacji z klastra Service Fabric przy użyciu programu PowerShell

Ten przykładowy skrypt usuwa uruchomione wystąpienie aplikacji Service Fabric i wyrejestrowuje typ aplikacji i wersję z klastra.  Usuwanie wystąpienia aplikacji spowoduje usunięcie wszystkich uruchomionych wystąpień usługi skojarzonych z aplikacją. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) | Usuwa uruchomione wystąpienie aplikacji Service Fabric z klastra.  |
| [Unregister — ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Wyrejestrowuje typ i wersję aplikacji Service Fabric z klastra. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Service Fabric module programu PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/service-fabric/overview).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
