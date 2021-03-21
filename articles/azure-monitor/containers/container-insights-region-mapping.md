---
title: Mapowania regionów usługi Container Insights
description: Opisuje mapowania regionów obsługiwane przez program Container Insights, obszar roboczy Log Analytics i metryki niestandardowe.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728879"
---
# <a name="region-mappings-supported-by-container-insights"></a>Mapowania regionów obsługiwane przez usługi Container Insights

 Podczas włączania usługi Container Insights tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego Log Analytics i klastra AKS oraz zbierania niestandardowych metryk przesłane do Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapowania obsługiwane w obszarze roboczym Log Analytics

Obsługiwane regiony AKS są wymienione w obszarze [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). Obszar roboczy Log Analytics musi znajdować się w tym samym regionie, z wyjątkiem regionów wymienionych w poniższej tabeli. Obejrzyj [Informacje o wersji AKS](https://github.com/Azure/AKS/releases) dla aktualizacji.


|**Region klastra AKS** | **Log Analytics region obszaru roboczego** |
|-----------------------|------------------------------------|
|**Afryka** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australia** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brazylia** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**Indie** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japonia** | |
|JapanWest |JapanEast |
|**Korea** | |
|KoreaSouth |KoreaCentral |
|**USA** | |
|WestCentralUS<sup>1</sup>|Wschodnie<sup>1</sup>|


<sup>1</sup> ze względu na ograniczenia pojemności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje obszar roboczy Log Analytics. Jednak istniejące połączone zasoby w regionie powinny być nadal wykonywane.

## <a name="custom-metrics-supported-regions"></a>Regiony niestandardowe obsługiwane przez metryki

Zbieranie metryk z węzłów i zasobników klastrów usługi Azure Kubernetes Services (AKS) są obsługiwane w przypadku publikowania jako metryki niestandardowych tylko w następujących [regionach świadczenia usługi Azure](../essentials/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra AKS, zapoznaj się z [tematem jak włączyć usługi Container Insights](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.  
