---
title: Event Hubs dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano sposób wyprowadzania danych z Azure Stream Analytics do usługi Azure Event Hubs.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 02abdd752528ce28642b6228648062ed961d5ae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452394"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Event Hubs dane wyjściowe z Azure Stream Analytics

Usługa [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) jest wysoce skalowalnym pozyskiwaniem zdarzeń publikowania/subskrybowania. Może zbierać miliony zdarzeń na sekundę. Jednym z nich jest użycie centrum zdarzeń jako danych wyjściowych, gdy dane wyjściowe zadania Stream Analytics staną się danymi wejściowymi innego zadania przesyłania strumieniowego. Aby uzyskać informacje o maksymalnym rozmiarze wiadomości i optymalizacji rozmiaru partii, zobacz sekcję [wyjściowy rozmiar wsadu](#output-batch-size) .

## <a name="output-configuration"></a>Konfiguracja wyjściowa

Poniższa tabela zawiera parametry, które są konieczne do skonfigurowania strumieni danych z centrów zdarzeń jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń | Kontener dla zestawu jednostek obsługi komunikatów. Po utworzeniu nowego centrum zdarzeń jest również tworzona przestrzeń nazw centrum zdarzeń. |
| Nazwa centrum zdarzeń | Nazwa danych wyjściowych centrum zdarzeń. |
| Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego, które można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
| Klucz zasad centrum zdarzeń | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw centrum zdarzeń. |
| Kolumna klucza partycji | Opcjonalny. Kolumna zawierająca klucz partycji dla danych wyjściowych centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro. |
| Encoding | W przypadku plików CSV i JSON jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią** kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON. **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON.  |
| Kolumny właściwości | Opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [właściwości metadanych niestandardowych dla danych wyjściowych](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Partycjonowanie

Partycjonowanie różni się w zależności od wyrównania partycji. Gdy klucz partycji dla danych wyjściowych centrum zdarzeń jest równomiernie wyrównany przy użyciu nadrzędnego (poprzedni) kroku zapytania, liczba modułów zapisujących jest taka sama jak liczba partycji w danych wyjściowych centrum zdarzeń. Każdy moduł zapisujący używa [klasy EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) do wysyłania zdarzeń do konkretnej partycji. Gdy klucz partycji dla danych wyjściowych centrum zdarzeń nie jest wyrównany przy użyciu nadrzędnego (poprzedni) kroku zapytania, liczba modułów zapisujących jest taka sama jak liczba partycji w tym poprzednim kroku. Każdy moduł zapisujący używa [klasy SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync) w **EventHubClient** do wysyłania zdarzeń do wszystkich partycji wyjściowych. 

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar komunikatu wynosi 256 KB lub 1 MB na wiadomość. Aby uzyskać więcej informacji, zobacz [limity Event Hubs](../event-hubs/event-hubs-quotas.md). Gdy Partycjonowanie danych wejściowych/wyjściowych nie jest wyrównane, każde zdarzenie jest spakowane osobno w `EventData` i wysyłane w partii o maksymalnym rozmiarze komunikatu. Dzieje się tak, jeśli są używane [niestandardowe właściwości metadanych](#custom-metadata-properties-for-output) . Gdy Partycjonowanie danych wejściowych/wyjściowych jest wyrównane, do jednego wystąpienia są pakowane wiele zdarzeń `EventData` , do którego zostanie wysłany maksymalny rozmiar komunikatu.

## <a name="custom-metadata-properties-for-output"></a>Niestandardowe właściwości metadanych dla danych wyjściowych

Kolumny zapytań można dołączać jako właściwości użytkownika do wiadomości wychodzących. Te kolumny nie przechodzą do ładunku. Właściwości są obecne w formie słownika w wiadomości wyjściowej. *Klucz* to nazwa kolumny i *wartość* jest wartością kolumny w słowniku właściwości. Wszystkie typy danych Stream Analytics są obsługiwane z wyjątkiem rekordów i tablic.

W poniższym przykładzie pola `DeviceId` i `DeviceStatus` są dodawane do metadanych.

1. Użyj następującego zapytania:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Skonfiguruj `DeviceId,DeviceStatus` jako kolumny właściwości w danych wyjściowych.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Kolumny właściwości":::

Na poniższej ilustracji przedstawiono oczekiwane właściwości komunikatów wyjściowych, które zostały sprawdzone w centrum EventHub przy użyciu [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Właściwości niestandardowe zdarzenia":::

## <a name="next-steps"></a>Następne kroki

* [Uzyskiwanie dostępu do centrum zdarzeń z zadania Azure Stream Analytics za pomocą tożsamości zarządzanych (wersja zapoznawcza)](event-hubs-managed-identity.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
