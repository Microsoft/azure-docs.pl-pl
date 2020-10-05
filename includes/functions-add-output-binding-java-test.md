---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673495"
---
## <a name="update-the-tests"></a>Aktualizowanie testów

Ponieważ Archetype również tworzy zestaw testów, należy zaktualizować te testy, aby obsłużyć nowy `msg` parametr w `run` podpisie metody.  

Przejdź do lokalizacji kodu testu w obszarze _src/test/Java_, Otwórz plik projektu *Functions. Java* i Zastąp wiersz kodu `//Invoke` następującym kodem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
