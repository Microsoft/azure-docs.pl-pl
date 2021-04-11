---
title: Monitorowanie diagnostyki platformy Azure na potrzeby zaświadczania platformy Azure
description: Monitorowanie diagnostyki platformy Azure na potrzeby zaświadczania platformy Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168352"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurowanie diagnostyki przy użyciu punktu końcowego Trusted Platform Module (TPM) zaświadczania platformy Azure

W tym artykule opisano sposób tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych. [Dzienniki platformy](/azure/azure-monitor/platform/platform-logs-overview) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, od których zależą. [Metryki platformy](/azure/azure-monitor/platform/data-platform-metrics) są zbierane domyślnie i są przechowywane w bazie danych metryk Azure monitor.

Przed rozpoczęciem upewnij się, że [skonfigurowano zaświadczenie platformy Azure z Azure PowerShell](quickstart-powershell.md).

Usługa punktu końcowego Trusted Platform Module (TPM) jest włączona w ustawieniach diagnostycznych i może służyć do monitorowania aktywności. Skonfiguruj [monitorowanie platformy Azure](/azure/azure-monitor/overview) dla punktu końcowego usługi modułu TPM przy użyciu następującego kodu.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Dzienniki aktywności znajdują się w sekcji **kontenery** na koncie magazynu. Aby uzyskać więcej informacji, zobacz [zbieranie i analizowanie dzienników zasobów z zasobów platformy Azure](/azure/azure-monitor/learn/tutorial-resource-logs).
