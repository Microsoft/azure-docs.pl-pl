---
author: baanders
description: Dołączanie pliku do usługi Azure Digital bliźniaczych reprezentacji — kod w celu utworzenia sznurka
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2020
ms.author: baanders
ms.openlocfilehash: 43d9ee0094890c7eeade99ee3426e1f0a016263c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145945"
---
```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
//Create the twin
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```