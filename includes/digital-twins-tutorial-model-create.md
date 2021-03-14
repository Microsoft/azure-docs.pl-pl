---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — instrukcje dotyczące modelu dla samouczka wiersza polecenia
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463923"
---
1. **Zaktualizuj numer wersji**, aby wskazać, że dostarczasz nowszą wersję tego modelu. W tym celu należy zmienić *1* na końcu `@id` wartości na *2*. Dowolna liczba większa niż bieżący numer wersji również będzie działała.
1. **Edytuj Właściwość**. Zmień nazwę `Humidity` właściwości na *HumidityLevel* (lub coś innego, jeśli chcesz. Jeśli używasz czegoś innego niż *HumidityLevel*, Zapamiętaj, które z nich korzystasz, i Kontynuuj, korzystając z niego, zamiast *HumidityLevel* w całym samouczku).
1. **Dodaj właściwość**. Poniżej `HumidityLevel` właściwości, która zostanie zakończona w wierszu 15, wklej następujący kod, aby dodać `RoomName` Właściwość do pokoju:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Dodaj relację**. Poniżej `RoomName` dodawanej właściwości wklej poniższy kod, aby dodać możliwość dla tego typu sznurka do formularza *zawiera* relacje z innymi bliźniaczych reprezentacji:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Gdy skończysz, zaktualizowany model powinien być zgodny z:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Pamiętaj, aby zapisać plik przed przechodzeniem.