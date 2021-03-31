---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673491"
---
W projekcie Java powiązania są zdefiniowane jako adnotacje wiążące dla metody Function. *function.jsna* pliku jest następnie generowany automatycznie na podstawie tych adnotacji.

Przejdź do lokalizacji kodu funkcji w obszarze _src/Main/Java_, Otwórz plik projektu *Functions. Java* i Dodaj następujący parametr do `run` definicji metody:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametr jest [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typem, który reprezentuje kolekcję ciągów, które są zapisywane jako komunikaty do powiązania danych wyjściowych po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue` . Parametry połączenia dla konta magazynu są ustawiane przez `connection` metodę. Zamiast parametrów połączenia należy przekazać ustawienie aplikacji, które zawiera parametry połączenia konta magazynu.

`run`Definicja metody powinna teraz wyglądać podobnie do poniższego przykładu:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::