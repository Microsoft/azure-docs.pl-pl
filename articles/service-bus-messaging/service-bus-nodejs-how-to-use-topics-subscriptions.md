---
title: Używanie Azure Service Bus tematów z pakietem Node.js Azure/Service-Bus
description: Dowiedz się, jak używać tematów Service Bus i subskrypcji na platformie Azure z poziomu aplikacji Node.js przy użyciu pakietu Azure/Service-Bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 219132fc8a0e618cdf2561947ae3904a9e2cb310
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91300755"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Szybki Start: jak używać tematów Service Bus i subskrypcji z Node.js i pakietem Azure-SB
W ramach tego samouczka nauczysz się tworzyć Node.js aplikacje do wysyłania komunikatów do Service Bus tematu i odbierania komunikatów z subskrypcji Service Bus przy użyciu pakietu [Azure-SB](https://www.npmjs.com/package/azure-sb) . Przykłady są zapisywane w języku JavaScript i używają modułu Node.js [platformy Azure](https://www.npmjs.com/package/azure) , który wewnętrznie używa `azure-sb` pakietu.

> [!IMPORTANT]
> Pakiet [Azure-SB](https://www.npmjs.com/package/azure-sb) używa [Service Bus interfejsów API REST w czasie wykonywania](/rest/api/servicebus/service-bus-runtime-rest). Możesz uzyskać szybsze środowisko przy użyciu nowego pakietu, [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) który korzysta z szybszego [protokołu AMQP 1,0](service-bus-amqp-overview.md). 
> 
> Aby dowiedzieć się więcej na temat nowego pakietu, zobacz temat [jak korzystać z Service Bus tematów i subskrypcji z @azure/service-bus pakietami Node.js i Package](./service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md), w przeciwnym razie Kontynuuj czytanie, aby zobaczyć, jak korzystać z pakietu [platformy Azure](https://www.npmjs.com/package/azure) .

Scenariusze omówione poniżej obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Otrzymywanie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji 

Aby uzyskać więcej informacji na temat tematów i subskrypcji, zobacz sekcję [następne kroki](#next-steps) .

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md) w celu utworzenia **przestrzeni nazw** Service Bus i pobrania **parametrów połączenia**.

    > [!NOTE]
    > W tym przewodniku szybki start utworzysz **temat** i **subskrypcję** **Node.js** tematu. 

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web systemu Azure], [Node.js usługi w chmurze][Node.js Cloud Service] przy użyciu programu Windows PowerShell lub witryny sieci Web z programem WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Aby użyć Service Bus, Pobierz pakiet Node.js platformy Azure. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługami REST Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Korzystanie z programu Node Package Manager (NPM) w celu uzyskania pakietu
1. Otwórz interfejs wiersza polecenia, taki jak **PowerShell** (Windows), **Terminal** (Mac) lub **bash** (UNIX).
2. Przejdź do folderu, w którym została utworzona aplikacja przykładowa.
3. Wpisz **npm Zainstaluj platformę Azure** w oknie polecenia, co powinno spowodować następujące dane wyjściowe:

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
3. Można ręcznie uruchomić **ls** polecenie, aby sprawdzić, czy folder ** \_ modułów węzła** został utworzony. W tym folderze Znajdź pakiet **platformy Azure** , który zawiera biblioteki potrzebne do uzyskiwania dostępu do tematów Service Bus.

### <a name="import-the-module"></a>Importowanie modułu
Za pomocą Notatnika lub innego edytora tekstów Dodaj następujący tekst na początku pliku **server.js** aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie Service Bus
Moduł platformy Azure odczytuje zmienną środowiskową `AZURE_SERVICEBUS_CONNECTION_STRING` dla parametrów połączenia, które zostały uzyskane w ramach [wymagań wstępnych](#prerequisites). Jeśli potrzebujesz instrukcji dotyczących ponownego uzyskiwania parametrów połączenia, zobacz [pobieranie parametrów połączenia](service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string). Jeśli ta zmienna środowiskowa nie jest ustawiona, podczas wywoływania należy określić informacje o koncie `createServiceBusService` .

Aby zapoznać się z przykładem ustawiania zmiennych środowiskowych dla usługi w chmurze platformy Azure, zobacz [Ustawianie zmiennych środowiskowych](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Tworzenie tematu
Obiekt **ServiceBusService** umożliwia współpracę z tematami. Poniższy kod tworzy obiekt **ServiceBusService** . Dodaj ją w górnej części pliku **server.js** , po instrukcji importowania modułu platformy Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Jeśli wywołasz `createTopicIfNotExists` obiekt **ServiceBusService** , zostanie zwrócony określony temat (jeśli istnieje) lub nowy temat o określonej nazwie. Poniższy kod używa `createTopicIfNotExists` do tworzenia lub łączenia się z tematem o nazwie `MyTopic` :

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createTopicIfNotExists`Metoda obsługuje również dodatkowe opcje, które umożliwiają przesłonięcie domyślnych ustawień tematu, takich jak czas wygaśnięcia wiadomości lub maksymalny rozmiar tematu. 

W poniższym przykładzie wartość maksymalnego rozmiaru tematu jest ustawiana na 5 GB z chwilą na chwilę na żywo:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtry
Do operacji wykonywanych za pomocą **ServiceBusService**można zastosować opcjonalne operacje filtrowania. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itd. Filtry są obiektami implementującymi metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po przeprowadzeniu wstępnego przetwarzania w opcjach żądania metoda wywołuje `next` i przekazuje wywołanie zwrotne z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W przypadku tego wywołania zwrotnego i po przetworzeniu `returnObject` (odpowiedzi z żądania do serwera) wywołanie zwrotne musi być wywoływane dalej (jeśli istnieje), aby kontynuować przetwarzanie innych filtrów lub wywołać `finalCallback` zakończenie wywołania usługi.

Dwa filtry, które implementują logikę ponawiania prób, wchodzą w skład zestawu Azure SDK dla platformy Node.js: **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Poniższy kod tworzy obiekt **ServiceBusService** , który używa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematów są również tworzone za pomocą obiektu **ServiceBusService** . Subskrypcje mają nazwę i mogą mieć opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do kolejki wirtualnej subskrypcji.

> [!NOTE]
> Domyślnie subskrypcje są trwałe do momentu ich usunięcia lub tematu, z którym są skojarzone, są usuwane. Jeśli aplikacja zawiera logikę do utworzenia subskrypcji, należy najpierw sprawdzić, czy subskrypcja istnieje przy użyciu `getSubscription` metody.
>
> Subskrypcje mogą być automatycznie usuwane przez ustawienie [Właściwości AutoDeleteOnIdle](/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Filtr **MatchAll** jest filtrem domyślnym używanym podczas tworzenia subskrypcji. Kiedy używasz filtru **MatchAll**, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie AllMessages i używa domyślnego filtru **MatchAll** .

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Możesz również utworzyć filtry, które umożliwiają określenie, które komunikaty wysyłane do tematu powinny być wyświetlane w ramach subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest obiekt **sqlfilter**, który IMPLEMENTUJE podzestaw standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, zobacz składnię [sqlfilter. sqlexpression][SqlFilter.SqlExpression] .

Filtry można dodać do subskrypcji przy użyciu `createRule` metody obiektu **ServiceBusService** . Ta metoda umożliwia dodanie nowych filtrów do istniejącej subskrypcji.

> [!NOTE]
> Ponieważ domyślny filtr jest automatycznie stosowany do wszystkich nowych subskrypcji, należy najpierw usunąć filtr domyślny lub **MatchAll** zastępuje wszystkie inne filtry, które można określić. Można usunąć regułę domyślną przy użyciu `deleteRule` metody obiektu **ServiceBusService** .
>
>

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` przy użyciu **sqlfilter** , który wybiera tylko komunikaty o właściwości niestandardowej `messagenumber` większej niż 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Podobnie Poniższy przykład tworzy subskrypcję o nazwie `LowMessages` przy użyciu **sqlfilter** , który wybiera tylko komunikaty o `messagenumber` Właściwości mniejszej lub równej 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Gdy wiadomość jest teraz wysyłana do usługi `MyTopic` , jest ona dostarczana do odbiorników subskrybowanych przez `AllMessages` subskrypcję tematu i selektywnie dostarczana do odbiorników subskrybowanych w ramach `HighMessages` `LowMessages` subskrypcji tematu i (w zależności od zawartości wiadomości).

## <a name="how-to-send-messages-to-a-topic"></a>Jak wysyłać komunikaty do tematu
Aby wysłać komunikat do Service Bus tematu, aplikacja musi używać `sendTopicMessage` metody obiektu **ServiceBusService** .
Komunikaty wysyłane do tematów Service Bus są obiektami **BrokeredMessage** .
Obiekty **BrokeredMessage** mają zestaw właściwości standardowych (takich jak `Label` i `TimeToLive` ), słownik używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji i treść ciągu. Aplikacja może ustawić treść komunikatu przez przekazanie wartości ciągu do `sendTopicMessage` i wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

W poniższym przykładzie pokazano, jak wysłać pięć wiadomości testowych do `MyTopic` . `messagenumber`Wartość właściwości każdego komunikatu różni się w zależności od iteracji pętli (Ta właściwość określa, które subskrypcje otrzymują je):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (var i = 0; i < 5; i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów przechowywanych w temacie, ale istnieje limit całkowitego rozmiaru komunikatów przechowywanych w temacie. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Komunikaty są odbierane z subskrypcji przy użyciu `receiveSubscriptionMessage` metody w obiekcie **ServiceBusService** . Domyślnie komunikaty są usuwane z subskrypcji podczas ich odczytywania. Można jednak ustawić dla parametru Optional `isPeekLock` **wartość true** , aby odczytywać (wgląd) i blokować komunikat bez usuwania go z subskrypcji.

Domyślnym zachowaniem odczytywania i usuwania komunikatu w ramach operacji odbierania jest najprostszy model i sprawdza się najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku wystąpienia błędu. Aby zrozumieć to zachowanie, rozważ scenariusz, w którym odbiorca wystawia żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, wtedy, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

Jeśli `isPeekLock` parametr ma **wartość true**, odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować pominiętych komunikatów. Gdy Service Bus odbiera żądanie, znajdzie następny komunikat do użycia, zablokuje go, aby uniemożliwić innym konsumentom odbieranie go i zwraca do aplikacji.
Gdy aplikacja przetwarza komunikat (lub przechowuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie metody **deleteMessage** i przekazuje komunikat do usunięcia jako parametr. Metoda **deleteMessage** oznacza wiadomość jako zużytą i usuwa ją z subskrypcji.

W poniższym przykładzie pokazano, jak komunikaty mogą być odbierane i przetwarzane przy użyciu `receiveSubscriptionMessage` . Przykład najpierw odbiera i usuwa komunikat z subskrypcji "LowMessages", a następnie odbiera komunikat z subskrypcji "HighMessages" przy użyciu opcji "true" `isPeekLock` . Następnie usuwa komunikat przy użyciu `deleteMessage` :

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlockMessage` metodę w obiekcie **ServiceBusService** . Ta metoda powoduje, że Service Bus odblokowywanie wiadomości w ramach subskrypcji i udostępnienie jej do ponownego odebrania. W tym wystąpieniu, za pomocą tej samej aplikacji lub innej aplikacji zużywanej.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach subskrypcji. Jeśli aplikacja nie będzie mogła przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład w przypadku awarii aplikacji), Service Bus odblokować komunikat automatycznie i udostępnienie go do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed `deleteMessage` wywołaniem metody komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Takie zachowanie jest często nazywane *co najmniej raz na przetwarzanie*. Oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli scenariusz nie może tolerować zduplikowanego przetwarzania, należy dodać logikę do aplikacji, aby obsługiwała zduplikowane dostarczanie komunikatów. Można użyć właściwości **MessageID** komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe, chyba że [Właściwość AutoDeleteOnIdle](/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) jest ustawiona i należy ją jawnie usunąć za pomocą [Azure Portal][Azure portal] lub programowo.
Poniższy przykład ilustruje sposób usuwania tematu o nazwie `MyTopic` :

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy przykład pokazuje, jak usunąć subskrypcję o nazwie `HighMessages` z `MyTopic` tematu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Service Bus tematów, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].
* Dokumentacja interfejsów API dla elementu [SqlFilter][SqlFilter].
* Odwiedź witrynę [Azure SDK dla repozytorium węzłów][Azure SDK for Node] w serwisie GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create and deploy a Node.js application to Azure App Service]: ../app-service/quickstart-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

