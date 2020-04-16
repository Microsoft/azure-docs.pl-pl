---
title: Transformacja filtru w przepływie danych mapowania
description: Odfiltruj wiersze przy użyciu transformacji filtrów w przepływie danych mapowania usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413731"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformacja filtru w przepływie danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Filtr przekształca umożliwia filtrowanie wierszy na podstawie warunku. Strumień danych wyjściowych zawiera wszystkie wiersze pasujące do warunku filtrowania. Transformacja filtru jest podobna do klauzuli WHERE w języku SQL.

## <a name="configuration"></a>Konfigurowanie

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku filtru. Aby otworzyć konstruktora wyrażeń, kliknij niebieskie pole. Warunek filtru musi być typu logicznego. Aby uzyskać więcej informacji na temat tworzenia wyrażenia, zobacz dokumentację [konstruktora wyrażeń.](concepts-data-flow-expression-builder.md)

![Transformacja filtru](media/data-flow/filter1.png "Transformacja filtru")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład to transformacja filtru o nazwie, `FilterBefore1960` która ma w strumieniu `CleanData`przychodzącym . Warunkiem filtru jest `year <= 1960`wyrażenie .

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Transformacja filtru](media/data-flow/filter1.png "Transformacja filtru")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Następne kroki

Odfiltruj kolumny z [transformacją wyboru](data-flow-select.md)
