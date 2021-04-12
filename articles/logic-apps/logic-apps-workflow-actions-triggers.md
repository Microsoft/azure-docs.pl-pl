---
title: Odwołanie do schematu dla typów wyzwalaczy i akcji
description: Przewodnik odwołujący się do schematu dla wyzwalacza języka definicji przepływu pracy i typów akcji w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 95f2e47d3cf0b967f42b988b565da3643796534d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490764"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Przewodnik odwołujący się do schematu dla wyzwalaczy i typów akcji w Azure Logic Apps

Ta dokumentacja zawiera opis typów ogólnych używanych do identyfikowania wyzwalaczy i akcji w podstawowej definicji przepływu pracy aplikacji logiki, która jest opisana i weryfikowana przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). Aby znaleźć określone wyzwalacze łącznika i akcje, których można używać w aplikacjach logiki, zobacz listę w obszarze [Przegląd łączników](/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Przegląd wyzwalaczy

Każdy przepływ pracy zawiera wyzwalacz, który definiuje wywołania wywołujące i uruchamiające przepływ pracy. Poniżej przedstawiono kategorie wyzwalaczy ogólnych:

* Wyzwalacz *sondowania* , który sprawdza punkt końcowy usługi w regularnych odstępach czasu

* Wyzwalacz *wypychania* , który tworzy subskrypcję punktu końcowego i udostępnia *adres URL wywołania zwrotnego* , aby punkt końcowy mógł powiadomić wyzwalacz, gdy wystąpi określone zdarzenie lub dane są dostępne. Wyzwalacz czeka na odpowiedź punktu końcowego przed jego wyzwoleniem.

Wyzwalacze mają te elementy najwyższego poziomu, chociaż niektóre są opcjonalne:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Nazwa wyzwalacza*> | Ciąg | Nazwa wyzwalacza | 
| <*Typ wyzwalacza*> | Ciąg | Typ wyzwalacza, taki jak "http" lub "ApiConnection" | 
| <*wyzwalacz — dane wejściowe*> | JSON, obiekt | Dane wejściowe, które definiują zachowanie wyzwalacza | 
| <*jednostka czasu*> | Ciąg | Jednostka czasu, która opisuje, jak często wyzwala wyzwalacz: "s", "minute", "Hour", "Day", "tydzień", "Month" | 
| <*Liczba jednostek czasu*> | Liczba całkowita | Wartość określająca częstotliwość uruchamiania wyzwalacza na podstawie częstotliwości, czyli liczbę jednostek czasu oczekiwania do momentu ponownego uruchomienia wyzwalacza <p>Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy </br>-Dzień: 1-500 dni </br>-Godz.: 1 – 12 godzin </br>-Minutę: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość wynosi "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Tablica — warunki*> | Tablica | Tablica, która zawiera jeden lub więcej [warunków](#trigger-conditions) , które określają, czy należy uruchomić przepływ pracy. Dostępne tylko dla wyzwalaczy. | 
| <*środowisko uruchomieniowe-config-opcje*> | JSON, obiekt | Można zmienić zachowanie uruchamiania wyzwalacza przez ustawienie `runtimeConfiguration` właściwości. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*splitOn — wyrażenie*> | Ciąg | Dla wyzwalaczy, które zwracają tablicę, można określić wyrażenie [dzielące lub *departia*](#split-on-debatch) elementów tablicowych na wiele wystąpień przepływu pracy do przetworzenia. | 
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista typów wyzwalaczy

Każdy typ wyzwalacza ma inny interfejs i dane wejściowe, które definiują zachowanie wyzwalacza. 

### <a name="built-in-triggers"></a>Wbudowane wyzwalacze

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Sprawdza lub *sonduje* dowolny punkt końcowy. Ten punkt końcowy musi być zgodny z określonym kontraktem wyzwalacza przy użyciu `202` wzorca asynchronicznego lub zwracając tablicę. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Tworzy możliwy do wywołania punkt końcowy dla aplikacji logiki, ale wywołuje określony adres URL, aby zarejestrować lub wyrejestrować. |
| [**Cykl**](#recurrence-trigger) | Uruchamiany zgodnie ze zdefiniowanym harmonogramem. Możesz ustawić przyszłą datę i godzinę uruchamiania tego wyzwalacza. Na podstawie częstotliwości można także określić godziny i dni uruchamiania przepływu pracy. | 
| [**Żądanie**](#request-trigger)  | Tworzy wywoływany punkt końcowy dla aplikacji logiki i jest również znany jako wyzwalacz "ręczny". Na przykład zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych http](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Wyzwalacze zarządzanych interfejsów API

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Sprawdza lub *sonduje* punkt końcowy przy użyciu [interfejsów API zarządzanych przez firmę Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Tworzy możliwy do wywołania punkt końcowy dla aplikacji logiki, wywołując [interfejsy API zarządzane przez firmę Microsoft](../connectors/apis-list.md) w celu subskrybowania i anulowania subskrypcji. | 
||| 

## <a name="triggers---detailed-reference"></a>Wyzwalacze — informacje szczegółowe

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Wyzwalacz APIConnection  

Ten wyzwalacz sprawdza lub *sonduje* punkt końcowy przy użyciu [interfejsów API zarządzanych przez firmę Microsoft](../connectors/apis-list.md) , dzięki czemu parametry dla tego wyzwalacza mogą się różnić w zależności od punktu końcowego. Wiele sekcji w tej definicji wyzwalacza są opcjonalne. Zachowanie wyzwalacza zależy od tego, czy sekcje są uwzględniane.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Ciąg | Nazwa wyzwalacza |
| <*Nazwa połączenia*> | Ciąg | Nazwa połączenia z zarządzanym interfejsem API, którego używa przepływ pracy |
| <*Typ metody*> | Ciąg | Metoda HTTP służąca do komunikacji z zarządzanym interfejsem API: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*Operacja interfejsu API*> | Ciąg | Operacja interfejsu API do wywołania |
| <*jednostka czasu*> | Ciąg | Jednostka czasu, która opisuje, jak często wyzwala wyzwalacz: "s", "minute", "Hour", "Day", "tydzień", "Month" |
| <*Liczba jednostek czasu*> | Liczba całkowita | Wartość określająca częstotliwość uruchamiania wyzwalacza na podstawie częstotliwości, czyli liczbę jednostek czasu oczekiwania do momentu ponownego uruchomienia wyzwalacza <p>Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy </br>-Dzień: 1-500 dni </br>-Godz.: 1 – 12 godzin </br>-Minutę: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość wynosi "miesiąc", cykl jest co 6 miesięcy. |
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, które mają zostać dołączone do wywołania interfejsu API. Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. | 
| <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | 
| <*splitOn — wyrażenie*> | Ciąg | Dla wyzwalaczy, które zwracają tablice, to wyrażenie odwołuje się do tablicy, która ma być używana, aby można było utworzyć i uruchomić wystąpienie przepływu pracy dla każdego elementu tablicy zamiast używać pętli "for each". <p>Na przykład, wyrażenie reprezentuje element w tablicy zwracany w treści wyzwalacza: `@triggerbody()?['value']` |
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). |
||||

*Dane wyjściowe*
 
| Element | Typ | Opis |
|---------|------|-------------|
| nagłówka | JSON, obiekt | Nagłówki odpowiedzi |
| body | JSON, obiekt | Treść z odpowiedzi |
| kod stanu | Liczba całkowita | Kod stanu z odpowiedzi |
|||| 

*Przykład*

Ta definicja wyzwalacza sprawdza pocztę e-mail codziennie w skrzynce odbiorczej dla konta służbowego:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Wyzwalacz ApiConnectionWebhook

Ten wyzwalacz wysyła żądanie subskrypcji do punktu końcowego przy użyciu [interfejsu API zarządzanego przez firmę Microsoft](../connectors/apis-list.md), udostępnia *adres URL wywołania zwrotnego* , do którego punkt końcowy może wysłać odpowiedź i czeka na odpowiedź punktu końcowego. Aby uzyskać więcej informacji, zobacz [subskrypcje punktów końcowych](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Nazwa połączenia*> | Ciąg | Nazwa połączenia z zarządzanym interfejsem API, którego używa przepływ pracy | 
| <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania jako ładunek do zarządzanego interfejsu API | 
||||

*Opcjonalne*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, które mają zostać dołączone do wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. |
| <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). |
| <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | 
| <*splitOn — wyrażenie*> | Ciąg | Dla wyzwalaczy, które zwracają tablice, to wyrażenie odwołuje się do tablicy, która ma być używana, aby można było utworzyć i uruchomić wystąpienie przepływu pracy dla każdego elementu tablicy zamiast używać pętli "for each". <p>Na przykład, wyrażenie reprezentuje element w tablicy zwracany w treści wyzwalacza: `@triggerbody()?['value']` |
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ta definicja wyzwalacza subskrybuje interfejs API pakietu Office 365, udostępnia adres URL wywołania zwrotnego do punktu końcowego interfejsu API i czeka na odpowiedź punktu końcowego po nadejściu nowej wiadomości e-mail.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP trigger

Ten wyzwalacz wysyła żądanie do określonego punktu końcowego HTTP lub HTTPS na podstawie określonego harmonogramu cyklu. Następnie wyzwalacz sprawdza odpowiedź, aby określić, czy przepływ pracy jest uruchomiony. Aby uzyskać więcej informacji, zobacz [wywoływanie punktów końcowych usługi za pośrednictwem protokołu HTTP lub HTTPS z Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `method` | <*Typ metody*> | Ciąg | Metoda używana do wysyłania żądania wychodzącego: "GET", "PUT", "POST", "PATCH" lub "DELETE" |
| `uri` | <*HTTP-lub-HTTPS-Endpoint-URL*> | Ciąg | Adres URL punktu końcowego HTTP lub HTTPS, do którego chcesz wysłać żądanie wychodzące. Maksymalny rozmiar ciągu: 2 KB <p>W przypadku usługi lub zasobu platformy Azure Ta składnia identyfikatora URI zawiera identyfikator zasobu i ścieżkę do zasobu, do którego chcesz uzyskać dostęp. |
| `frequency` | <*jednostka czasu*> | Ciąg | Jednostka czasu, która opisuje, jak często wyzwala wyzwalacz: "s", "minute", "Hour", "Day", "tydzień", "Month" |
| `interval` | <*Liczba jednostek czasu*> | Liczba całkowita | Wartość określająca częstotliwość uruchamiania wyzwalacza na podstawie częstotliwości, czyli liczbę jednostek czasu oczekiwania do momentu ponownego uruchomienia wyzwalacza <p>Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy </br>-Dzień: 1-500 dni </br>-Godz.: 1 – 12 godzin </br>-Minutę: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość wynosi "miesiąc", cykl jest co 6 miesięcy. |
|||||

*Opcjonalne*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `headers` | <*Nagłówek — zawartość*> | JSON, obiekt | Wszystkie nagłówki, które należy dołączyć do żądania <p>Na przykład, aby ustawić język i typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, których należy użyć w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do żądania. |
| `body` | <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania jako ładunek do żądania |
| `authentication` | <*Uwierzytelnianie-wartości typu i właściwości*> | JSON, obiekt | Model uwierzytelniania wykorzystywany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Poza harmonogramem `authority` Właściwość jest obsługiwana. Gdy nie zostanie określony, wartość domyślna to `https://management.azure.com/` , ale można użyć innej wartości. |
| `retryPolicy` > `type` | <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). |
| `operationOptions` | <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). |
|||||

*Dane wyjściowe*

| Element | Typ | Opis |
|---------|------|-------------|
| `headers` | JSON, obiekt | Nagłówki odpowiedzi |
| `body` | JSON, obiekt | Treść z odpowiedzi |
| `status code` | Liczba całkowita | Kod stanu z odpowiedzi |
||||

*Wymagania dotyczące żądań przychodzących*

Aby można było poprawnie współpracować z aplikacją logiki, punkt końcowy musi być zgodny z określonym wzorcem wyzwalacza lub umową i rozpoznawać te właściwości odpowiedzi:

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| Kod stanu | Tak | Kod stanu "200 OK" uruchamia uruchomienie. Każdy inny kod stanu nie uruchamia uruchomienia. |
| Ponowna próba po nagłówku | Nie | Liczba sekund do momentu ponownego sondowania punktu końcowego przez aplikację logiki |
| Nagłówek lokalizacji | Nie | Adres URL do wywołania następnego interwału sondowania. Jeśli nie zostanie określony, używany jest oryginalny adres URL. |
|||| 

*Przykładowe zachowania dla różnych żądań*

| Kod stanu | Ponów próbę po | Zachowanie | 
|-------------|-------------|----------|
| 200 | dawaj | Uruchom przepływ pracy, a następnie sprawdź ponownie, aby uzyskać więcej danych po zdefiniowanym cyklu. | 
| 200 | 10 sekund | Uruchom przepływ pracy, a następnie sprawdź ponownie, aby uzyskać więcej danych po 10 sekundach. |  
| 202 | 60 sekund | Nie Wyzwalaj przepływu pracy. Kolejna próba występuje w ciągu minuty, zgodnie ze zdefiniowanym cyklem. Jeśli zdefiniowany cykl jest krótszy niż jedna minuta, będzie miał pierwszeństwo nagłówek retry-After. W przeciwnym razie używany jest zdefiniowany cykl. | 
| 400 | dawaj | Złe żądanie, nie uruchamiaj przepływu pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostaną użyte zasady domyślne. Po osiągnięciu liczby ponownych prób wyzwalacz ponownie sprawdzi dane po zdefiniowanym cyklu. | 
| 500 | dawaj| Błąd serwera, nie uruchamiaj przepływu pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostaną użyte zasady domyślne. Po osiągnięciu liczby ponownych prób wyzwalacz ponownie sprawdzi dane po zdefiniowanym cyklu. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Wyzwalacz HTTPWebhook  

Ten wyzwalacz umożliwia wywołanie aplikacji logiki przez utworzenie punktu końcowego, który może zarejestrować subskrypcję, wywołując określony adres URL punktu końcowego. Gdy tworzysz ten wyzwalacz w przepływie pracy, żądanie wychodzące powoduje wywołanie rejestracji subskrypcji. Dzięki temu wyzwalacz może rozpocząć nasłuchiwanie zdarzeń. Gdy operacja powoduje nieprawidłowe działanie tego wyzwalacza, żądanie wychodzące automatycznie wykonuje wywołanie anulowania subskrypcji. Aby uzyskać więcej informacji, zobacz [subskrypcje punktów końcowych](#subscribe-unsubscribe).

Można również określić [limity asynchroniczne](#asynchronous-limits) dla wyzwalacza **HTTPWebhook** . Zachowanie wyzwalacza zależy od sekcji, które są używane lub pomijane.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Niektóre wartości, takie jak <*typu metody*>, są dostępne zarówno dla `"subscribe"` obiektów, jak i `"unsubscribe"` .

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Typ metody*> | Ciąg | Metoda HTTP do użycia dla żądania subskrypcji: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*punkt końcowy — subskrybowanie adresu URL*> | Ciąg | Adres URL punktu końcowego, pod którym ma zostać wysłane żądanie subskrypcji | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Typ metody*> | Ciąg | Metoda HTTP do użycia dla żądania anulowania: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*punkt końcowy — anulowanie subskrypcji — adres URL*> | Ciąg | Adres URL punktu końcowego, pod którym ma zostać wysłane żądanie anulowania | 
| <*treść — zawartość*> | Ciąg | Zawartość wiadomości do wysłania w ramach subskrypcji lub żądania anulowania | 
| <*Typ uwierzytelniania*> | JSON, obiekt | Model uwierzytelniania wykorzystywany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | 
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Dane wyjściowe* 

| Element | Typ | Opis |
|---------|------|-------------| 
| nagłówka | JSON, obiekt | Nagłówki odpowiedzi | 
| body | JSON, obiekt | Treść z odpowiedzi | 
| kod stanu | Liczba całkowita | Kod stanu z odpowiedzi | 
|||| 

*Przykład*

Ten wyzwalacz tworzy subskrypcję określonego punktu końcowego, zapewnia unikatowy adres URL wywołania zwrotnego i czeka na nowo opublikowane artykuły technologiczne.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Wyzwalacz cyklu  

Ten wyzwalacz jest uruchamiany na podstawie określonego harmonogramu cyklu i zapewnia łatwy sposób tworzenia regularnie działającego przepływu pracy.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*jednostka czasu*> | Ciąg | Jednostka czasu, która opisuje, jak często wyzwala wyzwalacz: "s", "minute", "Hour", "Day", "tydzień", "Month" | 
| <*Liczba jednostek czasu*> | Liczba całkowita | Wartość określająca częstotliwość uruchamiania wyzwalacza na podstawie częstotliwości, czyli liczbę jednostek czasu oczekiwania do momentu ponownego uruchomienia wyzwalacza <p>Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy </br>-Dzień: 1-500 dni </br>-Godz.: 1 – 12 godzin </br>-Minutę: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość wynosi "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Start-Date-Time-with-format-RRRR-MM-DDTgg: mm: SS*> | Ciąg | Data i godzina rozpoczęcia w tym formacie: <p>RRRR-MM-DDTgg: mm: SS w przypadku określenia strefy czasowej <p>-lub- <p>RRRR-MM-DDTgg: mm: SSS, jeśli nie określisz strefy czasowej <p>Na przykład jeśli chcesz, aby 18 września 2017 o 2:00 PM, określ "2017-09-18T14:00:00" i określ strefę czasową, taką jak "Pacyficzny czas standardowy", lub określ "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ta godzina rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodna ze [specyfikacją ISO 8601 Data Time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie czasu UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie określisz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, a w przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany dłużej niż godzina rozpoczęcia. Aby uzyskać więcej informacji na temat dat i godzin uruchamiania, zobacz [Tworzenie i planowanie regularnie wykonywanych zadań](../connectors/connectors-native-recurrence.md). | 
| <*Strefa czasowa*> | Ciąg | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Określ strefę czasową, która ma zostać zastosowana. | 
| <*znaki jednorazowe lub dłuższe*> | Tablica liczb całkowitych lub liczb całkowitych | Jeśli określisz wartość "Day" lub "tydzień" dla `frequency` , możesz określić co najmniej jedną liczbę całkowitą z zakresu od 0 do 23, rozdzieloną przecinkami, jako godziny, w których chcesz uruchomić przepływ pracy. <p>Na przykład, jeśli określisz wartość "10", "12" i "14", otrzymujesz 10 AM, 12 PM i 2 PM jako znaki godzinowe. | 
| <*co najmniej jedna minuta*> | Tablica liczb całkowitych lub liczb całkowitych | Jeśli określisz wartość "Day" lub "tydzień" dla `frequency` , możesz określić co najmniej jedną liczbę całkowitą z zakresu od 0 do 59, rozdzieloną przecinkami, jako minuty godziny, gdy chcesz uruchomić przepływ pracy. <p>Na przykład można określić wartość "30" jako znak minuty i użyć poprzedniego przykładu dla godzin dnia, otrzymują 10:30 AM, 12:30 PM i 2:30 PM. | 
| weekDays | Ciąg lub tablica ciągów | Jeśli określisz wartość "tydzień" dla `frequency` , możesz określić jeden lub więcej dni rozdzielonych przecinkami, gdy chcesz uruchomić przepływ pracy: "poniedziałek", "wtorek", "Środa", "czwartek", "piątek", "Sobota" i "Niedziela" | 
| <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | 
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład 1*

Ten podstawowy wyzwalacz cyklu jest uruchamiany codziennie:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Przykład 2*

Możesz określić datę i godzinę rozpoczęcia dla uruchomienia wyzwalacza. Ten wyzwalacz cyklu jest uruchamiany w określonym dniu, a następnie uruchamiany codziennie:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Przykład 3*

Ten wyzwalacz cyklu zaczyna się od 9 września 2017 o 2:00 PM i jest uruchamiany co tydzień w przypadku 10:30, 12:30 PM i 2:30 PM (czas standardowy):

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Aby uzyskać więcej informacji i przykłady dla tego wyzwalacza, zobacz [Tworzenie i planowanie regularnie wykonywanych zadań](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacz żądania

Ten wyzwalacz umożliwia wywołanie aplikacji logiki przez utworzenie punktu końcowego, który może akceptować żądania przychodzące. Dla tego wyzwalacza Podaj schemat JSON, który opisuje i weryfikuje ładunek lub dane wejściowe, które wyzwalacz odbiera od żądania przychodzącego. Schemat umożliwia również łatwiejsze odwoływanie się do dalszych działań w przepływie pracy.

Aby wywołać ten wyzwalacz, należy użyć `listCallbackUrl` interfejsu API, który jest opisany w [interfejsie API REST usługi przepływu pracy](/rest/api/logic/workflows). Aby dowiedzieć się, jak używać tego wyzwalacza jako punktu końcowego HTTP, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych http](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Nazwa właściwości*> | Ciąg | Nazwa właściwości w schemacie JSON opisująca ładunek | 
| <*typ właściwości*> | Ciąg | Typ właściwości | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Typ metody*> | Ciąg | Metoda, której żądania przychodzące muszą używać do wywoływania aplikacji logiki: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*ścieżka względna-dla zaakceptowanego parametru*> | Ciąg | Ścieżka względna parametru, który może zostać zaakceptowany przez adres URL punktu końcowego | 
| <*wymagane — właściwości*> | Tablica | Co najmniej jedna właściwość, która wymaga wartości | 
| <*maks. — przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*Maksymalna liczba uruchomień kolejki*> | Liczba całkowita | Gdy w przepływie pracy jest już uruchomiona Maksymalna liczba wystąpień, które można zmienić w zależności od `runtimeConfiguration.concurrency.runs` właściwości, wszystkie nowe uruchomienia są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | 
| <*Operation — opcja*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` Właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ten wyzwalacz określa, że żądanie przychodzące musi używać metody HTTP POST do wywołania wyzwalacza i zawiera schemat, który sprawdza poprawność danych wejściowych z żądania przychodzącego:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Warunki wyzwalacza

Dla każdego wyzwalacza i tylko wyzwalaczy, można dołączyć tablicę, która zawiera co najmniej jeden warunek określający, czy przepływ pracy powinien być uruchamiany. Aby dodać `conditions` Właściwość do wyzwalacza w przepływie pracy, Otwórz aplikację logiki w edytorze widoku kodu.

Można na przykład określić, że wyzwalacz ma być uruchamiany tylko wtedy, gdy witryna sieci Web zwróci błąd wewnętrzny serwera, odwołując się do kodu stanu wyzwalacza we `conditions` Właściwości:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Domyślnie wyzwalacz jest uruchamiany tylko po otrzymaniu odpowiedzi "200 OK". Gdy wyrażenie odwołuje się do kodu stanu wyzwalacza, zostanie zastąpione domyślne zachowanie wyzwalacza. Tak więc, jeśli wyzwalacz ma być uruchamiany dla więcej niż jednego kodu stanu, takiego jak kod stanu "200" i "201", należy uwzględnić to wyrażenie jako warunek:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Wyzwalanie wielu przebiegów

Jeśli wyzwalacz zwraca tablicę dla aplikacji logiki do przetworzenia, czasami pętla "for each" może trwać zbyt długo, aby przetwarzać każdy element tablicy. Zamiast tego można użyć właściwości **SplitOn** w wyzwalaczu, aby *wygrupować* tablicę. Usuwanie partii dzieli elementy tablicy i uruchamia nowe wystąpienie przepływu pracy, które jest uruchamiane dla każdego elementu tablicy. Takie podejście jest przydatne, na przykład gdy chcesz sondować punkt końcowy, który może zwrócić wiele nowych elementów między interwałami sondowania. Maksymalna liczba elementów tablicy, które **SplitOn** może przetwarzać w jednym przebiegu aplikacji logiki, znajduje się w temacie [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Nie można użyć **SplitOn** z wzorcem odpowiedzi synchronicznej. Każdy przepływ pracy korzystający z **SplitOn** i zawiera akcję odpowiedzi uruchamianą asynchronicznie i natychmiast wysyła `202 ACCEPTED` odpowiedź.
>
> Po włączeniu współbieżności wyzwalacza [Limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) został znacząco zmniejszony. Jeśli liczba elementów przekracza ten limit, funkcja SplitOn jest wyłączona.
 
Jeśli plik struktury Swagger wyzwalacza opisuje ładunek, który jest tablicą, właściwość **SplitOn** jest automatycznie dodawana do wyzwalacza. W przeciwnym razie Dodaj tę właściwość do ładunku odpowiedzi, który ma tablicę, którą chcesz departii.

*Przykład*

Załóżmy, że masz interfejs API, który zwraca tę odpowiedź: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Aplikacja logiki potrzebuje tylko zawartości z tablicy w `Rows` , więc można utworzyć wyzwalacz podobny do tego przykładu:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Jeśli używasz `SplitOn` polecenia, nie możesz uzyskać właściwości, które znajdują się poza tablicą. W tym przykładzie nie można uzyskać `status` właściwości w odpowiedzi zwróconej przez interfejs API.
> 
> Aby uniknąć błędu `Rows` , jeśli właściwość nie istnieje, w tym przykładzie używa `?` operatora.

Można teraz użyć definicji przepływu pracy `@triggerBody().name` do pobrania `name` wartości, które są `"customer-name-one"` z pierwszego uruchomienia i `"customer-name-two"` z drugiego przebiegu. Dlatego dane wyjściowe wyzwalacza wyglądają podobnie jak w następujących przykładach:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Actions overview (Omówienie akcji)

Azure Logic Apps oferuje różne typy akcji — każdy z różnymi danymi wejściowymi, które definiują unikatowe zachowanie akcji. Akcje mają te elementy wysokiego poziomu, chociaż niektóre są opcjonalne:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------|
| <*Akcja — nazwa*> | Ciąg | Nazwa akcji | 
| <*Typ akcji*> | Ciąg | Typ akcji, na przykład "http" lub "ApiConnection"| 
| <*Nazwa wejściowa*> | Ciąg | Nazwa danych wejściowych, która definiuje zachowanie akcji | 
| <*wartość wejściowa*> | Poszczególne | Wartość wejściowa, która może być ciągiem, liczbą całkowitą, obiektem JSON itd. | 
| <*Poprzedni-wyzwalacz-lub-akcja-status*> | JSON, obiekt | Może zostać uruchomiona nazwa i stan wynikający wyzwalacza lub akcji, które muszą zostać uruchomione bezpośrednio przed bieżącą akcją | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz zasady ponawiania. | 
| <*środowisko uruchomieniowe-config-opcje*> | JSON, obiekt | W przypadku niektórych akcji można zmienić zachowanie akcji w czasie wykonywania przez ustawienie `runtimeConfiguration` właściwości. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*Operation — opcja*> | Ciąg | W przypadku niektórych akcji można zmienić zachowanie domyślne przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista typów akcji

Poniżej przedstawiono niektóre powszechnie używane typy akcji: 

* [Wbudowane typy akcji](#built-in-actions) , takie jak te przykłady i inne: 

  * [**Protokół http**](#http-action) do wywoływania punktów końcowych za pośrednictwem protokołu HTTP lub https

  * [**Odpowiedź**](#response-action) na odpowiadanie na żądania

  * [**Wykonaj kod JavaScript**](#run-javascript-code) do uruchamiania fragmentów kodu JavaScript

  * [**Funkcja**](#function-action) wywoływania Azure Functions

  * Akcje operacji na danych, takie jak [**Join**](#join-action), [**redagowanie**](#compose-action), [**tabela**](#table-action), [**wybór**](#select-action)i inne, które tworzą lub przekształcają dane z różnych wejść

  * [**Przepływ pracy**](#workflow-action) do wywoływania innego przepływu pracy aplikacji logiki

* [Zarządzane typy akcji interfejsu API](#managed-api-actions) , takie jak [**ApiConnection**](#apiconnection-action) i [**ApiConnectionWebHook**](#apiconnectionwebhook-action) , które wywołują różne łączniki i interfejsy API zarządzane przez firmę Microsoft, na przykład Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub i innych

* [Kontroluj typy akcji przepływu pracy](#control-workflow-actions) , takie jak [**if**](#if-action), [**foreach**](#foreach-action), [**Switch**](#switch-action), [**SCOPE**](#scope-action)i [**until**](#until-action), które zawierają inne akcje i ułatwiają organizowanie wykonywania przepływu pracy

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Wbudowane akcje

| Typ akcji | Opis | 
|-------------|-------------| 
| [**Redaguj**](#compose-action) | Tworzy pojedyncze dane wyjściowe z danych wejściowych, które mogą mieć różne typy. | 
| [**Wykonaj kod JavaScript**](#run-javascript-code) | Uruchom fragmenty kodu JavaScript zgodne z określonymi kryteriami. Aby uzyskać wymagania dotyczące kodu i uzyskać więcej informacji, zobacz [Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funkcyjn**](#function-action) | Wywołuje funkcję platformy Azure. | 
| [**HTTP**](#http-action) | Wywołuje punkt końcowy HTTP. | 
| [**Dołączanie**](#join-action) | Tworzy ciąg ze wszystkich elementów w tablicy i oddziela te elementy o określonym znaku ogranicznika. | 
| [**Analiza JSON**](#parse-json-action) | Tworzy przyjazne dla użytkownika tokeny na podstawie właściwości w zawartości JSON. Następnie można odwołać się do tych właściwości, dołączając tokeny w aplikacji logiki. | 
| [**Dotyczących**](#query-action) | Tworzy tablicę z elementów w innej tablicy na podstawie warunku lub filtru. | 
| [**Odpowiedź**](#response-action) | Tworzy odpowiedź na wywołanie przychodzące lub żądanie. | 
| [**Wybierz pozycję**](#select-action) | Tworzy tablicę z obiektami JSON przez transformowanie elementów z innej tablicy na podstawie określonej mapy. | 
| [**Tabela**](#table-action) | Tworzy tabelę CSV lub HTML z tablicy. | 
| [**Zakończ**](#terminate-action) | Powoduje zatrzymanie aktywnie działającego przepływu pracy. | 
| [**Oczekiwanie**](#wait-action) | Wstrzymuje przepływ pracy przez określony czas lub do określonej daty i godziny. | 
| [**Przepływ pracy**](#workflow-action) | Zagnieżdża przepływ pracy w innym przepływie pracy. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Zarządzane akcje interfejsu API

| Typ akcji | Opis | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Wywołuje punkt końcowy HTTP przy użyciu [zarządzanego przez firmę Microsoft interfejsu API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Działa jak element webhook protokołu HTTP, ale używa [interfejsu API zarządzanego przez firmę Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Akcje sterowania przepływem pracy

Te akcje ułatwiają kontrolowanie wykonywania przepływu pracy i obejmują inne akcje. Poza akcją przepływu pracy formantu można bezpośrednio odwoływać się do akcji w ramach akcji przepływu pracy sterującej. Na przykład jeśli masz `Http` akcję wewnątrz zakresu, możesz odwoływać się do `@body('Http')` wyrażenia z dowolnego miejsca w przepływie pracy. Jednak akcje, które znajdują się w akcji przepływu pracy kontrolki, mogą być uruchamiane tylko po wykonaniu innych akcji, które znajdują się w tej samej strukturze przepływu pracy kontroli.

| Typ akcji | Opis | 
|-------------|-------------| 
| [**Spowodował**](#foreach-action) | Uruchom te same akcje w pętli dla każdego elementu w tablicy. | 
| [**Jeśli użytkownik**](#if-action) | Uruchom akcje w zależności od tego, czy określony warunek ma wartość true, czy false. | 
| [**Zakres**](#scope-action) | Uruchom akcje na podstawie stanu grupy z zestawu akcji. | 
| [**Przełącznik**](#switch-action) | Uruchamiaj akcje zorganizowane w przypadkach, gdy wartości z wyrażeń, obiektów lub tokenów pasują do wartości określonych w każdym przypadku. | 
| [**Zanim**](#until-action) | Uruchom akcje w pętli do momentu, gdy określony warunek ma wartość true. | 
|||  

## <a name="actions---detailed-reference"></a>Akcje — szczegółowe informacje

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akcja APIConnection

Ta akcja wysyła żądanie HTTP do zarządzanego przez [firmę Microsoft interfejsu API](../connectors/apis-list.md) i wymaga informacji na temat interfejsu API i parametrów oraz odwołania do prawidłowego połączenia. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Akcja — nazwa*> | Ciąg | Nazwa akcji udostępnianej przez łącznik | 
| <*nazwa interfejsu API*> | Ciąg | Nazwa interfejsu API zarządzanego przez firmę Microsoft, który jest używany do połączenia | 
| <*Typ metody*> | Ciąg | Metoda HTTP służąca do wywoływania interfejsu API: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*Operacja interfejsu API*> | Ciąg | Operacja interfejsu API do wywołania | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*inne — właściwości specyficzne dla operacji wejścia/wyjścia*> | JSON, obiekt | Wszystkie inne właściwości wejściowe, które dotyczą tej konkretnej akcji | 
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, które mają zostać dołączone do wywołania interfejsu API. <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. | 
| <*inne — właściwości specyficzne dla akcji*> | JSON, obiekt | Wszystkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
|||| 

*Przykład*

Ta definicja opisuje akcję **Wyślij wiadomość e-mail** dotyczącą łącznika programu Outlook pakietu Office 365, który jest interfejsem API zarządzanym przez firmę Microsoft: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Akcja APIConnectionWebhook

Ta akcja powoduje wysłanie żądania subskrypcji za pośrednictwem protokołu HTTP do punktu końcowego przy użyciu [interfejsu API zarządzanego przez firmę Microsoft](../connectors/apis-list.md), zapewnia *adres URL wywołania zwrotnego* , do którego punkt końcowy może wysłać odpowiedź i czeka na odpowiedź punktu końcowego. Aby uzyskać więcej informacji, zobacz [subskrypcje punktów końcowych](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Niektóre wartości, takie jak <*typu metody*>, są dostępne zarówno dla `"subscribe"` obiektów, jak i `"unsubscribe"` .

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Akcja — nazwa*> | Ciąg | Nazwa akcji udostępnianej przez łącznik | 
| <*Typ metody*> | Ciąg | Metoda HTTP używana do subskrybowania lub anulowania subskrypcji z punktu końcowego: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*Interfejs API subskrypcji — adres URL*> | Ciąg | Identyfikator URI, który ma być używany do subskrybowania interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Interfejs API — anulowanie subskrypcji — adres URL*> | Ciąg | Identyfikator URI, który ma być używany do anulowania subskrypcji z interfejsu API | 
| <*Nagłówek — zawartość*> | JSON, obiekt | Wszystkie nagłówki do wysłania w żądaniu <p>Na przykład, aby ustawić język i typ na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania w żądaniu | 
| <*Typ uwierzytelniania*> | JSON, obiekt | Model uwierzytelniania wykorzystywany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, które mają zostać dołączone do wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. | 
| <*inne — właściwości specyficzne dla operacji wejścia/wyjścia*> | JSON, obiekt | Wszystkie inne właściwości wejściowe, które dotyczą tej konkretnej akcji | 
| <*inne — właściwości specyficzne dla akcji*> | JSON, obiekt | Wszystkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
|||| 

Można również określić limity akcji **ApiConnectionWebhook** w taki sam sposób jak [limity asynchroniczne protokołu HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akcja redagowania

Ta akcja tworzy pojedyncze dane wyjściowe z wielu danych wejściowych, w tym wyrażeń. Zarówno dane wyjściowe, jak i dane wejściowe mogą mieć dowolny typ, który Azure Logic Apps natywnie obsługuje, takich jak tablice, obiekty JSON, XML i dane binarne. Następnie można użyć danych wyjściowych akcji w innych akcjach. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Wymagane* 

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*dane wejściowe do zredagowania*> | Dowolne | Dane wejściowe do tworzenia pojedynczego wyjścia | 
|||| 

*Przykład 1*

<!-- markdownlint-disable MD038 -->
Ta definicja akcji jest scalana `abcdefg ` z końcowym miejscem i wartością `1234` :
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Oto dane wyjściowe tworzone przez tę akcję:

`abcdefg 1234`

*Przykład 2*

Ta definicja akcji Scala zmienną ciągu zawierającą `abcdefg` i zmienną całkowitą, która zawiera `1234` :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Oto dane wyjściowe tworzone przez tę akcję:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Akcja wykonywania kodu JavaScript

Ta akcja powoduje uruchomienie fragmentu kodu JavaScript i zwrócenie wyników przez `Result` token, do którego mogą się odwoływać dalsze akcje.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*Kod JavaScript — fragment kodu*> | Różnie | Kod JavaScript, który ma zostać uruchomiony. Aby uzyskać wymagania dotyczące kodu i uzyskać więcej informacji, zobacz [Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md). <p>W `code` atrybucie fragment kodu może użyć obiektu tylko do odczytu `workflowContext` jako dane wejściowe. Ten obiekt ma właściwości podrzędne, które dają kodowi dostęp do wyników wyzwalacza i poprzednich akcji w przepływie pracy. Aby uzyskać więcej informacji na temat `workflowContext` obiektu, zobacz [wyzwalacz odwołania i wyniki akcji w kodzie](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Wymagane w niektórych przypadkach*

Ten `explicitDependencies` atrybut określa, że chcesz jawnie uwzględnić wyniki z wyzwalacza, poprzednich akcji lub obu tych elementów jako zależności dla fragmentu kodu. Aby uzyskać więcej informacji na temat dodawania tych zależności, zobacz [Dodawanie parametrów dla kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Dla `includeTrigger` atrybutu można określić `true` lub `false` wartości.

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*poprzednie akcje*> | Tablica ciągów | Tablica z określonymi nazwami akcji. Użyj nazw akcji, które pojawiają się w definicji przepływu pracy, gdzie w nazwach akcji są używane znaki podkreślenia (_), a nie spacje (""). |
||||

*Przykład 1*

Ta akcja uruchamia kod, który pobiera nazwę aplikacji logiki i zwraca tekst "Hello World from \<logic-app-name> " jako wynik. W tym przykładzie kod odwołuje się do nazwy przepływu pracy przez uzyskanie dostępu do `workflowContext.workflow.name` właściwości za pomocą obiektu tylko do odczytu `workflowContext` . Aby uzyskać więcej informacji na temat używania `workflowContext` obiektu, zobacz [wyzwalacz odwołania i wyniki akcji w kodzie](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Przykład 2*

Ta akcja uruchamia kod w aplikacji logiki, która wyzwala po nadejściu nowej wiadomości e-mail na konto służbowe. Aplikacja logiki używa również akcji Wyślij wiadomość e-mail dotyczącą zatwierdzenia, która przekazuje zawartość odebranej wiadomości e-mail wraz z żądaniem zatwierdzenia.

Kod wyodrębnia adresy e-mail z właściwości wyzwalacza `Body` i zwraca adresy wraz z `SelectedOption` wartością właściwości z akcji zatwierdzania. Akcja jawnie obejmuje akcję Wyślij wiadomość e-mail dotyczącą zatwierdzenia jako zależność w `explicitDependencies`  >  `actions` atrybucie.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Akcja funkcji

Ta akcja wywołuje wcześniej utworzoną [funkcję platformy Azure](../azure-functions/functions-get-started.md).

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*Identyfikator funkcji Azure*> | Ciąg | Identyfikator zasobu dla funkcji platformy Azure, która ma zostać wywołana. Oto format tej wartości:<p>"/subscriptions/<*Azure-Subscription-ID*>/ResourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/Sites/<*Azure-function-app-Name*>/Functions/<*Azure-Function-Name*>" | 
| <*Typ metody*> | Ciąg | Metoda HTTP używana do wywoływania funkcji: "GET", "PUT", "POST", "PATCH" lub "DELETE" <p>Jeśli nie zostanie określony, wartością domyślną jest metoda "POST". | 
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*Nagłówek — zawartość*> | JSON, obiekt | Wszystkie nagłówki do wysłania z wywołaniem <p>Na przykład, aby ustawić język i typ na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania w żądaniu | 
| <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, które mają zostać dołączone do wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. | 
| <*inne — właściwości specyficzne dla operacji wejścia/wyjścia*> | JSON, obiekt | Wszystkie inne właściwości wejściowe, które dotyczą tej konkretnej akcji | 
| <*inne — właściwości specyficzne dla akcji*> | JSON, obiekt | Wszystkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
||||

Po zapisaniu aplikacji logiki aparat Logic Apps wykonuje te testy na funkcji, której dotyczy odwołanie:

* Przepływ pracy musi mieć dostęp do funkcji.

* Przepływ pracy może używać tylko standardowego wyzwalacza HTTP lub wyzwalacza elementu webhook JSON. 

  Aparat Logic Apps pobiera i buforuje adres URL wyzwalacza, który jest używany w czasie wykonywania. Jeśli jednak każda operacja unieważnia buforowany adres URL, Akcja **funkcji** kończy się niepowodzeniem w czasie wykonywania. Aby rozwiązać ten problem, Zapisz ponownie aplikację logiki, aby aplikacja logiki odbierała i ponownie buforuje adres URL wyzwalacza.

* Funkcja nie może mieć zdefiniowanej trasy.

* Dozwolone są tylko poziomy autoryzacji "Function" i "Anonymous". 

*Przykład*

Ta definicja akcji wywołuje wcześniej utworzoną funkcję "getproductid":

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Akcja HTTP

Ta akcja wysyła żądanie do określonego punktu końcowego HTTP lub HTTPS i sprawdza odpowiedź w celu ustalenia, czy przepływ pracy jest uruchomiony. Aby uzyskać więcej informacji, zobacz [wywoływanie punktów końcowych usługi za pośrednictwem protokołu HTTP lub HTTPS z Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Wymagane*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `method` | <*Typ metody*> | Ciąg | Metoda używana do wysyłania żądania wychodzącego: "GET", "PUT", "POST", "PATCH" lub "DELETE" |
| `uri` | <*HTTP-lub-HTTPS-Endpoint-URL*> | Ciąg | Adres URL punktu końcowego HTTP lub HTTPS, do którego chcesz wysłać żądanie wychodzące. Maksymalny rozmiar ciągu: 2 KB <p>W przypadku usługi lub zasobu platformy Azure Ta składnia identyfikatora URI zawiera identyfikator zasobu i ścieżkę do zasobu, do którego chcesz uzyskać dostęp. |
|||||

*Opcjonalne*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `headers` | <*Nagłówek — zawartość*> | JSON, obiekt | Wszystkie nagłówki, które należy dołączyć do żądania <p>Na przykład, aby ustawić język i typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*zapytanie-parametry*> | JSON, obiekt | Wszystkie parametry zapytania, których należy użyć w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt dodaje `?api-version=2018-01-01` do wywołania. |
| `body` | <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania jako ładunek do żądania |
| `authentication` | <*Uwierzytelnianie-wartości typu i właściwości*> | JSON, obiekt | Model uwierzytelniania wykorzystywany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Poza harmonogramem `authority` Właściwość jest obsługiwana. Gdy nie zostanie określony, wartość domyślna to `https://management.azure.com/` , ale można użyć innej wartości. |
| `retryPolicy` > `type` | <*ponawianie próby — zachowanie*> | JSON, obiekt | Dostosowuje sposób ponawiania próby dla sporadycznych awarii, które mają kod stanu 408, 429 i 5XX oraz wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*inne — właściwości specyficzne dla operacji wejścia/wyjścia*> | <*Input-Property*> | JSON, obiekt | Wszystkie inne właściwości wejściowe, które dotyczą tej konkretnej akcji |
| <*inne — właściwości specyficzne dla akcji*> | <*wartość właściwości*> | JSON, obiekt | Wszystkie inne właściwości, które mają zastosowanie do tej konkretnej akcji |
|||||

*Przykład*

Ta definicja akcji pobiera najnowsze wiadomości, wysyłając żądanie do określonego punktu końcowego:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Akcja łączenia

Ta akcja tworzy ciąg ze wszystkich elementów w tablicy i oddziela te elementy o określonym znaku ogranicznika. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*macierzy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Jeśli określisz wyrażenie, ujmij to wyrażenie z podwójnymi cudzysłowami. | 
| <*ogranicznik*> | Pojedynczy ciąg znaków | Znak oddzielający każdy element w ciągu | 
|||| 

*Przykład*

Załóżmy, że masz wcześniej utworzoną zmienną "myIntegerArray", która zawiera tę tablicę liczb całkowitych: 

`[1,2,3,4]` 

Ta definicja akcji pobiera wartości ze zmiennej przy użyciu `variables()` funkcji w wyrażeniu i tworzy ten ciąg za pomocą tych wartości, które są oddzielone przecinkami: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Analiza akcji JSON

Ta akcja tworzy przyjazne dla użytkownika pola lub *tokeny* na podstawie właściwości w zawartości JSON. Następnie można uzyskać dostęp do tych właściwości w aplikacji logiki przy użyciu tokenów. Na przykład jeśli chcesz użyć danych wyjściowych JSON z usług takich jak Azure Service Bus i Azure Cosmos DB, możesz dołączyć tę akcję do aplikacji logiki, aby łatwiej odwoływać się do danych w tym wyniku.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Źródło JSON*> | JSON, obiekt | Zawartość JSON, którą chcesz przeanalizować | 
| <*JSON — schemat*> | JSON, obiekt | Schemat JSON, który opisuje podstawową zawartość JSON, której używa akcja do analizowania źródłowej zawartości JSON. <p>**Porada**: w projektancie Logic Apps można dostarczyć schemat lub dostarczyć przykładowy ładunek, aby akcja mogła generować schemat. | 
|||| 

*Przykład*

Ta definicja akcji tworzy te tokeny, których można użyć w przepływie pracy, ale tylko w akcjach uruchamianych po akcji **Przeanalizuj dane JSON** :

`FirstName`, `LastName` i `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

W tym przykładzie właściwość "Content" określa zawartość JSON dla akcji do przeanalizowania. Możesz również udostępnić tę zawartość JSON jako przykładowy ładunek do generowania schematu.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Właściwość "Schema" określa schemat JSON używany do opisywania zawartości JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Akcja zapytania

Ta akcja tworzy tablicę z elementów w innej tablicy na podstawie określonego warunku lub filtra.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*macierzy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Jeśli określisz wyrażenie, ujmij to wyrażenie z podwójnymi cudzysłowami. |
| <*warunek lub filtr*> | Ciąg | Warunek używany do filtrowania elementów w tablicy źródłowej <p>**Uwaga**: Jeśli żadna wartość nie spełnia warunku, wówczas akcja tworzy pustą tablicę. |
|||| 

*Przykład*

Ta definicja akcji tworzy tablicę, która ma wartości większe niż określona wartość, czyli dwa:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Akcja odpowiedzi  

Ta akcja tworzy ładunek odpowiedzi na żądanie HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*odpowiedź — stan-kod*> | Liczba całkowita | Kod stanu HTTP, który jest wysyłany do żądania przychodzącego. Domyślnym kodem jest "200 OK", ale kod może być dowolnym prawidłowym kodem stanu zaczynającym się od 2xx, 4xx lub 5xx, ale nie z 3xxx. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*odpowiedź — nagłówki*> | JSON, obiekt | Co najmniej jeden nagłówek, który ma zostać dołączony do odpowiedzi | 
| <*odpowiedź — treść*> | Poszczególne | Treść odpowiedzi, która może być ciągiem, obiektem JSON lub nawet zawartością binarną z poprzedniej akcji | 
|||| 

*Przykład*

Ta definicja akcji tworzy odpowiedź na żądanie HTTP z określonym kodem stanu, treścią komunikatu i nagłówkami komunikatów:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Ograniczenia*

W przeciwieństwie do innych działań Akcja **odpowiedzi** ma specjalne ograniczenia: 

* Przepływ pracy może użyć akcji **odpowiedzi** tylko wtedy, gdy przepływ pracy rozpoczyna się od wyzwalacza żądania HTTP, co oznacza, że przepływ pracy musi być wyzwalany przez żądanie HTTP.

* Przepływ pracy może używać akcji **odpowiedzi** wszędzie *z wyjątkiem* pętli **foreach** , **do momentu** , w tym pętle sekwencyjne i gałęzie równoległe. 

* Oryginalne żądanie pobiera odpowiedź przepływu pracy tylko wtedy, gdy wszystkie akcje wymagane przez akcję **odpowiedzi** zostaną zakończone w limicie [limitu czasu http](../logic-apps/logic-apps-limits-and-config.md#http-limits).

  Jeśli jednak przepływ pracy wywoła inną aplikację logiki jako zagnieżdżony przepływ pracy, nadrzędny przepływ pracy czeka, aż zakończy się zagnieżdżony przepływ pracy, niezależnie od tego, ile czasu kończy się przed ukończeniem zagnieżdżonego przepływu pracy.

* Gdy przepływ pracy używa akcji **odpowiedzi** i wzorca odpowiedzi synchronicznej, przepływ pracy nie może również użyć polecenia **splitOn** w definicji wyzwalacza, ponieważ to polecenie tworzy wiele uruchomień. Sprawdź, czy w przypadku użycia metody PUT i w przypadku wartości true zwraca odpowiedź "złe żądanie".

  W przeciwnym razie, jeśli przepływ pracy używa polecenia **splitOn** i akcji **odpowiedzi** , przepływ pracy jest uruchamiany asynchronicznie i natychmiast zwraca odpowiedź "202 zaakceptowane".

* Gdy wykonywanie przepływu pracy osiągnie akcję **odpowiedzi** , ale żądanie przychodzące otrzymało już odpowiedź, Akcja **odpowiedzi** jest oznaczona jako "Niepowodzenie" ze względu na konflikt. W związku z tym uruchomienie aplikacji logiki jest również oznaczone stanem "Niepowodzenie".

<a name="select-action"></a>

### <a name="select-action"></a>Wybierz akcję

Ta akcja tworzy tablicę z obiektami JSON przez transformowanie elementów z innej tablicy na podstawie określonej mapy. Tablica wyjściowa i tablica źródłowa zawsze mają tę samą liczbę elementów. Chociaż nie można zmienić liczby obiektów w tablicy wyjściowej, można dodawać lub usuwać właściwości i ich wartości w tych obiektach. `select`Właściwość określa co najmniej jedną parę klucz-wartość, która definiuje mapę do przekształcania elementów w tablicy źródłowej. Para klucz-wartość reprezentuje właściwość i jej wartość we wszystkich obiektach tablicy wyjściowej.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Wymagane* 

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*macierzy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Upewnij się, że wyrażenie zostało ujęte w podwójne cudzysłowy. <p>**Uwaga**: Jeśli tablica źródłowa jest pusta, akcja tworzy pustą tablicę. | 
| <*Nazwa klucza*> | Ciąg | Nazwa właściwości przypisana do wyniku z *wyrażenia* <> <p>Aby dodać nową właściwość dla wszystkich obiektów w tablicy wyjściowej, podaj <ową *nazwę klucza*> dla tej właściwości i *wyrażenie* <> dla wartości właściwości. <p>Aby usunąć Właściwość ze wszystkich obiektów w tablicy, Pomiń <*klucz-nazwa*> dla tej właściwości. | 
| <*wyrażenia*> | Ciąg | Wyrażenie, które przekształca element w tablicy źródłowej i przypisuje wynik do <*klucz-nazwa*> | 
|||| 

Akcja **Wybierz** powoduje utworzenie tablicy jako danych wyjściowych, dlatego wszelkie akcje, które chcą korzystać z tych danych wyjściowych, muszą przyjmować tablicę lub należy skonwertować tablicę na typ, który akcja odbiorcy akceptuje. Na przykład, aby przekonwertować tablicę wyjściową na ciąg, można przekazać tę tablicę do akcji **redagowania** , a następnie odwołać się do danych wyjściowych **akcji tworzenia w innych akcjach** .

*Przykład*

Ta definicja akcji tworzy tablicę obiektów JSON z tablicy liczb całkowitych. Akcja iteruje za pośrednictwem tablicy źródłowej, pobiera każdą wartość całkowitą przy użyciu `@item()` wyrażenia i przypisuje każdej wartości do właściwości " `number` " w każdym obiekcie JSON:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Oto tablica, którą tworzy ta akcja:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Aby użyć tego danych wyjściowych macierzy w innych akcjach, Przekaż dane wyjściowe do akcji **redagowania** :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Następnie można użyć danych wyjściowych z akcji **redagowania** w innych akcjach, na przykład w przypadku **pakietu Office 365 Outlook — Wyślij wiadomość e-mail** :

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Akcja tabeli

Ta akcja tworzy tabelę CSV lub HTML z tablicy. W przypadku tablic z obiektami JSON ta akcja powoduje automatyczne utworzenie nagłówków kolumn na podstawie nazw właściwości obiektów. W przypadku tablic z innymi typami danych należy określić nagłówki i wartości kolumn. Na przykład ta tablica zawiera właściwości "ID" i "Product_Name", których ta akcja może używać dla nazw nagłówków kolumn:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Wymagane* 

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| \<CSV *or* HTML>| Ciąg | Format tabeli, którą chcesz utworzyć. | 
| <*macierzy*> | Tablica | Tablica lub wyrażenie, które zawiera elementy źródłowe tabeli. <p>**Uwaga**: Jeśli tablica źródłowa jest pusta, akcja spowoduje utworzenie pustej tabeli. | 
|||| 

*Opcjonalne*

Aby określić lub dostosować nagłówki i wartości kolumn, użyj `columns` tablicy. Gdy `header-value` pary mają tę samą nazwę nagłówka, ich wartości są wyświetlane w tej samej kolumnie pod nazwą nagłówka. W przeciwnym razie każdy unikatowy nagłówek definiuje unikatową kolumnę.

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Nazwa kolumny*> | Ciąg | Nazwa nagłówka kolumny | 
| <*wartość kolumny*> | Dowolne | Wartość w tej kolumnie. | 
|||| 

*Przykład 1*

Załóżmy, że masz wcześniej utworzoną zmienną "myItemArray", która obecnie zawiera tę tablicę:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Ta definicja akcji tworzy tabelę CSV ze zmiennej "myItemArray". Wyrażenie używane przez `from` Właściwość pobiera tablicę z "myItemArray" za pomocą `variables()` funkcji:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Poniżej przedstawiono tabelę CSV, którą tworzy ta akcja: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Przykład 2*

Ta definicja akcji tworzy tabelę HTML ze zmiennej "myItemArray". Wyrażenie używane przez `from` Właściwość pobiera tablicę z "myItemArray" za pomocą `variables()` funkcji:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Oto tabela HTML, którą tworzy ta akcja: 

<table><thead><tr><th>ID (Identyfikator)</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Jabłka</td></tr><tr><td>1</td><td>Pomarańcze</td></tr></tbody></table>

*Przykład 3*

Ta definicja akcji tworzy tabelę HTML ze zmiennej "myItemArray". Jednak ten przykład zastępuje domyślne nazwy nagłówka kolumny "Stock_ID" i "Description" i dodaje słowo "Organiczn" do wartości w kolumnie "Description".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Oto tabela HTML, którą tworzy ta akcja: 

<table><thead><tr><th>Stock_ID</th><th>Opis</th></tr></thead><tbody><tr><td>0</td><td>Jabłka organiczne</td></tr><tr><td>1</td><td>Pomarańcze organiczne</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Przerwij akcję

Ta akcja powoduje zatrzymanie przebiegu dla wystąpienia przepływu pracy, anulowanie wszystkich akcji w toku, pomija wszystkie pozostałe akcje i zwraca określony stan. Na przykład możesz użyć akcji **Przerwij** , gdy aplikacja logiki musi zakończyć się całkowicie ze stanem błędu. Ta akcja nie wpływa na już wykonane akcje i nie może występować wewnątrz pętli **foreach** i **do until** , w tym sekwencyjne pętle.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Stany*> | Ciąg | Stan do zwrócenia dla przebiegu: "Niepowodzenie", "anulowana" lub "powodzenie" |
|||| 

*Opcjonalne*

Właściwości obiektu "runStatus" są stosowane tylko wtedy, gdy właściwość "runStatus" ma stan "Niepowodzenie".

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*błąd — kod lub nazwa*> | Ciąg | Kod lub nazwa błędu |
| <*błąd-komunikat*> | Ciąg | Komunikat lub tekst opisujący błąd oraz wszystkie akcje, które może wykonać użytkownik aplikacji | 
|||| 

*Przykład*

Ta definicja akcji przerywa przebieg przepływu pracy, ustawia stan uruchomienia na "Niepowodzenie" i zwraca stan, kod błędu i komunikat o błędzie:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Akcja oczekiwania

Ta akcja wstrzymuje wykonywanie przepływu pracy dla określonego interwału lub do określonego czasu, ale nie do obu tych wartości.

*Określony interwał*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Określony czas*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Liczba jednostek*> | Liczba całkowita | Dla akcji **opóźnionej** liczba jednostek do odczekania | 
| <*dat*> | Ciąg | Dla akcji **opóźnienia** interwał oczekiwania: "s", "minuta", "godzina", "dzień", "tydzień", "miesiąc" | 
| <*Data i godzina — sygnatura czasowa*> | Ciąg | Dla **Opóźnij do** akcji, datę i godzinę wznowienia wykonania. Ta wartość musi używać [formatu daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Przykład 1*

Ta definicja akcji wstrzymuje przepływ pracy przez 15 minut:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Przykład 2*

Ta definicja akcji wstrzymuje przepływ pracy do momentu określonego czasu:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Akcja przepływu pracy

Ta akcja wywołuje inną wcześniej utworzoną aplikację logiki, co oznacza, że można uwzględnić i ponownie użyć innych przepływów pracy aplikacji logiki. Możesz również użyć danych wyjściowych z podrzędnej lub *zagnieżdżonej* aplikacji logiki w akcjach, które są zgodne z zagnieżdżoną aplikacją logiki, pod warunkiem, że podrzędna aplikacja logiki zwróci odpowiedź.

Aparat Logic Apps sprawdza dostęp do wyzwalacza, który ma zostać wywołany, więc upewnij się, że można uzyskać dostęp do tego wyzwalacza. Ponadto zagnieżdżona aplikacja logiki musi spełniać następujące kryteria:

* Wyzwalacz sprawia, że zagnieżdżona aplikacja logiki jest wywoływana, taka jak [żądanie](#request-trigger) lub wyzwalacz [http](#http-trigger) .

* Ta sama subskrypcja platformy Azure jako nadrzędna aplikacja logiki

* Aby użyć danych wyjściowych z zagnieżdżonej aplikacji logiki w aplikacji logiki nadrzędnej, zagnieżdżona aplikacja logiki musi mieć akcję [odpowiedzi](#response-action)

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nested-Logic-App-Name*> | Ciąg | Nazwa aplikacji logiki, która ma zostać wywołana | 
| <*Nazwa wyzwalacza*> | Ciąg | Nazwa wyzwalacza w zagnieżdżonej aplikacji logiki, która ma zostać wywołana | 
| <*Azure-Subscription-ID*> | Ciąg | Identyfikator subskrypcji platformy Azure dla zagnieżdżonej aplikacji logiki |
| <*Azure-Resource-Group*> | Ciąg | Nazwa grupy zasobów platformy Azure dla zagnieżdżonej aplikacji logiki |
| <*nested-Logic-App-Name*> | Ciąg | Nazwa aplikacji logiki, która ma zostać wywołana |
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*Nagłówek — zawartość*> | JSON, obiekt | Wszystkie nagłówki do wysłania z wywołaniem | 
| <*treść — zawartość*> | JSON, obiekt | Zawartość wiadomości do wysłania z wywołaniem | 
||||

*Dane wyjściowe*

Wyniki tej akcji różnią się w zależności od akcji odpowiedzi zagnieżdżonej aplikacji logiki. Jeśli zagnieżdżona aplikacja logiki nie zawiera akcji odpowiedzi, dane wyjściowe są puste.

*Przykład*

Po pomyślnym zakończeniu akcji "Start_search" Ta definicja akcji przepływu pracy wywołuje inną aplikację logiki o nazwie "Get_product_information", która przekazuje określone dane wejściowe:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Szczegóły akcji przepływu pracy sterowania

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Akcja foreach

Ta akcja zapętlenia wykonuje iterację przez tablicę i wykonuje akcje dla każdego elementu tablicy. Domyślnie pętla "for each" jest uruchamiana równolegle do maksymalnej liczby pętli. Aby uzyskać tę wartość maksymalną, zobacz [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Dowiedz się [, jak utworzyć pętlę "for each"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Wymagane* 

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Akcja-1... Azotan*> | Ciąg | Nazwy akcji, które są uruchamiane dla każdego elementu tablicy | 
| <*Akcja-definicja-1... Azotan*> | JSON, obiekt | Definicje akcji, które są uruchamiane | 
| <*for-each-Expression*> | Ciąg | Wyrażenie odwołujące się do każdego elementu w określonej tablicy. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*liczbą*> | Liczba całkowita | Domyślnie iteracja pętli "for each" jest uruchamiana w tym samym czasie (współbieżnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczbę*> wartość, zobacz [zmiana "dla każdego" współbieżności pętli](#change-for-each-concurrency). | 
| <*Operation — opcja*> | Ciąg | Aby uruchomić pętlę "for each" sekwencyjnie, a nie równolegle, należy ustawić opcję <*operacji*> na `Sequential` lub <*liczbę*> do `1` , ale nie do obu jednocześnie. Aby uzyskać więcej informacji, zobacz [Uruchom polecenie "for each" sekwencyjnie](#sequential-for-each). | 
|||| 

*Przykład*

Ta pętla "for each" wysyła wiadomość e-mail dla każdego elementu w tablicy, który zawiera załączniki z przychodzącej wiadomości e-mail. Pętla wysyła wiadomość e-mail, w tym załącznik, do osoby, która przegląda załącznik.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Aby określić tylko tablicę, która jest przenoszona jako wyjście z wyzwalacza, to wyrażenie pobiera <tablicy *nazw*> tablicy z treści wyzwalacza. Aby uniknąć awarii, jeśli tablica nie istnieje, wyrażenie używa `?` operatora:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Jeśli akcja

Ta akcja, która jest *instrukcją warunkową*, oblicza wyrażenie reprezentujące warunek i uruchamia inną gałąź w zależności od tego, czy warunek ma wartość true, czy false. Jeśli warunek ma wartość true, warunek jest oznaczony jako "powodzenie". Dowiedz się [, jak tworzyć instrukcje warunkowe](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*rozgrzewa*> | JSON, obiekt | Warunek, który może być wyrażeniem do obliczenia | 
| <*Akcja-1*> | JSON, obiekt | Akcja do uruchomienia, gdy <*warunek*> ma wartość true | 
| <*Akcja — definicja*> | JSON, obiekt | Definicja akcji | 
| <*Akcja — 2*> | JSON, obiekt | Akcja, która ma być uruchamiana, gdy <*warunek*> wartość false | 
|||| 

Akcje w `actions` `else` obiekcie lub uzyskują te stany:

* "Powodzenie", gdy są uruchamiane i kończą się powodzeniem
* "Niepowodzenie", gdy są uruchamiane i kończą się niepowodzeniem
* "Pominięte", gdy odpowiednia gałąź nie jest uruchomiona

*Przykład*

Ten stan określa, że gdy zmienna całkowita ma wartość większą od zera, przepływ pracy sprawdza witrynę sieci Web. Jeśli zmienna ma wartość zero lub mniejszą, przepływ pracy sprawdza inną witrynę sieci Web.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Jak warunki używają wyrażeń

Poniżej przedstawiono kilka przykładów, które pokazują, jak można używać wyrażeń w warunkach:
  
| JSON | Wynik | 
|------|--------| 
| "Expression": " @parameters (" <*hasSpecialAction*> ")" | W przypadku tylko wyrażeń logicznych warunek jest przekazywany dla każdej wartości, która ma wartość true. <p>Aby przekonwertować inne typy na wartość logiczną, użyj następujących funkcji: `empty()` lub `equals()` . | 
| "Expression": " @greater (akcje (" <*Akcja*> "). Output. Value, parameters (" <*próg*> "))" | W przypadku funkcji porównania akcja jest uruchamiana tylko wtedy, gdy wartość wyjściowa> *akcji* <jest większa niż> *próg* <wartości. | 
| "Expression": " @or (większe (akcje" <*Action*> "). Output. Value, parameters (" <*Threshold*> ")), less (akcje (" <*Ta sama-akcja*> "). Output. Value, 100))" | W przypadku funkcji logiki i tworzenia zagnieżdżonych wyrażeń logicznych akcja jest uruchamiana, gdy wynik <*akcji*> jest większy niż *próg* <> wartość lub poniżej 100. | 
| "Expression": " @equals (długość (akcje:" <*Akcja*> "). Output. Errors), 0))" | Można użyć funkcji tablicowych do sprawdzenia, czy tablica zawiera jakieś elementy. Akcja jest uruchamiana, gdy `errors` Tablica jest pusta. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Akcja zakresu

Ta akcja logicznie grupuje akcje do *zakresów*, które uzyskują własny stan po zakończeniu działania w tym zakresie. Można następnie użyć stanu zakresu, aby określić, czy będą uruchamiane inne akcje. Dowiedz się [, jak tworzyć zakresy](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*wewnętrzna-akcja-1... Azotan*> | JSON, obiekt | Co najmniej jedna akcja, która jest uruchamiana w zakresie |
| <*Akcja — dane wejściowe*> | JSON, obiekt | Dane wejściowe dla każdej akcji |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Akcja Przełącznik

Ta akcja, znana także jako *instrukcja SWITCH*, organizuje inne akcje w *przypadkach* i przypisuje wartość do każdego przypadku, z wyjątkiem sytuacji domyślnej, jeśli taka istnieje. Gdy przepływ pracy zostanie uruchomiony, Akcja **przełącznika** porównuje wartość z wyrażenia, obiektu lub tokenu z wartościami określonymi dla każdego przypadku. Jeśli akcja **Switch** znajdzie pasujący przypadek, przepływ pracy uruchamia tylko akcje dla tego przypadku. Za każdym razem, gdy akcja **przełączania** zostanie uruchomiona, istnieje tylko jeden pasujący przypadek lub brak dopasowań. Jeśli nie ma żadnych dopasowań, Akcja **przełącznika** uruchamia domyślne akcje. Dowiedz się [, jak tworzyć instrukcje Switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Expression-Object-or-token*> | Różnie | Wyrażenie, obiekt JSON lub token do obliczenia | 
| <*Akcja — nazwa*> | Ciąg | Nazwa akcji do uruchomienia dla przypadku dopasowania | 
| <*Akcja — definicja*> | JSON, obiekt | Definicja akcji do uruchomienia dla przypadku dopasowania | 
| <*dopasowanie-wartość*> | Różnie | Wartość do porównania z obliczonym wynikiem | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Default-Action-Name*> | Ciąg | Nazwa domyślnej akcji do uruchomienia, gdy nie istnieje żaden pasujący przypadek | 
| <*Default — definicja akcji*> | JSON, obiekt | Definicja akcji, która ma być uruchamiana, gdy nie istnieje żaden pasujący przypadek | 
|||| 

*Przykład*

Ta definicja akcji służy do obliczania, czy osoba odpowiadająca na wiadomość e-mail z żądaniem zatwierdzenia została wybrana opcja "Zatwierdź" lub "Odrzuć". Na podstawie tego wyboru Akcja **przełączenia** uruchamia akcje dla przypadku dopasowania, które polega na wysłaniu kolejnej wiadomości e-mail do obiektu odpowiadającego, ale z różnymi słowami w każdym przypadku. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Do akcji do

Ta akcja pętli zawiera akcje, które są uruchamiane do momentu, gdy określony warunek ma wartość true. Pętla sprawdza warunek jako ostatni krok po wykonaniu wszystkich innych akcji. W obiekcie można uwzględnić więcej niż jedną akcję `"actions"` , a akcja musi definiować co najmniej jeden limit. Dowiedz się [, jak utworzyć pętle "until"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Akcja — nazwa*> | Ciąg | Nazwa akcji, która ma być uruchamiana wewnątrz pętli | 
| <*Typ akcji*> | Ciąg | Typ akcji, którą chcesz uruchomić | 
| <*Akcja — dane wejściowe*> | Poszczególne | Dane wejściowe akcji do uruchomienia | 
| <*rozgrzewa*> | Ciąg | Warunek lub wyrażenie do obliczenia po zakończeniu wszystkich akcji w pętli | 
| <*liczba pętli*> | Liczba całkowita | Limit największej liczby pętli, które może wykonać akcja. Aby uzyskać więcej informacji na temat domyślnego limitu i maksymalnego limitu, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*Pętla — limit czasu*> | Ciąg | Limit najdłuższego czasu, w którym można uruchomić pętlę. Wartość domyślna `timeout` to `PT1H` , który jest wymaganym [formatem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> Jeśli wyrażenie jest zależne od danych wyjściowych z akcji w pętli until, upewnij się, że dla każdego błędu powstałego w wyniku tej akcji istnieje konto.

*Przykład*

Ta definicja akcji pętli wysyła żądanie HTTP do określonego adresu URL do momentu spełnienia jednego z następujących warunków:

* Żądanie otrzymuje odpowiedź z kodem stanu "200 OK".
* Pętla jest uruchamiana 60 razy.
* Pętla jest uruchamiana przez jedną godzinę.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Elementy webhook i subskrypcje

Wyzwalacze i akcje oparte na elemencie webhook nie sprawdzają regularnie punktów końcowych, ale w zamian Zaczekaj na określone zdarzenia lub dane w tych punktach końcowych. Te wyzwalacze i akcje *subskrybują* punkty końcowe, podając *adres URL wywołania zwrotnego* , pod którym punkt końcowy może wysyłać odpowiedzi.

`subscribe`Wywołanie ma miejsce, gdy przepływ pracy zmienia się w dowolny sposób, na przykład w przypadku odnowienia poświadczeń lub zmiany parametrów wejściowych dla wyzwalacza lub akcji. To wywołanie używa tych samych parametrów, co w przypadku standardowych akcji HTTP. 

`unsubscribe`Wywołanie jest wykonywane automatycznie, gdy operacja powoduje nieprawidłowe działanie wyzwalacza lub akcji, na przykład:

* Usuwanie lub wyłączanie wyzwalacza. 
* Usuwanie lub wyłączanie przepływu pracy. 
* Usuwanie lub wyłączanie subskrypcji. 

W celu obsługi tych wywołań `@listCallbackUrl()` wyrażenie zwraca unikatowy adres URL wywołania zwrotnego dla wyzwalacza lub akcji. Ten adres URL reprezentuje unikatowy identyfikator dla punktów końcowych korzystających z interfejsu API REST usługi. Parametry tej funkcji są takie same jak wyzwalacz elementu webhook lub akcja.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Zmień czas trwania asynchronicznego

Dla obu wyzwalaczy i akcji można ograniczyć czas trwania wzorca asynchronicznego do określonego interwału przez dodanie `limit.timeout` właściwości. W ten sposób, jeśli akcja nie zakończyła się po zakończeniu interwału, stan akcji jest oznaczony jako `Cancelled` z `ActionTimedOut` kodem. `timeout`Właściwość używa [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Ustawienia konfiguracji środowiska uruchomieniowego

Można zmienić domyślne zachowanie środowiska uruchomieniowego dla wyzwalaczy i akcji, dodając te `runtimeConfiguration` właściwości do wyzwalacza lub definicji akcji.

| Właściwość | Typ | Opis | Wyzwalacz lub Akcja | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby wystąpień przepływów pracy, które mogą być uruchamiane w tym samym czasie (współbieżnie lub równolegle). Dostosowanie tej wartości może pomóc w ograniczeniu liczby żądań odbieranych przez systemy zaplecza. <p>Ustawianie `runs` właściwości tak `1` , aby działała tak samo jak ustawienie `operationOptions` właściwości na `SingleInstance` . Można ustawić każdą właściwość, ale nie obie jednocześnie. <p>Aby zmienić domyślny limit, zobacz [sekwencyjne Zmienianie wystąpień](#sequential-trigger) [współbieżności](#change-trigger-concurrency) lub wyzwalaczy wyzwalacza. | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby wystąpień przepływów pracy, które muszą oczekiwać na uruchomienie, gdy w aplikacji logiki jest już uruchomiona Maksymalna liczba równoczesnych wystąpień. <p>Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](#change-waiting-runs). | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.repetitions` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby iteracji pętli "for each", które mogą być uruchamiane w tym samym czasie (współbieżnie lub równolegle). <p>Ustawianie `repetitions` właściwości tak `1` , aby działała tak samo jak ustawienie `operationOptions` właściwości na `SingleInstance` . Można ustawić każdą właściwość, ale nie obie jednocześnie. <p>Aby zmienić domyślny limit, zobacz [zmiana "dla każdego" współbieżności](#change-for-each-concurrency) lub [uruchomienia "dla każdego" pętli sekwencyjnie](#sequential-for-each). | Działanie: <p>[Spowodował](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Liczba całkowita | W przypadku określonych akcji, które obsługują i mają włączone stronicowanie, ta wartość określa *minimalną* liczbę wyników do pobrania. <p>Aby włączyć podział na strony, zobacz [pobieranie danych zbiorczych, elementów lub wyników przy użyciu stronicowania](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Akcja: różnicowa |
| `runtimeConfiguration.secureData.properties` | Tablica | W wielu wyzwalaczach i akcjach te ustawienia ukrywają dane wejściowe, wyjściowe lub zarówno z historii uruchamiania aplikacji logiki. <p>Aby dowiedzieć się więcej na temat zabezpieczania danych, zobacz [ukrywanie wejść i wyjść z historii uruchamiania](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Większość wyzwalaczy i akcji |
| `runtimeConfiguration.staticResult` | JSON, obiekt | W przypadku akcji, które obsługują i mają włączone ustawienie [statycznego wyniku](../logic-apps/test-logic-apps-mock-data-static-results.md) , `staticResult` obiekt ma następujące atrybuty: <p>- `name`, która odwołuje się do nazwy definicji wyniku statycznego bieżącej akcji, która jest wyświetlana wewnątrz `staticResults` atrybutu w atrybucie przepływu pracy aplikacji logiki `definition` . Aby uzyskać więcej informacji, zobacz [statyczne wyniki — dokumentacja schematu dla języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, które określa, czy wyniki statyczne są `Enabled` lub nie dla bieżącej akcji. <p>Aby włączyć statyczne wyniki, zobacz [testowanie aplikacji logiki za pomocą danych makiety przez skonfigurowanie wyników statycznych](../logic-apps/test-logic-apps-mock-data-static-results.md) | Akcja: różnicowa |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opcje operacji

Można zmienić domyślne zachowanie wyzwalaczy i akcji z `operationOptions` właściwością w wyzwalaczu lub definicji akcji.

| Opcja operacji | Typ | Opis | Wyzwalacz lub Akcja | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Ciąg | Wykonywanie akcji opartych na protokole HTTP synchronicznie, a nie asynchronicznie. <p><p>Aby ustawić tę opcję, zobacz [Uruchamianie akcji synchronicznie](#disable-asynchronous-pattern). | Wykonane <p>[ApiConnection](#apiconnection-action), <br>[Protokół http](#http-action), <br>[Odpowiedź](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | Ciąg | W przypadku aplikacji logiki, które [umożliwiają Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) w celu autoryzowania dostępu do wywołań przychodzących do punktu końcowego wyzwalacza opartego na żądaniach, Uwzględnij `Authorization` Nagłówek z tokenu dostępu OAuth w danych wyjściowych wyzwalacza. Aby uzyskać więcej informacji, zobacz [dołączanie nagłówka "Authorization" w danych wyjściowych wyzwalacza żądania](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Wyzwalacze <p>[Żądanie](#request-trigger), <br>[HTTP Webhook](#http-webhook-trigger) | 
| `Sequential` | Ciąg | Uruchom "dla każdej" iteracji pętli pojedynczo, a nie wszystkie w tym samym czasie równolegle. <p>Ta opcja działa tak samo jak ustawienie `runtimeConfiguration.concurrency.repetitions` właściwości na `1` . Można ustawić każdą właściwość, ale nie obie jednocześnie. <p><p>Aby ustawić tę opcję, zobacz [Uruchom polecenie "for each" w sposób sekwencyjny](#sequential-for-each).| Działanie: <p>[Spowodował](#foreach-action) | 
| `SingleInstance` | Ciąg | Uruchom wyzwalacz dla każdego wystąpienia aplikacji logiki sekwencyjnie i poczekaj na zakończenie poprzednio aktywnego uruchomienia przed wyzwoleniem następnego wystąpienia aplikacji logiki. <p><p>Ta opcja działa tak samo jak ustawienie `runtimeConfiguration.concurrency.runs` właściwości na `1` . Można ustawić każdą właściwość, ale nie obie jednocześnie. <p>Aby ustawić tę opcję, zobacz [wyzwalacze wystąpień sekwencyjnie](#sequential-trigger). | Wszystkie wyzwalacze | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Zmienianie współbieżności wyzwalaczy

Domyślnie wystąpienia przepływu pracy aplikacji logiki są wykonywane w tym samym czasie (współbieżnie lub równolegle). To zachowanie oznacza, że każde wystąpienie wyzwalacza jest wyzwalane przed zakończeniem działania wcześniej aktywnego wystąpienia przepływu pracy. Jednak liczba współbieżnie uruchomionych wystąpień ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba wystąpień przepływów pracy współbieżnie uruchomionych osiąga ten limit, wszelkie inne nowe wystąpienia muszą oczekiwać na uruchomienie. Ten limit pomaga kontrolować liczbę żądań odbieranych przez systemy zaplecza.

Po włączeniu kontroli współbieżności wyzwalacza wystąpienia wyzwalające są uruchamiane równolegle do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten domyślny limit współbieżności, można użyć edytora widoku kodu lub projektanta Logic Apps, ponieważ zmiana ustawienia współbieżności za pomocą projektanta powoduje dodanie lub zaktualizowanie `runtimeConfiguration.concurrency.runs` właściwości w podstawowej definicji wyzwalacza i na odwrót. Ta właściwość określa maksymalną liczbę wystąpień nowych przepływów pracy, które mogą być uruchamiane równolegle.

Poniżej przedstawiono kilka kwestii, które należy przejrzeć przed włączeniem współbieżności dla wyzwalacza:

* Nie można wyłączyć współbieżności po włączeniu kontroli współbieżności.

* Po włączeniu współbieżności [Limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) został znacząco zredukowany w przypadku [tablic odwsadowych](#split-on-debatch). Jeśli liczba elementów przekracza ten limit, funkcja SplitOn jest wyłączona.

* Po włączeniu współbieżności długotrwałe wystąpienie aplikacji logiki może spowodować, że nowe wystąpienia aplikacji logiki będą mogły wprowadzić stan oczekiwania. Ten stan uniemożliwia Azure Logic Apps tworzenia nowych wystąpień i występuje nawet wtedy, gdy liczba współbieżnych uruchomień jest mniejsza niż określona maksymalna liczba współbieżnych uruchomień.

  * Aby przerwać ten stan, Anuluj najstarsze wystąpienia, które *nadal działają*.

    1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

    1. W sekcji **historia uruchomień** wybierz najwcześniejsze wystąpienie, które nadal działa, na przykład:

       ![Wybierz najwcześniejsze uruchomione wystąpienie](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Aby wyświetlić tylko wystąpienia, które są nadal uruchomione, Otwórz listę **wszystkie** i wybierz pozycję **Uruchom**.

    1. W obszarze **Uruchom aplikację logiki** wybierz pozycję **Anuluj przebieg**.

       ![Znajdź najwcześniejsze uruchomione wystąpienie](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Aby obejść tę możliwość, należy dodać limit czasu do każdej akcji, która może zawierać te uruchomienia. Jeśli pracujesz w edytorze kodu, zobacz [Zmienianie asynchronicznego czasu trwania](#asynchronous-limits). W przeciwnym razie, jeśli używasz projektanta, wykonaj następujące kroki:

    1. W aplikacji logiki na akcję, w której chcesz dodać limit czasu, w prawym górnym rogu wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

       ![Otwórz ustawienia akcji](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. W obszarze **limit czasu** Określ czas trwania limitu czasu w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Określ czas trwania limitu czasu](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Aby uruchomić aplikację logiki sekwencyjnie, ustaw współbieżność wyzwalacza na `1` wartość przy użyciu edytora widoku kodu lub projektanta. Upewnij się, że właściwość wyzwalacza nie jest ustawiana `operationOptions` na wartość `SingleInstance` w edytorze widoku kodu. W przeciwnym razie zostanie wyświetlony błąd walidacji. Aby uzyskać więcej informacji, zobacz [kolejno wyzwalacze wystąpień](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu 

W definicji wyzwalacza źródłowego Dodaj `runtimeConfiguration.concurrency.runs` Właściwość i ustaw wartość na podstawie [limitów współbieżności wyzwalacza](logic-apps-limits-and-config.md#concurrency-debatching). Aby uruchomić przepływ pracy sekwencyjnie, ustaw wartość właściwości na `1` .

Ten przykład ogranicza współbieżne uruchomienia do 10 wystąpień:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w projektancie Logic Apps

1. W prawym górnym rogu wyzwalacza wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności** ustaw opcję **Limit** **na wartość włączone**. 

1. Przeciągnij suwak **stopień równoległości** na żądaną wartość. Aby uruchomić aplikację logiki sekwencyjnie, przeciągnij wartość suwaka do **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Zmień współbieżność dla każdego z nich

Domyślnie wszystkie iteracje pętli "for each" są wykonywane w tym samym czasie (współbieżnie lub równolegle). To zachowanie oznacza, że każda iteracja zacznie działać przed ukończeniem poprzedniej iteracji. Jednak liczba współbieżnie uruchomionych iteracji ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba współbieżnie uruchomionych iteracji osiągnie ten limit, wszystkie inne iteracje muszą oczekiwać na uruchomienie.

Aby zmienić domyślny limit, można użyć edytora widoku kodu lub projektanta Logic Apps, ponieważ zmiana ustawienia współbieżności za pomocą projektanta powoduje dodanie lub zaktualizowanie `runtimeConfiguration.concurrency.repetitions` właściwości w źródłowej definicji akcji "for each" i odwrotnie. Ta właściwość określa maksymalną liczbę iteracji, które mogą być uruchamiane równolegle.

> [!NOTE] 
> Jeśli ustawisz akcję "for each", która będzie uruchamiana sekwencyjnie przy użyciu projektanta lub edytora widoku kodu, nie ustawiaj `operationOptions` właściwości Action na `Sequential` w edytorze widoku kodu. W przeciwnym razie zostanie wyświetlony błąd walidacji. Aby uzyskać więcej informacji, zobacz [Uruchom polecenie "for each" sekwencyjnie](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu 

W źródłowej definicji "for each" Dodaj lub zaktualizuj `runtimeConfiguration.concurrency.repetitions` Właściwość, która może mieć wartość z zakresu od `1` do `50` .

Oto przykład, który ogranicza współbieżne uruchomienia do 10 iteracji:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w projektancie Logic Apps

1. W obszarze **dla każdej** akcji w prawym górnym rogu wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności** ustaw opcję **Kontrola współbieżności** **na wartość włączone**.

1. Przeciągnij suwak **stopień równoległości** na żądaną wartość. Aby uruchomić aplikację logiki sekwencyjnie, przeciągnij wartość suwaka do **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Limit przebiegów oczekujących zmian

Domyślnie wystąpienia przepływu pracy aplikacji logiki są wykonywane w tym samym czasie (współbieżnie lub równolegle). To zachowanie oznacza, że każde wystąpienie wyzwalacza jest wyzwalane przed zakończeniem działania wcześniej aktywnego wystąpienia przepływu pracy. Jednak liczba współbieżnie uruchomionych wystąpień ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba wystąpień przepływów pracy współbieżnie uruchomionych osiąga ten limit, wszelkie inne nowe wystąpienia muszą oczekiwać na uruchomienie.

Liczba oczekujących przebiegów ma również [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba oczekujących przebiegów osiągnie ten limit, aparat Logic Apps nie akceptuje już nowych uruchomień. Wyzwalacze żądań i elementów webhook zwracają błędy 429, a Wyzwalacze cykliczne rozpoczynają pomijanie prób sondowania.

Można nie tylko [zmienić domyślny limit współbieżności wyzwalacza](#change-trigger-concurrency), ale można również zmienić domyślny limit dla oczekujących przebiegów. W definicji wyzwalacza źródłowego Dodaj `runtimeConfiguration.concurrency.maximumWaitingRuns` Właściwość, która może mieć wartość z zakresu od `1` do `100` .

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Wyzwalaj wystąpienia sekwencyjnie

Aby uruchomić każde wystąpienie przepływu pracy aplikacji logiki dopiero po zakończeniu poprzedniego wystąpienia, należy ustawić wyzwalacz do uruchomienia sekwencyjnie. Można użyć edytora widoku kodu lub projektanta Logic Apps, ponieważ zmiana ustawienia współbieżności za pomocą projektanta powoduje dodanie lub zaktualizowanie `runtimeConfiguration.concurrency.runs` właściwości w podstawowej definicji wyzwalacza i na odwrót.

> [!NOTE] 
> Gdy ustawiasz wyzwalacz do uruchomienia sekwencyjnie przy użyciu projektanta lub edytora widoku kodu, nie ustawiaj właściwości wyzwalacza `operationOptions` na `Sequential` w edytorze widoku kodu. W przeciwnym razie zostanie wyświetlony błąd walidacji. 

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu

W definicji wyzwalacza Ustaw jedną z tych właściwości, ale nie obie. 

Ustaw `runtimeConfiguration.concurrency.runs` Właściwość na `1` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*oraz*

Ustaw `operationOptions` Właściwość na `SingleInstance` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options) i [Opcje operacji](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w projektancie Logic Apps

1. W prawym górnym rogu wyzwalacza wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności** ustaw opcję **Limit** **na wartość włączone**. 

1. Przeciągnij suwak **stopień równoległości** do liczby `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Uruchamiaj kolejne pętle "for each"

Aby uruchomić iterację pętli "for each" tylko po zakończeniu poprzedniej iteracji, należy ustawić akcję "for each", która będzie uruchamiana sekwencyjnie. Można użyć edytora widoku kodu lub projektanta Logic Apps, ponieważ zmiana współbieżności akcji przez projektanta powoduje również dodanie lub zaktualizowanie `runtimeConfiguration.concurrency.repetitions` właściwości w podstawowej definicji akcji i odwrotnie.

> [!NOTE] 
> Po ustawieniu akcji "for each", aby uruchomić ją sekwencyjnie przy użyciu projektanta lub edytora widoku kodu, nie ustawiaj `operationOptions` właściwości Action na `Sequential` w edytorze widoku kodu. W przeciwnym razie zostanie wyświetlony błąd walidacji. 

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu

W definicji akcji Ustaw jedną z tych właściwości, ale nie obie. 

Ustaw `runtimeConfiguration.concurrency.repetitions` Właściwość na `1` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*oraz*

Ustaw `operationOptions` Właściwość na `Sequential` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options) i [Opcje operacji](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w projektancie Logic Apps

1. W prawym górnym rogu **dla każdej** akcji wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności** ustaw opcję **Kontrola współbieżności** **na wartość włączone**.

1. Przeciągnij suwak **stopień równoległości** do liczby `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Uruchamianie akcji w wzorcu operacji synchronicznej

Domyślnie akcja HTTP i akcje APIConnection w Azure Logic Apps są zgodne ze [*wzorcem standardowej operacji asynchronicznej*](/azure/architecture/patterns/async-request-reply), podczas gdy akcja odpowiedzi jest zgodna ze *wzorcem operacji synchronicznej*. Wzorzec asynchroniczny określa, że po wykonaniu akcji lub wysłaniu żądania do określonego punktu końcowego, usługi, systemu lub interfejsu API odbiornik natychmiast zwróci odpowiedź ["202 zaakceptowane"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Ten kod potwierdza, że odbiornik zaakceptował żądanie, ale nie zakończył przetwarzania. Odpowiedź może zawierać `location` nagłówek, który określa adres URL i identyfikator odświeżania, którego obiekt wywołujący może użyć do ciągłego sondowania lub sprawdzania stanu żądania asynchronicznego, dopóki odbiornik nie przestanie działać i zwróci odpowiedź ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) lub inną odpowiedź inną niż 202. Aby uzyskać więcej informacji, zobacz [asynchroniczne integrację mikrousług wymusza autonomię mikrousług](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* W Projektancie aplikacji logiki akcja HTTP, akcje APIConnection i akcja odpowiedzi mają ustawienie **wzorca asynchronicznego** . Po włączeniu tego ustawienia określa, że obiekt wywołujący nie czeka na zakończenie przetwarzania i może przejść do następnej akcji, ale kontynuuje sprawdzanie stanu do momentu zatrzymania przetwarzania. W przypadku wyłączenia tego ustawienia określa, że obiekt wywołujący czeka na zakończenie przetwarzania przed przejściem do następnej akcji. Aby znaleźć to ustawienie, wykonaj następujące kroki:

  1. Na pasku tytułu akcji HTTP wybierz przycisk wielokropka (**...**), który spowoduje otwarcie ustawień akcji.

  1. Znajdź ustawienie **wzorca asynchronicznego** .

     ![Ustawienie "wzorzec asynchroniczny"](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* W definicji bazowej JavaScript Object Notation działania akcja HTTP i akcje APIConnection niejawnie przestrzega wzorca operacji asynchronicznej.

W niektórych scenariuszach może być potrzebna akcja, która ma być zgodna ze wzorcem synchronicznym. Na przykład podczas korzystania z akcji HTTP można wykonać następujące czynności:

* [Unikaj limitów czasu HTTP dla długotrwałych zadań](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Wyłącz sprawdzanie nagłówków lokalizacji](../connectors/connectors-native-http.md#disable-location-header-check)

W takich przypadkach można wykonać akcję synchronicznie, korzystając z następujących opcji:

* Zamień wersję sondowania tej akcji na wersję elementu webhook, jeśli jest dostępna.

* Wyłącz zachowanie asynchroniczne działania, wykonując jedną z następujących czynności:

  * W Projektancie aplikacji logiki Wyłącz [ustawienie **wzorca asynchronicznego**](#turn-off-asynchronous-pattern-setting).

  * W podstawowej definicji JSON akcji [Dodaj `"DisableAsyncPattern"` opcję operacji](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Wyłącz ustawienie **wzorca asynchronicznego**

1. W Projektancie aplikacji logiki na pasku tytułu akcji wybierz przycisk wielokropka (**...**), który spowoduje otwarcie ustawień akcji.

1. Znajdź ustawienie **wzorca asynchronicznego** , Włącz ustawienie **Wyłącz** , jeśli jest włączone, a następnie wybierz pozycję **gotowe**.

   ![Wyłącz ustawienie "wzorzec asynchroniczny"](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Wyłącz wzorzec asynchroniczny w definicji JSON akcji

W podstawowej definicji JSON akcji Dodaj i ustaw [Właściwość "operationOptions"](#operation-options) w `"DisableAsyncPattern"` `"inputs"` sekcji akcji, na przykład:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Uwierzytelnianie wyzwalaczy i akcji

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. Na podstawie wyzwalacza lub akcji, która służy do wykonywania wywołań wychodzących lub żądań dostępu do tych punktów końcowych, można wybierać spośród różnych zakresów typów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)