---
title: Jak korzystać z usługi Azure Queue Storage w usłudze Node.js — Azure Storage
description: Dowiedz się, jak tworzyć i usuwać kolejki za pomocą usługi Azure Queue Storage. Dowiedz się, jak wstawiać, pobierać i usuwać komunikaty przy użyciu Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803684"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Jak korzystać z usługi Azure Queue Storage w programie Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Omówienie

W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Azure Queue Storage. Przykłady są zapisywane przy użyciu interfejsu API Node.js. Omówione scenariusze obejmują Wstawianie, wgląd, pobieranie i usuwanie komunikatów w kolejce. Dowiedz się również, jak tworzyć i usuwać kolejki.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js

Aby utworzyć pustą aplikację Node.js, zobacz [Tworzenie aplikacji internetowej Node.js w Azure App Service](../../app-service/quickstart-nodejs.md), [Kompilowanie i wdrażanie aplikacji Node.js na platformie Azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) przy użyciu programu PowerShell lub [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu

[Biblioteka klienta usługi Azure Storage dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) zawiera zestaw wygodnych bibliotek, które komunikują się z usługami REST usługi Storage.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Korzystanie z programu Node Package Manager (npm) w celu uzyskania pakietu

1. Użyj interfejsu wiersza polecenia, takiego jak PowerShell (Windows), Terminal (Mac) lub bash (UNIX), przejdź do folderu, w którym została utworzona aplikacja przykładowa.

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

1. Wpisz `npm install @azure/storage-queue` w oknie wiersza polecenia.

1. Sprawdź, czy `node_modules` folder został utworzony. Wewnątrz tego folderu znajdziesz `@azure/storage-queue` pakiet, który zawiera bibliotekę kliencką potrzebną do uzyskania dostępu do magazynu.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Wpisz `npm install azure-storage` w oknie wiersza polecenia.

1. Sprawdź, czy `node_modules` folder został utworzony. W tym folderze znajdziesz `azure-storage` pakiet zawierający biblioteki potrzebne do uzyskania dostępu do magazynu.

---

### <a name="import-the-package"></a>Importowanie pakietu

Korzystając z edytora kodu, Dodaj następujący kod do pliku JavaScript w miejscu, w którym zamierzasz używać kolejek.

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Jak utworzyć kolejkę

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Poniższy kod pobiera wartość zmiennej środowiskowej o nazwie `AZURE_STORAGE_CONNECTION_STRING` i używa jej do utworzenia [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) obiektu. Ten obiekt jest następnie używany do tworzenia [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) obiektu, który umożliwia współpracę z określoną kolejką.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Jeśli kolejka już istnieje, zgłaszany jest wyjątek.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Moduł Azure odczyta zmienne środowiskowe `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_ACCESS_KEY` lub `AZURE_STORAGE_CONNECTION_STRING` informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie podczas wywoływania `createQueueService` .

Poniższy kod tworzy `QueueService` obiekt, który umożliwia współpracę z kolejkami.

```javascript
var queueSvc = azure.createQueueService();
```

Wywoływanie `createQueueIfNotExists` metody w celu utworzenia nowej kolejki o określonej nazwie lub zwrócenia kolejki, jeśli już istnieje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Jeśli kolejka została utworzona, `result.created` ma wartość true. Jeśli kolejka istnieje, `result.created` ma wartość false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak wstawić komunikat do kolejki

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Aby dodać komunikat do kolejki, wywołaj [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metodę.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Aby wstawić komunikat do kolejki, wywołaj metodę, `createMessage` Aby utworzyć nową wiadomość i dodać ją do kolejki.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Jak uzyskać wgląd w następny komunikat

Możesz uzyskać wgląd w wiadomości w kolejce bez usuwania ich z kolejki, wywołując `peekMessages` metodę.

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Domyślnie [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) wgląd w jeden komunikat. Poniższy przykład wgląd w pierwsze pięć komunikatów w kolejce. Jeśli widoczne są mniej niż pięć komunikatów, zwracane są tylko widoczne komunikaty.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Domyślnie `peekMessages` wgląd w jeden komunikat.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Zawiera komunikat.

---

Wywołanie `peekMessages` w przypadku braku komunikatów w kolejce nie zwróci błędu. Jednak żadne komunikaty nie są zwracane.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak zmienić zawartość komunikatu w kolejce

Poniższy przykład aktualizuje tekst komunikatu.

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Zmień zawartość wiadomości w miejscu w kolejce przez wywołanie [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Zmień zawartość wiadomości w miejscu w kolejce przez wywołanie `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Jak usunąć z kolejki komunikat

Dwuetapowy proces usuwania komunikatu jest procesem:

1. Pobierz komunikat.

1. Usuń wiadomość.

Poniższy przykład pobiera komunikat, a następnie usuwa go.

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Aby uzyskać komunikat, wywołaj [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metodę. To wywołanie sprawia, że komunikaty są niewidoczne w kolejce, więc żaden inny klient nie może ich przetworzyć. Gdy aplikacja przetworzy komunikat, wywołaj polecenie, [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) Aby usunąć je z kolejki.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Domyślnie komunikat jest ukryty przez 30 sekund. Po 30 sekundach jest on widoczny dla innych klientów. Możesz określić inną wartość przez ustawienie [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) podczas wywoływania `receiveMessages` .

Wywołanie `receiveMessages` w przypadku braku komunikatów w kolejce nie zwróci błędu. Jednak żadne komunikaty nie zostaną zwrócone.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Aby uzyskać komunikat, wywołaj `getMessages` metodę. To wywołanie sprawia, że komunikaty są niewidoczne w kolejce, więc żaden inny klient nie może ich przetworzyć. Gdy aplikacja przetworzy komunikat, wywołaj polecenie, `deleteMessage` Aby usunąć je z kolejki.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Domyślnie komunikat jest ukryty przez 30 sekund. Po 30 sekundach jest on widoczny dla innych klientów. Możesz określić inną wartość za pomocą polecenia `options.visibilityTimeout` with `getMessages` .

Użycie `getMessages` w przypadku braku komunikatów w kolejce nie zwróci błędu. Jednak żadne komunikaty nie zostaną zwrócone.

---

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje związane z dekolejką komunikatów

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Pobieranie partii komunikatów (do 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Ustaw dłuższy lub krótszy limit czasu niewidoczności.

W poniższym przykładzie zastosowano `receiveMessages` metodę, aby uzyskać pięć komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limit czasu niewidoczności na pięć minut dla wszystkich komunikatów zwracanych przez tę metodę.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki:

- `options.numOfMessages`: Pobieranie partii komunikatów (do 32).
- `options.visibilityTimeout`: Ustaw dłuższy lub krótszy limit czasu niewidoczności.

Poniższy przykład używa `getMessages` metody do pobierania 15 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limit czasu niewidoczności na pięć minut dla wszystkich komunikatów zwracanych przez tę metodę.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Jak uzyskać długość kolejki

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

[`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-)Metoda zwraca metadane dotyczące kolejki, w tym przybliżoną liczbę komunikatów oczekujących w kolejce.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

`getQueueMetadata`Metoda zwraca metadane dotyczące kolejki, w tym przybliżoną liczbę komunikatów oczekujących w kolejce.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Jak wyświetlić listę kolejek

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Aby pobrać listę kolejek, wywołaj polecenie [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Aby pobrać listę przefiltrowanych według określonego prefiksu, ustaw [Opcje. prefiks](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) w wywołaniu `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Aby pobrać listę kolejek, użyj `listQueuesSegmented` . Aby pobrać listę przefiltrowanych według określonego prefiksu, użyj `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Jeśli nie można zwrócić wszystkich kolejek, Przekaż `result.continuationToken` jako pierwszy parametr `listQueuesSegmented` lub drugi parametr, `listQueuesSegmentedWithPrefix` Aby uzyskać więcej wyników.

---

## <a name="how-to-delete-a-queue"></a>Jak usunąć kolejkę

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metodę dla `QueueClient` obiektu.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Aby wyczyścić wszystkie komunikaty z kolejki bez usuwania, wywołaj [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `deleteQueue` metodę w obiekcie Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Aby wyczyścić wszystkie komunikaty z kolejki bez usuwania, wywołaj `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

- Odwiedź [Blog zespołu usługi Azure Storage](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) , aby dowiedzieć się, co nowego
- Odwiedź [bibliotekę klienta usługi Azure Storage dla repozytorium JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) w serwisie GitHub
