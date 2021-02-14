---
title: Połącz z usługą GitHub
description: Użyj usługi GitHub, aby określić odwołania do jednostek Common Data Model
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372284"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Za pomocą usługi GitHub Odczytuj odwołania do jednostek Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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