---
title: Publikowanie i subskrybowanie za pomocą Azure IoT Edge | Microsoft Docs
description: Używanie IoT Edge brokera MQTT do publikowania i subskrybowania komunikatów
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: ecb034ae621c935c3ebcd5b480e116c2cb1d864f
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435539"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publikowanie i subskrybowanie za pomocą Azure IoT Edge

Azure IoT Edge brokera MQTT można użyć do publikowania i subskrybowania komunikatów. W tym artykule opisano sposób nawiązywania połączenia z tym brokerem, publikowania i subskrybowania komunikatów za pomocą tematów zdefiniowanych przez użytkownika i używania IoT Hub podstawowych komunikatów. IoT Edge brokera MQTT jest wbudowanym Centrum IoT Edge. Aby uzyskać więcej informacji, zobacz [możliwości brokera centrum IoT Edge](iot-edge-runtime.md).

> [!NOTE]
> IoT Edge Broker MQTT jest obecnie w publicznej wersji zapoznawczej.

## <a name="pre-requisites"></a>Wymagania wstępne

- Konto platformy Azure z prawidłową subskrypcją
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/) z `azure-iot` zainstalowanym rozszerzeniem interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [kroki instalacji rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-reference-for-iot)Azure.
- **IoT Hub** jednostki SKU albo F1, S1, S2 lub S3.
- Mieć **urządzenie IoT Edge w wersji 1,2 lub nowszej**. Ponieważ IoT Edge Broker MQTT jest obecnie w publicznej wersji zapoznawczej, ustaw następujące zmienne środowiskowe na true w kontenerze edgeHub, aby włączyć brokera MQTT:

   | Nazwa | Wartość |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto klientów** zainstalowanych na urządzeniu IoT Edge. W tym artykule są wykorzystywane popularne Mosquitto Klienci, [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) i [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Zamiast tego można użyć innych klientów MQTT. Aby zainstalować klientów Mosquitto na urządzeniu Ubuntu, uruchom następujące polecenie:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Nie należy instalować serwera Mosquitto, ponieważ może to spowodować zablokowanie portów MQTT (8883 i 1883) i konflikt z Centrum IoT Edge.

## <a name="connect-to-iot-edge-hub"></a>Nawiązywanie połączenia z Centrum IoT Edge

Łączenie z usługą IoT Edge Hub odbywa się zgodnie z instrukcjami opisanymi w [artykule nawiązywanie połączenia z IoT Hub z ogólnym artykułem klienta MQTT](../iot-hub/iot-hub-mqtt-support.md) lub w [opisie koncepcji artykułu Centrum IoT Edge](iot-edge-runtime.md). Następujące kroki są następujące:

1. Opcjonalnie klient MQTT nawiązuje *bezpieczne połączenie* z Centrum IoT Edge przy użyciu Transport Layer Security (TLS)
2. Klient MQTT *uwierzytelnia* się w centrum IoT Edge
3. Centrum IoT Edge *autoryzuje* klienta MQTT zgodnie z zasadami autoryzacji

### <a name="secure-connection-tls"></a>Bezpieczne połączenie (TLS)

Transport Layer Security (TLS) służy do nawiązywania zaszyfrowanej komunikacji między klientem a centrum IoT Edge.

Aby wyłączyć protokół TLS, użyj portu 1883 (MQTT) i powiąż kontener edgeHub z portem 1883.

Aby włączyć protokół TLS, jeśli klient nawiąże połączenie z portem 8883 (MQTTS) z brokerem MQTT, zostanie zainicjowany kanał TLS. Broker wysyła swój łańcuch certyfikatów, którego klient musi zweryfikować. Aby można było sprawdzić poprawność łańcucha certyfikatów, certyfikat główny brokera MQTT musi być zainstalowany jako zaufany certyfikat na komputerze klienckim. Jeśli certyfikat główny nie jest zaufany, Biblioteka klienta zostanie odrzucona przez brokera MQTT z błędem weryfikacji certyfikatu. Kroki, które należy wykonać, aby zainstalować ten certyfikat główny brokera na kliencie, są takie same jak w przypadku [przezroczystego przypadku bramy](how-to-create-transparent-gateway.md) i zostały opisane w dokumentacji [przygotowanie urządzenia podrzędnego](how-to-connect-downstream-device.md#prepare-a-downstream-device) .

### <a name="authentication"></a>Authentication

Aby klient MQTT mógł się uwierzytelnić, najpierw musi wysłać pakiet CONNECT do brokera MQTT w celu zainicjowania połączenia w jego nazwie. Ten pakiet zawiera trzy informacje o uwierzytelnianiu: a `client identifier` , a `username` i `password` :

- `client identifier`Pole to nazwa urządzenia lub nazwy modułu w IoT Hub. Używa następującej składni:

  - Dla urządzenia: `<device_name>`

  - Dla modułu: `<device_name>/<module_name>`

   Aby można było połączyć się z brokerem MQTT, urządzenie lub moduł musi być zarejestrowany w IoT Hub.

   Broker nie będzie zezwalać na połączenia z wielu klientów przy użyciu tych samych poświadczeń. Jeśli drugi klient nawiąże połączenie przy użyciu tych samych poświadczeń, Broker rozłącza już podłączonego klienta.

- `username`Pole jest wyprowadzane z nazwy urządzenia lub modułu, a nazwa IoTHub, do którego należy urządzenie, przy użyciu następującej składni:

  - Dla urządzenia: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Dla modułu: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`Pole pakietu Connect zależy od trybu uwierzytelniania:

  - W przypadku korzystania z uwierzytelniania przy użyciu [kluczy symetrycznych](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` pole jest tokenem sygnatury dostępu współdzielonego.
  - W przypadku korzystania z [uwierzytelniania z podpisem własnym X. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) `password` pole nie istnieje. W tym trybie uwierzytelniania wymagany jest kanał TLS. Klient musi połączyć się z portem 8883, aby nawiązać połączenie TLS. Podczas uzgadniania protokołu TLS Broker MQTT żąda certyfikatu klienta. Ten certyfikat służy do weryfikowania tożsamości klienta i w ten sposób `password` nie jest potrzebny do późniejszego wysłania pakietu Connect. Wysłanie zarówno certyfikatu klienta, jak i pola hasła spowoduje wystąpienie błędu, a połączenie zostanie zamknięte. Biblioteki MQTT i biblioteki klienckie TLS zazwyczaj mają możliwość wysyłania certyfikatu klienta podczas inicjowania połączenia. Możesz zobaczyć przykład krok po kroku w sekcji [przy użyciu certyfikatu x509 na potrzeby uwierzytelniania klientów](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

Moduły wdrożone przez IoT Edge używają [uwierzytelniania kluczy symetrycznych](how-to-authenticate-downstream-device.md#symmetric-key-authentication) i mogą wywołać [interfejs API obciążenia lokalnego IoT Edge](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) , aby programowo uzyskać token sygnatury dostępu współdzielonego nawet w trybie offline.

### <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu klienta programu MQTT w usłudze IoT Edge Hub musi on mieć autoryzację, aby nawiązać połączenie. Po nawiązaniu połączenia należy mieć autoryzację, aby opublikować lub subskrybować określone tematy. Te autoryzacji są udzielane przez Centrum IoT Edge w oparciu o zasady autoryzacji. Zasady autoryzacji są zestawem instrukcji wyrażonych jako struktura JSON, która jest wysyłana do centrum IoT Edge za pośrednictwem jego sznurka. Edytuj dwuosiową IoT Edge centrum, aby skonfigurować zasady autoryzacji.

> [!NOTE]
> W publicznej wersji zapoznawczej edytowanie zasad autoryzacji brokera MQTT jest możliwe tylko za pośrednictwem programu Visual Studio, Visual Studio Code lub interfejsu wiersza polecenia platformy Azure. Obecnie Azure Portal nie obsługuje edytowania dwuosiowego Centrum IoT Edge i jego zasad autoryzacji.

Każda instrukcja zasad autoryzacji składa się z kombinacji `identities` , `allow` lub `deny` efektów, `operations` i `resources` :

- `identities` opisz temat zasad. Musi być mapowany na `client identifier` wysyłanych przez klientów w ich pakiecie Connect.
- `allow` lub `deny` efekty określają, czy zezwalać na operacje, czy odmówić.
- `operations` Zdefiniuj akcje do autoryzacji. `mqtt:connect``mqtt:publish`i `mqtt:subscribe` są dzisiaj trzy obsługiwane akcje.
- `resources` Zdefiniuj obiekt zasad. Może to być temat lub wzorzec tematu zdefiniowany za pomocą [symboli wieloznacznych MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Poniżej znajduje się przykład zasad autoryzacji, które jawnie nie zezwalają na nawiązywanie połączenia z klientem "rogue_client", umożliwia wszelkim klientom usługi Azure IoT Łączenie się z usługą "sensor_1" w celu publikowania w temacie `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Podczas pisania zasad autoryzacji należy pamiętać o kilku kwestiach:

- Wymaga `$edgeHub` schematu bliźniaczyego w wersji 1,2
- Domyślnie wszystkie operacje są odrzucane.
- Instrukcje autoryzacji są oceniane w kolejności, w jakiej są wyświetlane w definicji JSON. Rozpocznie się od przejrzenia, `identities` a następnie wybrania pierwszej instrukcji Zezwalaj lub Odmów zgodnej z żądaniem. W przypadku konfliktów między instrukcjami zezwalania i odmowy, instrukcja odmowy WINS.
- W zasadach autoryzacji można używać kilku zmiennych (na przykład podstawień):
    - `{{iot:identity}}` reprezentuje tożsamość aktualnie podłączonego klienta. Na przykład tożsamość urządzenia, taka jak `myDevice` lub tożsamość modułu, taką jak `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` reprezentuje tożsamość aktualnie podłączonego urządzenia. Na przykład tożsamość urządzenia, taka jak `myDevice` lub tożsamość urządzenia, na której jest uruchomiony moduł `myEdgeDevice` .
    - `{{iot:module_id}}` reprezentuje tożsamość aktualnie połączonego modułu. Ta zmienna jest pusta dla podłączonych urządzeń lub tożsamość modułu, na przykład `SampleModule` .
    - `{{iot:this_device_id}}` reprezentuje tożsamość urządzenia IoT Edge z uruchomionymi zasadami autoryzacji. Na przykład `myIoTEdgeDevice`.

Autoryzacje tematów usługi IoT Hub są obsługiwane nieco inaczej niż w przypadku tematów zdefiniowanych przez użytkownika. Oto kluczowe kwestie, które należy zapamiętać:

- Urządzenia lub moduły usługi Azure IoT wymagają jawnej reguły autoryzacji, aby nawiązać połączenie z usługą IoT Edge Hub Broker MQTT. Poniżej przedstawiono domyślne zasady autoryzacji łączenia.
- Usługa Azure IoT lub moduły mogą uzyskać dostęp do własnych tematów usługi IoT Hub domyślnie bez żadnej jawnej reguły autoryzacji. Jednak autoryzacje odpadają od relacji nadrzędny/podrzędny w tym przypadku, a te relacje muszą być ustawione. Moduły IoT Edge są automatycznie ustawiane jako elementy podrzędne urządzenia IoT Edge, ale urządzenia muszą być jawnie ustawione jako elementy podrzędne swojej bramy IoT Edge.
- Usługa Azure IoT lub moduły mogą uzyskać dostęp do tematów, w tym tematów dotyczących usługi IoT Hub, innych urządzeń lub modułów, które zapewniają, że zdefiniowane są odpowiednie reguły autoryzacji jawnej.

Oto domyślne zasady autoryzacji, które mogą być używane do włączania **połączeń** z brokerem przez wszystkie urządzenia lub moduły usługi Azure IoT:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Teraz, gdy rozumiesz, jak nawiązać połączenie z IoT Edge brokerem MQTT, zobacz, jak można go użyć do publikowania i subskrybowania wiadomości najpierw w tematach zdefiniowanych przez użytkownika, a następnie w tematach usługi IoT Hub, a wreszcie do innego brokera MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publikowanie i subskrybowanie tematów zdefiniowanych przez użytkownika

W tym artykule opisano użycie jednego klienta o nazwie **sub_client** , który subskrybuje temat i inny klient o nazwie **pub_client** , który publikuje w temacie. Użyjemy uwierzytelniania przy użyciu [klucza symetrycznego](how-to-authenticate-downstream-device.md#symmetric-key-authentication) , ale ten sam można wykonać przy użyciu uwierzytelniania z podpisem [własnym x. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) lub [uwierzytelniania podpisanego przez urząd certyfikacji x. 509](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Tworzenie klientów wydawcy i subskrybentów

Utwórz dwa urządzenia IoT w IoT Hub i uzyskaj swoje hasła. Korzystanie z interfejsu wiersza polecenia platformy Azure z terminalu do:

1. Utwórz dwa urządzenia IoT w IoT Hub, które są nadrzędne dla IoT Edge urządzeniu:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Uzyskaj swoje hasła, generując token SAS:

    - Dla urządzenia:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       gdzie 3600 to czas trwania tokenu sygnatury dostępu współdzielonego w sekundach (na przykład 3600 = 1 godzina).
    
    - Dla modułu:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       gdzie 3600 to czas trwania tokenu sygnatury dostępu współdzielonego w sekundach (na przykład 3600 = 1 godzina).

3. Skopiuj token sygnatury dostępu współdzielonego, który jest wartością odpowiadającą kluczowi "SAS" z danych wyjściowych. Poniżej przedstawiono przykładowe dane wyjściowe z polecenia platformy Azure w powyższym miejscu:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autoryzowanie klientów korzystających z wydawcy i subskrybentów

Aby autoryzować wydawcę i abonenta, Edytuj IoT Edgeą sieć centrów za pośrednictwem interfejsu wiersza polecenia platformy Azure, programu Visual Studio lub programu Visual Studio Code, aby uwzględnić następujące zasady autoryzacji:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Uwierzytelnianie kluczy symetrycznych bez protokołu TLS

#### <a name="subscribe"></a>Zasubskrybowanie

Połącz klienta **sub_client** MQTT z brokerem MQTT i Subskrybuj go, `test_topic` uruchamiając następujące polecenie na urządzeniu IoT Edge:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

`<edge_device_address>`  =  `localhost` w tym przykładzie, ponieważ klient jest uruchomiony na tym samym urządzeniu co IoT Edge.

Należy zauważyć, że w pierwszym przykładzie jest używany port 1883 (MQTT) bez protokołu TLS. Inny przykład z portem 8883 (MQTTS) z włączonym protokołem TLS jest widoczny w następnej sekcji.

Klient **sub_client** MQTT jest teraz uruchamiany i oczekuje na komunikaty przychodzące w usłudze `test_topic` .

#### <a name="publish"></a>Publikowanie

Połącz klienta **pub_client** MQTT z brokerem MQTT i opublikuje komunikat w tym samym pliku `test_topic` , co powyżej, uruchamiając następujące polecenie na urządzeniu IoT Edge z innego terminalu:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

`<edge_device_address>`  =  `localhost` w tym przykładzie, ponieważ klient jest uruchomiony na tym samym urządzeniu co IoT Edge.

Wykonanie polecenia, **sub_client** klient MQTT odbiera komunikat "Hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Uwierzytelnianie kluczy symetrycznych za pomocą protokołu TLS

Aby włączyć protokół TLS, należy zmienić port z 1883 (MQTT) na 8883 (MQTTS), a klienci muszą mieć certyfikat główny brokera programu MQTT, aby można było sprawdzić poprawność łańcucha certyfikatów wysyłanego przez brokera programu MQTT. Można to zrobić, wykonując kroki opisane w sekcji [bezpieczne połączenie (TLS)](#secure-connection-tls).

Ponieważ klienci działają na tym samym urządzeniu co Broker MQTT w powyższym przykładzie, te same kroki mają zastosowanie do włączenia protokołu TLS tylko przez zmianę numeru portu z 1883 (MQTT) na 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publikowanie i subskrybowanie tematów IoT Hub

[Zestawy SDK urządzeń Azure IoT](https://github.com/Azure/azure-iot-sdks) już umożliwiają klientom wykonywanie IoT Hub operacji, ale nie zezwalają na publikowanie/subskrybowanie tematów zdefiniowanych przez użytkownika. IoT Hub operacji można wykonać przy użyciu dowolnego klienta MQTT przy użyciu semantyki publikowania i subskrybowania, o ile są przestrzegane protokoły pierwotne usługi IoT Hub. Przejdziemy do konkretnych metod, aby zrozumieć, jak działają te protokoły.

### <a name="send-telemetry-data-to-iot-hub"></a>Wyślij dane telemetryczne do IoT Hub

Wysyłanie danych telemetrycznych do IoT Hub jest podobne do publikowania w temacie zdefiniowanym przez użytkownika, ale przy użyciu konkretnego tematu IoT Hub:

- W przypadku urządzenia, dane telemetryczne są wysyłane w temacie: `devices/<device_name>/messages/events`
- W przypadku modułu dane telemetryczne są wysyłane w temacie: `devices/<device_name>/<module_name>/messages/events`

Ponadto Utwórz trasę, taką jak, `FROM /messages/* INTO $upstream` Aby wysyłać dane telemetryczne z IoT Edge brokera MQTT do usługi IoT Hub. Aby dowiedzieć się więcej na temat routingu, zobacz [deklarowanie tras](module-composition.md#declare-routes).

### <a name="get-twin"></a>Pobierz sznurki

Pobieranie sznurka urządzenia/modułu nie jest typowym wzorcem MQTT. Klient musi wydać żądanie dotyczące sznurka, które IoT Hub ma obsłużyć.

Aby można było odbierać bliźniaczych reprezentacji, klient musi zasubskrybować IoT Hub określonego tematu `$iothub/twin/res/#` . Ta nazwa tematu jest dziedziczona po IoT Hub i wszyscy klienci muszą subskrybować ten sam temat. Nie oznacza to, że urządzenia lub moduły otrzymują sznur siebie nawzajem. IoT Hub i IoT Edge centrum wie, które sznurki powinny być dostarczone, nawet jeśli wszystkie urządzenia nasłuchują tej samej nazwy tematu. 

Po dokonaniu subskrypcji klient musi poprosił o dwuosiowy, publikując komunikat w IoT Hub określonym temacie, `$iothub/twin/GET/?rid=<request_id>/#` gdzie  `<request_id>` jest dowolnym identyfikatorem. Usługa IoT Hub wyśle następnie odpowiedź z żądanymi danymi w temacie `$iothub/twin/res/200/?rid=<request_id>` , do którego subskrybuje się klient. Jest to sposób, w jaki klient może sparować swoje żądania z odpowiedziami.

### <a name="receive-twin-patches"></a>Otrzymywanie poprawek bliźniaczych

Aby otrzymywać pojedyncze poprawki, klient musi subskrybować specjalny temat IoTHub `$iothub/twin/PATCH/properties/desired/#` . Po dokonaniu subskrypcji klient otrzymuje poprawki z przędzy wysyłane przez IoT Hub w tym temacie. 

### <a name="receive-direct-methods"></a>Otrzymywanie metod bezpośrednich

Otrzymanie metody bezpośredniej jest podobne do otrzymywania pełnej bliźniaczych reprezentacji z dodaniem, że klient musi potwierdzić, że odebrał wywołanie. Pierwszy klient subskrybuje specjalne zagadnienie w usłudze IoT Hub `$iothub/methods/POST/#` . Następnie po odebraniu metody bezpośredniej w tym temacie klient musi wyodrębnić identyfikator żądania `rid` z tematu podrzędnego, na którym jest odbierana Metoda bezpośrednia, a następnie opublikować komunikat z potwierdzeniem w temacie specjalnym Centrum IoT Hub `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Wyślij metody bezpośrednie

Wysyłanie metody bezpośredniej jest wywołaniem HTTP i w ten sposób nie przechodzi przez brokera MQTT. Aby wysłać metodę bezpośrednią do centrum IoT Hub, zobacz [Opis i wywoływanie metod bezpośrednich](../iot-hub/iot-hub-devguide-direct-methods.md). Aby bezpośrednio wysłać metodę bezpośrednią do innego modułu, zobacz ten [przykład wywołania metody bezpośredniej zestawu SDK języka C# dla usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publikowanie i subskrybowanie brokerów MQTT

Aby połączyć dwa brokerów MQTT, centrum IoT Edge obejmuje mostek MQTT. Mostek MQTT jest często używany do łączenia brokera MQTT z uruchomionym innym brokerem MQTT. Tylko podzestaw ruchu lokalnego jest zazwyczaj wypychany do innego brokera.

> [!NOTE]
> Mostka IoT Edge Hub można obecnie używać tylko między zagnieżdżonymi urządzeniami IoT Edge. Nie można jej używać do wysyłania danych do centrum IoT Hub, ponieważ Centrum IoT nie jest w pełni funkcjonalnym brokerem MQTT. Aby dowiedzieć się więcej o obsłudze funkcji brokera usługi IoT Hub MQTT, zobacz temat [komunikowanie się z usługą IoT Hub przy użyciu protokołu MQTT](../iot-hub/iot-hub-mqtt-support.md). Aby dowiedzieć się więcej na temat zagnieżdżania IoT Edge urządzeń, zobacz [łączenie urządzenia podrzędnego IoT Edge z bramą Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

W konfiguracji zagnieżdżonej, IoT Edge Hub MQTTer działa jako klient nadrzędnego brokera MQTT, dlatego reguły autoryzacji muszą być ustawione w EdgeHub nadrzędnym, aby zezwolić EdgeHub podrzędnemu na publikowanie i subskrybowanie określonych tematów zdefiniowanych przez użytkownika, dla których jest skonfigurowany ten mostek.

IoT Edge mostka MQTT jest konfigurowana za pośrednictwem struktury JSON wysyłanej do centrum IoT Edge za pośrednictwem jego sznurka. Edytuj dwuosiową IoT Edge centrum, aby skonfigurować jego mostek MQTT.

> [!NOTE]
> W publicznej wersji zapoznawczej konfiguracja mostka MQTT jest dostępna tylko za pośrednictwem programu Visual Studio, Visual Studio Code lub interfejsu wiersza polecenia platformy Azure. Obecnie Azure Portal nie obsługuje edytowania przędzy Centrum IoT Edge i konfiguracji mostka MQTT.

Mostek MQTT można skonfigurować tak, aby IoT Edge łączył brokera MQTT Hub z wieloma zewnętrznymi brokerami. Dla każdego brokera zewnętrznego wymagane są następujące ustawienia:

- `endpoint` jest adresem zdalnego brokera MQTT, z którym ma zostać nawiązane połączenie. Tylko urządzenia nadrzędne IoT Edge są obecnie obsługiwane i są zdefiniowane przez zmienną `$upstream` .
- `settings` definiuje tematy, które mają być połączone dla punktu końcowego. Dla każdego punktu końcowego może istnieć wiele ustawień, a do jego konfiguracji służą następujące wartości:
    - `direction`: `in` Aby subskrybować tematy zdalnego brokera lub `out` opublikować je w tematach zdalnego brokera
    - `topic`: podstawowy wzorzec tematu do dopasowania. W celu zdefiniowania tego wzorca można użyć [symboli wieloznacznych MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) . Różne prefiksy mogą być stosowane do tego wzorca tematu w przypadku brokera lokalnego i zdalnego brokera.
    - `outPrefix`: Prefiks, który jest stosowany do `topic` wzorca w brokerze zdalnym.
    - `inPrefix`: Prefiks, który jest stosowany do `topic` wzorca w brokerze lokalnym.

Poniżej znajduje się przykład konfiguracji IoT Edgeego mostka MQTT, która umożliwia ponowne opublikowanie wszystkich komunikatów odebranych w tematach `alerts/#` nadrzędnego urządzenia IoT Edge na podrzędnym urządzeniu IoT Edge w tych samych tematach i ponowne opublikowanie wszystkich komunikatów wysyłanych w tematach `/local/telemetry/#` podrzędnego urządzenia IoT Edge do nadrzędnego urządzenia IoT Edge w temacie `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Inne notatki dotyczące MQTTego Centrum IoT Edge:
- Protokół MQTT zostanie automatycznie użyty jako protokół nadrzędny, gdy jest używany Broker MQTT i że IoT Edge jest używany w konfiguracji zagnieżdżonej, na przykład z `parent_hostname` określonym. Aby dowiedzieć się więcej o protokołach nadrzędnych, zapoznaj się z tematem [komunikacja w chmurze](iot-edge-runtime.md#cloud-communication). Aby dowiedzieć się więcej na temat konfiguracji zagnieżdżonych, zobacz [łączenie urządzenia podrzędnego IoT Edge z bramą Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Następne kroki

[Omówienie centrum IoT Edge](iot-edge-runtime.md#iot-edge-hub)
