---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

Poniższe tabele zawierają limity przydziału i limity specyficzne dla [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje o Event Hubs cen, zobacz [Event Hubs cennika](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Typowe limity dla wszystkich warstw
Poniższe limity są wspólne dla wszystkich warstw. 

| Limit |  Uwagi | Wartość |
| --- |  --- | --- |
| Liczba Event Hubs nazw na subskrypcję |- |100 |
| Liczba centrów zdarzeń na przestrzeń nazw | Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Rozmiar nazwy centrum zdarzeń |- | 256 znaków |
| Rozmiar nazwy grupy odbiorców | Protokół platformy Kafka nie wymaga utworzenia grupy odbiorców. | <p>Kafka: 256 znaków</p><p>AMQP: 50 znaków |
| Liczba odbiorników spoza epoki na grupę odbiorców |- |5 |
| Liczba reguł autoryzacji na przestrzeń nazw | Kolejne żądania utworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation |  - | 50 na sekundę | 
| Liczba sieci wirtualnych (VNet) | - | 128 | 
| Liczba reguł konfiguracji adresu IP | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Warstwy Podstawowa i Standardowa
W poniższej tabeli przedstawiono limity, które mogą się różnić w przypadku warstw Podstawowa i Standardowa. 

| Limit | Uwagi | Podstawowa | Standardowa (Standard) |
|---|---|--|---|
| Maksymalny rozmiar Event Hubs publikacji| &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń | &nbsp; |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw | Kolejne żądania dodatkowych połączeń są odrzucane, a kod wywołujący odbiera wyjątek. |100 |5000|
| Maksymalny okres przechowywania danych zdarzeń | &nbsp; |1 dzień |1–7 dni |
| Maksymalna liczba jednostek przepływności |Przekroczenie tego limitu powoduje ograniczenie danych i generuje [wyjątek zajętego serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy złożyć [wniosek o pomoc techniczną.](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach po 20 na podstawie zatwierdzonego zakupu. |20 | 20 | 
| Liczba partycji na centrum zdarzeń | |32 | 32 | 

> [!NOTE]
>
> Zdarzenia można publikować indywidualnie lub w partiach. 
> Limit publikacji (zgodnie z SKU) ma zastosowanie niezależnie od tego, czy jest to pojedyncze zdarzenie, czy partia. Publikowanie zdarzeń większych niż maksymalny próg zostanie odrzucone.

### <a name="dedicated-tier-vs-standard-tier"></a>Warstwa dedykowana a warstwa Standardowa
Ta Event Hubs — warstwa Dedykowana jest rozliczana po stałej cenie miesięcznej z użyciem co najmniej 4 godzin. Warstwa Dedykowana oferuje wszystkie funkcje planu Standardowa, ale ze skalą przedsiębiorstwa i limitami dla klientów wymagających obciążeń. 

Zapoznaj się z tym [dokumentem,](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) aby dowiedzieć się, jak utworzyć dedykowany klaster Event Hubs użyciu Azure Portal.

| Cecha | Standardowa (Standard) | Dedykowane |
| --- |:---|:---|
| Przepustowość | 20 TU (do 40 TU) | 20 cU |
| Przestrzenie nazw |  100 na subskrypcję | 50 na jednostkę cu (100 na subskrypcję) |
| Event Hubs |  10 na przestrzeń nazw | 1000 na przestrzeń nazw |
| Zdarzenia danych przychodzących | Płatność za milion zdarzeń | Dołączono |
| Rozmiar komunikatu | 1 milion bajtów | 1 milion bajtów |
| Partycje | 32 na centrum zdarzeń | 1024 na centrum zdarzeń<br/>2000 na jednostkę cu |
| Grupy odbiorców | 20 na centrum zdarzeń | Brak limitu na jednostkę cu, 1000 na centrum zdarzeń |
| Połączenia brokerowane | Uwzględniono 1000, maksymalnie 5000 | Uwzględniono 100 tys. |
| Przechowywanie komunikatów | 7 dni, 84 GB dołączona na jednostkę tu | 90 dni, 10 TB dołączona na jednostkę cu |
| Przechwytywanie | Płatność za godzinę | Dołączono |


### <a name="schema-registry-limitations"></a>Ograniczenia rejestru schematów

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limity, które są takie same dla warstwy Standardowa i Dedykowana 
| Cechy | Limit | 
|---|---|
| Maksymalna długość nazwy grupy schematu | 50 |  
| Maksymalna długość nazwy schematu | 100 |    
| Rozmiar w bajtach na schemat | 1 MB |   
| Liczba właściwości na grupę schematów | 1024 |
| Rozmiar w bajtach na klucz właściwości grupy | 256 | 
| Rozmiar w bajtach na wartość właściwości grupy | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limity, które są różne dla warstwy Standardowa i Dedykowana 

| Limit | Standardowa (Standard) | Dedykowane | 
|---|---|--|
| Rozmiar rejestru schematu (przestrzeni nazw) w megabajtach | 25 |  1024 |
| Liczba grup schematów w rejestrze schematu lub przestrzeni nazw | 1 — z wyłączeniem grupy domyślnej | 1000 |
| Liczba wersji schematu we wszystkich grupach schematów | 25 | 10 000 |