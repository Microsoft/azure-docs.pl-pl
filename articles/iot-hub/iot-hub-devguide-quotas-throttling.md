---
title: Informacje o przydziałach i ograniczaniu IoT Hub platformy Azure | Microsoft Docs
description: Przewodnik dla deweloperów — opis przydziałów, które mają zastosowanie do IoT Hub i oczekiwanego zachowania ograniczenia.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 3de9eccd001e421ef3255f83630716df12b7a2ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595264"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Przydziały IoT Hub i ograniczanie przepustowości

W tym artykule opisano przydziały dla IoT Hub i przedstawiono informacje ułatwiające zrozumienie sposobu działania ograniczenia.

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Każda subskrypcja platformy Azure może mieć maksymalnie 50 centrów IoT i maksymalnie 1 bezpłatne centrum.

Każde centrum IoT jest aprowizowane za pomocą określonej liczby jednostek w określonej warstwie. Warstwa i liczba jednostek określają maksymalny dzienny limit przydziału komunikatów, które można wysłać. Rozmiar komunikatu używany do obliczania dziennego limitu wynosi 0,5 KB dla koncentratora warstwy bezpłatnej i 4 KB dla wszystkich innych warstw. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Warstwa określa również limity ograniczania, które IoT Hub wymuszane we wszystkich operacjach.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

Urządzenia IoT Plug and Play wysyłają co najmniej jeden komunikat telemetrii dla każdego interfejsu, łącznie z elementem głównym, co może zwiększyć liczbę komunikatów zliczane do przydziału wiadomości.

## <a name="operation-throttles"></a>Ograniczenia operacji

Ograniczanie liczby operacji jest ograniczeniami szybkości, które są stosowane w zakresach minut i są przeznaczone do zapobiegania nadużyciom. Są one również uzależnione od [kształtowania ruchu](#traffic-shaping).

W poniższej tabeli przedstawiono wymuszone ograniczenia. Wartości odnoszą się do poszczególnych centrów.

| Ograniczenie | Bezpłatna, B1 i S1 | B2 i S2 | B3 i S3 | 
| -------- | ------- | ------- | ------- |
| [Operacje na rejestrze tożsamości](#identity-registry-operations-throttle) (tworzenie, pobieranie, wyświetlanie listy, aktualizowanie, usuwanie) | 1.67/sek/jednostkę (100/min/jednostka) | 1.67/sek/jednostkę (100/min/jednostka) | 83.33/sek/jednostkę (5000/min/jednostka) |
| [Nowe połączenia urządzeń](#device-connections-throttle) (ten limit dotyczy stawki _nowych połączeń_, a nie łącznej liczby połączeń) | Wyższa z 100/s lub 12/sek/jednostkę <br/> Na przykład dwie jednostki S1 są 2 \* 12 = 24 nowe połączenia/s, ale masz co najmniej 100 nowych połączeń/s w poszczególnych jednostkach. W przypadku dziewięciu jednostek S1 masz 108 nowych połączeń/s (9 \* 12) w poszczególnych jednostkach. | 120 nowych połączeń/s/jednostka | 6 000 nowych połączeń/s/jednostka |
| Liczba elementów wysłanych z urządzenia do chmury | Ponad 100 operacji wysyłania/s lub 12 operacji wysyłania/s/jednostkę <br/> Na przykład dwie jednostki S1 są 2 \* 12 = 24/s, ale masz co najmniej 100 operacji wysyłania na sekundę w poszczególnych jednostkach. W przypadku dziewięciu jednostek S1 masz 108 operacji wysyłania/s (9 \* 12) w poszczególnych jednostkach. | 120 operacji wysyłania/s/jednostkę | 6 000 operacji wysyłania/s/jednostkę |
| Wysłane z chmury do urządzenia<sup>1</sup> | 1,67 operacji wysyłania/s/jednostkę (100 komunikatów/min/jednostka) | 1,67 operacji wysyłania/s/jednostkę (100 operacji wysyłania/min/jednostka) | 83,33 operacji wysyłania/s/jednostkę (5 000 operacji wysyłania/min/jednostka) |
| Odebrane z chmury do urządzenia<sup>1</sup> <br/> (tylko w przypadku, gdy urządzenie używa protokołu HTTPS)| 16,67 operacji odbioru/s/jednostkę (1 000 operacji odbioru/min/jednostka) | 16,67 operacji odbioru/s/jednostkę (1 000 operacji odbioru/min/jednostka) | 833,33 operacji odbioru/s/jednostkę (50 000 operacji odbioru/min/jednostka) |
| Przekazywanie plików | 1,67 liczba inicjacji przekazywania plików na sekundę/jednostkę (100/min/jednostka) | 1,67 liczba inicjacji przekazywania plików na sekundę/jednostkę (100/min/jednostka) | 83,33 liczba inicjacji przekazywania plików na sekundę/jednostkę (5000/min/jednostka) |
| Metody bezpośrednie<sup>1</sup> | 160KB/s/jednostka<sup>2</sup> | 480KB/s/jednostka<sup>2</sup> | 24MB/s/jednostka<sup>2</sup> | 
| Zapytania | 20/min/jednostkę | 20/min/jednostkę | 1000/min/jednostkę |
| Sznury (urządzenia i moduły) — odczyt<sup>1</sup> | 100/s | Wyższa z 100/s lub 10/s/jednostkę | 500/sek/jednostkę |
| Aktualizacje bliźniaczye (urządzenie i moduł)<sup>1</sup> | 50/s | Ponad 50/s lub 5/s/jednostkę | 250/sek/jednostkę |
| Operacje zadań<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 1.67/sek/jednostkę (100/min/jednostka) | 1.67/sek/jednostkę (100/min/jednostka) | 83.33/sek/jednostkę (5000/min/jednostka) |
| Zadania urządzenia — operacje<sup>1</sup> <br/> (z aktualizacją sznurów, wywołaj metodę bezpośrednią) | 10/s | Więcej z 10/s lub 1/sek/jednostkę | 50/SEK/jednostkę |
| Konfiguracje i wdrożenia brzegowe<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 0.33/sek/jednostkę (20/min/jednostka) | 0.33/sek/jednostkę (20/min/jednostka) | 0.33/sek/jednostkę (20/min/jednostka) |
| Współczynnik inicjacji strumienia urządzeń<sup>1</sup> | 5 nowych strumieni/s | 5 nowych strumieni/s | 5 nowych strumieni/s |
| Maksymalna liczba strumieni urządzeń połączonych współbieżnie<sup>1</sup> | 50 | 50 | 50 |
| Maksymalny<sup>transfer danych</sup> strumienia urządzeń (zagregowany wolumin dziennie) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Ta funkcja nie jest dostępna w warstwie Podstawowa IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać właściwy IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup> Rozmiar miernika ograniczającego to 4 KB. Ograniczanie jest oparte tylko na rozmiarze ładunku żądania.

### <a name="throttling-details"></a>Szczegóły ograniczenia

* Rozmiar licznika decyduje o tym, co zwiększa zużycie limitu ograniczania. Jeśli ładunek bezpośredniego wywołania jest z zakresu od 0 do 4 KB, jest liczony jako 4 KB. Można dokonywać do 40 wywołań na sekundę na jednostkę, zanim zostanie osiągnięty limit 160 KB/s/jednostkę.

   Podobnie, jeśli ładunek ma wartość z przedziału od 4 KB do 8 KB, każde wywołanie konta dla 8 KB i można wykonać maksymalnie 20 wywołań na sekundę na jednostkę, zanim zostanie osiągnięty maksymalny limit.

   Na koniec, jeśli rozmiar ładunku jest z przedziału od 156KB do 160 KB, będzie możliwe wykonanie tylko 1 wywołania na sekundę na jednostkę w centrum, zanim zostanie osiągnięty limit 160 KB/s/jednostkę.

*  W przypadku zadań wykonywanych na *urządzeniach (aktualizacja sznurów, metoda wywołania bezpośredniego)* dla warstwy S3, 50/SEK/jednostka ma zastosowanie tylko w przypadku wywołania metod przy użyciu zadań. Jeśli metody bezpośrednie są wywoływane bezpośrednio, stosowane są pierwotne limity ograniczania wynoszące 24 MB/s/Unit (dla S3).

*  **Limit przydziału** to zagregowana liczba komunikatów, które można wysłać w Twoim centrum *na dzień*. Limit przydziału centrum można znaleźć w kolumnie **łączna liczba komunikatów/dzień** na [stronie cennika IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Ograniczenia między chmurą i urządzeniem a chmurą określają maksymalną *szybkość* , z jaką można wysyłać komunikaty — liczba komunikatów niezależnie od fragmentów 4 KB. Każdy komunikat może mieć do 256 KB, który jest [maksymalnym rozmiarem komunikatu](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Dobrym sposobem jest ograniczenie liczby wywołań, aby nie trafiać/przekraczać limitów ograniczania. Jeśli osiągnięto limit, IoT Hub reaguje na kod błędu 429 i klient powinien wykonać wycofanie i ponowić próbę. Limity są przypadające na centrum (lub w niektórych przypadkach na Hub/jednostkę). Aby uzyskać więcej informacji, zobacz [Zarządzanie łącznością i niezawodnymi wzorcami komunikatów/ponownych prób](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Kształtowanie ruchu

W celu uwzględnienia ruchu sieciowego, IoT Hub akceptuje żądania powyżej ograniczenia przez ograniczony czas. Pierwsze kilka z tych żądań jest przetwarzanych od razu. Jeśli jednak liczba żądań będzie nadal naruszać ograniczenie przepustowości, IoT Hub zaczyna umieszczać żądania w kolejce i przetwarzać je z szybkością limitu. Ten efekt nazywa się *kształtem ruchu*. Ponadto rozmiar tej kolejki jest ograniczony. W przypadku kontynuowania naruszenia ograniczenia przepustowości kolejka jest wypełniana, a IoT Hub rozpoczyna Odrzucanie żądań za pomocą `429 ThrottlingException` .

Na przykład można użyć symulowanego urządzenia do 200 wysyłania komunikatów z urządzenia do chmury na sekundę do IoT Hub S1 (ma limit 100/s D2C). W pierwszej minucie lub dwóch komunikaty są przetwarzane od razu. Ponieważ jednak urządzenie nadal wysyła więcej komunikatów niż limit ograniczania przepustowości, IoT Hub rozpoczyna tylko przetwarzanie komunikatów 100 na sekundę i umieszczenie reszty w kolejce. Obserwowanie zwiększony czas oczekiwania. Wreszcie rozpoczyna się to, `429 ThrottlingException` gdy kolejka jest wypełniana, a [wartość "liczba błędów ograniczania" IoT Hub](monitor-iot-hub-reference.md#device-telemetry-metrics) się zwiększać. Aby dowiedzieć się, jak tworzyć alerty i wykresy na podstawie metryk, zobacz [Monitor IoT Hub](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Ograniczanie przepustowości operacji rejestru tożsamości

Operacje rejestru tożsamości urządzeń są przeznaczone do użycia w czasie wykonywania w scenariuszach zarządzania urządzeniami i aprowizacji. Odczytywanie lub aktualizowanie dużej liczby tożsamości urządzeń jest obsługiwane za pomocą [zadań importu i eksportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

W przypadku inicjowania operacji tożsamości za pomocą [zbiorczych operacji na urządzeniach](iot-hub-bulk-identity-mgmt.md)należy zastosować te same limity ograniczania przepustowości. Na przykład jeśli chcesz przesłać operację zbiorczą do tworzenia 50 urządzeń i masz IoT Hub S1 z 1 jednostką, tylko dwa z tych żądań zbiorczych są akceptowane na minutę. Jest to spowodowane tym, że ograniczenie operacji tożsamości dla dla IoT Hub S1 z 1 jednostką wynosi 100/min/jednostkę. W takim przypadku trzecie żądanie (i więcej) w tej samej minucie zostałyby odrzucone, ponieważ limit został już osiągnięty. 

### <a name="device-connections-throttle"></a>Ograniczenie połączeń urządzeń

Ograniczenie *połączeń urządzeń* reguluje szybkość, z jaką nowe połączenia urządzeń mogą być nawiązywane za pomocą Centrum IoT. Ograniczenie *połączeń urządzeń* nie kontroluje maksymalnej liczby jednocześnie połączonych urządzeń. Częstotliwość *połączeń urządzeń* jest zależna od liczby jednostek, które są obsługiwane dla Centrum IoT.

Na przykład w przypadku zakupienia pojedynczej jednostki S1 uzyskasz przepustowość 100 połączeń na sekundę. W związku z tym, aby połączyć 100 000 urządzeń, trwa co najmniej 1 000 sekund (około 16 minut). Można jednak mieć dowolną liczbę jednocześnie połączonych urządzeń, ponieważ masz urządzenia zarejestrowane w rejestrze tożsamości.

## <a name="other-limits"></a>Inne limity

IoT Hub wymusza inne limity operacyjne:

| Operacja | Limit |
| --------- | ----- |
| Urządzenia | Łączna liczba urządzeń i modułów, które mogą być zarejestrowane w pojedynczym Centrum IoT Hub, jest ograniczona do 1 000 000. Jedynym sposobem zwiększenia tego limitu jest skontaktowanie się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).|
| Operacje przekazywania plików | 10 współbieżnych przekazywania plików na urządzenie. |
| Zadania<sup>1</sup> | Maksymalna liczba współbieżnych zadań to 1 (za darmo i S1), 5 (dla S2) i 10 (dla S3). Maksymalne współbieżne [zadania importowania/eksportowania urządzeń](iot-hub-bulk-identity-mgmt.md) mają jednak wartość 1 dla wszystkich warstw. <br/>Historia zadania jest przechowywana do 30 dni. |
| Dodatkowe punkty końcowe | Płatne centra jednostek SKU mogą mieć 10 dodatkowych punktów końcowych. Bezpłatne centra SKU mogą mieć jeden dodatkowy punkt końcowy. |
| Zapytania routingu komunikatów | Płatne centra jednostek SKU mogą mieć 100 zapytań routingu. Bezpłatne centra SKU mogą mieć pięć zapytań routingu. |
| Wzbogacenia wiadomości | Płatne centra jednostek SKU mogą mieć maksymalnie 10 wzbogacania komunikatów. Bezpłatne centra SKU mogą mieć maksymalnie 2 wzbogacanie komunikatów.|
| Obsługa komunikatów przesyłanych z urządzeń do chmury | Maksymalny rozmiar komunikatu 256 KB |
| Obsługa komunikatów z chmury do urządzeń<sup>1</sup> | Maksymalny rozmiar komunikatu 64 KB. Maksymalna liczba oczekujących komunikatów do dostarczenia to 50 na urządzenie. |
| Metoda bezpośrednia<sup>1</sup> | Maksymalny rozmiar ładunku metody bezpośredniej to 128 KB. |
| Automatyczne konfiguracje urządzeń i modułów<sup>1</sup> | 100 konfiguracje na płatne centra SKU. 20 konfiguracji na bezpłatne centrum SKU. |
| IoT Edge wdrożeń automatycznych<sup>1</sup> | 50 modułów na wdrożenie. 100 wdrożenia (w tym wdrożenia warstwowe) na płatne centra SKU. 10 wdrożeń na bezpłatne centrum SKU. |
| Bliźniaczych reprezentacji<sup>1</sup> | Maksymalny rozmiar żądanych właściwości i raportowane sekcje są 32 KB każdego z nich. Maksymalny rozmiar sekcji tagów to 8 KB. |
| Zasady dostępu współużytkowanego | Maksymalna liczba zasad dostępu współdzielonego wynosi 16. |
| Certyfikaty x509 urzędu certyfikacji | Maksymalna liczba certyfikatów x509 urzędu certyfikacji, które można zarejestrować w IoT Hub to 25. |

<sup>1</sup> Ta funkcja nie jest dostępna w warstwie Podstawowa IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać właściwy IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Zwiększenie limitu przydziału lub ograniczenia przepustowości

W dowolnym momencie można zwiększyć limity przydziału lub ograniczenia, [zwiększając liczbę jednostek zainicjowanych w centrum IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Opóźnienie

IoT Hub dąży do zapewnienia małych opóźnień dla wszystkich operacji. Jednak ze względu na warunki sieciowe i inne nieprzewidywalne czynniki nie mogą one zagwarantować pewnego opóźnienia. Podczas projektowania rozwiązania należy:

* Należy unikać wprowadzania wszelkich założeń dotyczących maksymalnego opóźnienia operacji IoT Hub.
* Zainicjuj obsługę usługi IoT Hub w regionie platformy Azure znajdującym się najbliżej Twoich urządzeń.
* Rozważ użycie Azure IoT Edge do wykonywania operacji zależnych od opóźnienia na urządzeniu lub na bramie blisko urządzenia.

Wiele jednostek IoT Hub wpływa na ograniczanie, jak opisano wcześniej, ale nie zapewniają dodatkowych korzyści opóźnienia ani gwarancji.

Jeśli widzisz nieoczekiwane zwiększenie opóźnienia operacji, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Szczegółowe omówienie działania związanego z ograniczaniem IoT Hub można znaleźć w wpisie w blogu [IoT Hub ograniczenia przepustowości i](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Inne tematy referencyjne w tym IoT Hub przewodniku dla deweloperów obejmują:

* [Punkty końcowe centrum IoT Hub](iot-hub-devguide-endpoints.md)
* [Monitorowanie usługi IoT Hub](monitor-iot-hub.md)
