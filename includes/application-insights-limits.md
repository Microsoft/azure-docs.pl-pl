---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726187"
---
Istnieją pewne ograniczenia dotyczące liczby metryk i zdarzeń dla aplikacji, czyli według klucza Instrumentacji. Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Zasób | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz więcej danych, możesz zwiększyć limit w portalu, do 1 000 GB. W przypadku pojemności większej niż 1 000 GB Wyślij wiadomość e-mail na adres AIDataCap@microsoft.com .
| Ograniczanie przepływności | 32 000 zdarzeń/sekundę | Limit jest mierzony przez minutę.
| Dzienniki przechowywania danych | [30-730 dni](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Ten zasób jest przeznaczony dla [dzienników](../articles/azure-monitor/logs/log-query-overview.md).
| Metryki przechowywania danych | 90 dni| Ten zasób jest przeznaczony dla [Eksplorator metryk](../articles/azure-monitor/essentials/metrics-charts.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/availability-multistep.md) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar elementu telemetrii | 64 kB |
| Maksymalna liczba elementów telemetrii na partię | 64 K |
| Długość nazwy właściwości i metryki | 150 | Zobacz [schematy typów](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Długość ciągu wartości właściwości | 8192  | Zobacz [schematy typów](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Długość komunikatu śledzenia i wyjątku | 32 768  | Zobacz [schematy typów](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| Przechowywanie danych [profilera](../articles/azure-monitor/app/profiler.md) | 5 dni |
| Dane [profilera](../articles/azure-monitor/app/profiler.md) wysłane dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/azure-monitor/app/pricing.md).