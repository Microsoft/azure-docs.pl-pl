---
title: Statystyka opóźnienia podróży sieci platformy Azure | Microsoft Docs
description: Dowiedz się więcej o statystyce opóźnienia błądzenia między regionami platformy Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994015"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statystyka opóźnienia podróży sieci platformy Azure

Platforma Azure nieustannie monitoruje opóźnienia (szybkość) podstawowych obszarów swojej sieci przy użyciu wewnętrznych narzędzi do monitorowania oraz pomiarów zebranych przez [ThousandEyes](https://thousandeyes.com), a także do innych firmowych usług monitorowania syntetycznego.

## <a name="how-are-the-measurements-collected"></a>Jak zbierane są pomiary?

Pomiary opóźnienia są zbierane od agentów ThousandEyes, hostowanych w regionach chmury platformy Azure na całym świecie, które w sposób ciągły wysyłają sondy sieci między sobą w interwałach 1-minutowych. Statystyki miesięczne opóźnienia pochodzą od średniej zebranych próbek przez miesiąc.

## <a name="april-2020-round-trip-latency-figures"></a>Kwoty opóźnienia podróży w kwietniu 2020

Poniżej przedstawiono średni miesięczny czas błądzenia między regionami platformy Azure w ciągu ostatnich 30 dni (od 30 kwietnia 2020). Następujące pomiary są obsługiwane przez [ThousandEyes](https://thousandeyes.com).

[![Statystyka opóźnień między regionami platformy Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
