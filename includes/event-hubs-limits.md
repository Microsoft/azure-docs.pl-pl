---
title: dołączanie pliku
description: dołączanie pliku
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ea1ac064799b0cede1de82851a514a2b389f20aa
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499221"
---
W poniższych tabelach przedstawiono limity przydziału i limity dotyczące [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cennika Event Hubs, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Typowe limity dla wszystkich warstw
Poniższe limity są wspólne dla wszystkich warstw. 

| Limit |  Uwagi | Wartość |
| --- |  --- | --- |
| Liczba przestrzeni nazw Event Hubs na subskrypcję |- |100 |
| Liczba centrów zdarzeń na przestrzeń nazw | Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na centrum zdarzeń |- |32 |
| Rozmiar nazwy centrum zdarzeń |- | 256 znaków |
| Rozmiar nazwy grupy odbiorców |- | 256 znaków |
| Liczba odbiorników niezwiązanych z nieepoką na grupę konsumentów |- |5 |
| Liczba reguł autoryzacji na przestrzeń nazw | Kolejne żądania utworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation — |  - | 50 na sekundę | 
| Liczba reguł sieci wirtualnej (VNet) i konfiguracji adresów IP | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Warstwy Podstawowa i Standardowa
W poniższej tabeli przedstawiono limity, które mogą być różne dla warstw Podstawowa i standardowa. 

| Limit | Uwagi | Podstawowa | Standardowa (Standard) |
| --- |  --- | -- | --- |
| Maksymalny rozmiar zdarzenia Event Hubs| &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń | &nbsp; |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw | Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |100 |5000|
| Maksymalny okres przechowywania danych zdarzenia | &nbsp; |1 dzień |1-7 dni |
| Maksymalna liczba jednostek przepływności |Przekroczenie limitu jednostek przepływności powoduje ograniczenie danych i wygenerowanie [wyjątku zajętego serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy [wysłać żądanie pomocy technicznej](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach od 20 na podstawie zatwierdzonego zakupu. |20 | 20 | 

### <a name="dedicated-tier"></a>Warstwa dedykowana
W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

Zapoznaj się z tym [dokumentem](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) , jak utworzyć dedykowany klaster Event Hubs przy użyciu Azure Portal.

| Promowanie | Limity |
| --- | ---|
| Przepustowość |  20 jednostek |
| Przestrzenie nazw | 50 na CU |
| Usługa Event Hubs |  1000 na przestrzeń nazw |
| Rozmiar komunikatu | 1 MB |
| Partycje | 2000 na CU |
| Grupy odbiorców | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 100 K uwzględnionych |
| Czas przechowywania wiadomości | 90 dni, 10 TB uwzględnionych na CU |
| Zdarzenia związane z transferem danych przychodzących | Dołączono |
| Przechwytywanie | Dołączono |


### <a name="schema-registry-limitations"></a>Ograniczenia rejestru schematu

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limity, które są takie same dla warstw **standardowa** i **dedykowana** 
| Promowanie | Limit | 
| --- |  --- | -- |
| Maksymalna długość nazwy grupy schematu | 50 |  
| Maksymalna długość nazwy schematu | 100 |    
| Rozmiar w bajtach na schemat | 1 MB |   
| Liczba właściwości na grupę schematów | 1024 |
| Rozmiar klucza właściwości w bajtach na grupę | 256 | 
| Rozmiar w bajtach na wartość właściwości grupy | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limity różne dla warstw **standardowa** i **dedykowana** 

| Limit | Standardowa | Dedykowane | 
| --- |  --- | -- | --- |
| Rozmiar rejestru schematu (przestrzeń nazw) w bajtach. | 25 |  1024 |
| Liczba grup schematów w rejestrze schematu (przestrzeń nazw)| 1 (z wyłączeniem wartości domyślnej) | 1000 |
| Liczba wersji schematu we wszystkich grupach schematu | 25 | 10 000 |





