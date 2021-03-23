---
title: Działanie walidacji w Azure Data Factory
description: Działanie sprawdzania poprawności nie kontynuuje wykonywania potoku, dopóki nie zostanie zweryfikowany dołączony zestaw danych z określonymi kryteriami, które użytkownik określi.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 2c5208f754e66f92cf5019fdad3026decac88284
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785958"
---
# <a name="validation-activity-in-azure-data-factory"></a>Działanie walidacji w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Można użyć walidacji w potoku, aby upewnić się, że potok będzie nadal wykonywany po zweryfikowaniu, że istnieje odwołanie do dołączonego zestawu danych, że spełnia określone kryteria lub został osiągnięty limit czasu.


## <a name="syntax"></a>Składnia

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania "Walidacja" | Ciąg | Tak |
typ | Musi być ustawiona na wartość  **Walidacja**. | Ciąg | Tak |
zestawu | Działanie będzie blokować wykonywanie do momentu zweryfikowania, czy odwołanie do tego zestawu danych istnieje i że spełnia określone kryteria, lub został osiągnięty limit czasu. Dostarczony zestaw danych powinien obsługiwać Właściwość "MinimumSize" lub "ChildItems". | Odwołanie do zestawu danych | Tak |
timeout | Określa limit czasu pracy działania. Jeśli wartość nie jest określona, wartość domyślna to 7 dni ("7.00:00:00"). Format to d. hh: mm: SS | Ciąg | Nie |
chodzenia | Opóźnienie w sekundach między próbami walidacji. Jeśli wartość nie jest określona, wartość domyślna to 10 sekund. | Liczba całkowita | Nie |
childItems | Sprawdza, czy folder ma elementy podrzędne. Można ustawić na wartość-true: Sprawdź, czy folder istnieje i czy zawiera elementy. Bloki do momentu, gdy co najmniej jeden element jest obecny w folderze lub wartość limitu czasu zostanie osiągnięta.-false: Sprawdź, czy folder istnieje i czy jest pusty. Bloki do momentu, gdy folder jest pusty lub zostanie osiągnięta wartość limitu czasu. Jeśli wartość nie zostanie określona, działanie zostanie zablokowane do momentu, aż folder istnieje lub zostanie osiągnięty limit czasu. | Wartość logiczna | Nie |
minimumSize | Minimalny rozmiar pliku w bajtach. Jeśli wartość nie jest określona, wartość domyślna to 0 bajtów. | Liczba całkowita | Nie |


## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
