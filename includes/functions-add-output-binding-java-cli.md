---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673303"
---
W projekcie Java powiązania są zdefiniowane jako adnotacje wiążące dla metody Function. *function.jsna* pliku jest następnie generowany automatycznie na podstawie tych adnotacji.

Przejdź do lokalizacji kodu funkcji w obszarze _src/Main/Java_, Otwórz plik projektu *Functions. Java* i Dodaj następujący parametr do `run` definicji metody:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg`Parametr jest [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typem, który reprezentuje kolekcję ciągów, które są zapisywane jako komunikaty do powiązania danych wyjściowych po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue` . Parametry połączenia dla konta magazynu są ustawiane przez `connection` metodę. Zamiast parametrów połączenia należy przekazać ustawienie aplikacji, które zawiera parametry połączenia konta magazynu.

`run`Definicja metody powinna teraz wyglądać podobnie do poniższego przykładu:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```