---
title: Jak korzystać z usługi queue storage z magazynu Ruby-Azure
description: Dowiedz się, jak używać usługa kolejki platformy Azure do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów. Przykłady zapisywane w języku Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 7270ea589d82c09081aec5d81d1cd0b50b1b8a9f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785582"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Jak używać Magazynu kolejek w języku Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze za pomocą usługi Microsoft Azure Queue Storage. Przykłady są zapisywane przy użyciu interfejsu API Ruby platformy Azure.
Omówione scenariusze obejmują **Wstawianie** , **wgląd** , **pobieranie** i **usuwanie** komunikatów w kolejce, a także **Tworzenie i usuwanie kolejek** .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Tworzenie aplikacji języka Ruby
Tworzenie aplikacji Ruby. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji Ruby w App Service w systemie Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu
Aby korzystać z usługi Azure Storage, należy pobrać i użyć pakietu platformy Azure w języku Ruby, który obejmuje zestaw wygodnych bibliotek, które komunikują się z usługami REST usługi Storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "rozwiązania Gem Install Azure" w oknie polecenia, aby zainstalować rozwiązania Gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj swojego ulubionego edytora tekstu, Dodaj następujące polecenie na początku pliku Ruby, w którym zamierzasz używać magazynu:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage
Moduł Azure odczyta zmienne środowiskowe **konta usługi Azure \_ Storage \_** i **usługi Azure \_ Storage \_ ACCESS_KEY** , aby uzyskać informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie przed użyciem **platformy Azure:: QueueService** z następującym kodem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Aby uzyskać te wartości z klasycznego konta magazynu lub konta magazynu menedżera zasobów w witrynie Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku Ustawienia po prawej stronie kliknij pozycję **Klucze dostępu** .
4. W wyświetlonym bloku Klucze dostępu widoczny będzie klucz dostępu 1 i klucz dostępu 2. Możesz użyć jednego z nich. 
5. Kliknij ikonę kopiowania, aby skopiować klucz do schowka. 

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki
Poniższy kod tworzy obiekt **Azure:: QueueService** , który umożliwia współpracę z kolejkami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Użyj metody **create_queue ()** , aby utworzyć kolejkę o określonej nazwie.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, użyj metody **create_message ()** , aby utworzyć nową wiadomość i dodać ją do kolejki.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: wgląd do następnego komunikatu
Możesz uzyskać wgląd w komunikat z przodu kolejki bez usuwania go z kolejki, wywołując metodę **wglądu \_ wiadomości ()** . Domyślnie wgląd do **\_ wiadomości ()** wgląd w jeden komunikat. Możesz również określić liczbę wiadomości, które chcesz uzyskać.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: dequeueing Next Message
Możesz usunąć komunikat z kolejki w dwóch krokach.

1. Gdy wywołujesz **listę \_ komunikatów ()** , domyślnie otrzymujesz następny komunikat w kolejce. Można również określić liczbę wiadomości, które mają zostać pobrane. Komunikaty zwrócone z **komunikatów list \_ ()** staną się niewidoczne dla innych kodów odczytujących komunikaty z tej kolejki. Limit czasu widoczności (w sekundach) jest przekazywany jako parametr.
2. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać **delete_message ()** .

Ten dwuetapowy proces usuwania komunikatu gwarantuje, że gdy kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje **komunikat usuwania \_ ()** bezpośrednio po przetworzeniu komunikatu.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: zmienianie zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. W poniższym kodzie zastosowano metodę **update_message ()** w celu zaktualizowania wiadomości. Metoda zwróci krotkę zawierającą wyskakujące potwierdzenie komunikatu kolejki oraz wartość daty czasu UTC, która reprezentuje, kiedy komunikat będzie widoczny w kolejce.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instrukcje: dodatkowe opcje związane z dekolejką komunikatów
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

1. Możesz uzyskać wsadowy komunikat.
2. Można ustawić dłuższy lub krótszy limit czasu niewidoczności, co pozwala na zwiększenie lub skrócenie czasu w celu pełnego przetworzenia poszczególnych komunikatów.

Poniższy przykład kodu używa metody **list \_ Messages ()** do pobierania 15 komunikatów w jednym wywołaniu. Następnie drukuje i usuwa każdy komunikat. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **pobierania \_ \_ metadanych kolejki ()** żąda, aby usługa kolejki zwracała przybliżoną liczbę komunikatów i metadane dotyczące kolejki.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: usuwanie kolejki
Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj metodę **delete \_ Queue ()** w obiekcie Queue.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy magazynu kolejek, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Odwiedź [Blog zespołu usługi Azure Storage](/archive/blogs/windowsazurestorage/)
* Odwiedź witrynę [Azure SDK dla repozytorium Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) w witrynie GitHub

Aby zapoznać się z porównaniem między usługą kolejki platformy Azure omówioną w tym artykule i Azure Service Bus kolejkami omówionymi w artykule [jak korzystać z kolejek Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) , zobacz kolejki [platformy azure i kolejki Service Bus — porównane i różnicowe](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)