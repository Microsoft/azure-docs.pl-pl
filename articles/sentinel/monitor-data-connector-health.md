---
title: Monitoruj kondycję łączników danych za pomocą tego skoroszytu wskaźnikowego platformy Azure | Microsoft Docs
description: Użyj skoroszytu monitorowania kondycji, aby śledzić łączność i wydajność łączników danych.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656994"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Monitoruj kondycję łączników danych za pomocą tego skoroszytu wskaźnikowego platformy Azure

**Skoroszyt monitorowania kondycji łączników danych** umożliwia śledzenie kondycji, łączności i wydajności łączników danych z poziomu platformy Azure. Skoroszyt zawiera dodatkowe monitory, wykrywa anomalie i zapewnia wgląd w informacje o stanie pozyskiwania danych obszaru roboczego. Można użyć logiki skoroszytu do monitorowania ogólnej kondycji pozyskiwanych danych oraz tworzenia widoków niestandardowych i alertów opartych na regułach.

## <a name="use-the-health-monitoring-workbook"></a>Korzystanie ze skoroszytu monitorowania kondycji

1. W portalu Azure, wybierz pozycję **skoroszyty** z menu **zarządzania zagrożeniami** .

1. W galerii **skoroszytów** wprowadź wartość *kondycja* na pasku wyszukiwania, a następnie wybierz pozycję **monitorowanie kondycji zbierania danych** spośród wyników.

1. Wybierz pozycję **Wyświetl szablon** , aby użyć tego skoroszytu, lub wybierz pozycję **Zapisz** , aby utworzyć edytowalną kopię skoroszytu. Po utworzeniu kopii wybierz pozycję **Wyświetl zapisany skoroszyt**.

1. W skoroszycie najpierw wybierz **subskrypcję** i **obszar roboczy** , który chcesz wyświetlić, a następnie zdefiniuj **TimeRange** , aby filtrować dane zgodnie z potrzebami. Za pomocą przełącznika **Pokaż pomoc** można wyświetlić objaśnienie w miejscu skoroszytu.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Strona docelowa skoroszytu monitorowania kondycji łącznika danych" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

W tym skoroszycie znajdują się trzy sekcje z kartami:

1. Karta **Przegląd** przedstawia ogólny stan pozyskiwania danych w wybranym obszarze roboczym: miary wolumenu, szybkość EPS i czas ostatniego odebranego dziennika.

1. Karta **anomalie zbierania danych** pomoże w wykrywaniu anomalii w procesie zbierania danych, według tabeli i źródła danych. Każda karta przedstawia anomalie dla określonej tabeli (karta **Ogólne** zawiera kolekcję tabel). Anomalie są obliczane przy użyciu funkcji **series_decompose_anomalies ()** , która zwraca **wynik anomalii**. [Dowiedz się więcej na temat tej funkcji](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Ustaw następujące parametry dla funkcji, która ma zostać obliczona:

    - **AnomaliesTimeRange**: Ten selektor godziny dotyczy tylko widoku anomalii dotyczących zbierania danych.
    - **SampleInterval**: Przedział czasu, w którym dane są próbkowane w danym przedziale czasu. Wynik anomalii jest obliczany tylko dla danych ostatniego przedziału.
    - **PositiveAlertThreshold**: Ta wartość definiuje pozytywny próg wyniku anomalii. Akceptuje wartości dziesiętne.
    - **NegativeAlertThreshold**: Ta wartość definiuje negatywny próg wyniku anomalii. Akceptuje wartości dziesiętne.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Strona anomalie w skoroszycie monitorowania kondycji łącznika danych" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. Karta **Informacje o agencie** zawiera informacje o kondycji agentów log Analytics zainstalowanych na różnych komputerach, o tym, czy maszyna wirtualna platformy Azure, inna maszyna wirtualna w chmurze, lokalna maszyna wirtualna lub fizyczna. Można monitorować następujące elementy:

   - Lokalizacja systemowa

   - Stan pulsu i czas oczekiwania

   - Dostępna pamięć i miejsce na dysku

   - Operacje agenta

    W tej sekcji należy wybrać kartę opisującą środowisko maszyn: Wybierz kartę **maszyny zarządzane przez platformę Azure** , jeśli chcesz wyświetlić tylko maszyny zarządzane przez usługę Azure Arc. Wybierz kartę **wszystkie komputery** , aby wyświetlić maszyny zarządzane i nienależące do platformy Azure z zainstalowanym agentem log Analytics.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Strona informacji o agencie monitorowania kondycji łącznika danych" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak dołączać [dane do danych wskaźnikowych platformy Azure](quickstart-onboard.md), [łączyć się ze źródłami danych](connect-data-sources.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).