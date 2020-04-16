---
title: Działanie sprawdzania poprawności w fabryce danych platformy Azure
description: Działanie sprawdzania poprawności nie kontynuuje wykonywania potoku, dopóki nie weryfikuje dołączonego zestawu danych z określonymi kryteriami określonymi przez użytkownika.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417933"
---
# <a name="validation-activity-in-azure-data-factory"></a>Działanie sprawdzania poprawności w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Można użyć sprawdzania poprawności w potoku, aby upewnić się, że potok kontynuuje wykonywanie tylko po zatwierdzeniu dołączone odwołanie do dołączonego zestawu danych istnieje, że spełnia określone kryteria lub limit czasu został osiągnięty.


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

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania "Sprawdzanie poprawności" | Ciąg | Tak |
type | Musi być **ustawiona**na Sprawdzanie poprawności . | Ciąg | Tak |
Dataset | Działanie zablokuje wykonanie, dopóki nie potwierdzi, że to odwołanie do tego zestawu danych istnieje i że spełnia określone kryteria lub limit czasu został osiągnięty. Zestaw danych pod warunkiem, powinny obsługiwać "MinimumSize" lub "ChildItems" właściwości. | Odwołanie do zestawu danych | Tak |
timeout | Określa limit czasu pracy działania. Jeśli nie określono żadnej wartości, wartość domyślna to 7 dni ("7.00:00:00"). Format to d.hh:mm:ss | Ciąg | Nie |
Snu | Opóźnienie w sekundach między próbami sprawdzania poprawności. Jeśli żadna wartość nie zostanie określona, wartość domyślna to 10 sekund. | Liczba całkowita | Nie |
Childitems | Sprawdza, czy folder zawiera elementy podrzędne. Można ustawić na true: Sprawdź, czy folder istnieje i czy zawiera elementy. Bloki, dopóki co najmniej jeden element jest obecny w folderze lub wartość limitu czasu zostanie osiągnięty.-false: Sprawdź, czy folder istnieje i czy jest pusty. Blokuje, dopóki folder nie będzie pusty lub do momentu osiągnięcia wartości limitu czasu. Jeśli nie określono żadnej wartości, działanie zostanie zablokowane, dopóki folder nie istnieje lub do momentu osiągnięcia limitu czasu. | Wartość logiczna | Nie |
Minimumsize | Minimalny rozmiar pliku w bajtach. Jeśli żadna wartość nie zostanie określona, wartość domyślna to 0 bajtów | Liczba całkowita | Nie |


## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
