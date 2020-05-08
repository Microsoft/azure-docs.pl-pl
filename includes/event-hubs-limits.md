---
title: dołączanie pliku
description: dołączanie pliku
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75901694"
---
W poniższych tabelach przedstawiono limity przydziału i limity dotyczące [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cennika Event Hubs, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

W warstwach Podstawowa, standardowa i dedykowana są wspólne następujące limity. 

| Limit | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- |
| Liczba przestrzeni nazw Event Hubs na subskrypcję |Subskrypcja |- |100 |
| Liczba centrów zdarzeń na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na centrum zdarzeń |Jednostka |- |32 |
| Maksymalny rozmiar nazwy centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba odbiorników niezwiązanych z nieepoką na grupę konsumentów |Jednostka |- |5 |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostek przepływności powoduje ograniczenie danych i wygenerowanie [wyjątku zajętego serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy [wysłać żądanie pomocy technicznej](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach od 20 na podstawie zatwierdzonego zakupu. |20 |
| Liczba reguł autoryzacji na przestrzeń nazw |Przestrzeń nazw|Kolejne żądania utworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation — | Jednostka | - | 50 na sekundę | 
| Liczba reguł sieci wirtualnej (VNet) i konfiguracji adresów IP | Jednostka | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs podstawowe i standardowe przydziały i limity
| Limit | Zakres | Uwagi | Podstawowy | Standardowa |
| --- | --- | --- | -- | --- |
| Maksymalny rozmiar zdarzenia Event Hubs|Jednostka | &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń |Jednostka | &nbsp; |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |100 |5000|
| Maksymalny okres przechowywania danych zdarzenia |Jednostka | &nbsp; |1 dzień |1-7 dni |
|Apache Kafka włączona przestrzeń nazw|Przestrzeń nazw |Event Hubs przestrzeni nazw strumieniuje aplikacje przy użyciu protokołu Kafka |Nie | Tak |
|Przechwytywanie |Jednostka | Gdy ta funkcja jest włączona, mikro partie w tym samym strumieniu |Nie |Tak |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs — warstwa Dedykowana — przydziały i limity
W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

| Funkcja | Limity |
| --- | ---|
| Szerokość pasma |  20 jednostek |
| Namespaces | 50 na CU |
| Event Hubs |  1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Dołączono |
| Rozmiar komunikatu | 1 MB |
| Partycje | 2000 na CU |
| Grupy odbiorców | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 100 K uwzględnionych |
| Przechowywanie komunikatów | 90 dni, 10 TB uwzględnionych na CU |
| Przechwytywanie | Dołączono |
