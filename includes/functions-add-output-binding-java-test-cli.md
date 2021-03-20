---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673272"
---
## <a name="update-the-tests"></a>Aktualizowanie testów

Ponieważ Archetype również tworzy zestaw testów, należy zaktualizować te testy, aby obsłużyć nowy `msg` parametr w `run` podpisie metody.  

Przejdź do lokalizacji kodu testu w obszarze _src/test/Java_, Otwórz plik projektu *Functions. Java* i Zastąp wiersz kodu `//Invoke` następującym kodem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::