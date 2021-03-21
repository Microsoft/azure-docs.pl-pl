---
title: Przekształcenie filtru w strumieniu danych mapowania
description: Filtrowanie wierszy przy użyciu transformacji filtru w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84112810"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Przekształcenie filtru w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Przekształcenia filtru umożliwiają filtrowanie wierszy na podstawie warunku. Strumień wyjściowy zawiera wszystkie wiersze, które pasują do warunku filtrowania. Transformacja filtru jest podobna do klauzuli WHERE w SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfigurowanie

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

Poniższy przykład to transformacja filtru o nazwie `FilterBefore1960` , która przyjmuje strumień przychodzący `CleanData` . Warunek filtru jest wyrażeniem `year <= 1960` .

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
