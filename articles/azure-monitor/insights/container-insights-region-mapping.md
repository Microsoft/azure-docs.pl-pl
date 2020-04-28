---
title: Azure Monitor mapowania regionów kontenerów
description: W tym artykule opisano mapowania regionów obsługiwane między Azure Monitor for Containers, Log Analytics Workspace i metrykami niestandardowymi.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75403419"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapowania regionów obsługiwane przez Azure Monitor dla kontenerów

 Podczas włączania Azure Monitor dla kontenerów tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i klastra AKS oraz zbierania niestandardowych metryk przesłanych do Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapowania obsługiwane w obszarze roboczym Log Analytics

Zasoby klastra AKS lub obszar roboczy Log Analytics mogą znajdować się w innych regionach, a w poniższej tabeli przedstawiono mapowania.

|**Region klastra AKS** | **Log Analytics region obszaru roboczego** |
|-----------------------|------------------------------------|
|**Afryka** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australia** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Azja i Pacyfik** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brazylia** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Indie** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japonia** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|Wschodnie<sup>1</sup>|
|US Gov Wirginia |US Gov Wirginia |

<sup>1</sup> ze względu na ograniczenia pojemności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje obszar roboczy Log Analytics. Jednak istniejące połączone zasoby w regionie powinny być nadal wykonywane.

## <a name="custom-metrics-supported-regions"></a>Regiony niestandardowe obsługiwane przez metryki

Zbieranie metryk z węzłów i zasobników klastrów usługi Azure Kubernetes Services (AKS) są obsługiwane w przypadku publikowania jako metryki niestandardowych tylko w następujących [regionach świadczenia usługi Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra AKS, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.  
