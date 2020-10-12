---
title: Azure Monitor mapowania regionów kontenerów
description: Opisuje mapowania regionów obsługiwane między Azure Monitor dla kontenerów, Log Analytics obszaru roboczego i metryk niestandardowych.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272909"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapowania regionów obsługiwane przez Azure Monitor dla kontenerów

 Podczas włączania Azure Monitor dla kontenerów tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i klastra AKS oraz zbierania niestandardowych metryk przesłanych do Azure Monitor.

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

Zbieranie metryk z węzłów i zasobników klastrów usługi Azure Kubernetes Services (AKS) są obsługiwane w przypadku publikowania jako metryki niestandardowych tylko w następujących [regionach świadczenia usługi Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra AKS, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.  
