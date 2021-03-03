---
title: Monitorowanie diagnostyczne platformy Azure — zaświadczanie platformy Azure
description: Monitorowanie diagnostyki platformy Azure na potrzeby zaświadczania platformy Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726482"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurowanie diagnostyki przy użyciu punktu końcowego Trusted Platform Module (TPM) dla zaświadczania platformy Azure

[Dzienniki platformy](../azure-monitor/essentials/platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. [Metryki platformy](../azure-monitor/essentials/data-platform-metrics.md) są zbierane domyślnie i zazwyczaj przechowywane w bazie danych metryk Azure monitor. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych. 

Usługa punktu końcowego modułu TPM jest włączona z ustawieniem diagnostycznym i może służyć do monitorowania działania. Aby skonfigurować [monitorowanie platformy Azure](../azure-monitor/overview.md) dla punktu końcowego usługi modułu TPM przy użyciu programu PowerShell, wykonaj poniższe kroki. 

Skonfiguruj usługę zaświadczania platformy Azure. 

[Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Dzienniki aktywności można znaleźć w sekcji kontenery na koncie magazynu. Szczegółowe informacje można znaleźć w witrynie [zbieranie dzienników zasobów z zasobów platformy Azure i analizowanie ich przy użyciu Azure monitor-Azure monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
