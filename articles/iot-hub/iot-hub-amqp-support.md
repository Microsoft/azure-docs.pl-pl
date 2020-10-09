---
title: Informacje o pomocy technicznej dotyczącej usługi Azure IoT Hub AMQP | Microsoft Docs
description: Przewodnik dla deweloperów — obsługa urządzeń łączących się z punktami końcowymi opartymi na urządzeniach i opartymi IoT Hub na usłudze, przy użyciu protokołu AMQP. Zawiera informacje o wbudowanej obsłudze AMQP w zestawach SDK urządzeń Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81730751"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Komunikacja z Centrum IoT Hub przy użyciu protokołu AMQP

Usługa Azure IoT Hub obsługuje funkcję [języka oasis Advanced Message Queuing Protocol (AMQP) w wersji 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) w celu dostarczania różnorodnych funkcji za pomocą punktów końcowych dostępnych dla urządzeń i usług. W tym dokumencie opisano sposób używania klientów AMQP do nawiązywania połączenia z usługą IoT Hub w celu korzystania z funkcji IoT Hub.

## <a name="service-client"></a>Klient usługi

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Nawiązywanie połączenia i uwierzytelnianie do centrum IoT Hub (klient usługi)

Aby nawiązać połączenie z usługą IoT Hub przy użyciu programu AMQP, klient może korzystać z uwierzytelniania [opartego na oświadczeniach (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) lub [warstwa zabezpieczeń i prostego uwierzytelnienia (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Klient usługi wymaga następujących informacji:

| Informacje | Wartość |
|-------------|--------------|
| Nazwa hosta Centrum IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Nazwa klucza | `service` |
| Klucz dostępu | Klucz podstawowy lub pomocniczy skojarzony z usługą |
| Sygnatura dostępu współdzielonego | Sygnatura dostępu współdzielonego o krótkim czasie życia w następującym formacie: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Aby uzyskać kod służący do generowania tej sygnatury, zobacz [Kontrola dostępu do IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Poniższy fragment kodu używa [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python) do nawiązywania połączenia z usługą IoT Hub za pośrednictwem linku nadawcy.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Wywoływanie komunikatów z chmury do urządzeń (klient usługi)

Aby dowiedzieć się więcej o wymianie komunikatów z chmury do urządzenia między usługą a centrum IoT i między urządzeniem i usługą IoT Hub, zobacz [wysyłanie komunikatów z chmury do urządzenia z Centrum IoT Hub](iot-hub-devguide-messages-c2d.md). Klient usługi używa dwóch linków do wysyłania komunikatów i otrzymywania informacji zwrotnych dotyczących wcześniej wysłanych komunikatów z urządzeń, zgodnie z opisem w poniższej tabeli:

| Utworzone przez | Typ linku | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Usługa | Link nadawcy | `/messages/devicebound` | Komunikaty z chmury do urządzenia, które są przeznaczone dla urządzeń, są wysyłane do tego linku przez usługę. Komunikaty wysyłane za pośrednictwem tego linku mają `To` ustawioną właściwość na ścieżkę łącza odbiorcy urządzenia docelowego `/devices/<deviceID>/messages/devicebound` . |
| Usługa | Link odbiornika | `/messages/serviceBound/feedback` | Komunikaty dotyczące uzupełniania, odrzucania i przesyłania informacji zwrotnych, które pochodzą z urządzeń otrzymanych w tym łączu przez usługę. Aby uzyskać więcej informacji na temat komunikatów dotyczących opinii, zobacz [wysyłanie komunikatów z chmury do urządzeń z Centrum IoT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Poniższy fragment kodu przedstawia sposób tworzenia komunikatu z chmury do urządzenia i wysyłania go do urządzenia przy użyciu [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Aby otrzymać opinię, klient usługi tworzy łącze odbiorcy. Poniższy fragment kodu przedstawia sposób tworzenia linku przy użyciu [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Jak pokazano w powyższym kodzie, komunikat opinii z chmury do urządzenia ma typ zawartości *aplikacja/vnd.microsoft.iothub.feedback.js*. Za pomocą właściwości w treści JSON komunikatu można wywnioskować stan dostarczania oryginalnej wiadomości:

* Klucz `statusCode` w treści opinii ma jedną z następujących wartości: *sukces*, *wygasłe*, *DeliveryCountExceeded*, *odrzucone*lub *przeczyszczane*.

* Klucz `deviceId` w treści opinii ma identyfikator urządzenia docelowego.

* Klucz `originalMessageId` w treści opinii ma identyfikator oryginalnego komunikatu z chmury do urządzenia, który został wysłany przez usługę. Ten stan dostarczania służy do skorelowania informacji zwrotnych dotyczących komunikatów z chmury do urządzenia.

### <a name="receive-telemetry-messages-service-client"></a>Odbieranie komunikatów telemetrycznych (klient usługi)

Domyślnie Centrum IoT przechowuje komunikaty telemetryczne urządzenia odebrane w wbudowanym centrum zdarzeń. Klient usługi może użyć protokołu AMQP do odbierania przechowywanych zdarzeń.

W tym celu klient usługi najpierw musi połączyć się z punktem końcowym Centrum IoT Hub i odebrać adres przekierowania do wbudowanych centrów zdarzeń. Klient usługi następnie używa podanego adresu do nawiązania połączenia z wbudowanym centrum zdarzeń.

W każdym kroku klient musi przedstawić następujące informacje:

* Prawidłowe poświadczenia usługi (token sygnatury dostępu współdzielonego usługi).

* Dobrze sformatowana ścieżka do partycji grupy odbiorców, z której zamierza pobrać komunikaty. Dla danej grupy odbiorców i identyfikatora partycji ścieżka ma następujący format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (domyślna grupa odbiorców to `$Default` ).

* Opcjonalny predykat filtrowania do wyznaczania punktu początkowego w partycji. Ten predykat może mieć postać numeru sekwencyjnego, przesunięcia lub znacznika czasu do kolejki.

Poniższy fragment kodu używa [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python) do zademonstrowania powyższych kroków:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

W przypadku danego identyfikatora urządzenia Centrum IoT Hub używa skrótu identyfikatora urządzenia do określenia partycji, w której mają być przechowywane wiadomości. W poprzednim fragmencie kodu pokazano, jak zdarzenia są odbierane z jednej takiej partycji. Należy jednak pamiętać, że typowa aplikacja często musi pobrać zdarzenia przechowywane we wszystkich partycjach centrum zdarzeń.

## <a name="device-client"></a>Klient urządzenia

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Nawiązywanie połączenia i uwierzytelnianie do centrum IoT Hub (klient urządzenia)

Aby nawiązać połączenie z usługą IoT Hub przy użyciu usługi AMQP, urządzenie może korzystać z uwierzytelniania [opartego na oświadczeniach (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) lub [warstwa zabezpieczeń i prostego uwierzytelnienia (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) .

Dla klienta urządzenia wymagane są następujące informacje:

| Informacje | Wartość |
|-------------|--------------|
| Nazwa hosta Centrum IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Klucz dostępu | Klucz podstawowy lub pomocniczy skojarzony z urządzeniem |
| Sygnatura dostępu współdzielonego | Sygnatura dostępu współdzielonego o krótkim czasie życia w następującym formacie: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Aby uzyskać kod służący do generowania tej sygnatury, zobacz [Kontrola dostępu do IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Poniższy fragment kodu używa [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python) do nawiązywania połączenia z usługą IoT Hub za pośrednictwem linku nadawcy.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Następujące ścieżki linków są obsługiwane jako operacje na urządzeniach:

| Utworzone przez | Typ linku | Ścieżka łącza | Opis |
|------------|-----------|-----------|-------------|
| Devices | Link odbiornika | `/devices/<deviceID>/messages/devicebound` | Komunikaty z chmury do urządzenia, które są przeznaczone dla urządzeń, są odbierane przez każde urządzenie docelowe. |
| Devices | Link nadawcy | `/devices/<deviceID>/messages/events` | Komunikaty przesyłane z urządzenia do chmury są wysyłane przez ten link. |
| Devices | Link nadawcy | `/messages/serviceBound/feedback` | Informacje zwrotne z chmury do urządzenia są wysyłane do usługi za pośrednictwem tego linku przez urządzenia. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Otrzymywanie poleceń z chmury do urządzenia (klient urządzenia)

Polecenia z chmury do urządzenia, które są wysyłane do urządzeń, docierają do `/devices/<deviceID>/messages/devicebound` linku. Urządzenia mogą odbierać te komunikaty w partiach, a w razie konieczności używać ładunku danych komunikatów, właściwości komunikatu, adnotacji lub właściwości aplikacji.

Poniższy fragment kodu używa [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python)) do odbierania komunikatów z chmury do urządzenia przez urządzenie.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Wysyłanie komunikatów telemetrycznych (klient urządzenia)

Możesz również wysyłać komunikaty telemetryczne z urządzenia za pomocą AMQP. Urządzenie może opcjonalnie dostarczyć słownik właściwości aplikacji lub różne właściwości komunikatów, takie jak identyfikator wiadomości.

Poniższy fragment kodu używa [biblioteki uAMQP w języku Python](https://github.com/Azure/azure-uamqp-python) do wysyłania komunikatów z urządzenia do chmury z urządzenia.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Połączenia AMQP mogą ulec zakłóceniu z powodu błędu sieci lub wygaśnięcia tokenu uwierzytelniania (generowanego w kodzie). Klient usługi musi obsłużyć te sytuacje i ponownie nawiązać połączenie i linki, jeśli jest to konieczne. Jeśli token uwierzytelniania wygaśnie, klient może uniknąć porzucenia połączenia przy użyciu aktywnego odnowienia tokenu przed jego wygaśnięciem.

* Klient musi czasami być w stanie obsłużyć prawidłowe przekierowania linków. Aby zrozumieć tę operację, zobacz dokumentację klienta programu AMQP.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o protokole AMQP, zobacz [specyfikację AMQP v 1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Aby dowiedzieć się więcej na temat IoT Hub Messaging, zobacz:

* [Komunikaty z chmury do urządzenia](./iot-hub-devguide-messages-c2d.md)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Obsługa protokołu transportowego telemetrii usługi kolejkowania komunikatów (MQTT)](./iot-hub-mqtt-support.md)
