---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739822"
---
Ponieważ Archetype również tworzy zestaw testów, należy zaktualizować te testy, aby obsłużyć nowy `msg` parametr w `run` podpisie metody.  

Przejdź do lokalizacji kodu testu w obszarze _src/test/Java_, Otwórz plik projektu *Functions. Java* i Zastąp wiersz kodu `//Invoke` następującym kodem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
