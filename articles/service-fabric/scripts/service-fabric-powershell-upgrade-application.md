---
title: Uaktualnianie aplikacji Service Fabric w programie PowerShell
description: Przykładowy skrypt Azure PowerShell — uaktualnianie i monitorowanie aplikacji Service Fabric platformy Azure przy użyciu programu PowerShell.
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
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784700"
---
# <a name="upgrade-a-service-fabric-application"></a>Uaktualnianie aplikacji Service Fabric

Ten przykładowy skrypt uaktualnia uruchomione wystąpienie aplikacji Service Fabric do wersji 1.3.0. Skrypt kopiuje nowy pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i usuwa zbędny pakiet aplikacji.  Skrypt uruchamia monitorowane uaktualnienie i stale sprawdza stan uaktualnienia do momentu zakończenia lub wycofania uaktualnienia. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Pobiera wszystkie aplikacje w klastrze Service Fabric lub konkretnej aplikacji.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Pobiera stan uaktualnienia aplikacji Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Pobiera Service Fabric typy aplikacji zarejestrowane w klastrze Service Fabric. |
| [Unregister — ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Wyrejestrowuje typ aplikacji Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Kopiuje pakiet aplikacji Service Fabric do magazynu obrazów.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Rejestruje typ aplikacji Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Uaktualnia aplikację Service Fabric do określonej wersji typu aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Usuwa pakiet aplikacji Service Fabric z magazynu obrazów.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Service Fabric module programu PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/service-fabric/overview).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
