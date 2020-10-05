---
title: Przekształcenie rangi w mapowaniu przepływu danych
description: Jak używać transformacji rangi przepływu danych mapowania Azure Data Factory generowanie kolumny klasyfikacji
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: cb3a8c1c6df61a4a20ce596e3ff1118c2870033a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716520"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Przekształcenie rangi w mapowaniu przepływu danych 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj transformacji rangi, aby wygenerować uporządkowaną klasyfikację na podstawie warunków sortowania określonych przez użytkownika. 

## <a name="configuration"></a>Konfigurowanie

![Ustawienia rangi](media/data-flow/rank-configuration.png "Ustawienia rangi")

Bez **uwzględniania wielkości liter:** Jeśli kolumna sortowania jest typu String, wielkość liter zostanie uwzględniona w klasyfikacji. 

**Gęste:** Jeśli ta funkcja jest włączona, ranga kolumny rangi będzie gęsta. Każda liczba rangi będzie taka sama jak liczba, a wartości rangi nie będą pomijane po elemencie równości.

**Kolumna rangi:** Nazwa wygenerowanej kolumny rangi. Ta kolumna będzie typu Long.

**Warunki sortowania:** Wybierz kolumny, według których ma być wykonywane sortowanie, i kolejność sortowania. Kolejność określa priorytet sortowania.

Powyższa konfiguracja pobiera przychodzące dane Basketball i tworzy kolumnę rangi o nazwie "pointsRanking". Wiersz o najwyższej *wartości kolumny nr* , będzie miał wartość *pointsRanking* równą 1.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Przykład

![Ustawienia rangi](media/data-flow/rank-configuration.png "Ustawienia rangi")

Skrypt przepływu danych dla powyższej konfiguracji rangi znajduje się w poniższym fragmencie kodu.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Następne kroki

Filtrowanie wierszy na podstawie wartości rangi przy użyciu [transformacji filtru](data-flow-filter.md).