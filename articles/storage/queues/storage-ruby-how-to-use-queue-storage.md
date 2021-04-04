---
title: Jak używać Queue Storage z magazynu Ruby-Azure
description: Dowiedz się, jak używać Queue Storage platformy Azure do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów. Przykłady zapisywane w języku Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587666"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Jak używać Queue Storage z języka Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

W tym przewodniku pokazano, jak wykonywać typowe scenariusze za pomocą usługi Microsoft Azure Queue Storage. Przykłady są zapisywane przy użyciu interfejsu API Ruby platformy Azure. Omówione scenariusze obejmują **Wstawianie**, **wgląd**, **pobieranie** i **usuwanie** komunikatów w kolejce, a także **Tworzenie i usuwanie kolejek**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Tworzenie aplikacji języka Ruby

Tworzenie aplikacji Ruby. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji Ruby w App Service w systemie Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu

Aby korzystać z usługi Azure Storage, należy pobrać i użyć pakietu platformy Azure w języku Ruby, który obejmuje zestaw wygodnych bibliotek, które komunikują się z usługami REST usługi Storage.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu

1. Użyj interfejsu wiersza polecenia, takiego jak PowerShell (system Windows), Terminal (system Mac) lub Bash (system Unix).
2. Wpisz `gem install Azure` w oknie polecenia, aby zainstalować rozwiązania Gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu

Użyj swojego ulubionego edytora tekstu, Dodaj następujące polecenie na początku pliku Ruby, w którym zamierzasz używać magazynu:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage

Moduł platformy Azure odczyta zmienne środowiskowe `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_ACCESS_KEY` informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie przed użyciem `Azure::QueueService` z następującym kodem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Aby uzyskać te wartości z klasycznego konta magazynu lub konta magazynu menedżera zasobów w witrynie Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku **Ustawienia** po prawej stronie kliknij pozycję **klucze dostępu**.
4. W wyświetlonym bloku **klucze dostępu** zobaczysz klucz dostępu 1 i klawisz dostępu 2. Możesz użyć jednego z nich.
5. Kliknij ikonę kopiowania, aby skopiować klucz do schowka.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki

Poniższy kod tworzy `Azure::QueueService` obiekt, który umożliwia współpracę z kolejkami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Użyj `create_queue()` metody, aby utworzyć kolejkę o określonej nazwie.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki

Aby wstawić komunikat do kolejki, użyj `create_message()` metody w celu utworzenia nowej wiadomości i dodania jej do kolejki.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: wgląd do następnego komunikatu

Możesz wgląd do komunikatu z przodu kolejki bez usuwania go z kolejki, wywołując `peek_messages()` metodę. Domyślnie `peek_messages()` wgląd w jeden komunikat. Możesz również określić liczbę wiadomości, które chcesz uzyskać.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: dequeueing Next Message

Możesz usunąć komunikat z kolejki w dwóch krokach.

1. Gdy wywołasz `list_messages()` , domyślnie otrzymujesz następny komunikat w kolejce. Można również określić liczbę wiadomości, które mają zostać pobrane. Komunikaty zwrócone przez nie są `list_messages()` widoczne dla żadnego innego kodu odczytującego komunikaty z tej kolejki. Limit czasu widoczności (w sekundach) jest przekazywany jako parametr.
2. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę `delete_message()` .

Ten dwuetapowy proces usuwania komunikatu gwarantuje, że gdy kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje `delete_message()` się bezpośrednio po przetworzeniu komunikatu.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: zmienianie zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Poniższy kod używa `update_message()` metody do zaktualizowania wiadomości. Metoda zwróci krotkę zawierającą wyskakujące potwierdzenie komunikatu kolejki oraz wartość czasu UTC `DateTime` , która reprezentuje, kiedy komunikat będzie widoczny w kolejce.

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

Poniższy przykład kodu używa `list_messages()` metody do pobierania 15 komunikatów w jednym wywołaniu. Następnie drukuje i usuwa każdy komunikat. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. `get_queue_metadata()`Metoda zwraca przybliżoną liczbę komunikatów i inne metadane kolejki.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `delete_queue()` metodę w obiekcie Queue.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

- Odwiedź [Blog zespołu usługi Azure Storage](/archive/blogs/windowsazurestorage/)
- Odwiedź witrynę [Azure SDK dla repozytorium Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) w witrynie GitHub

Aby zapoznać się z porównaniem Queue Storage platformy Azure omówionego w tym artykule i Azure Service Busch kolejek omówionych w temacie [How to use Service Bus Queues](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/), zobacz temat [Azure queue storage i Service Bus Queues — porównane i rozróżnienia](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
