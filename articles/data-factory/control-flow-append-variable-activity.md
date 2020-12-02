---
title: Działanie dołączania zmiennej w Azure Data Factory
description: Dowiedz się, jak ustawić działanie Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 16bdd1d31440ed440faf67e939485da613e3886f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490945"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Działanie dołączania zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Użyj działania Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description (opis) | Tekst opisujący działanie działania | nie
typ | Typ działania to AppendVariable | yes
value | Literał ciągu lub wartość obiektu Expression użyta do dołączenia do określonej zmiennej | yes
variableName | Nazwa zmiennej, która zostanie zmodyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Działanie ustawiania zmiennej](control-flow-set-variable-activity.md)
