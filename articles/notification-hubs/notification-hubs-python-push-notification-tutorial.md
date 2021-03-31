---
title: Jak używać Notification Hubs z językiem Python
description: Dowiedz się, jak używać usługi Azure Notification Hubs z poziomu aplikacji w języku Python.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f81614005a1b0374dc249187c4ff3c920b7c97e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424849"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak używać Notification Hubs w języku Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Możesz uzyskać dostęp do wszystkich funkcji Notification Hubs z poziomu języka Java/PHP/Python/Ruby zaplecza przy użyciu interfejsu REST centrum powiadomień, jak opisano w artykule MSDN [Notification Hubs interfejsy API REST](/previous-versions/azure/reference/dn223264(v=azure.100)).

> [!NOTE]
> Jest to Przykładowa implementacja referencyjna służąca do implementowania powiadomień wysyłanych w języku Python i nie jest oficjalnie obsługiwanym zestawem SDK języka Python dla centrum powiadomień. Przykład został utworzony przy użyciu języka Python 3,4.

W tym artykule wyjaśniono, jak:

- Utwórz klienta REST dla funkcji Notification Hubs w języku Python.
- Wysyłać powiadomienia przy użyciu interfejsu języka Python do interfejsów API REST centrum powiadomień.
- Pobierz zrzut żądania REST protokołu HTTP/odpowiedzi na potrzeby debugowania/celów edukacyjnych.

Możesz skorzystać z [samouczka](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) wprowadzenie dla wybranej platformy mobilnej, implementując część zaplecza w języku Python.

> [!NOTE]
> Zakres przykładu jest ograniczony tylko do wysyłania powiadomień i nie wykonuje żadnych czynności związanych z zarządzaniem rejestracją.

## <a name="client-interface"></a>Interfejs klienta

Główny interfejs klienta może zapewnić te same metody, które są dostępne w [zestawie SDK .net Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx). Ten interfejs umożliwia bezpośrednie przetłumaczenie wszystkich samouczków i przykładów dostępnych w tej witrynie oraz współtworzenie przez społeczność użytkowników Internetu.

Możesz znaleźć cały kod dostępny w [przykładzie otoki REST języka Python].

Na przykład, aby utworzyć klienta:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Aby wysłać powiadomienie wyskakujące systemu Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementacja

Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami w [samouczku wprowadzenie] do ostatniej sekcji, w której ma zostać wdrożony zaplecze.

Wszystkie szczegóły dotyczące implementacji pełnej otoki REST można znaleźć w [witrynie MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). W tej sekcji opisano implementację języka Python głównych kroków wymaganych do uzyskania dostępu Notification Hubs punktów końcowych REST i wysyłania powiadomień

1. Analizowanie parametrów połączenia
2. Generowanie tokenu autoryzacji
3. Wyślij powiadomienie przy użyciu interfejsu API REST protokołu HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

Oto główna Klasa implementująca klienta, którego Konstruktor analizuje parametry połączenia:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Utwórz token zabezpieczający

Szczegóły tworzenia tokenów zabezpieczających są dostępne [tutaj](/previous-versions/azure/reference/dn495627(v=azure.100)).
Dodaj następujące metody do `NotificationHub` klasy, aby utworzyć token oparty na identyfikatorze URI bieżącego żądania, a poświadczenia wyodrębnione z parametrów połączenia.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Wyślij powiadomienie przy użyciu interfejsu API REST protokołu HTTP

Po pierwsze, użyj Definiuj klasy reprezentującej powiadomienie.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Ta klasa jest kontenerem dla natywnej treści powiadomienia lub zestawu właściwości powiadomienia szablonu, zestawu nagłówków, który zawiera format (natywną platformę lub szablon) i właściwości specyficzne dla platformy (na przykład właściwość wygaśnięcia firmy Apple i nagłówki WNS).

Zapoznaj się z [dokumentacją interfejsów API REST Notification Hubs](/previous-versions/azure/reference/dn495827(v=azure.100)) i format określonych platform powiadomień dla wszystkich dostępnych opcji.

Teraz z tą klasą Napisz metody wysyłania powiadomień wewnątrz `NotificationHub` klasy.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Te metody wysyłają żądanie HTTP POST do punktu końcowego/messages centrum powiadomień z prawidłową treścią i nagłówkami, aby wysłać powiadomienie.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Używanie właściwości Debug do włączania szczegółowego rejestrowania

Włączenie właściwości Debug podczas inicjowania centrum powiadomień zapisuje szczegółowe informacje o rejestrowaniu żądania HTTP i zrzutu odpowiedzi oraz szczegółowy komunikat z powiadomieniem.
[Właściwość Notification Hubs TestSend](/previous-versions/azure/reference/dn495827(v=azure.100)) zwraca szczegółowe informacje o wyniku wysłania powiadomienia.
Aby użyć go — zainicjuj przy użyciu następującego kodu:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Adres URL HTTP żądania wysłania centrum powiadomień jest dołączany z wynikiem ciągu zapytania "test".

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Ukończ samouczek

Teraz możesz ukończyć samouczek wprowadzenie, wysyłając powiadomienie z zaplecza w języku Python.

Zainicjuj Notification Hubs klienta (Zastąp ciąg połączenia i nazwę centrum zgodnie z instrukcjami w [samouczku wprowadzenie]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Następnie Dodaj kod wysyłania w zależności od docelowej platformy mobilnej. Ten przykład dodaje również metody wyższego poziomu umożliwiające wysyłanie powiadomień opartych na platformie, na przykład send_windows_notification dla systemu Windows; send_apple_notification (dla firmy Apple) itp.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8,1 (bez Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 i 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle pożar

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Uruchomienie kodu w języku Python powinno spowodować wyświetlenie powiadomienia na urządzeniu docelowym.

## <a name="examples"></a>Przykłady

### <a name="enabling-the-debug-property"></a>Włączanie `debug` właściwości

Po włączeniu flagi debugowania podczas inicjowania NotificationHub zobaczysz szczegółowy zrzut żądań HTTP i odpowiedzi, a także NotificationOutcome jak poniżej, gdzie można zrozumieć, jakie nagłówki HTTP są przesyłane w żądaniu i jakie odpowiedzi HTTP odebrano z centrum powiadomień:

![Zrzut ekranu przedstawiający konsolę z szczegółowymi informacjami na temat żądania i zrzutu odpowiedzi i komunikatów o wyniku powiadomienia, pokreślonych na czerwono.][1]

Zobaczysz na przykład szczegółowy wynik centrum powiadomień.

- Po pomyślnym wysłaniu komunikatu do usługi powiadomień wypychanych.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Jeśli nie znaleziono żadnych elementów docelowych dla powiadomień wypychanych, prawdopodobnie zobaczysz następujące dane wyjściowe jako odpowiedź (co wskazuje, że nie znaleziono żadnych rejestracji do dostarczenia powiadomienia prawdopodobnie z powodu niezgodności tagów)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Emituj powiadomienie wyskakujące do systemu Windows

Zwróć uwagę na nagłówki, które są wysyłane podczas wysyłania wyskakujących powiadomień o emisji do klienta systemu Windows.

```python
hub.send_windows_notification(wns_payload)
```

![Zrzut ekranu konsoli zawierającej szczegóły żądania H T P oraz format powiadomienia Service Bus i wartości typu X W N S podane na czerwono.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Wyślij powiadomienie określające tag (lub wyrażenie tagu)

Zwróć uwagę na nagłówek HTTP, który zostanie dodany do żądania HTTP (w poniższym przykładzie powiadomienie jest wysyłane tylko do rejestracji z ładunkiem "sportem")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Zrzut ekranu konsoli z szczegółowymi informacjami o żądaniu H T T P i formatem powiadomienia Service Bus, tagiem powiadomienia Service Bus i wartościami typu X W N S opisanymi na czerwono.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Wyślij powiadomienie z podaniem wielu tagów

Zwróć uwagę, jak znaczniki w nagłówku HTTP są zmieniane po wysłaniu wielu tagów.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Zrzut ekranu konsoli z szczegółowymi informacjami o żądaniu H T P, a Service Busm formacie powiadomienia, wieloma tagami powiadomień Service Bus i wartościami typu X W N S opisanymi na czerwono.][4]

### <a name="templated-notification"></a>Powiadomienie z szablonem

Należy zauważyć, że format nagłówka HTTP i treść ładunku są wysyłane jako część treści żądania HTTP:

**Szablon zarejestrowany po stronie klienta:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Wysyłanie ładunku po stronie serwera:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Zrzut ekranu konsoli zawierającej szczegóły żądania H T P i typu zawartości oraz Service Bus wartości formatu powiadomień pokreślonych na czerwono.][5]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć klienta REST w języku Python dla Notification Hubs. W tym miejscu można wykonać następujące czynności:

- Pobierz pełny [przykład otoki REST języka Python], który zawiera cały kod w tym artykule.
- Kontynuuj uczenie się Notification Hubs funkcji tagowania w [samouczku z najświeższymi wiadomościami]
- Kontynuuj uczenie się dotyczące funkcji szablonów Notification Hubs w [samouczku lokalizowania wiadomości]

<!-- URLs -->
[Przykład otoki REST języka Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Samouczek wprowadzenie]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Samouczek dotyczący najświeższych wiadomości]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Samouczek lokalizowania wiadomości]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
