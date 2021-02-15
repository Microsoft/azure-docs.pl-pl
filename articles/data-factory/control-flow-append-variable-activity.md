---
title: Działanie dołączania zmiennej w Azure Data Factory
description: Dowiedz się, jak ustawić działanie Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383418"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Działanie dołączania zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Użyj działania Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description (opis) | Tekst opisujący działanie działania | nie
typ | Typ działania to AppendVariable | tak
wartość | Literał ciągu lub wartość obiektu Expression użyta do dołączenia do określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie zmodyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Działanie ustawiania zmiennej](control-flow-set-variable-activity.md)
