---
title: Jak używać kolejek Azure Service Bus przy użyciu języka PHP
description: W ramach tego samouczka nauczysz się tworzyć aplikacje PHP do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85341113"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Szybki Start: jak używać kolejek Service Bus przy użyciu języka PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W ramach tego samouczka nauczysz się tworzyć aplikacje PHP do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki.
    1. Zapoznaj się **overview** z krótkim omówieniem **kolejek**Service Bus. 
    2. Utwórz **przestrzeń nazw**Service Bus. 
    3. Pobierz **Parametry połączenia**. 

        > [!NOTE]
        > W tym samouczku utworzysz **kolejkę** w przestrzeni nazw Service Bus przy użyciu języka PHP. 
3. [Zestaw Azure SDK dla środowiska PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem do tworzenia aplikacji języka PHP, która uzyskuje dostęp do Blob service platformy Azure, jest odwołanie do klas w [zestawie Azure SDK dla języka PHP](https://github.com/Azure/azure-sdk-for-php) z poziomu kodu. Możesz użyć dowolnych narzędzi programistycznych do utworzenia aplikacji lub Notatnika.

> [!NOTE]
> W instalacji PHP musi być zainstalowane i włączone [rozszerzenie OpenSSL](https://php.net/openssl) .

W tym przewodniku zostaną użyte funkcje usługi, które można wywoływać z poziomu aplikacji w języku PHP lokalnie lub w kodzie uruchomionym w ramach roli sieci Web, roli procesu roboczego lub witryny internetowej platformy Azure.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienckie platformy Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Aby skorzystać z interfejsów API Service Bus Queue, wykonaj następujące czynności:

1. Odwołuje się do pliku automatycznej ładowarki przy użyciu instrukcji [require_once][require_once] .
2. Odwołuje się do dowolnych klas, które mogą być używane.

Poniższy przykład pokazuje, jak dołączyć plik automatycznej ładowarki i odwołać się do `ServicesBuilder` klasy.

> [!NOTE]
> W tym przykładzie (i innych przykładach w tym artykule) założono, że zainstalowano biblioteki klienckie PHP dla systemu Azure przez układacz. Jeśli biblioteki zostały zainstalowane ręcznie lub jako pakiet php, należy odwołać się do pliku automatycznej ładowarki **windowsazure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcja będzie zawsze wyświetlana, ale przywoływane są tylko klasy niezbędne do wykonania tego przykładu.

## <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie Service Bus
Aby utworzyć wystąpienie klienta Service Bus, należy najpierw dysponować prawidłowymi parametrami połączenia w tym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` jest zazwyczaj format `[yourNamespace].servicebus.windows.net` .

Aby utworzyć dowolnego klienta usługi platformy Azure, należy użyć `ServicesBuilder` klasy. Oto co możesz zrobić:

* Przekaż parametry połączenia bezpośrednio do niego.
* Użyj **CloudConfigurationManager (CCM)** , aby sprawdzić wiele zewnętrznych źródeł parametrów połączenia:
  * Domyślnie jest on dostarczany z obsługą jednego zewnętrznego źródła — zmienne środowiskowe
  * Możesz dodać nowe źródła, rozszerzając `ConnectionStringSource` klasę

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
Operacje zarządzania dla kolejek Service Busych można wykonywać za pośrednictwem `ServiceBusRestProxy` klasy. `ServiceBusRestProxy`Obiekt jest konstruowany za pośrednictwem `ServicesBuilder::createServiceBusService` metody fabryki z odpowiednimi parametrami połączenia, które hermetyzują uprawnienia tokenu do zarządzania nim.

Poniższy przykład pokazuje, jak utworzyć wystąpienie obiektu `ServiceBusRestProxy` i wywołać, `ServiceBusRestProxy->createQueue` Aby utworzyć kolejkę o nazwie `myqueue` w `MySBNamespace` przestrzeni nazw usługi:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Możesz użyć `listQueues` metody dla obiektów, `ServiceBusRestProxy` Aby sprawdzić, czy kolejka o określonej nazwie już istnieje w przestrzeni nazw.
> 
> 

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki Service Bus, aplikacja wywołuje `ServiceBusRestProxy->sendQueueMessage` metodę. Poniższy kod pokazuje, jak wysłać komunikat do `myqueue` kolejki utworzonej wcześniej w `MySBNamespace` przestrzeni nazw usługi.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Komunikaty wysyłane do (i odbieranych z) Service Bus kolejki są wystąpieniami klasy [BrokeredMessage][BrokeredMessage] . Obiekty [BrokeredMessage][BrokeredMessage] mają zestaw standardowych metod i właściwości, które są używane do przechowywania niestandardowych właściwości specyficznych dla aplikacji oraz treści dowolnych danych aplikacji.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Górny limit rozmiaru kolejki wynosi 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Najlepszym sposobem odbierania komunikatów z kolejki jest użycie `ServiceBusRestProxy->receiveQueueMessage` metody. Komunikaty mogą być odbierane w dwóch różnych trybach: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) i [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Ustawienie domyślne to **PeekLock**.

W przypadku korzystania z trybu [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie jest operacją pojedynczego zrzutu. oznacza to, że gdy Service Bus odbiera żądanie odczytu komunikatu w kolejce, oznacza komunikat jako używany i zwraca go do aplikacji. Tryb [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, a następnie aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięta wiadomość, która była używana przed awarią.

W domyślnym trybie [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) otrzymywanie komunikatu staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy Service Bus odbiera żądanie, znajdzie następny komunikat do użycia, zablokuje go, aby uniemożliwić innym konsumentom odbieranie go, a następnie zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania, przekazując odebrany komunikat do `ServiceBusRestProxy->deleteMessage` . Gdy Service Bus widzi `deleteMessage` wywołanie, oznaczy komunikat jako używany i usuń go z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikat przy użyciu trybu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (tryb domyślny).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlockMessage` metodę dla odebranego komunikatu (zamiast `deleteMessage` metody). Spowoduje to Service Bus odblokowanie komunikatu w kolejce i udostępnienie go do odebrania przez tę samą aplikację wykorzystującą lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli aplikacja ulega awarii), Service Bus automatycznie odblokuje komunikat i udostępni go do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed `deleteMessage` wystawieniem żądania, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *co najmniej jednym* przetwarzaniem; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, zaleca się dodanie dodatkowej logiki do aplikacji w celu obsługi dostarczania duplikatów komunikatów. Jest to często osiągane przy użyciu `getMessageId` metody komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Service Busych kolejek, zobacz sekcję [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions] , aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


