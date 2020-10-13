---
title: Jak skonfigurować przechowywanie w środowisku — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skonfigurować przechowywanie w środowisku Azure Time Series Insightsu.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569401"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Konfigurowanie przechowywania w Azure Time Series Insights Gen1

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano sposób konfigurowania zachowania **czasu przechowywania danych** i **limitu magazynowania** w Azure Time Series Insights.

## <a name="summary"></a>Podsumowanie

Każde środowisko Azure Time Series Insights ma ustawienie służące do konfigurowania **czasu przechowywania danych**. Wartość obejmuje od 1 do 400 dni. Dane są usuwane w zależności od pojemności magazynu środowiska lub czasu trwania przechowywania (1-400), zależnie od tego, co nastąpi wcześniej.

Każde środowisko Azure Time Series Insights ma dodatkowe zachowanie podczas **przekroczenia limitu magazynowania**. To ustawienie steruje zachowaniem ruchu przychodzącego i przeczyszczania, gdy osiągnięto maksymalną pojemność środowiska. Istnieją dwa zachowania do wyboru:

- **Przeczyść stare dane** (ustawienie domyślne)
- **Wstrzymaj ruch przychodzący**

Aby uzyskać szczegółowe informacje dotyczące lepszego zrozumienia tych ustawień, przejrzyj [Informacje o przechowywaniu w Azure Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko Azure Time Series Insights. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz środowisko Azure Time Series Insights.

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja magazynu**.

    [![W obszarze Ustawienia wybierz pozycję Konfiguracja magazynu.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Wybierz **czas przechowywania danych (w dniach)** , aby skonfigurować przechowywanie przy użyciu suwaka lub wpisz liczbę w polu tekstowym.

1. Zanotuj ustawienie **wydajności** , ponieważ ta konfiguracja wpływa na maksymalną ilość zdarzeń danych i łączną pojemność magazynu do przechowywania danych.

1. Przełącz ustawienie **zachowania Przekrocz limit magazynu** . Wybierz opcję **Przeczyść stare dane** lub **Wstrzymaj** zachowanie transferu danych przychodzących.

    [![Wstrzymaj ruch przychodzący — Zaakceptuj i Zapisz.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Zapoznaj się z dokumentacją, aby zrozumieć potencjalne zagrożenia związane z utratą danych. Wybierz pozycję **Zapisz** , aby skonfigurować zmiany.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz się [, jak skalować środowisko Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Dowiedz się więcej [na temat planowania środowiska](time-series-insights-environment-planning.md).
