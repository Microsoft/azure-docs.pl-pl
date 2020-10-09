---
title: Różnice w usłudze Azure Service Fabric w systemach Linux i Windows
description: Różnice między usługą Azure Service Fabric w systemie Linux a usługą Azure Service Fabric w systemie Windows.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: sfrev
ms.openlocfilehash: c064291491110b17c172a561afbd1e65d6c08d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75748531"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Różnice między usługą Service Fabric w systemach Linux i Windows

Pewne funkcje, które są obsługiwane w systemie Windows, nie są jeszcze obsługiwane w systemie Linux. Po pewnym czasie zestawy funkcji będą działać w obydwu systemach i w każdej nowej wersji ta różnica między funkcjami będzie się zmniejszać. Między najnowszymi dostępnymi wersjami istnieją następujące różnice.

* Wysłannika (zwrotny serwer proxy) jest w wersji zapoznawczej w systemie Linux
* Autonomiczny instalator dla systemu Linux nie jest dostępny w systemie Linux
* Przekierowywanie konsoli (nieobsługiwane w klastrach produkcyjnych w systemie Linux ani Windows)
* Usługa analizy błędów w systemie Linux
* Usługa DNS dla usług Service Fabric (usługa DNS jest obsługiwana dla kontenerów w systemie Linux)
* Odpowiedniki poleceń interfejsu wiersza polecenia dla niektórych poleceń programu PowerShell (lista znajduje się poniżej — większość z nich ma zastosowanie tylko do klastrów autonomicznych)
* [Różnice w implementacji dziennika, które mogą mieć wpływ na skalowalność](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Polecenia cmdlet programu PowerShell, które nie działają względem klastra usługi Service Fabric z systemem Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie środowiska projektowego w systemie Linux](service-fabric-get-started-linux.md)
* [Przygotowywanie środowiska projektowego w systemie OSX](service-fabric-get-started-mac.md)
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman](service-fabric-create-your-first-linux-application-with-java.md) (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu programu Yeoman)
* [Tworzenie i wdrażanie pierwszej Service Fabric aplikacji Java w systemie Linux przy użyciu wtyczki Service Fabric dla programu zaćmienie](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux](service-fabric-create-your-first-linux-application-with-csharp.md) (Tworzenie pierwszej aplikacji CSharp w systemie Linux)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md) (Zarządzanie aplikacjami przy użyciu interfejsu wiersza polecenia usługi Service Fabric)
