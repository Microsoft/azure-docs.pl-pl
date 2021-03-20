---
title: Wykres metryk klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Jak skonfigurować wykres metryk i wizualizować powiązane anomalie w danych.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043157"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Instrukcje: budowanie grafu metryk do analizowania powiązanych metryk

Każda Metryka w usłudze Advisor metryk jest monitorowana osobno przez model, który uczy się od danych historycznych w celu przewidywania przyszłych trendów. Każda Metryka ma oddzielny model, który jest stosowany do niego. Jednak w niektórych przypadkach kilka metryk może się wiązać ze sobą, a anomalie muszą być analizowane w wielu metrykach. **Wykres metryk** pozwala na to. 

Jeśli na przykład masz różne strumienie telemetrii w osobnych metrykach, klasyfikator metryk będzie monitorować je osobno. Jeśli anomalie w jednej metryce powodują anomalie w innych, znalezienie tych relacji i głównej przyczyny w danych może być przydatne podczas rozwiązywania problemów. Wykres metryk umożliwia utworzenie grafu topologii wizualizacji pod kątem znalezionych anomalii. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Wybierz metrykę, aby umieścić pierwszy węzeł grafu

Kliknij kartę **metryki wykresu** na pasku nawigacyjnym. Pierwszym krokiem tworzenia wykresu metryk jest umieszczenie węzła na grafie. Wybierz strumieniowe źródło danych i metrykę w górnej części strony. Węzeł zostanie wyświetlony w dolnym panelu. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Wybierz metrykę":::

## <a name="add-a-noderelation-on-existing-node"></a>Dodaj węzeł/relację w istniejącym węźle

Następnie należy dodać inny węzeł i określić relację z istniejącymi węzłami. Wybierz istniejący węzeł i kliknij go prawym przyciskiem myszy. Zostanie wyświetlone menu kontekstowe z kilkoma opcjami. 

Kliknij pozycję **Dodaj relację** i będzie można wybrać inną metrykę i określić typ relacji między dwoma węzłami. Można również zastosować określone filtry wymiarów. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Dodaj węzeł i relację":::

Po powtórzeniu powyższych kroków będzie można grafować metryki opisujące relacje między wszystkimi powiązanymi metrykami.
**Wskazówka dotycząca kolorów węzła**
> [!TIP]
> - Po wybraniu filtru metryki i wymiaru wszystkie węzły z tą samą metryką i filtrem wymiaru na wykresie będą wyświetlane jako **<font color=blue>niebieskie</font>**.
> - Niewybrane węzły, które reprezentują metrykę grafu, będą kolorami **<font color=green>zielonymi</font>**.
> - Jeśli wystąpi anomalia w bieżącej metryce, węzeł będzie miał kolor **<font color=red>czerwony</font>**.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Wyświetlanie stanu anomalii powiązanych metryk w centrum zdarzeń

Gdy wykres metryk jest zbudowany, za każdym razem, gdy na wykresie zostanie wykryta anomalia, będzie można wyświetlić powiązane Stany anomalii i uzyskać ogólny widok zdarzenia. 

Kliknij zdarzenie w grafie i przewiń w dół do **analizy metryk krzyżowych** poniżej informacji diagnostycznych.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Wyświetl powiązane metryki i anomalie":::

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie wykrywania anomalii przy użyciu opinii](anomaly-feedback.md)
- [Diagnozuj zdarzenie](diagnose-incident.md).
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
