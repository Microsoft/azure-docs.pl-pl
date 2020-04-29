---
title: Przekształcenie filtru w strumieniu danych mapowania
description: Filtrowanie wierszy przy użyciu transformacji filtru w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606438"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Przekształcenie filtru w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Przekształcenia filtru umożliwiają filtrowanie wierszy na podstawie warunku. Strumień wyjściowy zawiera wszystkie wiersze, które pasują do warunku filtrowania. Transformacja filtru jest podobna do klauzuli WHERE w SQL.

## <a name="configuration"></a>Konfiguracja

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku filtru. Aby otworzyć Konstruktora wyrażeń, kliknij niebieską ramkę. Warunek filtru musi być typu Boolean. Aby uzyskać więcej informacji na temat tworzenia wyrażenia, zobacz dokumentację programu [Expression Builder](concepts-data-flow-expression-builder.md) .

![Przekształcenie filtru](media/data-flow/filter1.png "Przekształcenie filtru")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład to transformacja filtru o nazwie `FilterBefore1960` , która przyjmuje strumień `CleanData`przychodzący. Warunek filtru jest wyrażeniem `year <= 1960`.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Przekształcenie filtru](media/data-flow/filter1.png "Przekształcenie filtru")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Następne kroki

Odfiltruj kolumny z użyciem [przekształcenia SELECT](data-flow-select.md)
