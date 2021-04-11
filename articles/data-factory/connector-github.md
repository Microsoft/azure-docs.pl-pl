---
title: Połącz z usługą GitHub
description: Użyj usługi GitHub, aby określić odwołania do jednostek Common Data Model
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222116"
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