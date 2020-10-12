---
title: Działanie dołączania zmiennej w Azure Data Factory
description: Dowiedz się, jak ustawić działanie Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81414215"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Działanie dołączania zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Użyj działania Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie działania | nie
typ | Typ działania to AppendVariable | tak
value | Literał ciągu lub wartość obiektu Expression użyta do dołączenia do określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie zmodyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Działanie ustawiania zmiennej](control-flow-set-variable-activity.md)
