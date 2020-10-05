---
title: Korzystanie z kolejek Azure Service Bus w Node.js przy użyciu pakietu Azure-SB
description: Dowiedz się, jak tworzyć Node.js aplikacje do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus przy użyciu pakietu Azure-SB.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 0e94d21bd6550a7d62ef3e7dec302c53f9851a4a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326324"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Szybki Start: korzystanie z kolejek Service Bus na platformie Azure z Node.js i pakietem Azure-SB
W ramach tego samouczka nauczysz się tworzyć Node.js aplikacje do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus przy użyciu pakietu [Azure-SB](https://www.npmjs.com/package/azure-sb) . Przykłady są zapisywane w języku JavaScript i używają modułu Node.js [platformy Azure](https://www.npmjs.com/package/azure) , który wewnętrznie używa pakietu Azure-SB.

> [!IMPORTANT]
> Pakiet [Azure-SB](https://www.npmjs.com/package/azure-sb) używa [Service Bus interfejsów API REST w czasie wykonywania](/rest/api/servicebus/service-bus-runtime-rest). Możesz uzyskać szybsze środowisko przy użyciu nowego, [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) który korzysta z szybszego [protokołu AMQP 1,0](service-bus-amqp-overview.md). 
> 
> Aby dowiedzieć się więcej na temat nowego pakietu, zobacz [jak używać kolejek Service Bus z Node.js i @azure/service-bus pakietem](./service-bus-nodejs-how-to-use-queues-new-package.md). w przeciwnym razie Kontynuuj odczytywanie, aby zobaczyć, jak korzystać z pakietu [platformy Azure](https://www.npmjs.com/package/azure) .

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki.
    1. Zapoznaj się **overview** z krótkim omówieniem **kolejek**Service Bus. 
    2. Utwórz **przestrzeń nazw**Service Bus. 
    3. Pobierz **Parametry połączenia**. 

        > [!NOTE]
        > **Kolejka** zostanie utworzona w przestrzeni nazw Service Bus przy użyciu Node.js w tym samouczku. 
 

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure][Create and deploy a Node.js application to an Azure Website]lub [Node.js usługi w chmurze][Node.js Cloud Service] przy użyciu programu Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Aby użyć Azure Service Bus, Pobierz pakiet Node.js platformy Azure i użyj go. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługami REST Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Korzystanie z programu Node Package Manager (NPM) w celu uzyskania pakietu
1. Za pomocą okna polecenia programu **Windows PowerShell dla Node.js** przejdź do folderu **c: \\ Node \\ sbqueues \\ WebRole1** , w którym została utworzona aplikacja przykładowa.
2. W oknie polecenia wpisz **npm Zainstaluj platformę Azure** , co powinno skutkować wyjściem podobnym do poniższego przykładu:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Możesz ręcznie uruchomić polecenie **ls**, aby sprawdzić, czy utworzono folder **node_modules**. W tym folderze Znajdź pakiet **platformy Azure** , który zawiera biblioteki potrzebne do uzyskiwania dostępu do kolejek Service Bus.

### <a name="import-the-module"></a>Importowanie modułu
Za pomocą Notatnika lub innego edytora tekstów Dodaj następujący tekst na początku pliku **server.js** aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Skonfiguruj połączenie Azure Service Bus
Moduł platformy Azure odczytuje zmienną środowiskową `AZURE_SERVICEBUS_CONNECTION_STRING` w celu uzyskania informacji wymaganych do nawiązania połączenia z Service Bus. Jeśli ta zmienna środowiskowa nie jest ustawiona, podczas wywoływania należy określić informacje o koncie `createServiceBusService` .

Aby zapoznać się z przykładem ustawiania zmiennych środowiskowych w [Azure Portal][Azure portal] witryny sieci Web platformy Azure, zobacz [Node.js aplikacji sieci Web z magazynem][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Tworzenie kolejki
Obiekt **ServiceBusService** umożliwia współpracę z kolejkami Service Bus. Poniższy kod tworzy obiekt **ServiceBusService** . Dodaj ją w górnej części pliku **server.js** , po instrukcji importowania modułu platformy Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Wywołanie `createQueueIfNotExists` obiektu **ServiceBusService** powoduje zwrócenie określonej kolejki (jeśli istnieje) lub utworzenie nowej kolejki z określoną nazwą. Następujący kod używa `createQueueIfNotExists` do tworzenia lub łączenia z kolejką o nazwie `myqueue` :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService`Metoda obsługuje również dodatkowe opcje, które umożliwiają przesłonięcie domyślnych ustawień kolejki, takich jak czas wygaśnięcia komunikatów lub maksymalny rozmiar kolejki. W poniższym przykładzie ustawiono maksymalny rozmiar kolejki na 5 GB, a wartość czasu wygaśnięcia (TTL) wynosząca 1 minuta:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtry
Do operacji wykonywanych za pomocą **ServiceBusService**można zastosować opcjonalne operacje filtrowania. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itd. Filtry są obiektami implementującymi metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu wstępnego przetwarzania na temat opcji żądania Metoda musi wywołać metodę `next` , przekazując wywołanie zwrotne z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W przypadku tego wywołania zwrotnego i po przetworzeniu `returnObject` (odpowiedzi z żądania do serwera) wywołanie zwrotne musi zostać wywołane, `next` Jeśli istnieje, aby kontynuować przetwarzanie innych filtrów lub wywołać `finalCallback` , co spowoduje zakończenie wywołania usługi.

Dwa filtry, które implementują logikę ponowień, są dołączone do zestawu Azure SDK dla Node.js `ExponentialRetryPolicyFilter` i `LinearRetryPolicyFilter` . Poniższy kod tworzy `ServiceBusService` obiekt, który używa `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki Service Bus, aplikacja wywołuje `sendQueueMessage` metodę dla obiektu **ServiceBusService** . Komunikaty wysyłane do (i odbieranych z) Service Bus kolejki są obiektami **BrokeredMessage** i mają zestaw właściwości standardowych (takich jak **etykieta** i **TimeToLive**), słownik używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja może ustawić treść komunikatu przez przekazanie ciągu jako wiadomości. Wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

W poniższym przykładzie pokazano, jak wysłać wiadomość testową do kolejki o nazwie `myqueue` using `sendQueueMessage` :

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów przechowywanych w kolejce, ale istnieje limit całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Komunikaty są odbierane z kolejki przy użyciu `receiveQueueMessage` metody w obiekcie **ServiceBusService** . Domyślnie komunikaty są usuwane z kolejki podczas ich odczytywania; można jednak odczytywać (wgląd) i blokować komunikat bez usuwania go z kolejki przez ustawienie opcjonalnego parametru `isPeekLock` na **true**.

Domyślnym zachowaniem odczytywania i usuwania komunikatu w ramach operacji odbierania jest najprostszy model i sprawdza się najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzać komunikatu w przypadku wystąpienia błędu. Aby zrozumieć to zachowanie, rozważ scenariusz, w którym odbiorca wystawia żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, a następnie aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

Jeśli `isPeekLock` parametr ma **wartość true**, odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie `deleteMessage` metody i podanie komunikatu, który ma zostać usunięty jako parametr. `deleteMessage`Metoda oznacza komunikat jako używany i usuwa go z kolejki.

W poniższym przykładzie pokazano, jak odbierać i przetwarzać komunikaty przy użyciu programu `receiveQueueMessage` . Przykład najpierw odbiera i usuwa komunikat, a następnie odbiera komunikat przy użyciu wartości `isPeekLock` **true**, a następnie usuwa komunikat przy użyciu `deleteMessage` :

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlockMessage` metodę w obiekcie **ServiceBusService** . spowoduje to Service Bus odblokowanie komunikatu w kolejce i udostępnienie go do odebrania przez tę samą aplikację lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli aplikacja ulega awarii), Service Bus automatycznie odblokuje komunikat i udostępni go do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed `deleteMessage` wywołaniem metody, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Ta metoda jest często nazywana *co najmniej raz, co*oznacza, że każdy komunikat zostanie przetworzony co najmniej jeden raz, ale w pewnych sytuacjach ten sam komunikat może zostać ponownie dostarczony. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, deweloperzy aplikacji powinni dodać do aplikacji dodatkową logikę do obsługi powielonego dostarczania komunikatów. Jest to często osiągane przy użyciu właściwości **MessageID** komunikatu, która pozostanie stała między kolejnymi próbami dostarczenia.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o kolejkach, zobacz następujące zasoby.

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]
* [Zestaw Azure SDK dla repozytorium węzłów][Azure SDK for Node] w serwisie GitHub
* [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/quickstart-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md