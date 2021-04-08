---
title: Co to jest usługa Azure Event Hubs? — usługa pozyskiwania danych big data | Microsoft Docs
description: Informacje na temat usługi Azure Event Hubs, usługi strumieniowego przesyłania danych big data, umożliwiającej pozyskiwanie milionów zdarzeń na sekundę.
ms.topic: overview
ms.date: 01/13/2021
ms.openlocfilehash: 36eeb38d9ed1696c9524ae9b346065756ce49c46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98195770"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Usługa Azure Event Hubs — platforma przesyłania strumieniowego danych big data i usługa pozyskiwania zdarzeń
Usługa Azure Event Hubs to platforma przesyłania strumieniowego danych big data i usługa pozyskiwania zdarzeń. Może odbierać i przetwarzać miliony zdarzeń na sekundę. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania.

Następujące scenariusze to niektóre z możliwych zastosowań usługi Event Hubs:

- Wykrywanie anomalii (oszustwa/wartości odstające)
- Rejestrowanie aplikacji
- Potoki analiz, na przykład w analizie strumienia kliknięć
- Tworzenie dynamicznych pulpitów nawigacyjnych
- Archiwizowanie danych
- Przetwarzanie transakcji
- Przetwarzanie dane telemetrycznych o użytkownikach
- Strumieniowe przesyłanie danych telemetrycznych z urządzenia

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Dlaczego warto korzystać z usługi Event Hubs?

Dane mają wartość tylko wtedy, gdy można łatwo przetwarzać dane z różnych źródeł i formułować na ich podstawie aktualne wnioski. Usługa Event Hubs to rozproszona platforma do przetwarzania strumieniowego z małymi opóźnieniami, którą można bezproblemowo integrować z usługami danych i analiz na platformie Azure i poza nią w celu utworzenia pełnego potoku danych big data.

Usługa Event Hubs stanowi „drzwi wejściowe” dla potoku zdarzeń i jest często nazywana *systemem zbierania zdarzeń* w architekturach rozwiązań. System zbierania zdarzeń to składnik lub usługa, która znajduje się między wydawcami zdarzeń, a ich odbiorcami. Jego rolą jest oddzielenie procesu tworzenia strumienia zdarzeń od użycia tych zdarzeń. Usługa Event Hubs udostępnia ujednoliconą platformę przesyłania strumieniowego z buforem przechowywana czasowego, co umożliwia uniezależnienie wydawców i odbiorców zdarzeń.

W poniższych sekcjach opisano najważniejsze funkcje usługi Azure Event Hubs:

## <a name="fully-managed-paas"></a>W pełni zarządzana usługa PaaS

Event Hubs to w pełni zarządzana usługa PaaS (Platform-as-a-Service), która wymaga niewielkich nakładów na konfigurację i zarządzanie, dzięki czemu możesz skoncentrować się na swoich rozwiązaniach biznesowych. [Usługa Event Hubs dla ekosystemów platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) zapewnia środowisko platformy Kafka jako usługę bez konieczności konfigurowania i uruchamiania klastrów ani zarządzania nimi.

## <a name="support-for-real-time-and-batch-processing"></a>Obsługa przetwarzania wsadowego w czasie rzeczywistym

Możesz pozyskiwać, buforować, przechowywać i przetwarzać strumień danych w czasie rzeczywistym, aby uzyskiwać praktyczne informacje. W usłudze Event Hubs jest używany [partycjonowany model odbiorców](event-hubs-scalability.md#partitions), umożliwiający wielu aplikacjom jednoczesne przetwarzanie strumienia danych, podczas gdy Ty kontrolujesz szybkość przetwarzania.

Funkcja [Capture](event-hubs-capture-overview.md) umożliwia przechwytywanie danych niemal w czasie rzeczywistym do usługi [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)  na potrzeby przechowywania długoterminowego lub przetwarzania mikropartii. Jest to możliwe w ramach tego samego strumienia, który jest używany do analizy w czasie rzeczywistym. Przechwytywanie danych zdarzeń można szybko skonfigurować. Używanie go nie wiąże się z żadnymi kosztami administracyjnymi, a ponadto jest ono automatycznie skalowane za pomocą  [jednostek przepływności](event-hubs-scalability.md#throughput-units) usługi Event Hubs. Usługa Event Hubs pozwala Ci skoncentrować się na przetwarzaniu, a nie przechwytywaniu danych.

Usługę Azure Event Hubs można również zintegrować z usługą [Azure Functions](../azure-functions/index.yml) w celu uzyskania architektury bezserwerowej.

## <a name="scalable"></a>Skalowalność

Korzystając z usługi Event Hubs, możesz zacząć od strumieni danych liczonych w megabajtach i rozwijać je do rozmiaru gigabajtów, a nawet terabajtów. Funkcja [automatycznego rozszerzania](event-hubs-auto-inflate.md) jest jedną z wielu dostępnych opcji skalowania liczby jednostek przepływności odpowiednio do potrzeb użycia.

## <a name="rich-ecosystem"></a>Zaawansowany ekosystem

Z szerokim ekosystemem opartym na standardzie branżowym AMQP 1,0 i dostępnym w różnych językach [.NET](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Python](https://github.com/Azure/azure-sdk-for-python/)i [JavaScript](https://github.com/Azure/azure-sdk-for-js/), można łatwo rozpocząć przetwarzanie strumieni od Event Hubs. Wszystkie obsługiwane języki klienta zapewniają integrację niskiego poziomu. Ten ekosystem umożliwia również bezproblemową integrację z usługami platformy Azure, takimi jak Azure Stream Analytics i Azure Functions, umożliwiając w ten sposób tworzenie architektur bezserwerowych.

[Event Hubs dla ekosystemów Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) dodatkowo umożliwia klientom i aplikacjom [Apache Kafka (1,0 i nowszych)](https://kafka.apache.org/) komunikowanie się z Event Hubs. Nie trzeba konfigurować i konfigurować własnych klastrów Kafka i dozorcy ani zarządzać nimi ani korzystać z innej oferty Kafka-as-a-Service, która nie jest natywna dla platformy Azure.
## <a name="key-architecture-components"></a>Główne składniki architektury
Usługa Event Hubs zawiera następujące [kluczowe składniki](event-hubs-features.md):

- **Producenci zdarzeń**: wszelkie jednostki, które wysyłają dane do centrum zdarzeń. Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołu HTTPS, protokołu AMQP 1.0 lub platformy Apache Kafka (w wersji 1.0 lub nowszej).
- **Partycje**: każdy użytkownik odczytuje tylko określony podzestaw, czyli partycję, strumienia komunikatów.
- **Grupy odbiorców**: widok (stan, pozycja lub przesunięcie) całego centrum zdarzeń. Dzięki grupom konsumentów każda z aplikacji korzystających z danych może mieć osobny widok strumienia zdarzeń. Odczytują one strumienie niezależnie, we własnym tempie i z własnym przesunięciem.
- **Jednostki przepływności**: zakupione wcześniej jednostki pojemności, które określają przepływność usługi Event Hubs.
- **Odbiorcy zdarzeń**: wszelkie jednostki, które odczytują dane zdarzenia z centrum zdarzeń. Wszyscy odbiorcy usługi Event Hubs łączą się za pomocą sesji protokołu AMQP 1.0. Usługa Event Hubs dostarcza zdarzenia za pośrednictwem sesji w miarę ich udostępniania. Wszyscy użytkownicy platformy Kafka nawiązują połączenia za pośrednictwem protokołu Kafka w wersji 1.0 lub nowszej.

Na poniższym rysunku przedstawiono architekturę przetwarzania strumienia usługi Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)

## <a name="event-hubs-on-azure-stack-hub"></a>Usługa Event Hubs w infrastrukturze Azure Stack Hub
Event Hubs w centrum Azure Stack pozwala zrealizować scenariusze chmury hybrydowej. Obsługiwane są rozwiązania przesyłania strumieniowego i oparte na zdarzeniach, zarówno w przypadku przetwarzania lokalnego, jak i w chmurze platformy Azure. Bez względu na to, czy scenariusz jest hybrydowy (połączony), czy odłączony, rozwiązanie może obsługiwać przetwarzanie zdarzeń/strumieni w dużej skali. Twój scenariusz jest związany tylko z rozmiarem klastra Event Hubs, który można udostępnić zgodnie z potrzebami. 

Wersje Event Hubs (w centrum Azure Stack i na platformie Azure) oferują wysoką dostępność funkcji. Ta parzystość oznacza zestawy SDK, przykłady, PowerShell, interfejsu wiersza polecenia i portale, które oferują podobne środowisko, z niewielkimi różnicami. 

Event Hubs na stosie jest bezpłatny w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Event Hubs w centrum Azure Stack Omówienie](/azure-stack/user/event-hubs-overview).


## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z Event Hubs, zobacz samouczki **wysyłania i odbierania zdarzeń** :

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Przejdź](event-hubs-go-get-started-send.md)
- [C (tylko wysyłanie)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko odbieranie)](event-hubs-storm-getstarted-receive.md)


Więcej informacji na temat usługi Event Hubs zawierają następujące artykuły:

- [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](event-hubs-features.md)
- [Często zadawane pytania](event-hubs-faq.md).
