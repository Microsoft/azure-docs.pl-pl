---
title: Dokumentacja interfejsu API platformy Azure IoT Hub MQTT 5 (wersja zapoznawcza)
description: Dowiedz się więcej na temat dokumentacji interfejsu API MQTT 5 IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603564"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Dokumentacja interfejsu API IoT Hub MQTT 5 płaszczyzny danych

Ten dokument zawiera definicje operacji dostępnych w wersji 2,0 (API-Version: `2020-10-01-preview` ) dla IoT Hub interfejsu API płaszczyzny danych.

## <a name="operations"></a>Operacje

### <a name="get-twin"></a>Pobierz sznurki

Pobierz stan dwuosiowy

#### <a name="request"></a>Żądanie

**Nazwa tematu:**`$iothub/twin/get`

**Właściwości**: brak

**Ładunek**: pusty

#### <a name="success-response"></a>Odpowiedź sukcesu

**Właściwości**: brak

**Ładunek**: Sznurek

#### <a name="alternative-responses"></a>Odpowiedzi alternatywne

| Stan | Nazwa | Opis |
| :----- | :--- | :---------- |
| 0100 |  Nieprawidłowe żądanie | Komunikat operacji jest źle sformułowany i nie można go przetworzyć. |
| 0101 |  Brak autoryzacji | Klient nie ma autoryzacji do wykonania tej operacji. |
| 0102 |  Niedozwolone | Operacja jest niedozwolona. |
| 0501 |  Ograniczone | częstotliwość żądań jest zbyt duża dla jednostki SKU |
| 0502 |  Przekroczono limit przydziału | Przekroczono dzienny limit przydziału na bieżącą jednostkę SKU |
| 0601 |  Błąd serwera | wewnętrzny błąd serwera |
| 0602 |  Limit czasu | Przekroczono limit czasu operacji przed ukończeniem |
| 0603 |  Serwer zajęty | serwer zajęty |

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Zgłoszono niewysłanych poprawek

Zgłoszony stan zgłoszenia przędzy

#### <a name="request"></a>Żądanie

**Nazwa tematu:**`$iothub/twin/patch/reported`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| If-Version | u64 | nie |  |

**Ładunek**: TwinState

#### <a name="success-response"></a>Odpowiedź sukcesu

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Wersja | u64 | tak | Wersja zgłoszonego stanu po zastosowaniu poprawki |

**Ładunek**: pusty

#### <a name="alternative-responses"></a>Odpowiedzi alternatywne

| Stan | Nazwa | Opis |
| :----- | :--- | :---------- |
| 0104 |  Niepowodzenie warunku wstępnego | warunek wstępny nie został spełniony z powodu anulowania żądania |
| 0100 |  Nieprawidłowe żądanie | Komunikat operacji jest źle sformułowany i nie można go przetworzyć. |
| 0101 |  Brak autoryzacji | Klient nie ma autoryzacji do wykonania tej operacji. |
| 0102 |  Niedozwolone | Operacja jest niedozwolona. |
| 0501 |  Ograniczone | częstotliwość żądań jest zbyt duża dla jednostki SKU |
| 0502 |  Przekroczono limit przydziału | Przekroczono dzienny limit przydziału na bieżącą jednostkę SKU |
| 0601 |  Błąd serwera | wewnętrzny błąd serwera |
| 0602 |  Limit czasu | Przekroczono limit czasu operacji przed ukończeniem |
| 0603 |  Serwer zajęty | serwer zajęty |

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Polecenia odbioru

Polecenia odbierania i obsługi

#### <a name="message"></a>Wiadomość

**Nazwa tematu:**`$iothub/commands`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Sekwencja-No | u64 | tak | Numer sekwencyjny komunikatu |
| czas w kolejce | time | tak | Sygnatura czasowa, o której wiadomość przeszedł system |
| Liczba dostaw | u32 | tak | Liczba prób dostarczenia komunikatu |
| czas utworzenia | time | nie | Sygnatura czasowa, o której wiadomość została utworzona (podana przez nadawcę) |
| Identyfikator komunikatu | ciąg | nie | Tożsamość komunikatu (podana przez nadawcę) |
| user-id | ciąg | nie | Tożsamość użytkownika (podana przez nadawcę) |
| correlation-id | ciąg | nie | Tożsamość korelacji (podana przez nadawcę) |
| Typ zawartości | ciąg | nie | Określa typ zawartości ładunku |
| Kodowanie zawartości | ciąg | nie | Określa kodowanie zawartości ładunku |

**Ładunek**: dowolna sekwencja bajtów

#### <a name="success-acknowledgment"></a>Pomyślne potwierdzenie

Wskazuje, że polecenie zostało zaakceptowane do obsługi przez klienta

**Właściwości**: brak

**Ładunek**: pusty

#### <a name="alternative-acknowledgments"></a>Potwierdzenia alternatywne

| Kod przyczyny | Stan | Nazwa | Opis |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandon | Wskazuje, że polecenie nie zostanie teraz przetworzone i powinno zostać dostarczone w przyszłości. |
| 131 | 0100 | Reject | Wskazuje, że polecenie zostało odrzucone przez klienta i nie powinno być podejmowane ponownie. |

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Otrzymywanie metod bezpośrednich

Odbieraj i obsługuj bezpośrednie wywołania metod

#### <a name="request"></a>Żądanie

**Nazwa tematu:**`$iothub/methods/{name}`

**Właściwości**: brak

**Ładunek**: dowolna sekwencja bajtów

#### <a name="success-response"></a>Odpowiedź sukcesu

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| odpowiedź — kod | u32 | tak |  |

**Ładunek**: dowolna sekwencja bajtów

#### <a name="alternative-responses"></a>Odpowiedzi alternatywne

| Stan | Nazwa | Opis |
| :----- | :--- | :---------- |
| 06A0 |  Niedostępny | Wskazuje, że klient nie jest osiągalny za poorednictwem tego połączenia. |

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Odbierz zmiany stanu pożądanych Stanów

Otrzymywanie aktualizacji dla żądanego stanu przędzy

#### <a name="message"></a>Wiadomość

**Nazwa tematu:**`$iothub/twin/patch/desired`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Wersja | u64 | tak | Wersja żądanego stanu odpowiadająca tej aktualizacji |

**Ładunek**: TwinState

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Wyślij dane telemetryczne

Opublikuj wiadomość w kanale telemetrii-EventHubs domyślnie lub w innym punkcie końcowym za pośrednictwem konfiguracji routingu.

#### <a name="message"></a>Wiadomość

**Nazwa tematu:**`$iothub/telemetry`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Typ zawartości | ciąg | nie | tłumaczy na `content-type` Właściwość systemową ogłoszonej wiadomości |
| Kodowanie zawartości | ciąg | nie | tłumaczy na `content-encoding` Właściwość systemową ogłoszonej wiadomości |
| Identyfikator komunikatu | ciąg | nie | tłumaczy na `message-id` Właściwość systemową ogłoszonej wiadomości |
| user-id | ciąg | nie | tłumaczy na `user-id` Właściwość systemową ogłoszonej wiadomości |
| correlation-id | ciąg | nie | tłumaczy na `correlation-id` Właściwość systemową ogłoszonej wiadomości |
| czas utworzenia | time | nie | tłumaczy na `iothub-creation-time-utc` Właściwość ogłoszonej wiadomości |

**Ładunek**: dowolna sekwencja bajtów

#### <a name="success-acknowledgment"></a>Pomyślne potwierdzenie

Wiadomość została pomyślnie opublikowana w kanale telemetrii

**Właściwości**: brak

**Ładunek**: pusty

#### <a name="alternative-acknowledgments"></a>Potwierdzenia alternatywne

| Kod przyczyny | Stan | Nazwa | Opis |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Nieprawidłowe żądanie | Komunikat operacji jest źle sformułowany i nie można go przetworzyć. |
| 135 | 0101 | Brak autoryzacji | Klient nie ma autoryzacji do wykonania tej operacji. |
| 131 | 0102 | Niedozwolone | Operacja jest niedozwolona. |
| 131 | 0601 | Błąd serwera | wewnętrzny błąd serwera |
| 151 | 0501 | Ograniczone | częstotliwość żądań jest zbyt duża dla jednostki SKU |
| 151 | 0502 | Przekroczono limit przydziału | Przekroczono dzienny limit przydziału na bieżącą jednostkę SKU |
| 131 | 0602 | Limit czasu | Przekroczono limit czasu operacji przed ukończeniem |
| 131 | 0603 | Serwer zajęty | serwer zajęty |

#### <a name="pseudo-code-sample"></a>Przykład pseudo kodu

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Odpowiedzi

### <a name="bad-request"></a>Nieprawidłowe żądanie

Komunikat operacji jest źle sformułowany i nie można go przetworzyć.

**Kod przyczyny:**`131`

**Stan:**`0100`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="conflict"></a>Konflikt

Operacja jest w konflikcie z inną trwającą operacją.

**Kod przyczyny:**`131`

**Stan:**`0103`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="not-allowed"></a>Niedozwolone

Operacja jest niedozwolona.

**Kod przyczyny:**`131`

**Stan:**`0102`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="not-authorized"></a>Brak autoryzacji

Klient nie ma autoryzacji do wykonania tej operacji.

**Kod przyczyny:**`135`

**Stan:**`0101`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |

**Ładunek**: pusty

### <a name="not-found"></a>Nie znaleziono

żądany zasób nie istnieje

**Kod przyczyny:**`131`

**Stan:**`0504`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="not-modified"></a>Nie zmodyfikowano

Zasób nie został zmodyfikowany na podstawie podanego warunku wstępnego.

**Kod przyczyny:**`0`

**Stan:**`0001`

**Właściwości**: brak

**Ładunek**: pusty

### <a name="precondition-failed"></a>Niepowodzenie warunku wstępnego

Warunek wstępny nie został spełniony z powodu anulowania żądania

**Kod przyczyny:**`131`

**Stan:**`0104`

**Właściwości**: brak

**Ładunek**: pusty

### <a name="quota-exceeded"></a>Przekroczono limit przydziału

Przekroczono dzienny limit przydziału na bieżącą jednostkę SKU

**Kod przyczyny:**`151`

**Stan:**`0502`

**Właściwości**: brak

**Ładunek**: pusty

### <a name="resource-exhausted"></a>Zasób wyczerpany

zasób nie ma pojemności do ukończenia operacji

**Kod przyczyny:**`131`

**Stan:**`0503`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="server-busy"></a>Serwer zajęty

serwer zajęty

**Kod przyczyny:**`131`

**Stan:**`0603`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |

**Ładunek**: pusty

### <a name="server-error"></a>Błąd serwera

wewnętrzny błąd serwera

**Kod przyczyny:**`131`

**Stan:**`0601`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |

**Ładunek**: pusty

### <a name="target-failed"></a>Niepowodzenie elementu docelowego

Obiekt docelowy odpowiedział, ale odpowiedź była nieprawidłowa lub źle sformułowana

**Kod przyczyny:**`131`

**Stan:**`06A2`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="target-timeout"></a>Limit czasu elementu docelowego

Przekroczono limit czasu oczekiwania na wykonanie żądania przez element docelowy

**Kod przyczyny:**`131`

**Stan:**`06A1`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |
| reason | ciąg | nie | zawiera informacje na temat tego, co jest nieprawidłowe w przypadku wiadomości |

**Ładunek**: pusty

### <a name="target-unavailable"></a>Element docelowy jest niedostępny

Element docelowy jest nieosiągalny do ukończenia żądania

**Kod przyczyny:**`131`

**Stan:**`06A0`

**Właściwości**: brak

**Ładunek**: pusty

### <a name="throttled"></a>Ograniczone

częstotliwość żądań jest zbyt duża dla jednostki SKU

**Kod przyczyny:**`151`

**Stan:**`0501`

**Właściwości**: brak

**Ładunek**: pusty

### <a name="timeout"></a>Limit czasu

Przekroczono limit czasu operacji przed ukończeniem

**Kod przyczyny:**`131`

**Stan:**`0602`

**Właściwości**:

| Nazwa | Typ | Wymagane | Opis |
| :--- | :--- | :------- | :---------- |
| Identyfikator śledzenia | ciąg | nie | Identyfikator śledzenia dla korelacji z dodatkową diagnostyką dla błędu |

**Ładunek**: pusty

