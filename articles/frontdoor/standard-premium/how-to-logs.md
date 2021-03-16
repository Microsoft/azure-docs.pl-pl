---
title: Rejestrowanie w warstwie Standardowa/Premium platformy Azure (wersja zapoznawcza)
description: W tym artykule wyjaśniono, jak rejestrowanie działa na platformie Azure — warstwa standardowa/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573901"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Rejestrowanie w warstwie Standardowa/Premium platformy Azure (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Drzwi frontonu platformy Azure udostępniają różne rejestrowania, które ułatwiają śledzenie, monitorowanie i debugowanie drzwi do przodu. 

* W dziennikach dostępu znajdują się szczegółowe informacje dotyczące każdego żądania, które AFD odbiera i które ułatwia analizowanie i monitorowanie wzorców dostępu oraz problemy z debugowaniem. 
* Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure.  
* Dzienniki sondowania kondycji udostępniają dzienniki dla każdej sondy zakończonej niepowodzeniem do źródła. 
* Dzienniki zapory aplikacji sieci Web (WAF) zawierają szczegółowe informacje o żądaniach, które są rejestrowane w trybie wykrywania lub zapobiegania punktowi końcowego drzwi frontonu platformy Azure. Domenę niestandardową, która jest skonfigurowana za pomocą WAF, można także wyświetlić za pomocą tych dzienników.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzienniki dostępu, dzienniki sondy kondycji i dzienniki WAF nie są domyślnie włączone. Wykonaj poniższe kroki, aby włączyć rejestrowanie. Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal. Dzienniki mogą być opóźnieniami do kilku minut. 

Masz trzy opcje przechowywania dzienników: 

* **Konto magazynu:** Konta magazynu najlepiej używać w scenariuszach, gdy dzienniki są przechowywane przez dłuższy czas i są analizowane w razie potrzeby. 
* **Centra zdarzeń:** Centra zdarzeń są świetną opcją integracji z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) lub zewnętrznymi magazynami danych. Na przykład: Splunk/usługi Datadog/Sumo. 
* **Log Analytics platformy Azure:** Usługa Azure Log Analytics w Azure Monitor jest najlepiej stosowana do ogólnego monitorowania w czasie rzeczywistym i analizy wydajności czołowych drzwi platformy Azure.

## <a name="configure-logs"></a>Konfigurowanie dzienników

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj pozycję Azure Front-Standard/Premium, a następnie wybierz profil z drzwiami platformy Azure.

1. W profilu przejdź do pozycji **monitorowanie**, a następnie wybierz pozycję **ustawienie diagnostyczne**. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Zrzut ekranu strony docelowej ustawień diagnostycznych.":::

1. W obszarze **Ustawienia diagnostyczne** wprowadź nazwę  **nazwy ustawień diagnostycznych**.

1. Wybierz  **Dziennik** z **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** i **FrontDoorWebApplicationFirewallLog**.

1. Wybierz **szczegóły lokalizacji docelowej**. Opcje miejsca docelowego to: 

    * **Wysyłanie do usługi Log Analytics**
        * Wybierz *obszar roboczy* *subskrypcja* i log Analytics.
    * **Archiwizowanie na koncie magazynu**
        * Wybierz *subskrypcję* i *konto magazynu*. i Ustaw czas **przechowywania (w dniach)**.
    * **Przesyłanie strumieniowe do centrum zdarzeń**
        * Wybierz *subskrypcję, przestrzeń nazw centrum zdarzeń, nazwę centrum zdarzeń (opcjonalnie)* i *nazwę zasad centrum zdarzeń*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Zrzut ekranu strony ustawień diagnostycznych.":::

1. Kliknij pozycję **Zapisz**.

## <a name="access-log"></a>Dziennik dostępu

Drzwi frontonu platformy Azure udostępniają obecnie pojedyncze żądania interfejsu API z każdym wpisem, który ma następujący schemat i jest rejestrowany w formacie JSON, jak pokazano poniżej. 

| Właściwość | Opis |
|----------|-------------| 
| TrackingReference | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez AFD, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |
| Godzina | Data i godzina dostarczenia przez AFD krawędzi żądania zawartości do klienta (w formacie UTC). |
| HttpMethod | Metoda HTTP używana przez żądanie: DELETE, GET, główna, OPTIONS, PATCH, POST lub PUT. |
| HttpVersion | Wersja protokołu HTTP, którą przeglądarka określiła w żądaniu. |
| RequestUri | Identyfikator URI odebranego żądania. To pole jest pełnym schematem, portem, domeną, ścieżką i ciągiem zapytania |
| HostName | Nazwa hosta w żądaniu od klienta. W przypadku włączenia domen niestandardowych i posiadania domeny wieloznacznej (*. contoso.com) nazwa hosta to a.contoso.com. Jeśli używasz domeny z platformą Azure (contoso.azurefd.net), nazwa hosta to contoso.azurefd.net. |
| RequestBytes | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania. Liczba bajtów danych zawartych w żądaniu przez przeglądarkę, łącznie z nagłówkami. |
| ResponseBytes | Bajty wysłane przez serwer wewnętrznej bazy danych jako odpowiedź. |
| UserAgent | Typ przeglądarki używany przez klienta. |
| ClientIp | Adres IP klienta, który dokonał oryginalnego żądania. Jeśli w żądaniu wystąpiło wyrażenie X-Forwarded-For, adres IP klienta zostanie pobrany z tego samego. |
| SocketIp | Adres IP bezpośredniego połączenia z krawędzią AFD. Jeśli Klient użył serwera proxy HTTP lub modułu równoważenia obciążenia do wysłania żądania, wartość SocketIp jest adresem IP serwera proxy lub modułu równoważenia obciążenia. |
| Opóźnienie | Czas od czasu AFD graniczny serwer odbiera żądanie klienta do momentu, w którym AFD wysyła ostatni bajt odpowiedzi do klienta w milisekundach. To pole nie uwzględnia opóźnienia sieci i buforowania protokołu TCP. |
| RequestProtocol | Protokół określony przez klienta w żądaniu: HTTP, HTTPS. |
| To elementu SecurityProtocol | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania. Możliwe wartości to: protokół SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Jeśli wartość protokołu żądania to HTTPS, to pole wskazuje szyfrowanie TLS/SSL negocjowane przez klienta i AFD na potrzeby szyfrowania. |
| Punkt końcowy | Nazwa domeny punktu końcowego AFD, na przykład contoso.z01.azurefd.net |
| HttpStatusCode | Kod stanu HTTP zwrócony z AFD. |
| Skakując | Punkt obecności, który odpowiedział na żądanie użytkownika.  |
| Stan pamięci podręcznej | Zawiera kod stanu, w jaki żądanie jest obsługiwane przez usługę CDN, gdy przyjdzie do buforowania. Możliwe wartości są TRAFIeń: żądanie HTTP zostało obsłużone z pamięci podręcznej AFD Edge. <br> **Chybień**: żądanie HTTP zostało obsłużone z pochodzenia. <br/> **PARTIAL_HIT**: niektóre bajty z żądania zostały obsłużone z pamięci podręcznej AFD Edge, podczas gdy niektóre bajty zostały obsłużone z punktu początkowego dla scenariuszy fragmentacji obiektów. <br> **CACHE_NOCONFIG**: przekazywanie żądań bez ustawień buforowania, w tym scenariusza obejścia. <br/> **PRIVATE_NOSTORE**: nie skonfigurowano pamięci podręcznej w ustawieniach buforowania przez klientów. <br> **REMOTE_HIT**: żądanie zostało obsłużone przez pamięć podręczną węzłów nadrzędnych. <br/> **N/A**:* * żądanie odrzucone przy użyciu PODPISANEGO adresu URL i zestawu reguł. |
| MatchedRulesSetName | Nazwy przetworzonych reguł. |
| RouteName | Nazwa trasy, która pasuje do żądania. |
| ClientPort | Port IP klienta, który wykonał żądanie. |
| Odwołującym | Adres URL witryny, z której pochodzi żądanie. |
| TimetoFirstByte | Czas w milisekundach od AFD odbiera żądanie do momentu wysłania pierwszego bajtu do klienta, jak to jest mierzone na frontonie platformy Azure. Ta właściwość nie mierzy danych klienta. |
| ErrorInfo | To pole zawiera szczegółowe informacje o tokenie błędu dla każdej odpowiedzi. <br> **NOERROR**: wskazuje, że nie znaleziono błędu. <br> **CertificateError**: ogólny błąd certyfikatu SSL. <br> **CertificateNameCheckFailed**: Nazwa hosta w certyfikacie SSL jest nieprawidłowa lub nie jest zgodna. <br> **ClientDisconnected**: niepowodzenie żądania z powodu połączenia sieciowego klienta. <br> **ClientGeoBlocked**: klient został zablokowany do lokalizacji geograficznej adresu IP. <br> **UnspecifiedClientError**: ogólny błąd klienta. <br> **InvalidRequest**: Nieprawidłowe żądanie. Przyczyną może być źle sformułowany nagłówek, treść i adres URL. <br> **DNSFailure**: błąd DNS. <br> **DNSTimeout**: zapytanie DNS służące do rozpoznawania wewnętrznej bazy danych przekroczyło limit czasu. <br> **DNSNameNotResolved**: nie można rozpoznać nazwy serwera lub adresu. <br> **OriginConnectionAborted**: połączenie ze źródłem zostało nieprawidłowo rozłączone. <br> **OriginConnectionError**: błąd połączenia ogólnego pochodzenia. <br> **OriginConnectionRefused**: połączenie z pochodzenia nie zostało ustanowione. <br> **OriginError**: ogólny błąd pochodzenia. <br> **OriginInvalidRequest**: Nieprawidłowe żądanie zostało wysłane do źródła. <br> **ResponseHeaderTooBig**: element źródłowy zwrócił za dużo nagłówka odpowiedzi. <br> **OriginInvalidResponse**:* * Origin zwróciła nieprawidłową lub nierozpoznaną odpowiedź. <br> **OriginTimeout**: upłynął limit czasu dla żądania źródła. <br> **ResponseHeaderTooBig**: element źródłowy zwrócił za dużo nagłówka odpowiedzi. <br> **RestrictedIP**: żądanie zostało zablokowane z powodu ograniczonych adresów IP. <br> **SSLHandshakeError**: nie można nawiązać połączenia ze źródłem z powodu błędu rozwstrząsania protokołu SSL. <br> **SSLInvalidRootCA**: RootCA jest nieprawidłowy. <br> **SSLInvalidCipher**: szyfr był nieprawidłowy, dla którego NAWIĄZANO połączenie HTTPS. <br> **OriginConnectionAborted**: połączenie ze źródłem zostało nieprawidłowo rozłączone. <br> **OriginConnectionRefused**: połączenie z pochodzenia nie zostało ustanowione. <br> **UnspecifiedError**: Wystąpił błąd, który nie mieści się w żadnym z błędów w tabeli. |
| OriginURL | Pełny adres URL źródła, w którym są wysyłane żądania. Składający się z schematu, nagłówka hosta, portu, ścieżki i ciągu zapytania. <br> Ponowne **Zapisywanie adresów URL**: Jeśli w zestawie reguł występuje reguła ponownego zapisywania adresu URL, ścieżka dotyczy ponownie zapisanej ścieżki. <br> **Pamięć podręczna w punkcie pop krawędzi** Jeśli jest osiągnięta pamięć podręczna w punkcie POP krawędzi, punkt początkowy to N/A. <br> **Duże żądanie** Jeśli żądana zawartość jest duża z wieloma żądaniami fragmentarycznymi pochodzącymi z powrotem do źródła, to pole będzie odpowiadać pierwszemu żądaniu do źródła. Aby uzyskać więcej informacji, zobacz fragmenty obiektów, aby uzyskać więcej szczegółów. |
| OriginIP | Adres IP źródła, który obsłużył żądanie. <br> **Trafienie pamięci podręcznej w punkcie pop krawędzi** Jeśli jest osiągnięta pamięć podręczna w punkcie POP krawędzi, punkt początkowy to N/A. <br> **Duże żądanie** Jeśli żądana zawartość jest duża z wieloma żądaniami fragmentarycznymi pochodzącymi z powrotem do źródła, to pole będzie odpowiadać pierwszemu żądaniu do źródła. Aby uzyskać więcej informacji, zobacz fragmenty obiektów, aby uzyskać więcej szczegółów. |
| Pochodzenie| Pełna nazwa DNS (nazwy hosta w adresie URL pochodzenia) do źródła. <br> **Trafienie pamięci podręcznej w punkcie pop krawędzi** Jeśli jest osiągnięta pamięć podręczna w punkcie POP krawędzi, punkt początkowy to N/A. <br> **Duże żądanie** Jeśli żądana zawartość jest duża z wieloma żądaniami fragmentarycznymi pochodzącymi z powrotem do źródła, to pole będzie odpowiadać pierwszemu żądaniu do źródła. Aby uzyskać więcej informacji, zobacz fragmenty obiektów, aby uzyskać więcej szczegółów. |

## <a name="health-probe-log"></a>Dziennik sondy kondycji

Dzienniki sondowania kondycji zapewniają rejestrowanie dla każdej sondy zakończonej niepowodzeniem w celu ułatwienia zdiagnozowania źródła.Dzienniki zawierają informacje, których można użyć w celu przywrócenia pochodzenia do usługi. Niektóre scenariusze przydatne dla tego dziennika mogą być następujące:

* Zauważono ruch z przodu platformy Azure do niektórych źródeł. Na przykład tylko trzy z czterech źródeł, które odbierają ruch. Chcesz się dowiedzieć, czy źródła są otrzymują sondy, a jeśli nie, Przyczyna błędu.  

* Zauważono, że wartość% kondycji pochodzenia jest niższa niż oczekiwano i chcesz wiedzieć, które źródło nie powiodło się i przyczynę niepowodzenia.

### <a name="health-probe-log-properties"></a>Właściwości dziennika sondy kondycji

Każdy dziennik sondy kondycji ma następujący schemat.

| Właściwość | Opis |
| --- | --- |
| HealthProbeId  | Unikatowy identyfikator identyfikujący żądanie. |
| Godzina | Czas zakończenia sondy |
| HttpMethod | Metoda HTTP używana w żądaniu sondy kondycji. Wartości obejmują GET i na podstawie konfiguracji sondy kondycji. |
| Wynik | Stan sondy kondycji na źródło, wartość obejmuje sukces i inny tekst błędu. |
| HttpStatusCode  | Kod stanu HTTP zwrócony ze źródła. |
| ProbeURL (obiekt docelowy) | Pełny adres URL źródła, w którym są wysyłane żądania. Składający się z schematu, nagłówka hosta, ścieżki i ciągu zapytania. |
| Pochodzenie  | Źródło, w którym są wysyłane żądania. To pole służy do lokalizowania postanowień zainteresowania, jeśli źródło jest skonfigurowane do nazwy FDQN. |
| POP | Punkt pop, który wysłał żądanie sondowania. |
| Źródłowy adres IP | Docelowy adres IP źródła. To pole jest przydatne w lokalizowaniu miejsc pochodzenia w przypadku konfigurowania pochodzenia przy użyciu nazwy FDQN. |
| TotolaLatency | Czas od AFDX Edge wysyła żądanie do źródła do źródła czasu wysyła ostatnią odpowiedź do AFDX Edge. |
| ConnectionLatency| Czas trwania podczas konfigurowania połączenia TCP w celu wysłania żądania sondowania HTTP do źródła. | 
| Opóźnienie DNSResolution | Czas trwania rozpoznawania nazw DNS, jeśli źródło jest skonfigurowane jako nazwy FDQN zamiast adresu IP. Nie dotyczy, jeśli źródło jest skonfigurowane na adres IP. |

### <a name="health-probe-log-sample-in-json"></a>Przykład dziennika sondy kondycji w formacie JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają informacje o operacjach wykonywanych na platformie Azure — Standard/Premium. Dzienniki zawierają szczegółowe informacje o tym, co i kiedy operacja zapisu została wykonana w ramach zewnętrznych drzwi platformy Azure. 

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji GET. Nie obejmują one również operacji wykonywanych przy użyciu Azure Portal lub oryginalnego interfejsu API zarządzania. 

Uzyskuj dostęp do dzienników aktywności w swoich drzwiach lub wszystkich dzienników zasobów platformy Azure w Azure Monitor.

Aby wyświetlić dzienniki aktywności:

1. Wybierz profil dla drzwi przednich.

1. Wybierz pozycję **Dziennik aktywności.**

1. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [raportach usługi Azure Front-Standard/Premium (wersja zapoznawcza)](how-to-reports.md).
- Dowiedz się więcej o [metrykach monitorowania w czasie rzeczywistym na platformie Azure (wersja zapoznawcza)](how-to-monitor-metrics.md).
