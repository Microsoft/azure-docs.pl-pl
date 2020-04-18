---
title: Jak skonfigurować przechowywanie w środowisku — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować przechowywanie w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 524e47e5be142b720687ad48ee9407da23284bd4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605045"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurowanie przechowywania w usłudze Time Series Insights

W tym artykule opisano sposób konfigurowania **czasu przechowywania danych** i limit magazynu **przekroczone zachowanie** w usłudze Azure Time Series Insights.

## <a name="summary"></a>Podsumowanie

Każde środowisko usługi Azure Time Series Insights ma ustawienie, aby skonfigurować **czas przechowywania danych.** Wartość obejmuje od 1 do 400 dni. Dane są usuwane na podstawie pojemności magazynu środowiska lub czasu przechowywania (1-400), w zależności od tego, co nastąpi wcześniej.

Każde środowisko usługi Time Series Insights ma dodatkowe ustawienie **Limit magazynu przekroczone zachowanie**. To ustawienie kontroluje zachowanie transferu przychodzącego i przeczyszczać po osiągnięciu maksymalnej pojemności środowiska. Istnieją dwa zachowania do wyboru:

- **Czyszczenie starych danych** (domyślnie)
- **Wstrzymanie przychywania się**

Aby uzyskać szczegółowe informacje, aby lepiej zrozumieć te ustawienia, zapoznaj [się z opisem przechowywania w aplikacji Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko usługi Time Series Insights. Wybierz **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi Time Series Insights.

1. W nagłówku **Ustawienia** wybierz pozycję **Konfiguracja magazynu**.

    [![W obszarze Ustawienia wybierz pozycję Konfiguracja magazynu](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Wybierz **czas przechowywania danych (w dniach),** aby skonfigurować przechowywanie za pomocą paska suwaka lub wpisz liczbę w polu tekstowym.

1. Należy zwrócić uwagę **na ustawienie Capacity,** ponieważ ta konfiguracja ma wpływ na maksymalną ilość zdarzeń danych i całkowitą pojemność magazynu do przechowywania danych.

1. Przełącz ustawienie **zachowanie przekroczone limit magazynowania.** Wybierz **pozycję Przeczyść stare dane** lub Wstrzymaj zachowanie ruchu **przychodzącego.**

    [![Wstrzymaj ruch przychodzący - zaakceptuj i zapisz.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Przejrzyj dokumentację, aby zrozumieć potencjalne ryzyko utraty danych. Wybierz **pozycję Zapisz,** aby skonfigurować zmiany.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj [się z opisem przechowywania w aplikacji Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz [się, jak skalować środowisko usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Dowiedz się więcej o [planowaniu środowiska](time-series-insights-environment-planning.md).
