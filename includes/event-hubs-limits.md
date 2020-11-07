---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 320fa542f2b786f0a256c22f2d2eb299c476dcae
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94362759"
---
W poniższych tabelach przedstawiono limity przydziału i limity dotyczące [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cennika Event Hubs, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="common-limits-for-all-tiers"></a>Typowe limity dla wszystkich warstw
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


## <a name="basic-vs-standard-tiers"></a>Warstwy Podstawowa i Standardowa
W poniższej tabeli przedstawiono limity, które mogą być różne dla warstw Podstawowa i standardowa. 

| Limit | Uwagi | Podstawowa | Standardowa (Standard) |
|---|---|--|---|
| Maksymalny rozmiar zdarzenia Event Hubs| &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń | &nbsp; |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw | Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |100 |5000|
| Maksymalny okres przechowywania danych zdarzenia | &nbsp; |1 dzień |1-7 dni |
| Maksymalna liczba jednostek przepływności |Przekroczenie tego limitu powoduje ograniczenie danych i wygenerowanie [wyjątku zajętości serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy [wysłać żądanie pomocy technicznej](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach od 20 na podstawie zatwierdzonego zakupu. |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>Warstwa dedykowana a warstwa standardowa
W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

Zapoznaj się z tym [dokumentem](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) , jak utworzyć dedykowany klaster Event Hubs przy użyciu Azure Portal.

| Cechy | Standardowa | Dedykowane |
| --- |:---|:---|
| Przepustowość | 20 TUs (do 40 TUs) | 20 jednostek |
| Przestrzenie nazw |  1 | 50 na CU |
| Event Hubs |  10 na przestrzeń nazw | 1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Dołączono |
| Rozmiar komunikatu | 1 000 000 bajtów | 1 000 000 bajtów |
| Partycje | 32 za centrum zdarzeń | 1024 za centrum zdarzeń<br/>2000 na CU |
| Grupy odbiorców | 20 na centrum zdarzeń | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 1 000, maksymalnie 5 000 | 100 K uwzględnione i maks. |
| Przechowywanie komunikatów | 7 dni, 84 GB uwzględnionych na jednostek PRZEPŁYWNOŚCI | 90 dni, 10 TB uwzględnionych na CU |
| Przechwytywanie | Płatność za godzinę | Dołączono |


## <a name="schema-registry-limitations"></a>Ograniczenia rejestru schematu

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limity, które są takie same dla warstw **standardowa** i **dedykowana** 
| Cechy | Limit | 
|---|---|--|
| Maksymalna długość nazwy grupy schematu | 50 |  
| Maksymalna długość nazwy schematu | 100 |    
| Rozmiar w bajtach na schemat | 1 MB |   
| Liczba właściwości na grupę schematów | 1024 |
| Rozmiar klucza właściwości w bajtach na grupę | 256 | 
| Rozmiar w bajtach na wartość właściwości grupy | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limity różne dla warstw **standardowa** i **dedykowana** 

| Limit | Standardowa | Dedykowane | 
|---|---|--|---|
| Rozmiar rejestru schematu (przestrzeń nazw) w bajtach. | 25 |  1024 |
| Liczba grup schematu w rejestrze lub przestrzeni nazw schematu | 1 — wykluczanie grupy domyślnej | 1000 |
| Liczba wersji schematu we wszystkich grupach schematu | 25 | 10 000 |





