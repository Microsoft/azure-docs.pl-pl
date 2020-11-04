---
title: Jak używać magazynu kolejek w języku PHP — Azure Storage
description: Dowiedz się, jak używać usługi Azure queue storage do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów. Przykłady są zapisywane w języku PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 0e5b7ed75f22659a9a38ac761cc61c841102a067
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345843"
---
# <a name="how-to-use-queue-storage-from-php"></a>Jak używać Magazynu kolejek w języku PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

W tym przewodniku pokazano, jak wykonywać typowe scenariusze za pomocą usługi Azure queue storage. Przykłady są zapisywane za pośrednictwem klas z [biblioteki klienta usługi Azure Storage dla języka PHP][download]. Scenariusze objęte usługą obejmują Wstawianie, wgląd, pobieranie i usuwanie komunikatów w kolejce, a także tworzenie i usuwanie kolejek.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP

Jedynym wymaganiem do utworzenia aplikacji PHP, która uzyskuje dostęp do usługi Azure queue storage, jest odwołanie do klas w [bibliotece klienta usługi Azure Storage dla języka PHP][download] z poziomu kodu. Do utworzenia aplikacji możesz użyć dowolnych narzędzi programistycznych, w tym programu Notatnik.

W tym przewodniku należy użyć funkcji usługi magazynu kolejek, które mogą być wywoływane lokalnie w aplikacji PHP lub w kodzie uruchomionym w aplikacji sieci Web na platformie Azure.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienckie platformy Azure

### <a name="install-via-composer"></a>Instalowanie przez układacz

1. Utwórz plik o nazwie **composer.js** w folderze głównym projektu i Dodaj do niego następujący kod:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Pobierz **[układacz. PHAR][composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i wykonaj następujące polecenie w katalogu głównym projektu

    ```
    php composer.phar install
    ```

Alternatywnie przejdź do [biblioteki klienta php usługi Azure Storage][download] w witrynie GitHub, aby sklonować kod źródłowy.

## <a name="configure-your-application-to-access-queue-storage"></a>Skonfiguruj aplikację do magazynu kolejki dostępu

Aby używać interfejsów API dla usługi Azure queue storage, należy wykonać następujące:

1. Odwołuje się do pliku automatycznej ładowarki przy użyciu instrukcji [require_once] .
2. Odwołuje się do dowolnych klas, które mogą być używane.

Poniższy przykład pokazuje, jak uwzględnić plik automatycznej ładowarki i odwołać się do klasy **QueueRestProxy** .

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

W poniższych przykładach `require_once` instrukcja jest wyświetlana zawsze, ale przywoływane są tylko klasy, które są niezbędne do wykonania przykładu.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage

Aby utworzyć wystąpienie klienta usługi Azure queue storage, należy najpierw dysponować prawidłowymi parametrami połączenia. Format parametrów połączenia usługi kolejki jest następujący:.

Aby uzyskać dostęp do usługi Live:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Aby uzyskać dostęp do magazynu emulatora:

```php
UseDevelopmentStorage=true
```

Aby utworzyć klienta usługa kolejki platformy Azure, należy użyć klasy **QueueRestProxy** . Można użyć jednej z następujących technik:

- Przekaż parametry połączenia bezpośrednio do niego.
- Użyj zmiennych środowiskowych w aplikacji sieci Web do przechowywania parametrów połączenia. Aby skonfigurować parametry połączenia, zobacz dokument [Ustawienia konfiguracji aplikacji sieci Web platformy Azure](../../app-service/configure-common.md) .

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Obiekt **QueueRestProxy** umożliwia utworzenie kolejki przy **użyciu metody.** Podczas tworzenia kolejki można ustawić opcje dla kolejki, ale nie jest to wymagane. (W poniższym przykładzie pokazano, jak ustawić metadane w kolejce).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> W przypadku kluczy metadanych nie należy polegać na rozróżnianiu wielkości liter. Wszystkie klucze są odczytywane z usługi małymi literami.

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Aby dodać komunikat do kolejki, użyj **QueueRestProxy->OnMessage**. Metoda przyjmuje nazwę kolejki, tekst komunikatu i Opcje wiadomości (opcjonalne).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu

Możesz uzyskać wgląd w komunikat (lub komunikaty) na początku kolejki bez usuwania jej z kolejki, wywołując **QueueRestProxy->peekMessages**. Domyślnie metoda **peekMessage** zwraca pojedynczy komunikat, ale można zmienić tę wartość za pomocą metody **PeekMessagesOptions->setNumberOfMessages** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Usunięcie następnego komunikatu z kolejki

Kod usuwa komunikat z kolejki w dwóch krokach. Najpierw należy wywołać **QueueRestProxy->listMessages** , co sprawia, że komunikat jest niewidoczny dla każdego innego kodu, który odczytuje z kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. (Jeśli wiadomość nie zostanie usunięta w tym okresie, zostanie ona ponownie widoczna w kolejce). Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać **QueueRestProxy->deleteMessage**. Ten dwuetapowy proces usuwania komunikatu gwarantuje, że gdy kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje **deleteMessage** bezpośrednio po przetworzeniu komunikatu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w miejscu w kolejce, wywołując **QueueRestProxy->updateMessage**. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki z nową zawartością i ustawia limit czasu widoczności, aby zwiększyć kolejny 60 sekund. Spowoduje to zapisanie stanu pracy skojarzonej z wiadomością i umożliwia klientowi kolejną minutę kontynuowanie pracy nad komunikatem. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Dodatkowe opcje do usuwania komunikatów z kolejkowania

Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu widoczności, co pozwala na zwiększenie lub skrócenie czasu w celu pełnego przetworzenia poszczególnych komunikatów. Poniższy przykład kodu używa metody **GetMessages** do pobierania 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli **for** . Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **QueueRestProxy->getQueueMetadata** żąda, aby usługa kolejki zwracała metadane dotyczące kolejki. Wywołanie metody **getApproximateMessageCount** w zwracanym obiekcie zapewnia liczbę komunikatów w kolejce. Licznik jest tylko przybliżony, ponieważ komunikaty mogą być dodawane lub usuwane po udzieleniu odpowiedzi przez usługę kolejki na żądanie.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj metodę **QueueRestProxy->deleteQueue** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy usługi Azure queue storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu:

- Odwiedź stronę [referencyjną interfejsu API dla biblioteki klienta php usługi Azure Storage](https://azure.github.io/azure-storage-php/)
- Zapoznaj się z [przykładem zaawansowanej kolejki](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Aby uzyskać więcej informacji, zobacz również [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
