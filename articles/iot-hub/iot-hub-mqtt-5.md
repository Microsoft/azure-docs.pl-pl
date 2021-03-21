---
title: Obsługa platformy Azure IoT Hub MQTT 5 (wersja zapoznawcza)
description: Dowiedz się więcej o obsłudze IoT Hub MQTT 5
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603553"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5 — Omówienie pomocy technicznej (wersja zapoznawcza)

**Wersja:** 2,0 **API-Version:** 2020-10-01 — wersja zapoznawcza

Ten dokument zawiera definicje IoT Hub interfejsu API płaszczyzny danych za pośrednictwem protokołu MQTT w wersji 5,0. Zobacz [Dokumentacja interfejsu API](iot-hub-mqtt-5-reference.md) , aby uzyskać pełne definicje w tym interfejsie API.

## <a name="prerequisites"></a>Wymagania wstępne

- [Włącz tryb podglądu](iot-hub-preview-mode.md) w marce nowego centrum IoT Hub, aby wypróbować program MQTT 5.
- Wymagana jest wcześniejsza znajomość [specyfikacji MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) .

## <a name="level-of-support-and-limitations"></a>Poziom pomocy technicznej i ograniczeń

Obsługa IoT Hub MQTT 5 jest w wersji zapoznawczej i jest ograniczona w następujący sposób (komunikowane się z klientem za pośrednictwem `CONNACK` właściwości, chyba że wyraźnie zaznaczono inaczej):

- Brak oficjalnego wsparcia z [zestawem SDK urządzeń IoT Hub platformy Azure](iot-hub-devguide-sdks.md) .
- Identyfikatory subskrypcji nie są obsługiwane.
- Udostępnione subskrypcje nie są obsługiwane.
- `RETAIN` nie jest obsługiwana.
- `Maximum QoS` to `1`.
- `Maximum Packet Size` jest `256 KiB` (z zastrzeżeniem dalszych ograniczeń dla operacji).
- Przypisane identyfikatory klientów nie są obsługiwane.
- `Keep Alive` jest ograniczone do `19 min` (maksymalne opóźnienie sprawdzania na żywo — `28.5 min` ).
- `Topic Alias Maximum` to `10`.
- `Response Information` nie jest obsługiwana; `CONNACK` nie zwraca `Response Information` właściwości, nawet jeśli `CONNECT` zawiera `Request Response Information` Właściwość.
- `Receive Maximum` (maksymalna liczba dozwolonych niepotwierdzonych `PUBLISH` pakietów (w kierunku serwera klienta) z `QoS: 1` ) to `16` .
- Pojedynczy klient nie może mieć więcej niż `50` subskrypcji.
  Po osiągnięciu limitu program `SUBACK` zwróci `0x97` kod przyczyny (przekroczenie limitu przydziału) dla subskrypcji.

## <a name="connection-lifecycle"></a>Cykl życia połączenia

### <a name="connection"></a>Połączenie

Aby połączyć klienta IoT Hub przy użyciu tego interfejsu API, Ustanów połączenie dla specyfikacji MQTT 5.
Klient musi wysłać `CONNECT` pakiet w ciągu 30 sekund po pomyślnym uzgadnianiu protokołu TLS lub serwer zamknie połączenie.
Oto przykład `CONNECT` pakietu:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` Właściwość jest wymagana i określa, która metoda uwierzytelniania jest używana. Aby uzyskać więcej informacji na temat metody uwierzytelniania, zobacz [uwierzytelnianie](#authentication).
- `Authentication Data` Obsługa właściwości zależy od `Authentication Method` . Jeśli `Authentication Method` jest ustawiona na `SAS` , `Authentication Data` jest wymagana i musi zawierać prawidłowy podpis. Aby uzyskać więcej informacji na temat danych uwierzytelniania, zobacz [uwierzytelnianie](#authentication).
- `api-version` Właściwość jest wymagana i musi być ustawiona na wartość wersji interfejsu API podaną w nagłówku tej specyfikacji, aby można było zastosować tę specyfikację.
- `host` Właściwość definiuje nazwę hosta dzierżawcy. Jest to wymagane, chyba że podczas uzgadniania protokołu TLS zaprezentowano rozszerzenie SNI w rekordzie powitalnym klienta
- `sas-at` definiuje czas połączenia.
- `sas-expiry` definiuje czas wygaśnięcia podanej sygnatury dostępu współdzielonego.
- `client-agent` Opcjonalnie komunikuje informacje o kliencie tworzącym połączenie.

> [!NOTE]
> `Authentication Method` i inne właściwości w ramach specyfikacji z nazwami dużymi są właściwościami pierwszej klasy w MQTT 5 — są one opisane szczegółowo w specyfikacji MQTT 5. `api-version` i inne właściwości w przypadku łącznika są właściwościami użytkownika specyficznymi dla IoT Hub API.

Program IoT Hub reaguje na `CONNACK` pakiet po zakończeniu uwierzytelniania i pobierania danych w celu zapewnienia obsługi połączenia. Jeśli połączenie zostanie pomyślnie nawiązane, wygląda następująco `CONNACK` :

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Te `CONNACK` właściwości pakietu są zgodne ze [specyfikacją MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Odzwierciedlają możliwości IoT Hub.

### <a name="authentication"></a>Authentication

`Authentication Method`Właściwość na `CONNECT` kliencie definiuje rodzaj uwierzytelniania używanego dla tego połączenia:

- `SAS` — Sygnatura dostępu współdzielonego jest udostępniana we `CONNECT` `Authentication Data` właściwości,
- `X509` -Klient korzysta z uwierzytelniania certyfikatu klienta.

 Uwierzytelnianie nie powiedzie się, jeśli metoda uwierzytelniania nie jest zgodna z konfiguracją metody klienta w IoT Hub.

> [!NOTE]
> Ten interfejs API wymaga `Authentication Method` , aby właściwość została ustawiona w `CONNECT` pakiecie. Jeśli `Authentication Method` Właściwość nie jest określona, połączenie kończy się niepowodzeniem z `Bad Request` odpowiedzią.

Uwierzytelnianie nazwy użytkownika/hasła używane w poprzednich wersjach interfejsu API nie jest obsługiwane.

#### <a name="sas"></a>SAS

W przypadku uwierzytelniania za pomocą sygnatury dostępu współdzielonego klient musi podać podpis kontekstu połączenia. To potwierdza autentyczność połączenia usługi MQTT. Podpis musi być oparty na jednym z dwóch kluczy uwierzytelniania w konfiguracji klienta w IoT Hub lub jednym z dwóch kluczy dostępu współdzielonego [zasad dostępu współdzielonego](iot-hub-devguide-security.md).

Ciąg do podpisania musi być sformułowany w następujący sposób:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` jest wyprowadzany z rozszerzenia SNI (prezentowany przez klienta w rekordzie powitalnej klienta podczas uzgadniania protokołu TLS) lub `host` Właściwość użytkownika w `CONNECT` pakiecie.
- `Client Id` jest identyfikatorem klienta w `CONNECT` pakiecie.
- `sas-policy` -Jeśli istnieje, definiuje zasady dostępu IoT Hub używane na potrzeby uwierzytelniania. Jest ona zaszyfrowana jako właściwość użytkownika w `CONNECT` pakiecie. Opcjonalne: pominięcie oznacza, że zamiast tego będą używane ustawienia uwierzytelniania w rejestrze urządzeń.
- `sas-at` -Jeśli jest obecny, określa czas bieżącego połączenia. Jest ona zaszyfrowana jako właściwość użytkownika `time` typu w `CONNECT` pakiecie.
- `sas-expiry` definiuje czas wygaśnięcia uwierzytelniania. Jest to `time` Właściwość użytkownika o określonym typie dla `CONNECT` pakietu. Ta właściwość jest wymagana.

W przypadku parametrów opcjonalnych, w przypadku pominięcia, należy zamiast tego użyć pustego ciągu do podpisania.

HMAC-SHA256 jest używany do mieszania ciągu na podstawie jednego z kluczy symetrycznych urządzenia. Wartość skrótu jest następnie ustawiana jako wartość `Authentication Data` właściwości.

#### <a name="x509"></a>X509

Jeśli `Authentication Method` Właściwość jest ustawiona na `X509` , IoT Hub uwierzytelnia połączenie na podstawie podanego certyfikatu klienta.

#### <a name="reauthentication"></a>Ponowne uwierzytelnianie

W przypadku użycia uwierzytelniania opartego na protokole SAS zalecamy użycie tokenów uwierzytelniania o krótkim czasie życia. Aby zachować uwierzytelnienie połączenia i uniemożliwić rozłączenie z powodu wygaśnięcia, klient musi się ponownie uwierzytelnić, wysyłając `AUTH` pakiet z `Reason Code: 0x19` (ponowne uwierzytelnienie):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Przepisy

- `Authentication Method` musi być taka sama jak wartość używana do uwierzytelniania początkowego
- Jeśli połączenie zostało pierwotnie uwierzytelnione przy użyciu SYGNATURy dostępu współdzielonego opartego na zasadach współużytkowania, podpis używany do ponownego uwierzytelnienia musi być oparty na tych samych zasadach.

Jeśli ponowne uwierzytelnienie zakończy się pomyślnie, IoT Hub wysyła `AUTH` pakiet z `Reason Code: 0x00` (powodzenie). W przeciwnym razie IoT Hub wysyła `DISCONNECT` pakiet z `Reason Code: 0x87` (nie autoryzowane) i zamyka połączenie.

### <a name="disconnection"></a>Odłączenie

Serwer może rozłączyć klienta z kilku powodów:

- Klient jest błędna w sposób, który nie może odpowiedzieć bezpośrednio na negatywne potwierdzenie (lub odpowiedź),
- serwer nie może zachować Aktualności połączenia,
- Klient z taką samą tożsamością został połączony.

Serwer może rozłączyć się z dowolnym kodem przyczyny zdefiniowanym w specyfikacji MQTT 5,0. Ważne uwagi:

- `135` (Nieautoryzowane) w przypadku niepowodzenia ponownego uwierzytelniania bieżący token sygnatury dostępu współdzielonego wygasa lub zmiany poświadczeń urządzenia
- `142` (Sesja przejmowana) po otwarciu nowego połączenia z tą samą tożsamością klienta.
- `159` (Przekroczono częstotliwość połączeń), gdy szybkość połączenia dla Centrum IoT Hub przekracza  
- `131` (Błąd specyficzny dla implementacji) jest używany dla wszystkich błędów niestandardowych zdefiniowanych w tym interfejsie API. `status` i `reason` właściwości zostaną użyte do przekazywania szczegółowych informacji o przyczynie odłączenia (patrz [odpowiedź](#response) na szczegóły).

## <a name="operations"></a>Operacje

Wszystkie funkcje w tym interfejsie API są wyrażane jako operacje. Oto przykład operacji wysyłania telemetrii:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Aby uzyskać pełną specyfikację operacji w tym interfejsie API, zobacz [Dokumentacja interfejsu API](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Wszystkie przykłady w tej specyfikacji są pokazane w perspektywie klienta. Znak `->` oznacza, że pakiet jest wysyłany `<-` przez klienta.

### <a name="message-topics-and-subscriptions"></a>Tematy i subskrypcje dotyczące wiadomości

Tematy używane w komunikatach operacji w tym interfejsie API zaczynają się od `$iothub/` .
Semantyka brokera MQTT nie ma zastosowania do tych operacji (zobacz "[Tematy \$ zaczynające się ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)od" w celu uzyskania szczegółowych informacji).
Tematy zaczynające się od `$iothub/` tego nie są zdefiniowane w tym interfejsie API nie są obsługiwane:

- Wysyłanie komunikatów do niezdefiniowanego tematu skutkuje `Not Found` odpowiedzią (zobacz [odpowiedź](#response) na szczegóły poniżej),
- Subskrybowanie niezdefiniowanego tematu skutkuje wynikiem `SUBACK` `Reason Code: 0x8F` (nieprawidłowy filtr tematu).

W nazwach tematów i nazwach właściwości jest rozróżniana wielkość liter i musi być dokładne dopasowanie. Na przykład `$iothub/telemetry/` nie jest obsługiwany w czasie `$iothub/telemetry` .

> [!NOTE]
> Symbole wieloznaczne w subskrypcjach w obszarze `$iothub/..` nie są obsługiwane. Oznacza to, że klient nie może subskrybować usługi `$iothub/+` lub `$iothub/#` . Podjęto próbę wykonania tej czynności w programie `SUBACK` `Reason Code: 0xA2` (subskrypcje wieloznaczne nie są obsługiwane). Obsługiwane są tylko symbole wieloznaczne pojedynczego segmentu ( `+` ) zamiast parametrów ścieżki w nazwie tematu dla operacji, które je zawierają.

### <a name="interaction-types"></a>Typy interakcji

Wszystkie operacje w tym interfejsie API są oparte na jednym z dwóch typów interakcji:

- Komunikat z opcjonalnym potwierdzeniem (MessageAck)
- Request-Response (ReqRep)

Operacje również różnią się w zależności od kierunku początkowego komunikatu w programie Exchange:

- Klient-serwer (C2S)
- Serwer-klient (S2C)

Na przykład wysyłanie danych telemetrycznych to operacja klient-serwer typu "komunikat z potwierdzeniem", podczas gdy obsługa bezpośredniej metody jest operacją serwer-klient typu Request-Response.

#### <a name="message-acknowledgement-interactions"></a>Interakcje z potwierdzeniem komunikatów

Obsługa komunikatów z opcjonalnym potwierdzeniem (MessageAck) jest wyrażana jako `PUBLISH` wymiana `PUBACK` pakietów i w MQTT. Potwierdzenie jest opcjonalne i nadawca może zrezygnować z żądania, wysyłając `PUBLISH` pakiet za pomocą polecenia `QoS: 0` .

> [!NOTE]
> Jeśli właściwości w `PUBACK` pakiecie muszą być obcinane ze względu na `Maximum Packet Size` zadeklarowane przez klienta, IoT Hub zachowa tyle właściwości użytkownika, ile może się zmieścić w danym limicie. Właściwości użytkownika w pierwszej kolejności mają wyższą szansę wysłania niż wymienione później; `Reason String` Właściwość ma najniższy priorytet.

##### <a name="example-of-simple-messageack-interaction"></a>Przykład prostej interakcji MessageAck

Komunikat:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Potwierdzenie (powodzenie):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interakcje Request-Response

W Request-Response (ReqRep) interakcje zarówno żądania, jak i odpowiedzi przekładają się na `PUBLISH` pakiety z `QoS: 0` .

`Correlation Data` Właściwość musi być ustawiona w obu i jest używana do dopasowania pakietu odpowiedzi do żądania pakietu.

Ten interfejs API używa pojedynczego tematu odpowiedzi `$iothub/responses` dla wszystkich operacji ReqRep. Subskrybowanie/anulowanie subskrypcji z tego tematu dla operacji klient-serwer nie jest wymagane — serwer założono, że wszyscy klienci mają subskrypcję.

##### <a name="example-of-simple-reqrep-interaction"></a>Przykład prostej interakcji ReqRep

Żądanie:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Odpowiedź (sukces):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Interakcje ReqRep nie obsługują `PUBLISH` pakietów z `QoS: 1` komunikatami żądania lub odpowiedzi. Wysyłanie `PUBLISH` wyników żądania w `Bad Request` odpowiedzi.

Maksymalna długość obsługiwana w `Correlation Data` właściwości wynosi 16 bajtów. Jeśli `Correlation Data` Właściwość `PUBLISH` pakietu ma ustawioną wartość dłuższą niż 16 bajtów, IoT Hub wysyłany `DISCONNECT` z `Bad Request` wynikiem i zamyka połączenie. To zachowanie dotyczy tylko pakietów wymienianych w ramach tego interfejsu API.

> [!NOTE]
> Dane korelacji są dowolną sekwencją bajtów, np. nie jest to ciąg UTF-8.
>
> ReqRep użyć wstępnie zdefiniowanych tematów do odpowiedzi; Właściwość tematu odpowiedzi w `PUBLISH` pakiecie żądania (jeśli jest ustawiona przez nadawcę) jest ignorowana.

IoT Hub automatycznie subskrybuje klienta w odpowiedzi na temat wszystkich operacji ReqRep klienta na serwerze. Nawet jeśli klient jawnie anulował subskrypcję z tematu odpowiedzi, IoT Hub przywraca subskrypcję automatycznie. W przypadku interakcji z ReqRep serwer-klient nadal konieczne jest zasubskrybowanie urządzenia.

### <a name="message-properties"></a>Właściwości komunikatu

Właściwości operacji — zdefiniowane przez system lub użytkownika — są wyrażane jako właściwości pakietu w MQTT 5.

Nazwy właściwości użytkownika są rozróżniane wielkości liter i muszą być napisane dokładnie zgodnie z definicją. Na przykład `Trace-ID` nie jest obsługiwany w czasie `trace-id` .

Żądania ze właściwościami użytkownika poza specyfikacją i bez prefiksu `@` powodują błąd.

Właściwości systemu są kodowane jako właściwości pierwszej klasy (na przykład `Content Type` ) lub jako właściwości użytkownika. Specyfikacja zawiera wyczerpującą listę obsługiwanych właściwości systemu.
Wszystkie właściwości pierwszej klasy są ignorowane, chyba że ich obsługa jest jawnie określona w specyfikacji.

W przypadku, gdy właściwości zdefiniowane przez użytkownika są dozwolone, ich nazwy muszą następować po formacie `@{property name}` . Właściwości zdefiniowane przez użytkownika obsługują tylko prawidłowe wartości ciągu UTF-8. na przykład `MyProperty1` Właściwość o wartości `15` musi być zakodowana jako właściwość użytkownika o nazwie `@MyProperty` i wartości `15` .

Jeśli IoT Hub nie rozpoznaje właściwości użytkownika, jest on traktowany jako błąd i IoT Hub odpowiada za `PUBACK` pomocą `Reason Code: 0x83` (błąd specyficzny dla implementacji) i `status: 0100` (Nieprawidłowe żądanie). Jeśli nie zażądano potwierdzenia (QoS: 0), `DISCONNECT` pakiet z tym samym błędem zostanie wysłany z powrotem, a połączenie zostanie przerwane.

Ten interfejs API definiuje następujące typy danych oprócz `string` :

- `time`: liczba milisekund od `1970-01-01T00:00:00.000Z` . na `1600987195320` przykład, `2020-09-24T22:39:55.320Z`
- `u32`: unsigned 32-bit Number Integer,
- `u64`: unsigned 64-bit Number Integer,
- `i32`: ze znakiem 32-bitową liczbą całkowitą.

### <a name="response"></a>Reakcja

Interakcje mogą powodować różne wyniki: `Success` , `Bad Request` , `Not Found` i inne.
Wyniki różnią się od siebie przez `status` Właściwość użytkownika. `Reason Code` w `PUBACK` pakietach (dla interakcji MessageAck) dopasowuje `status` się w sensie, gdzie jest to możliwe.

> [!NOTE]
> Jeśli klient określi `Request Problem Information: 0` pakiet Connect, żadne właściwości użytkownika nie będą wysyłane w pakietach w `PUBACK` celu zachowania zgodności ze specyfikacją MQTT 5, łącznie z `status` właściwością. W takim przypadku klient nadal może polegać na `Reason Code` określeniu, czy potwierdzenie jest pozytywne czy ujemne. 

Każda interakcja ma domyślny (lub powodzenie). Jest on `Reason Code` `0` i ma `status` Właściwość "nie ustawiono". W przeciwnym razie:

- W przypadku interakcji MessageAck są one `PUBACK` `Reason Code` inne niż 0x0 (powodzenie). `status` Właściwość może być obecna w celu dokładniejszego wyjaśnienia wyniku.
- W przypadku interakcji ReqRep odpowiedź `PUBLISH` Pobiera `status` ustawioną właściwość.
- Ponieważ nie ma możliwości reagowania na interakcje MessageAck `QoS: 0` bezpośrednio, `DISCONNECT` zamiast tego jest wysyłany pakiet z informacjami o odpowiedzi, a po nim następuje rozłączenie.

Przykłady:

Złe żądanie (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Brak autoryzacji (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Brak autoryzacji (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

W razie konieczności IoT Hub ustawia następujące właściwości użytkownika:

- `status` -IoT Hub rozszerzony kod stanu operacji. Ten kod może służyć do odróżniania wyników.
- `trace-id` — Identyfikator śledzenia dla operacji; IoT Hub może utrzymywać dodatkową diagnostykę dotyczącą operacji, która może być używana do wewnętrznego badania.
- `reason` — czytelny dla człowieka komunikat z informacją o tym, dlaczego operacja została zakończona w stanie wskazywanym przez `status` Właściwość.

> [!NOTE]
> Jeśli klient ustawia `Maximum Packet Size` Właściwość w programie Connect na bardzo małą wartość, nie wszystkie właściwości użytkownika mogą pasować i nie będą wyświetlane w pakiecie.
> 
> `reason` jest przeznaczona tylko dla osób i nie powinna być używana w logice klienta. Ten interfejs API umożliwia zmianę komunikatów w dowolnym momencie bez ostrzeżenia lub zmiany wersji.
>
> Jeśli klient wysyła `RequestProblemInformation: 0` w programie Connect, właściwości użytkownika nie będą uwzględniane w potwierdzeniach na [MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Kod stanu

`status` Właściwość przenosi kod stanu dla operacji. Jest zoptymalizowany pod kątem wydajności odczytu maszynowego.
Składa się z liczby całkowitej bez znaku dwubajtowego zakodowanego jako wartość szesnastkowa w ciągu, np `0501` .
Struktura kodu (mapa bitowa):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Pierwszy bajt jest używany dla flag:

- bity 0 i 1 wskazują typ wyników:
  - `00` -sukces
  - `01` -Błąd klienta
  - `10` -Błąd serwera
- bit 2: `1` wskazuje, że błąd jest ponawiany
- bity od 3 do 7 są zarezerwowane i muszą być ustawione na `0`

Drugi bajt zawiera rzeczywisty odrębny kod odpowiedzi. Kody błędów z różnymi flagami mogą mieć taką samą wartość drugiego bajtu. Na przykład może istnieć `0001` ,,, `0101` `0201` `0301` kody błędów mających różne znaczenie.

Na przykład, `Too Many Requests` to klient, Błąd powtarzający się z kodem `1` . Jego wartość to `0000 0101 0000 0001` lub `0x0501` .

Klienci mogą używać bitów typu, aby określić, czy operacja zakończyła się pomyślnie. Klienci mogą również używać bitu do ponawiania, aby zdecydować, czy jest to rozsądna operacja ponawiania.

## <a name="recommendations"></a>Zalecenia

### <a name="session-management"></a>Zarządzanie sesjami

`CONNACK` Pakiet przenosi `Session Present` Właściwość w celu wskazania, czy serwer został przywrócony wcześniej utworzoną sesję. Użyj tej właściwości, aby ustalić, czy subskrybować tematy lub pominąć subskrypcję, ponieważ subskrypcja została wcześniej ukończona.

Aby polegać na tym `Session Present` , że klient musi śledzić subskrypcje, które zostały wykonane (to jest wysłany `SUBSCRIBE` pakiet i otrzymany `SUBACK` z powodzeniem kod przyczyny), lub Pamiętaj, aby subskrybować wszystkie tematy w jednej `SUBSCRIBE` / `SUBACK` wymianie. W przeciwnym razie, jeśli klient wysyła dwa `SUBSCRIBE` pakiety, a tylko jeden z nich zostanie przetworzony pomyślnie przez serwer, serwer komunikuje się `Session Present: 1` w `CONNACK` czasie, gdy tylko część subskrypcji klienta zostanie zaakceptowana.

Aby zapobiec sytuacji, w której Starsza wersja klienta nie subskrybuje wszystkich tematów, lepiej jest subskrybować niewarunkowo, gdy zmienia się zachowanie klienta (na przykład w ramach aktualizacji oprogramowania układowego). Ponadto w celu zagwarantowania, że nie ma żadnych starych subskrypcji (z maksymalną dozwoloną liczbą subskrypcji), należy jawnie anulować subskrypcję subskrypcji, które nie są już używane.

### <a name="batching"></a>Przetwarzanie wsadowe

Nie ma specjalnego formatu do wysłania partii komunikatów. Aby zmniejszyć obciążenie operacji intensywnie korzystających z zasobów w protokołach TLS i sieci, pakietach pakietów ( `PUBLISH` , `PUBACK` , `SUBSCRIBE` i tak dalej), przed przekazaniem ich do bazowego stosu TLS/TCP. Ponadto Klient może ułatwić alias tematu w ramach "partii":

- Umieść pełną nazwę tematu w pierwszym `PUBLISH` pakiecie dla tego połączenia i skojarz z nim alias tematu,
- Umieść następujące pakiety dla tego samego tematu z pustą nazwą tematu i właściwością alias tematu.

## <a name="migration"></a>Migracja

W tej sekcji przedstawiono zmiany w interfejsie API w porównaniu do [poprzedniego interfejsu API MQTT](iot-hub-mqtt-support.md).

- Protokół transportowy to MQTT 5. Wcześniej — MQTT 3.1.1.
- Informacje kontekstowe uwierzytelniania SAS są zawarte w `CONNECT` pakiecie bezpośrednio, a nie kodowane wraz z sygnaturą.
- Metoda uwierzytelniania służy do wskazywania używanej metody uwierzytelniania.
- Sygnatura dostępu współdzielonego jest umieszczana we właściwości dane uwierzytelniania. Użyto wcześniej pola hasła.
- Tematy dotyczące operacji są różne:
  - Telemetrię: `$iothub/telemetry` zamiast `devices/{Client Id}/messages/events` ,
  - Polecenia: `$iothub/commands` zamiast `devices/{Client Id}/messages/devicebound` ,
  - Zgłoszone `$iothub/twin/patch/reported` podzbiór poprawek: zamiast `$iothub/twin/PATCH/properties/reported` ,
  - Powiadom o zmianie żądanego stanu sznurka: `$iothub/twin/patch/desired` zamiast `$iothub/twin/PATCH/properties/desired` .
- Subskrypcja odpowiedzi dla operacji klient-serwer żądanie-odpowiedź nie jest wymagana.
- Właściwości użytkownika są używane zamiast właściwości kodowania w segmencie nazwy tematu.
- nazwy właściwości są pisane w konwencji nazewnictwa "symbol kreski" zamiast skrótów z prefiksem specjalnym. Właściwości zdefiniowane przez użytkownika teraz wymagają prefiksu. Na przykład `$.mid` ma teraz `message-id` `myProperty1` `@myProperty1` .
- Właściwość danych korelacji jest używana do skorelowania komunikatów żądań i odpowiedzi dla operacji żądanie-odpowiedź zamiast `$rid` Właściwości zakodowanej w temacie.
- `iothub-connection-auth-method` Właściwość nie jest już stemplowana dla zdarzeń telemetrii.
- Polecenia C2D nie zostaną przeczyszczone w przypadku braku subskrypcji z urządzenia. Pozostaną one umieszczone w kolejce do momentu zasubskrybowania urządzenia lub wygaśnie.

## <a name="examples"></a>Przykłady

### <a name="send-telemetry"></a>Wysyłanie danych telemetrycznych

Komunikat:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Dziękowanie

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternatywne potwierdzenie (z ograniczeniami):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Wyślij stan pobrania sznurka

Żądanie:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Odpowiedź (sukces):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Odpowiedź (niedozwolone):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Obsługuj bezpośrednie wywołanie metody

Żądanie:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Odpowiedź (sukces):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` nie ustawiono — jest to odpowiedź o powodzeniu.

Odpowiedź urządzenia niedostępna:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Wystąpił błąd podczas korzystania z funkcji QoS 0, część 1

Żądanie:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Odpowiedź:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Wystąpił błąd podczas korzystania z funkcji QoS 0, część 2

Żądanie:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Odpowiedź:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z dokumentacją interfejsu API programu MQTT 5 (wersja zapoznawcza), zobacz temat [Dokumentacja interfejsu api IoT Hub płaszczyzny danych MQTT 5](iot-hub-mqtt-5-reference.md).
- Aby postępować zgodnie z przykładem w języku C#, zobacz [przykładowe repozytorium usługi GitHub](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).