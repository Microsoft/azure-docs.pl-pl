---
title: Schemat metadanych wnioskowania — Azure
description: W tym artykule przedstawiono informacje o schemacie metadanych wnioskowania.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 6239713fc92b75b8ed026a8f04953e92a24c4596
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691860"
---
# <a name="inference-metadata-schema"></a>Schemat metadanych wnioskowania 

Każdy obiekt wnioskowania bez względu na użycie umowy opartej na protokole HTTP lub kontraktu opartego na gRPC następuje po modelu obiektów opisanym w tym temacie.

## <a name="object-model"></a>Model obiektów

![Model obiektów](./media/inference-metadata-schema/object-model.png)
 
|Definicja typu|Opis|
|---|---|
|Tag|Tag lub etykieta skojarzona z wynikiem. Alng przy użyciu tagowania, możesz uzyskać wartość ufności skojarzoną ze znacznikiem.|
|Atrybut|Dodatkowe atrybuty skojarzone z wynikiem. Można dodać nowe atrybuty otrzymane z aparatu inferencing wraz z wartością ufności.|
|Lista atrybutów|Lista opcjonalnych atrybutów.|
|Prostokąt|Prostokątny region względem lewego górnego rogu obrazu. Wymagane właściwości będą mieć wartość "Length", "width", Height "i" górną odległość od początku ".|
|Klasyfikacja|Etykieta klasyfikatora często stosowana do całego przykładu. Za pomocą "tagu" można sklasyfikować wynik.|
|Jednostka|Obiekt został wykryty lub zidentyfikowany w próbce. Gdy obiekt jest wykrywany przez aparat inferencing, pobierany jest "tag", dodatkowe atrybuty, które zostały wywnioskowane, a współrzędnych prostokątnego pola wokół znalezionej znalezionej jednostki jest zwracana.|
|Wydarzenie|Wykryto zdarzenie w próbce. W przypadku wykrycia zdarzenia w przykładzie zostanie zwrócona nazwa zdarzenia i właściwości specyficzne dla zdarzenia.|
|Ruchu|Wykryto ruch w próbce. Gdy ruch zostanie wykryty w próbce, jest zwracana granica prostokątnego pola ograniczenia, w którym wykryto ruch.|
|Tekst|Zwracany jest tekst skojarzony z próbką wraz z początkową i końcową sygnaturą czasową tekstu.|
|Inne|Zwraca inne informacje o ładunku ogólnym.|

Poniższy przykład zawiera jedno zdarzenie z obsługiwanymi typami wnioskowania:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Następne kroki

- [Kontrakt danych gRPC](grpc-data-contract.md)
- [Kontrakt danych HTTP](http-data-contract.md)
