---
title: Monitorowanie diagnostyczne platformy Azure — zaświadczanie platformy Azure
description: Monitorowanie diagnostyki platformy Azure na potrzeby zaświadczania platformy Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606107"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurowanie diagnostyki przy użyciu punktu końcowego moduł TPM (TPM) dla zaświadczania platformy Azure

[Dzienniki platformy](/azure/azure-monitor/platform/platform-logs-overview) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. [Metryki platformy](/azure/azure-monitor/platform/data-platform-metrics) są zbierane domyślnie i zazwyczaj przechowywane w bazie danych metryk Azure monitor. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych. 

Usługa punktu końcowego modułu TPM jest włączona z ustawieniem diagnostycznym i może służyć do monitorowania działania. Aby skonfigurować [monitorowanie platformy Azure](/azure/azure-monitor/overview) dla punktu końcowego usługi modułu TPM przy użyciu programu PowerShell, wykonaj poniższe kroki. 

Skonfiguruj usługę zaświadczania platformy Azure. 

[Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Dzienniki aktywności można znaleźć w sekcji kontenery na koncie magazynu. Szczegółowe informacje można znaleźć w witrynie [zbieranie dzienników zasobów z zasobów platformy Azure i analizowanie ich przy użyciu Azure monitor-Azure monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
