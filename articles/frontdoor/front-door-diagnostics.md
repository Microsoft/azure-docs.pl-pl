---
title: Monitorowanie metryk i dzienników w usłudze Azure front-drzwi | Microsoft Docs
description: W tym artykule opisano różne metryki i dzienniki dostępu obsługiwane przez usługę Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471731"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorowanie metryk i dzienników na platformie Azure — drzwiczki

Korzystając z usług frontonu platformy Azure, można monitorować zasoby w następujący sposób:

- **Metryki**. Drzwi frontonu platformy Azure mają obecnie siedem metryk do wyświetlania liczników wydajności.
- **Dzienniki**. Dzienniki działań i diagnostyki umożliwiają zapisywanie lub zużywanie wydajności, dostępu i innych danych z zasobów na potrzeby monitorowania.

### <a name="metrics"></a>Metryki

Metryki to funkcja niektórych zasobów platformy Azure, która umożliwia wyświetlanie liczników wydajności w portalu. Dostępne są następujące metryki czołowych drzwi:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| RequestCount | Liczba żądań | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba żądań klientów obsłużonych przez tylne drzwi.  |
| RequestSize | Rozmiar żądania | Bajty | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako żądania od klientów do przednich drzwi. |
| ResponseSize | Rozmiar odpowiedzi | Bajty | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako odpowiedzi z pierwszych drzwi do klientów. |
| TotalLatency | Łączne opóźnienie | ) | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Czas, który został obliczony przez żądanie klienta odebrane przez tylne drzwi do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z drzwi przednich. |
| BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>Zaplecze | Liczba żądań wysyłanych z przednich drzwi do frontonu. |
| BackendRequestLatency | Opóźnienie żądania wewnętrznej bazy danych | ) | Zaplecze | Czas, jaki upłynął od momentu wysłania żądania przez drzwi do zaplecza do momentu odebrania ostatniego bajtu odpowiedzi z zaplecza do przodu. |
| BackendHealthPercentage | Procent kondycji zaplecza | Wartość procentowa | Zaplecze</br>Ustawień httpsettings elementu | Procent pomyślnych sond kondycji z czołowych drzwi do frontonu. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Liczba | PolicyName</br>RuleName</br>Akcja | Liczba żądań klientów przetworzonych przez zabezpieczenia warstwy aplikacji dla drzwi z przodu. |

## <a name="activity-logs"></a><a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności zawierają informacje o operacjach wykonywanych na wierzchu drzwi. Określają one również elementy, kto i kiedy dla operacji zapisu (Put, post lub Delete) podejmowane na wierzchu drzwi.

>[!NOTE]
>Dzienniki aktywności nie obejmują operacji odczytu (Get). Nie obejmują one również operacji wykonywanych przy użyciu Azure Portal lub oryginalnego interfejsu API zarządzania.

Uzyskuj dostęp do dzienników aktywności w swoich drzwiach lub wszystkich dzienników zasobów platformy Azure w Azure Monitor. Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie z drzwiami czołowymi.
2. Wybierz pozycję **Dziennik aktywności**.

    ![Dziennik aktywności](./media/front-door-diagnostics/activity-log.png)

3. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności.

Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane przez zasób. Aby uzyskać więcej informacji, zobacz [Azure monitor dzienników diagnostycznych](../azure-monitor/platform/platform-logs-overview.md).

![Dzienniki diagnostyczne](./media/front-door-diagnostics/diagnostic-log.png)

Aby skonfigurować dzienniki diagnostyczne dla drzwi czołowych:

1. Wybierz swoje drzwi z platformy Azure.

2. Wybierz pozycję **Ustawienia diagnostyczne**.

3. Wybierz pozycję **Włącz diagnostykę**. Archiwizuj dzienniki diagnostyczne wraz z metrykami na koncie magazynu, przesyłaj strumieniowo do centrum zdarzeń lub wysyłaj je do dzienników Azure Monitor.

Drzwi z przodu zawierają obecnie dzienniki diagnostyczne (wsadowe co godzinę). Dzienniki diagnostyczne zapewniają pojedyncze żądania interfejsu API za pomocą każdego wpisu, który ma następujący schemat:

| Właściwość  | Opis |
| ------------- | ------------- |
| BackendHostname | Jeśli żądanie zostało przekazane do zaplecza, to pole reprezentuje nazwę hosta zaplecza. To pole będzie puste, jeśli żądanie zostało przekierowane lub przekazane do regionalnej pamięci podręcznej (gdy buforowanie jest włączone dla reguły routingu). |
| CacheStatus | W przypadku scenariuszy buforowania to pole definiuje trafień/chybień pamięci podręcznej w punkcie POP |
| ClientIp | Adres IP klienta, który wykonał żądanie. Jeśli w żądaniu wystąpiło wyrażenie X-Forwarded-For, adres IP klienta zostanie pobrany z tego samego. |
| ClientPort | Port IP klienta, który wykonał żądanie. |
| HttpMethod | Metoda HTTP używana przez żądanie. |
| HttpStatusCode | Kod stanu HTTP zwrócony z serwera proxy. |
| HttpStatusDetails | Stan wynikający z żądania. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołania do stanu. |
| HttpVersion | Typ żądania lub połączenia. |
| POP | Krótka nazwa krawędzi, w której wylądune jest żądanie. |
| RequestBytes | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania. |
| RequestUri | Identyfikator URI odebranego żądania. |
| ResponseBytes | Bajty wysłane przez serwer wewnętrznej bazy danych jako odpowiedź.  |
| RoutingRuleName | Nazwa reguły routingu, która pasuje do żądania. |
| To elementu SecurityProtocol | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania. |
| SentToOriginShield | Pole logiczne reprezentujące, czy podczas pierwszego środowiska wystąpił chybień w pamięci podręcznej, a żądanie zostało wysłane do regionalnej pamięci podręcznej. Zignoruj to pole, jeśli reguła routingu jest przekierowaniem lub gdy buforowanie nie jest włączone. |
| TimeTaken | Czas trwania akcji (w milisekundach). |
| TrackingReference | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |
| UserAgent | Typ przeglądarki używany przez klienta. |

**Uwaga:** W przypadku różnych konfiguracji routingu i zachowań ruchu niektóre pola, takie jak backendHostname, cacheStatus, sentToOriginShield i POP, mogą reagować z innymi wartościami. W poniższej tabeli objaśniono różne wartości, te pola będą mieć różne scenariusze:

| Scenariusze | Liczba wpisów dziennika | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Reguła routingu bez włączonego buforowania | 1 | Kod POP krawędzi | Zaplecze, gdzie żądanie zostało przesłane dalej | Fałsz | CONFIG_NOCACHE |
| Reguła routingu z włączonym buforowaniem. Trafienie pamięci podręcznej w punkcie POP krawędzi | 1 | Kod POP krawędzi | Pusty | Fałsz | Podstawa |
| Reguła routingu z włączonym buforowaniem. Chybienia w pamięci podręcznej w punkcie obecności, ale w podręcznym buforze zostanie osiągnięty bufor | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. nadrzędna pamięć podręczna w pamięci podręcznej</br>2. puste | 1. true</br>2. false | 1. CHYBIEŃ</br>2. PARTIAL_HIT |
| Reguła routingu z włączonym buforowaniem. Chybienia w pamięci podręcznej zarówno na krawędzi | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. nadrzędna pamięć podręczna w pamięci podręcznej</br>2. zaplecze, które ułatwia wypełnienie pamięci podręcznej | 1. true</br>2. false | 1. CHYBIEŃ</br>2. CHYBIEŃ |


## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu frontu drzwi](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)
