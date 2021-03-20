---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78190923"
---
W projekcie biblioteki klas języka C# powiązania są zdefiniowane jako atrybuty powiązania w metodzie funkcji. *function.jsw* pliku wymaganym przez funkcje są generowane automatycznie na podstawie tych atrybutów.

Otwórz plik projektu *HttpExample. cs* i Dodaj następujący parametr do `Run` definicji metody:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametr jest `ICollector<T>` typem, który reprezentuje kolekcję komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue` . Parametry połączenia dla konta magazynu są ustawiane przez `StorageAccountAttribute` . Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i może być stosowane na poziomie klasy, metody lub parametru. W takim przypadku można pominąć, `StorageAccountAttribute` ponieważ jest już używane domyślne konto magazynu.

Definicja metody Run powinna teraz wyglądać następująco:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
