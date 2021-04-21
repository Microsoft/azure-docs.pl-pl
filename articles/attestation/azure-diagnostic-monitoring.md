---
title: Monitorowanie diagnostyczne platformy Azure dla Azure Attestation
description: Monitorowanie diagnostyczne platformy Azure dla Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833638"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurowanie diagnostyki przy użyciu punktu końcowego Trusted Platform Module (TPM) Azure Attestation

Ten artykuł ułatwia tworzenie i konfigurowanie ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych. [Dzienniki platformy na](/azure/azure-monitor/platform/platform-logs-overview) platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dotyczące zasobów platformy Azure i platformy Azure, od której zależą. [Metryki platformy są](/azure/azure-monitor/platform/data-platform-metrics) zbierane domyślnie i są przechowywane w bazie Azure Monitor Metrics.

Przed rozpoczęciem upewnij się, że masz [już Azure Attestation za pomocą Azure PowerShell](quickstart-powershell.md).

Usługa Trusted Platform Module (TPM) jest włączona w ustawieniach diagnostycznych i może służyć do monitorowania aktywności. Skonfiguruj monitorowanie [platformy Azure](/azure/azure-monitor/overview) dla punktu końcowego usługi TPM przy użyciu następującego kodu.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Dzienniki aktywności znajdują się w **sekcji Kontenery** konta magazynu. Aby uzyskać więcej informacji, zobacz [Collect and analyze resource logs from an Azure resource (Zbieranie i analizowanie dzienników zasobów z zasobu platformy Azure).](/azure/azure-monitor/learn/tutorial-resource-logs)
