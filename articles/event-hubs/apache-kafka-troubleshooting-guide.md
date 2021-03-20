---
title: Rozwiązywanie problemów z usługą Azure Event Hubs dla Apache Kafka
description: W tym artykule opisano sposób rozwiązywania problemów z usługą Azure Event Hubs dla Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061431"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Apache Kafka Przewodnik rozwiązywania problemów dla Event Hubs
W tym artykule przedstawiono wskazówki dotyczące rozwiązywania problemów, które mogą wystąpić podczas korzystania z Event Hubs dla Apache Kafka. 

## <a name="server-busy-exception"></a>Wyjątek zajętości serwera
Ze względu na ograniczenie Kafka może zostać wyświetlony wyjątek "zajęty serwer". W przypadku Event Hubs klientów AMQP funkcja natychmiast zwraca wyjątek " **zajęty serwer** " podczas ograniczania usługi. Jest to równoważne komunikat "Spróbuj ponownie później". W Kafka, komunikaty są opóźnione przed ukończeniem. Długość opóźnienia jest zwracana w milisekundach, jak `throttle_time_ms` w odpowiedzi generowania/pobierania. W większości przypadków te opóźnione żądania nie są rejestrowane jako wyjątki zajęte przez serwer na Event Hubs pulpitach nawigacyjnych. Zamiast tego `throttle_time_ms` należy użyć wartości odpowiedzi jako wskaźnika, że przepływność przekroczyła przydział przyznanego przydziału.

Jeśli ruch jest zbyt długi, usługa ma następujące zachowanie:

- Jeśli opóźnienie żądania wygenerowania przekracza limit czasu żądania, Event Hubs zwraca kod błędu **naruszenia zasad** .
- Jeśli opóźnienie żądania pobrania przekracza limit czasu żądania, Event Hubs rejestruje żądanie jako ograniczone i odpowiada za pomocą pustego zestawu rekordów i nie ma kodu błędu.

[Dedykowane klastry](event-hubs-dedicated-overview.md) nie mają mechanizmów ograniczania przepustowości. Możesz korzystać ze wszystkich zasobów klastra.

## <a name="no-records-received"></a>Nie odebrano żadnych rekordów
Klienci mogą widzieć, że nie otrzymają żadnych rekordów i ciągle ponownego zrównoważenia. W tym scenariuszu klienci nie pobierają żadnych rekordów i ciągle ponownie zrównoważą. Nie ma wyjątku lub błędu, gdy wystąpi taka sytuacja, ale dzienniki Kafka pokazują, że odbiorcy będą mogli ponownie przyłączyć się do grupy i przypisywać partycje. Istnieje kilka możliwych przyczyn:

- Upewnij się, że masz `request.timeout.ms` co najmniej zalecaną wartość 60000, a wartość jest równa co `session.timeout.ms` najmniej zalecanej wartości 30000. Zbyt niska wartość tych ustawień może spowodować przekroczenie limitu czasu przez klienta, co spowoduje ponowne zrównoważenie (co spowoduje więcej limitów czasu, co może spowodować dalsze ponowne zrównoważenie itd.) 
- Jeśli Twoja konfiguracja jest zgodna z zalecanymi wartościami i nadal widzisz stałe ponowne bilansowanie, możesz otworzyć problem (Pamiętaj o uwzględnieniu całej konfiguracji w ramach problemu, aby można było pomóc debugować)!

## <a name="compressionmessage-format-version-issue"></a>Problem z wersją kompresji/komunikatu
Kafka obsługuje kompresję, a Event Hubs dla Kafka obecnie nie. Błędy wskazujące, że wersja formatu wiadomości (na przykład `The message format version on the broker does not support the request.` ) jest spowodowana próbą wysłania przez klienta skompresowanych komunikatów Kafka do naszych brokerów.

Jeśli są wymagane skompresowane dane, Kompresuj dane przed wysłaniem ich do brokerów i dekompresowanie po odebraniu jest prawidłowym obejściem. Treść komunikatu jest po prostu tablicą bajtów do usługi, więc kompresja i dekompresja po stronie klienta nie spowoduje żadnych problemów.

## <a name="unknownserverexception"></a>UnknownServerException
Użytkownik może otrzymać UnknownServerException z bibliotek klienta Kafka, podobnie jak w poniższym przykładzie: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Otwórz bilet z pomocą techniczną firmy Microsoft.  Debugowanie na poziomie debugowania i sygnatury czasowe wyjątków w formacie UTC są przydatne w debugowaniu problemu. 

## <a name="other-issues"></a>Inne problemy
Sprawdź następujące elementy, jeśli podczas korzystania z programu Kafka na Event Hubs są widoczne problemy.

- **Ruch blokowany przez zaporę** — upewnij się, że port **9093** nie jest blokowany przez zaporę.
- **TopicAuthorizationException** — Najczęstszymi przyczynami tego wyjątku są:
    - Literówka w parametrach połączenia w pliku konfiguracyjnym lub
    - Próba użycia Event Hubs dla Kafka w przestrzeni nazw warstwy podstawowej. Funkcja Event Hubs for Kafka jest [obsługiwana tylko w przypadku przestrzeni nazw warstwy standardowej i dedykowanej](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Niezgodność wersji Kafka** -Event Hubs dla ekosystemów Kafka obsługuje Kafka w wersji 1,0 i nowszych. Niektóre aplikacje korzystające z programu Kafka w wersji 0,10 i nowszych mogą czasem współdziałać ze względu na zgodność z poprzednimi wersjami protokołu Kafka, ale zdecydowanie zalecamy użycie starych wersji interfejsu API. Kafka wersje 0,9 i wcześniejsze nie obsługują wymaganych protokołów SASL i nie mogą łączyć się z Event Hubs.
- **Dziwne kodowanie w nagłówkach AMQP podczas korzystania z Kafka** -podczas wysyłania zdarzeń do centrum zdarzeń przez AMQP, wszystkie nagłówki ładunku AMQP są serializowane w kodowaniu AMQP. Kafka konsumenci nie deserializacji nagłówków z AMQP. Aby odczytać wartości nagłówka, ręcznie Dekoduj nagłówki AMQP. Alternatywnie możesz uniknąć używania nagłówków AMQP, Jeśli wiesz, że będziesz zużywać za pośrednictwem protokołu Kafka. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)w serwisie GitHub.
- **Uwierzytelnianie SASL** — Przygotowanie struktury do współpracy z protokołem uwierzytelniania SASL wymaganym przez Event Hubs może być trudniejsze. Sprawdź, czy możesz rozwiązywać problemy z konfiguracją, korzystając z zasobów platformy przy uwierzytelnianiu SASL. 

## <a name="limits"></a>Limity
Apache Kafka a Event Hubs Kafka. W większości przypadków interfejs Kafka platformy Azure Event Hubs ma takie same wartości domyślne, właściwości, kody błędów i ogólne zachowanie, które Apache Kafka wykonuje. Wystąpienia, które te dwie jawnie różnią się (lub gdzie Event Hubs nakładają limit, który nie jest Kafka) są wymienione poniżej:

- Maksymalna długość `group.id` Właściwości to 256 znaków
- Maksymalny rozmiar `offset.metadata.max.bytes` to 1024 bajtów
- Zatwierdzenia przesunięcia są ograniczone do 4 wywołań/sekundę na partycję z maksymalnym rozmiarem dziennika wewnętrznego wynoszącym 1 MB


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs i Event Hubs dla Kafka, zobacz następujące artykuły:  

- [Apache Kafka Przewodnik dla deweloperów Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka Przewodnik migracji Event Hubs](apache-kafka-migration-guide.md)
- [Często zadawane pytania — Event Hubs dla Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Zalecane konfiguracje](apache-kafka-configurations.md)
