---
title: Service Bus tematy danych wyjściowych z Azure Stream Analytics
description: W tym artykule opisano Service Bus tematy jako dane wyjściowe Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: d2a9063a202ba542279efd8017d282fe0aa78d42
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129868"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Service Bus tematy danych wyjściowych z Azure Stream Analytics

Kolejki Service Bus zapewniają metodę komunikacji jeden-do-jednego od nadawcy do odbiorcy. [Tematy Service Bus](/previous-versions/azure/hh367516(v=azure.100)) zapewniają formę komunikacji typu "jeden do wielu".

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych tematu Service Bus.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego tematu Service Bus. |
| Service Bus przestrzeń nazw |Kontener dla zestawu jednostek obsługi komunikatów. Po utworzeniu nowego centrum zdarzeń utworzono również przestrzeń nazw Service Bus. |
| Nazwa tematu |Tematy to jednostki obsługi komunikatów, podobne do centrów zdarzeń i kolejek. Są one przeznaczone do zbierania strumieni zdarzeń z urządzeń i usług. Po utworzeniu tematu ma również określoną nazwę. Komunikaty wysyłane do tematu nie są dostępne, jeśli subskrypcja nie zostanie utworzona, dlatego upewnij się, że w temacie istnieje co najmniej jedna subskrypcja. |
| Nazwa zasad tematu |Podczas tworzenia tematu Service Bus można także utworzyć zasady dostępu współdzielonego na karcie **Konfiguracja** tematu. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
| Klucz zasad tematu |Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Service Bus. |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro. |
| Encoding |Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik |Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Kolumny właściwości | Opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [właściwości metadanych niestandardowych dla danych wyjściowych](#custom-metadata-properties-for-output). |
| Kolumny właściwości systemu | Opcjonalny. Pary klucz-wartość właściwości systemu i odpowiednich nazw kolumn, które muszą być dołączone do wiadomości wychodzącej zamiast ładunku. |

Liczba partycji jest [określana na podstawie Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="partitioning"></a>Partycjonowanie

Partycjonowanie jest wybierane automatycznie. Liczba partycji jest określana na podstawie [Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji. Liczba składników zapisywania danych wyjściowych jest taka sama jak liczba partycji w temacie danych wyjściowych.

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar wiadomości to 256 KB na komunikat dla warstwy Standardowa i 1 MB dla warstwy Premium. Aby uzyskać więcej informacji, zobacz [limity Service Bus](../service-bus-messaging/service-bus-quotas.md). Aby zoptymalizować, użyj jednego zdarzenia na komunikat.

## <a name="custom-metadata-properties-for-output"></a>Niestandardowe właściwości metadanych dla danych wyjściowych

Kolumny zapytań można dołączać jako właściwości użytkownika do wiadomości wychodzących. Te kolumny nie przechodzą do ładunku. Właściwości są obecne w formie słownika w wiadomości wyjściowej. *Klucz* to nazwa kolumny i *wartość* jest wartością kolumny w słowniku właściwości. Wszystkie typy danych Stream Analytics są obsługiwane z wyjątkiem rekordów i tablic.

W poniższym przykładzie pola `DeviceId` i `DeviceStatus` są dodawane do metadanych.

1. Użyj następującego zapytania:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Skonfiguruj `DeviceId,DeviceStatus` jako kolumny właściwości w danych wyjściowych.

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="Kolumny właściwości":::

Na poniższej ilustracji przedstawiono oczekiwane właściwości komunikatów wyjściowych, które zostały sprawdzone w centrum EventHub przy użyciu [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="Kolumny właściwości":::

## <a name="system-properties"></a>Właściwości systemu

Kolumny zapytania można dołączać jako [Właściwości systemu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) do kolejki lub komunikatów tematu usługi wychodzącej magistrali usług. Te kolumny nie znajdują się w ładunku, natomiast odpowiednia [Właściwość systemu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) BrokeredMessage jest wypełniana wartościami kolumn zapytania.
Te właściwości systemu są obsługiwane — `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Wartości ciągu tych kolumn są analizowane jako odpowiadające im typy wartości właściwości systemu, a wszystkie błędy analizy są traktowane jako błędy danych.
To pole jest dostępne jako format obiektu JSON. Szczegółowe informacje o tym formacie są następujące:

* Ujęte w nawiasy klamrowe {} .
* Zapisywane w parach klucz/wartość.
* Klucze i wartości muszą być ciągami.
* Klucz jest nazwą i wartością właściwości systemowej jest nazwa kolumny zapytania.
* Klucze i wartości są oddzielone dwukropkiem.
* Każda para klucz/wartość jest oddzielona przecinkiem.

Pokazuje, jak używać tej właściwości —

* Dotyczących `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumny właściwości systemu: `{ "MessageId": "column1", "PartitionKey": "column2"}`

To ustawienie powoduje, że `MessageId` komunikaty w kolejce usługi Service Bus z `column1` wartościami i PartitionKey są ustawione na `column2` wartości.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)