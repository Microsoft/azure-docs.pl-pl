---
title: Monitorowanie metryk i dzienników w usłudze Azure front-drzwi | Microsoft Docs
description: W tym artykule opisano różne metryki i dzienniki dostępu obsługiwane przez usługę Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: a972123604cafbfc4c6753c1fe3ad21147b53b2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550663"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorowanie metryk i dzienników na platformie Azure — drzwiczki

Korzystając z usług frontonu platformy Azure, można monitorować zasoby w następujący sposób:

- **Metryki**. Drzwi frontonu platformy Azure mają obecnie osiem metryk do wyświetlania liczników wydajności.
- **Dzienniki**. Dzienniki działań i diagnostyki umożliwiają zapisywanie lub zużywanie wydajności, dostępu i innych danych z zasobów na potrzeby monitorowania.

##  <a name="metrics"></a><a name="metrics"></a>Metryki

Metryki to funkcja niektórych zasobów platformy Azure, która umożliwia wyświetlanie liczników wydajności w portalu. Dostępne są następujące metryki czołowych drzwi:

| Metric | Nazwa wyświetlana metryki | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| RequestCount | Liczba żądań | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba żądań klientów obsłużonych przez tylne drzwi.  |
| RequestSize | Rozmiar żądania | Bajty | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako żądania od klientów do przednich drzwi. |
| ResponseSize | Rozmiar odpowiedzi | Bajty | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako odpowiedzi z pierwszych drzwi do klientów. |
| TotalLatency | Łączne opóźnienie | ) | Wartości httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Łączny czas od żądania klienta odebranego przez drzwi do momentu wysłania ostatniego bajtu odpowiedzi z AFD do klienta. |
| BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Liczba | Wartości httpStatus</br>HttpStatusGroup</br>Zaplecze | Liczba żądań wysyłanych z przednich drzwi do frontonu. |
| BackendRequestLatency | Opóźnienie żądania wewnętrznej bazy danych | ) | Zaplecze | Czas, jaki upłynął od momentu wysłania żądania przez drzwi do zaplecza do momentu odebrania ostatniego bajtu odpowiedzi z zaplecza do przodu. |
| BackendHealthPercentage | Procent kondycji zaplecza | Procent | Zaplecze</br>Ustawień httpsettings elementu | Procent pomyślnych sond kondycji z czołowych drzwi do frontonu. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Liczba | PolicyName</br>RuleName</br>Akcja | Liczba żądań klientów przetworzonych przez zabezpieczenia warstwy aplikacji dla drzwi z przodu. |

## <a name="activity-logs"></a><a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności zawierają informacje o operacjach wykonywanych na wierzchu drzwi. Określają one również elementy, kto i kiedy dla operacji zapisu (Put, post lub Delete) podejmowane na wierzchu drzwi.

>[!NOTE]
>Dzienniki aktywności nie obejmują operacji odczytu (Get). Nie obejmują one również operacji wykonywanych przy użyciu Azure Portal lub oryginalnego interfejsu API zarządzania.

Uzyskuj dostęp do dzienników aktywności w swoich drzwiach lub wszystkich dzienników zasobów platformy Azure w Azure Monitor. Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie z drzwiami czołowymi.
2. Wybierz pozycję **Dziennik aktywności**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Dziennik aktywności":::

3. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności.

Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane przez zasób. Aby uzyskać więcej informacji, zobacz [Azure monitor dzienników diagnostycznych](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Dzienniki diagnostyczne":::

Aby skonfigurować dzienniki diagnostyczne dla drzwi czołowych:

1. Wybierz swoje drzwi z platformy Azure.

2. Wybierz pozycję **Ustawienia diagnostyczne**.

3. Wybierz pozycję **Włącz diagnostykę**. Archiwizuj dzienniki diagnostyczne wraz z metrykami na koncie magazynu, przesyłaj strumieniowo do centrum zdarzeń lub wysyłaj je do dzienników Azure Monitor.

Drzwi z przodu obecnie udostępniają dzienniki diagnostyczne. Dzienniki diagnostyczne zapewniają pojedyncze żądania interfejsu API za pomocą każdego wpisu, który ma następujący schemat:

| Właściwość  | Opis |
| ------------- | ------------- |
| BackendHostname | Jeśli żądanie zostało przekazane do zaplecza, to pole reprezentuje nazwę hosta zaplecza. To pole będzie puste, jeśli żądanie zostanie przekierowane lub przekazane do regionalnej pamięci podręcznej (gdy buforowanie jest włączone dla reguły routingu). |
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
| RulesEngineMatchNames | Nazwy reguł, które pasują do żądania. |
| To elementu SecurityProtocol | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania. |
| SentToOriginShield </br> (przestarzałe) * **zapoznaj się z uwagami dotyczącymi wycofania w poniższej sekcji.**| Jeśli wartość jest równa true, oznacza to, że żądanie zostało odebrane z pamięci podręcznej osłony pochodzenia, zamiast w punkcie obecności krawędzi. Tarcza źródła jest nadrzędną pamięcią podręczną służącą do usprawnienia współczynnika trafień pamięci podręcznej. |
| isReceivedFromClient | Jeśli wartość jest równa true, oznacza to, że żądanie pochodzi od klienta. Jeśli wartość jest równa false, żądanie jest chybień w krawędzi (podrzędny) i jest odrzucane z osłony pochodzenia (element nadrzędny). |
| TimeTaken | Czas od pierwszego bajtu żądania w przód do ostatniego bajtu odpowiedzi w sekundach. |
| TrackingReference | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |
| UserAgent | Typ przeglądarki używany przez klienta. |
| ErrorInfo | To pole zawiera konkretny typ błędu w celu dalszej procedury rozwiązywania problemów. </br> Możliwe wartości to: </br> **NOERROR**: wskazuje, że nie znaleziono błędu. </br> **CertificateError**: ogólny błąd certyfikatu SSL.</br> **CertificateNameCheckFailed**: Nazwa hosta w certyfikacie SSL jest nieprawidłowa lub nie jest zgodna. </br> **ClientDisconnected**: niepowodzenie żądania z powodu połączenia sieciowego klienta. </br> **UnspecifiedClientError**: ogólny błąd klienta. </br> **InvalidRequest**: Nieprawidłowe żądanie. Przyczyną może być źle sformułowany nagłówek, treść i adres URL. </br> **DNSFailure**: błąd DNS. </br> **DNSNameNotResolved**: nie można rozpoznać nazwy serwera lub adresu. </br> **OriginConnectionAborted**: połączenie ze źródłem zostało zatrzymane w sposób nieoczekiwany. </br> **OriginConnectionError**: błąd połączenia ogólnego pochodzenia. </br> **OriginConnectionRefused**: nie można nawiązać połączenia z pochodzenia. </br> **OriginError**: ogólny błąd pochodzenia. </br> **OriginInvalidResponse**: Źródło zwróciło nieprawidłową lub nierozpoznaną odpowiedź. </br> **OriginTimeout**: upłynął limit czasu dla żądania źródła. </br> **ResponseHeaderTooBig**: Źródło zwróciło za dużo nagłówka odpowiedzi. </br> **RestrictedIP**: żądanie zostało zablokowane z powodu ograniczonych adresów IP. </br> **SSLHandshakeError**: nie można nawiązać połączenia ze źródłem z powodu błędu rozwstrząsania protokołu SSL. </br> **UnspecifiedError**: Wystąpił błąd, który nie mieści się w żadnym z błędów w tabeli. |

### <a name="sent-to-origin-shield-deprecation"></a>Wysłane do przestarzałej osłony pochodzenia
Właściwość nieprzetworzonego dziennika **isSentToOriginShield** jest przestarzała i zastąpiona przez nowe pole **isReceivedFromClient**. Użyj nowego pola, jeśli używasz już przestarzałego pola. 

Dzienniki pierwotne obejmują dzienniki wygenerowane z obu krawędzi sieci CDN (podręczny) i osłony pochodzenia. Tarcza pierwotna odnosi się do węzłów nadrzędnych, które są strategicznie zlokalizowane na całym świecie. Te węzły komunikują się z serwerami pochodzenia i zmniejszają obciążenie ruchu podczas pochodzenia. 

Dla każdego żądania, które przechodzi do osłony pochodzenia, istnieją wpisy z 2 dziennikami:

* Jeden dla węzłów brzegowych
* Jeden dla osłony pochodzenia. 

Aby odróżnić ruch wychodzący lub odpowiedzi między węzłami krawędzi a osłoną pierwotną, możesz użyć pola **isReceivedFromClient** , aby uzyskać poprawne dane. 

Jeśli wartość jest równa false, oznacza to, że żądanie jest odpowiedzi z osłony pochodzenia do węzłów brzegowych. Takie podejście obowiązuje do porównania nieprzetworzonych dzienników z danymi dotyczącymi rozliczeń. Opłaty nie są naliczane dla ruchu wychodzącego z osłony pochodzenia do węzłów brzegowych. Opłaty są naliczane za ruch wychodzący z węzłów krawędzi do klientów. 

**Przykład zapytania Kusto do wykluczenia dzienników generowanych w ramach osłony pochodzenia w Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> W przypadku różnych konfiguracji routingu i zachowań ruchu niektóre pola, takie jak backendHostname, cacheStatus, isReceivedFromClient i POP, mogą reagować z innymi wartościami. W poniższej tabeli objaśniono różne wartości tych pól dla różnych scenariuszy:

| Scenariusze | Liczba wpisów dziennika | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Reguła routingu bez włączonego buforowania | 1 | Kod POP krawędzi | Zaplecze, gdzie żądanie zostało przesłane dalej | Prawda | CONFIG_NOCACHE |
| Reguła routingu z włączonym buforowaniem. Trafienie pamięci podręcznej w punkcie POP krawędzi | 1 | Kod POP krawędzi | Pusty | Prawda | Podstawa |
| Reguła routingu z włączonym buforowaniem. Chybienia w pamięci podręcznej w punkcie obecności, ale w podręcznym buforze w pamięci podręcznej | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. nadrzędna pamięć podręczna w pamięci podręcznej</br>2. puste | 1. true</br>2. false | 1. CHYBIEŃ</br>2. TRAFIENIE |
| Reguła routingu z włączonym buforowaniem. Buforuje chybień w punkcie obecności, ale część pamięci podręcznej trafij w menu nadrzędnej pamięci podręcznej | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. nadrzędna pamięć podręczna w pamięci podręcznej</br>2. zaplecze, które ułatwia wypełnienie pamięci podręcznej | 1. true</br>2. false | 1. CHYBIEŃ</br>2. PARTIAL_HIT |
| Reguła routingu z włączonym buforowaniem. Pamięć podręczna PARTIAL_HIT w punkcie obecności, ale w podręcznym buforze zostanie osiągnięta pamięć podręczna | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. true</br>2. false | 1. PARTIAL_HIT</br>2. TRAFIENIE |
| Reguła routingu z włączonym buforowaniem. Chybienia w pamięci podręcznej zarówno w POPP, jak i nadrzędnej pamięci podręcznej | 2 | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. kod POP krawędzi</br>2. nadrzędny kod POP pamięci podręcznej | 1. true</br>2. false | 1. CHYBIEŃ</br>2. CHYBIEŃ |

> [!NOTE]
> W przypadku scenariuszy buforowania wartość w polu stan pamięci podręcznej będzie partial_hit, gdy niektóre bajty dla żądania zostaną obsłużone z pamięci podręcznej z przodu lub z bufora osłony pochodzenia, a niektóre z nich są obsługiwane przez źródło dla dużych obiektów.

Drzwi przednich wykorzystują technikę nazywaną fragmentem obiektu. Gdy żądanie dużego pliku jest wymagane, drzwiczki przednie pobierają mniejsze fragmenty pliku ze źródła. Po otrzymaniu przez serwer POP z drzwiczkami pełnego lub bajtowego zakresu żądanego pliku serwer graniczny frontu drzwi żąda pliku od źródła w fragmentach 8 MB.

Po nadejściu fragmentu na krawędzi przedniej drzwi jest on buforowany i natychmiast obsługiwany przez użytkownika. Drzwiczki z przodu następnie pobierają równolegle Następny fragment. Ten pobrany z wyprzedzeniem zapewnia, że zawartość pozostaje jednego fragmentu przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli będzie to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient zamknie połączenie. Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz RFC 7233. Drzwi przednich buforują wszystkie fragmenty, gdy są odbierane. Cały plik nie musi być buforowany w pamięci podręcznej z przodu. Żądania dotyczące plików lub zakresów bajtów są obsługiwane z pamięci podręcznej z przodu. Jeśli nie wszystkie fragmenty znajdują się w pamięci podręcznej, należy wykonać pobieranie z wyprzedzeniem, aby zażądać fragmentów ze źródła. Ta optymalizacja polega na umożliwieniu serwerowi pochodzenia obsługi żądań z zakresu bajtów. Jeśli serwer pochodzenia nie obsługuje żądań zakresu bajtów, Ta optymalizacja nie jest skuteczna.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu frontu drzwi](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)
