---
title: Statystyka opóźnienia w sieci platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat statystyk opóźnienia rundy w regionach platformy Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 72168a56bfb4e08c7f44c84c773d9f6599cfa607
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91848890"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statystyka opóźnienia w sieci platformy Azure

Platforma Azure nieustannie monitoruje opóźnienia (szybkość) podstawowych obszarów swojej sieci przy użyciu wewnętrznych narzędzi do monitorowania oraz pomiarów zebranych przez [ThousandEyes](https://thousandeyes.com), a także do innych firmowych usług monitorowania syntetycznego.

## <a name="how-are-the-measurements-collected"></a>Jak zbierane są pomiary?

Pomiary opóźnienia są zbierane od agentów ThousandEyes, hostowanych w regionach chmury platformy Azure na całym świecie, które w sposób ciągły wysyłają sondy sieci między sobą w interwałach 1-minutowych. Statystyki miesięczne opóźnienia pochodzą od średniej zebranych próbek przez miesiąc.

## <a name="september-2020-round-trip-latency-figures"></a>Dane o opóźnieniu rundy z września 2020

Poniżej przedstawiono średni miesięczny czas błądzenia między regionami platformy Azure w ciągu ostatnich 30 dni (od 30 września 2020). Następujące pomiary są obsługiwane przez [ThousandEyes](https://thousandeyes.com).

[![Statystyka opóźnień między regionami platformy Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
