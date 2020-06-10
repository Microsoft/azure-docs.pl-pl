---
title: Korzystanie z usługi Azure queue storage z usługi Node.js — Azure Storage
description: Dowiedz się, jak używać usługa kolejki platformy Azure do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów. Przykłady zapisywane w Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 878c4a5028b114ad10d19c03c0239c9d7a8bc6ce
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659562"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Tworzenie i usuwanie kolejek z Node.js za pomocą usługi Azure Queue Service
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługa kolejki Microsoft Azure. Przykłady są zapisywane przy użyciu interfejsu API Node.js. Omówione scenariusze obejmują **Wstawianie**, **wgląd**, **pobieranie**i **usuwanie** komunikatów w kolejce, a także **Tworzenie i usuwanie kolejek**.

> [!IMPORTANT]
> Ten artykuł odnosi się do starszej wersji biblioteki klienta usługi Azure Storage dla języka JavaScript. Aby rozpocząć pracę z najnowszą wersją, zobacz [Szybki Start: Biblioteka kliencka usługi Azure queue storage dla języka JavaScript](storage-quickstart-queues-nodejs.md)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie aplikacji internetowej Node.js w Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [Kompilowanie i wdrażanie aplikacji Node.js w usłudze w chmurze platformy Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) przy użyciu programu Windows PowerShell lub [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu
Aby można było korzystać z usługi Azure Storage, wymagany jest zestaw SDK usługi Azure Storage dla Node.js, który obejmuje zestaw wygodnych bibliotek, które komunikują się z usługami REST magazynu.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Korzystanie z programu Node Package Manager (NPM) w celu uzyskania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak program **PowerShell** (Windows), **Terminal** (Mac,) lub **bash** (UNIX), przejdź do folderu, w którym została utworzona Przykładowa aplikacja.
2. Wpisz ciąg **npm install azure-storage** w oknie polecenia. Dane wyjściowe polecenia są podobne do poniższego przykładu.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Można ręcznie uruchomić **ls** polecenie, aby sprawdzić, czy folder ** \_ modułów węzła** został utworzony. Wewnątrz tego folderu znajduje się pakiet **azure-storage** zawierający biblioteki wymagane do uzyskiwania dostępu do magazynu.

### <a name="import-the-package"></a>Importowanie pakietu
Za pomocą Notatnika lub innego edytora tekstów Dodaj następujący tekst do pliku **server.js** aplikacji, w której zamierzasz używać magazynu:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage
Moduł Azure odczyta zmienne środowiskowe konta usługi AZURE \_ Storage \_ i \_ \_ klucza dostępu do usługi Azure Storage \_ albo \_ Parametry połączenia usługi Azure Storage, \_ \_ Aby uzyskać informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie podczas wywoływania **createQueueService**.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki
Poniższy kod tworzy obiekt **QueueService** , który umożliwia współpracę z kolejkami.

```javascript
var queueSvc = azure.createQueueService();
```

Użyj metody **createQueueIfNotExists** , która zwraca określoną kolejkę, jeśli już istnieje, lub tworzy nową kolejkę o określonej nazwie, jeśli jeszcze nie istnieje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Jeśli kolejka została utworzona, `result.created` ma wartość true. Jeśli kolejka istnieje, `result.created` ma wartość false.

### <a name="filters"></a>Filtry
Do operacji wykonywanych za pomocą **QueueService**można zastosować opcjonalne operacje filtrowania. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itd. Filtry są obiektami implementującymi metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu wstępnego przetwarzania w opcjach żądania Metoda musi wywołać polecenie "Next", przekazując wywołanie zwrotne z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W przypadku tego wywołania zwrotnego i po przetworzeniu obiektu returnobject (odpowiedź z żądania do serwera) wywołanie zwrotne musi zostać wywołane dalej, jeśli istnieje, aby kontynuować przetwarzanie innych filtrów, lub po prostu wywołaj finalCallback w przeciwnym razie, aby zakończyć wywołanie usługi.

Dwa filtry, które implementują logikę ponawiania prób, wchodzą w skład zestawu Azure SDK dla platformy Node.js: **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Poniższy element tworzy obiekt **QueueService** , który używa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, użyj metody **OnMessage** , aby utworzyć nową wiadomość i dodać ją do kolejki.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: wgląd do następnego komunikatu
Możesz uzyskać wgląd w komunikat z przodu kolejki bez usuwania go z kolejki, wywołując metodę **peekMessages** . Domyślnie **peekMessages** wgląd w jeden komunikat.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Zawiera komunikat.

> [!NOTE]
> Przy użyciu **peekMessages** , gdy nie ma żadnych komunikatów w kolejce nie zwróci błędu, ale nie zostaną zwrócone żadne komunikaty.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: dequeueing Next Message
Przetwarzanie wiadomości jest procesem dwuetapowym:

1. Usuwa komunikat z kolejki.
2. Usuń wiadomość.

Aby usunąć z kolejki komunikat, użyj elementu **GetMessages**. Powoduje to, że komunikaty są niewidoczne w kolejce, więc żaden inny klient nie będzie mógł ich przetworzyć. Gdy aplikacja przetworzy komunikat, wywołaj **deleteMessage** , aby usunąć go z kolejki. Poniższy przykład pobiera komunikat, a następnie usuwa:

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

> [!NOTE]
> Domyślnie komunikat jest ukryty przez 30 sekund, po upływie którego jest widoczny dla innych klientów. Możesz określić inną wartość za pomocą polecenia `options.visibilityTimeout` **GetMessages**.
> 
> [!NOTE]
> Przy użyciu funkcji **GetMessages** , gdy nie ma komunikatów w kolejce, nie zwróci błędu, ale nie zostaną zwrócone żadne komunikaty.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: zmienianie zawartości komunikatu w kolejce
Można zmienić zawartość wiadomości w miejscu w kolejce przy użyciu **updateMessage**. Poniższy przykład aktualizuje tekst komunikatu:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instrukcje: dodatkowe opcje związane z dekolejką komunikatów
Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki:

* `options.numOfMessages`— Pobierz partię komunikatów (do 32).
* `options.visibilityTimeout`-Ustaw dłuższy lub krótszy limit czasu niewidoczności.

W poniższym przykładzie zastosowano metodę **GetMessages** , aby pobrać 15 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limit czasu niewidoczności na pięć minut dla wszystkich komunikatów zwracanych przez tę metodę.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>Instrukcje: pobieranie długości kolejki
**GetQueueMetadata** zwraca metadane dotyczące kolejki, w tym przybliżoną liczbę komunikatów oczekujących w kolejce.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Instrukcje: Wyświetlanie listy kolejek
Aby pobrać listę kolejek, użyj **listQueuesSegmented**. Aby pobrać listę przefiltrowanych według określonego prefiksu, użyj **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Jeśli nie można zwrócić wszystkich kolejek, `result.continuationToken` można użyć jako pierwszego parametru **listQueuesSegmented** lub drugiego parametru **listQueuesSegmentedWithPrefix** , aby uzyskać więcej wyników.

## <a name="how-to-delete-a-queue"></a>Instrukcje: usuwanie kolejki
Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj metodę **deleteQueue** w obiekcie Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Aby wyczyścić wszystkie komunikaty z kolejki bez usuwania, użyj **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Instrukcje: korzystanie z sygnatur dostępu współdzielonego
Sygnatury dostępu współdzielonego (SAS) są bezpiecznym sposobem zapewnienia szczegółowego dostępu do kolejek bez podawania nazwy lub kluczy konta magazynu. Sygnatury dostępu współdzielonego są często używane, aby zapewnić ograniczony dostęp do kolejek, takich jak umożliwienie aplikacji mobilnej przesyłanie komunikatów.

Zaufana aplikacja, taka jak usługa oparta na chmurze, generuje sygnaturę dostępu współdzielonego przy użyciu **GenerateSharedAccessSignature** **QueueService**i udostępnia ją niezaufanej lub częściowo zaufanej aplikacji. Na przykład aplikacja mobilna. Sygnatura dostępu współdzielonego jest generowana przy użyciu zasad opisujących daty rozpoczęcia i zakończenia okresu, w którym ta sygnatura obowiązuje, a także poziom dostępu przyznany właścicielowi sygnatury dostępu współdzielonego.

Poniższy przykład generuje nowe zasady dostępu współdzielonego, które umożliwi posiadaczowi SAS Dodawanie komunikatów do kolejki i wygasa 100 minut po chwili utworzenia.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Należy pamiętać, że informacje o hoście muszą być również podane, ponieważ są wymagane, gdy posiadacz SAS próbuje uzyskać dostęp do kolejki.

Aplikacja kliencka następnie używa sygnatury dostępu współdzielonego z **QueueServiceWithSAS** do wykonywania operacji względem kolejki. Poniższy przykład nawiązuje połączenie z kolejką i tworzy komunikat.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Ze względu na to, że sygnatura dostępu współdzielonego została wygenerowana przy użyciu elementu Dodaj, Jeśli podjęto próbę odczytu, aktualizacji lub usunięcia komunikatów, zwracany jest błąd.

### <a name="access-control-lists"></a>Listy kontroli dostępu
Do ustawienia zasad dostępu powiązanych z sygnaturą dostępu współdzielonego można również użyć listy kontroli dostępu (ACL, Access Control List). Jest to przydatne, jeśli chcesz zezwolić wielu klientom na dostęp do kolejki, ale podać różne zasady dostępu dla każdego klienta.

Lista ACL jest implementowana przy użyciu tablicy zasad dostępu z identyfikatorem skojarzonym z poszczególnymi zasadami. W poniższym przykładzie zdefiniowano dwie zasady: jeden dla "Użytkownik1" i jeden dla "=":

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Poniższy przykład pobiera bieżącą listę ACL dla elementu **webqueue**, a następnie dodaje nowe zasady przy użyciu **setQueueAcl**. W przypadku takiego podejścia:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Po ustawieniu listy ACL można utworzyć sygnaturę dostępu współdzielonego na podstawie identyfikatora zasad. W poniższym przykładzie jest tworzona nowa sygnatura dostępu współdzielonego dla użytkownika „user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy magazynu kolejek, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Odwiedź [Blog zespołu odpowiedzialnego za usługę Azure Storage][Azure Storage Team Blog].
* Odwiedź witrynę [Azure Storage SDK dla repozytorium węzłów][Azure Storage SDK for Node] w serwisie GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Tworzenie aplikacji internetowej Node.js w usłudze Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Tworzenie i wdrażanie aplikacji Node.js do usługi w chmurze Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
