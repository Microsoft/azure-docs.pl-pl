---
title: Połącz z usługą GitHub
description: Użyj usługi GitHub, aby określić odwołania do jednostek Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 3ad666a477595b8367b388c4343df8aaed561a87
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435443"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Za pomocą usługi GitHub Odczytuj odwołania do jednostek Common Data Model

Łącznik usługi GitHub w Azure Data Factory jest używany tylko do odbierania schematu odwołań do jednostki dla formatu [Common Data Model](format-common-data-model.md) w mapowaniu przepływu danych.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi GitHub są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość **GitHub**. | tak
| userName | Nazwa użytkownika usługi GitHub | tak |
| hasło | Hasło usługi GitHub | tak |

## <a name="next-steps"></a>Następne kroki

Utwórz [źródłowy zestaw](data-flow-source.md) danych w mapowaniu przepływu danych.