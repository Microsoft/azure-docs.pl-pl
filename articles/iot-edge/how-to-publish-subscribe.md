---
title: Publikowanie i subskrybowanie przy użyciu Azure IoT Edge | Microsoft Docs
description: Publikowanie i subskrybowanie komunikatów za pomocą IoT Edge brokera MQTT
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516182"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Publikowanie i subskrybowanie przy Azure IoT Edge (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Do publikowania i subskrybowania komunikatów Azure IoT Edge brokera MQTT. W tym artykule przedstawiono sposób nawiązywania połączenia z tym brokerem, publikowania i subskrybowania komunikatów w tematach zdefiniowanych przez użytkownika oraz używania IoT Hub podstawowych komunikatów. Broker IoT Edge MQTT jest wbudowany w centrum IoT Edge. Aby uzyskać więcej informacji, [zobacz brokering capabilities of the IoT Edge hub](iot-edge-runtime.md)(Funkcje brokera IoT Edge Hub).

> [!NOTE]
> IoT Edge broker MQTT jest obecnie w publicznej wersji zapoznawczej.

## <a name="pre-requisites"></a>Wymagania wstępne

- Konto platformy Azure z ważną subskrypcją
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/) z `azure-iot` zainstalowanym rozszerzeniem interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Kroki instalacji rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure.](/cli/azure/azure-cli-reference-for-iot)
- Jeden **IoT Hub** SKU F1, S1, S2 lub S3.
- Mieć urządzenie **IoT Edge w wersji 1.2 lub nowszą.** Ponieważ IoT Edge MQTT jest obecnie w publicznej wersji zapoznawczej, ustaw następujące zmienne środowiskowe na wartość true w kontenerze edgeHub, aby włączyć brokera MQTT:

   | Nazwa | Wartość |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Klienci Usługi Mosquitto** zainstalowani na IoT Edge urządzeniu. W tym artykule użyto popularnych klientów Usługi Mosquitto [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) i [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Zamiast tego można użyć innych klientów MQTT. Aby zainstalować klientów Mosquitto na urządzeniu z systemem Ubuntu, uruchom następujące polecenie:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Nie instaluj serwera Mosquitto, ponieważ może to spowodować zablokowanie portów MQTT (8883 i 1883) i konflikt z IoT Edge hubem.

## <a name="connect-to-iot-edge-hub"></a>Nawiązywanie połączenia z IoT Edge hub

Nawiązywanie połączenia IoT Edge Hub jest opisane w artykule Connecting to IoT Hub with a generic MQTT client (Nawiązywanie połączenia z usługą IoT Hub przy użyciu ogólnego klienta [MQTT)](../iot-hub/iot-hub-mqtt-support.md) lub w opisie koncepcyjnym artykułu [IoT Edge Hub](iot-edge-runtime.md). Te kroki są następujące:

1. Opcjonalnie klient MQTT nawiązuje  bezpieczne połączenie z centrum IoT Edge przy użyciu Transport Layer Security (TLS)
2. Klient MQTT *uwierzytelnia się w* IoT Edge hub
3. Centrum IoT Edge *autoryzuje klienta* MQTT na jego zasady autoryzacji

### <a name="secure-connection-tls"></a>Bezpieczne połączenie (TLS)

Transport Layer Security (TLS) służy do nawiązywania zaszyfrowanej komunikacji między klientem a centrum IoT Edge sieci.

Aby wyłączyć obsługę TLS, użyj portu 1883 (MQTT) i powiąż kontener edgeHub z portem 1883.

Aby włączyć obsługę TLS, jeśli klient połączy się z brokerem MQTT za pośrednictwem portu 8883 (MQTTS), zostanie zainicjowany kanał TLS. Broker wysyła swój łańcuch certyfikatów, który klient musi zweryfikować. Aby można było zweryfikować łańcuch certyfikatów, certyfikat główny brokera MQTT musi być zainstalowany jako zaufany certyfikat na kliencie. Jeśli certyfikat główny nie jest zaufany, biblioteka klienta zostanie odrzucona przez brokera MQTT z błędem weryfikacji certyfikatu. Kroki, które należy wykonać, aby zainstalować ten certyfikat główny brokera na kliencie, są takie same jak w przypadku przezroczystej bramy i są opisane w dokumentacji dotyczącej przygotowywania urządzenia [podrzędnego.](how-to-connect-downstream-device.md#prepare-a-downstream-device) [](how-to-create-transparent-gateway.md)

### <a name="authentication"></a>Authentication

Aby klient MQTT się uwierzytelnił, najpierw musi wysłać pakiet CONNECT do brokera MQTT, aby zainicjować połączenie w jego nazwie. Ten pakiet zawiera trzy informacje o uwierzytelnianiu: `client identifier` , `username` , i `password` :

- Pole to nazwa urządzenia lub modułu w `client identifier` IoT Hub. Używa następującej składni:

  - Dla urządzenia: `<device_name>`

  - W przypadku modułu: `<device_name>/<module_name>`

   Aby można było nawiązać połączenie z brokerem MQTT, urządzenie lub moduł muszą być zarejestrowane w IoT Hub.

   Broker nie zezwoli na połączenia z wielu klientów przy użyciu tych samych poświadczeń. Broker rozłączy już połączonego klienta, jeśli drugi klient połączy się przy użyciu tych samych poświadczeń.

- Pole pochodzi od nazwy urządzenia lub modułu, a nazwa `username` usługi IoTHub, do której należy urządzenie, przy użyciu następującej składni:

  - W przypadku urządzenia: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - W przypadku modułu: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Pole `password` pakietu CONNECT zależy od trybu uwierzytelniania:

  - W przypadku [korzystania z uwierzytelniania za pomocą kluczy symetrycznych](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` pole jest tokenem SAS.
  - W przypadku korzystania z uwierzytelniania z podpisem własnym [X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) `password` pole nie jest obecne. W tym trybie uwierzytelniania wymagany jest kanał protokołu TLS. Klient musi nawiązać połączenie z portem 8883, aby nawiązać połączenie TLS. Podczas uściślniania TLS broker MQTT żąda certyfikatu klienta. Ten certyfikat służy do weryfikowania tożsamości klienta, dlatego pole nie jest potrzebne `password` później podczas wysłania pakietu CONNECT. Wysłanie zarówno certyfikatu klienta, jak i pola hasła spowoduje błąd i połączenie zostanie zamknięte. Biblioteki MQTT i biblioteki klienta TLS zazwyczaj mają sposób wysyłania certyfikatu klienta podczas inicjowania połączenia. Przykład krok po kroku można znaleźć w sekcji Using X509 Certificate for client authentication (Używanie certyfikatu [X509 do uwierzytelniania klienta).](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)

Moduły wdrożone przez usługę [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) IoT Edge używają uwierzytelniania za pomocą kluczy symetrycznych i mogą wywołać lokalny interfejs [API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) obciążenia IoT Edge, aby programowo uzyskać token SAS nawet w trybie offline.

### <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu klienta MQTT w IoT Edge centrum należy autoryzować go do nawiązywania połączenia. Po na połączeniu musi mieć autoryzację do publikowania lub subskrybowania określonych tematów. Te autoryzacje są udzielane przez centrum IoT Edge na podstawie jego zasad autoryzacji. Zasady autoryzacji to zestaw instrukcji wyrażonych jako struktura JSON, która jest wysyłana do centrum IoT Edge za pośrednictwem bliźniaczej reprezentacji. Edytuj bliźniacze IoT Edge centrum danych, aby skonfigurować jego zasady autoryzacji.

> [!NOTE]
> W publicznej wersji zapoznawczej tylko interfejs wiersza polecenia platformy Azure obsługuje wdrożenia zawierające zasady autoryzacji brokera MQTT. Ten Azure Portal obecnie nie obsługuje edytowania bliźniaczej IoT Edge bliźniaczej reprezentacji centrum danych i jej zasad autoryzacji.

Każda instrukcja zasad autoryzacji składa się z kombinacji `identities` elementów , `allow` lub `deny` effects, i `operations` `resources` :

- `identities` opisz temat zasad. Musi być mapowany na pakiety CONNECT wysyłane `client identifier` przez klientów.
- `allow` Efekty `deny` lub definiują, czy operacje mają być zezwalane, czy odrzucane.
- `operations` Zdefiniuj akcje do autoryzowania. `mqtt:connect`, `mqtt:publish` i są obecnie `mqtt:subscribe` trzema obsługiwanymi akcjami.
- `resources` Zdefiniuj obiekt zasad. Może to być temat lub wzorzec tematu zdefiniowany za pomocą symboli wieloznacznych [MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)

Poniżej znajduje się przykład zasad autoryzacji, które jawnie nie zezwalają na łączenie się z klientem "rogue_client", zezwalają klientom usługi Azure IoT na łączenie się i zezwalają na publikowanie w temacie sensor_1 `events/alerts` "sensor_1".

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

- Wymagany jest `$edgeHub` schemat bliźniaczej reprezentacji w wersji 1.2
- Domyślnie wszystkie operacje są odmowa.
- Instrukcje autoryzacji są oceniane w kolejności, w których występują w definicji JSON. Rozpoczyna się od wybrania, a następnie wybrania pierwszej instrukcji zezwalania lub `identities` odmowy, które pasują do żądania. W przypadku konfliktów między instrukcje zezwalania i odmowy, instrukcja odmowy wygrywa.
- W zasadach autoryzacji można użyć kilku zmiennych (na przykład podstawień):

  - `{{iot:identity}}` reprezentuje tożsamość aktualnie połączonego klienta. Na przykład tożsamość urządzenia, na przykład `myDevice` lub tożsamość modułu, na przykład `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` reprezentuje tożsamość aktualnie podłączonego urządzenia. Na przykład tożsamość urządzenia, na przykład lub tożsamość urządzenia, `myDevice` w której działa moduł, taki jak `myEdgeDevice` .
  - `{{iot:module_id}}` reprezentuje tożsamość aktualnie połączonego modułu. Ta zmienna jest pusta dla połączonych urządzeń lub tożsamości modułu, takiej jak `SampleModule` .
  - `{{iot:this_device_id}}` reprezentuje tożsamość urządzenia, IoT Edge na urządzeniu z uruchomionymi zasadami autoryzacji. Na przykład `myIoTEdgeDevice`.

Autoryzacje dla tematów centrum IoT są obsługiwane nieco inaczej niż tematy zdefiniowane przez użytkownika. Oto kluczowe kwestie do zapamiętania:

- Urządzenia lub moduły usługi Azure IoT wymagają reguły jawnej autoryzacji, aby nawiązać połączenie IoT Edge brokerem MQTT centrum. Poniżej przedstawiono domyślne zasady autoryzacji połączeń.
- Urządzenia lub moduły usługi Azure IoT domyślnie mogą uzyskać dostęp do własnych tematów centrum IoT bez żadnej jawnej reguły autoryzacji. Jednak w takim przypadku autoryzacje wynikają z relacji nadrzędny/podrzędny i należy ustawić te relacje. IoT Edge są automatycznie ustawiane jako urządzenia IoT Edge, ale urządzenia muszą być jawnie ustawione jako urządzenia IoT Edge bramy.

Poniżej podano domyślne zasady autoryzacji, których można użyć, aby  umożliwić łączenie się z brokerem wszystkim urządzeniem lub modułom usługi Azure IoT:

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

Teraz, gdy już wiesz, jak nawiązać połączenie z brokerem MQTT usługi IoT Edge, zobaczmy, jak można go użyć do publikowania i subskrybowania komunikatów najpierw w tematach zdefiniowanych przez użytkownika, a na koniec w tematach dotyczących centrum IoT, a na koniec w innym brokerze MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publikowanie i subskrybowanie tematów zdefiniowanych przez użytkownika

W tym artykule użyjesz jednego klienta o nazwie **sub_client** subskrybowania tematu i innego klienta o nazwie **pub_client** który publikuje do tematu. Użyjemy uwierzytelniania za pomocą klucza symetrycznego, ale to samo można wykonać w przypadku uwierzytelniania z podpisem własnym [X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) lub uwierzytelniania z podpisem urzędu [certyfikacji X.509.](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication) [](how-to-authenticate-downstream-device.md#symmetric-key-authentication)

### <a name="create-publisher-and-subscriber-clients"></a>Tworzenie klientów wydawcy i subskrybenta

Utwórz dwa urządzenia IoT w IoT Hub i uzyskaj ich hasła. Korzystając z interfejsu wiersza polecenia platformy Azure z terminalu, można:

1. Utwórz dwa urządzenia IoT w IoT Hub nadrzędne na urządzeniu IoT Edge urządzenia:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. Uzyskaj hasła, generując token SAS:

   - W przypadku urządzenia:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     gdzie 3600 to czas trwania tokenu SAS w sekundach (na przykład 3600 = 1 godzina).

   - W przypadku modułu:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     gdzie 3600 to czas trwania tokenu SAS w sekundach (na przykład 3600 = 1 godzina).

3. Skopiuj token SAS, który jest wartością odpowiadającą kluczowi "sas" z danych wyjściowych. Oto przykładowe dane wyjściowe z powyższego polecenia interfejsu wiersza polecenia platformy Azure:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autoryzowanie klientów wydawcy i subskrybenta

Aby autoryzować wydawcę i subskrybenta, edytuj bliźniacze IoT Edge centrum w IoT Edge, które obejmuje następujące zasady autoryzacji.

>[!NOTE]
>Obecnie wdrożenia zawierające właściwości autoryzacji MQTT można stosować tylko do IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure.

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
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
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

### <a name="symmetric-keys-authentication-without-tls"></a>Uwierzytelnianie kluczy symetrycznych bez protokołu TLS

#### <a name="subscribe"></a>Subskrybuj

Połącz klienta **sub_client** MQTT z brokerem MQTT i zasubskrybuj usługę , uruchamiając następujące polecenie `test_topic` IoT Edge urządzeniu:

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

gdzie `<edge_device_address>`  =  `localhost` w tym przykładzie, ponieważ klient jest uruchomiony na tym samym urządzeniu co IoT Edge.

Należy pamiętać, że w tym pierwszym przykładzie jest używany port 1883 (MQTT) bez użycia TLS. Inny przykład z portem 8883 (MQTTS) z włączonym TLS jest przedstawiony w następnej sekcji.

Klient **sub_client** MQTT jest teraz uruchomiony i oczekuje na komunikaty przychodzące w programie `test_topic` .

#### <a name="publish"></a>Publikowanie

Połącz klienta **pub_client** MQTT z brokerem MQTT i opublikuje komunikat w taki sam sposób jak powyżej, uruchamiając następujące polecenie na urządzeniu IoT Edge z innego `test_topic` terminalu:

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

gdzie `<edge_device_address>`  =  `localhost` w tym przykładzie, ponieważ klient jest uruchomiony na tym samym urządzeniu co IoT Edge.

Wykonanie polecenia oznacza, że **sub_client** MQTT odbiera komunikat "hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Uwierzytelnianie za pomocą kluczy symetrycznych przy użyciu protokołu TLS

Aby włączyć obsługę TLS, należy zmienić port z 1883(MQTT) na 8883(MQTTS), a klienci muszą mieć certyfikat główny brokera MQTT, aby móc zweryfikować łańcuch certyfikatów wysłany przez brokera MQTT. W tym celu należy wykonać kroki opisane w sekcji [Bezpieczne połączenie (TLS).](#secure-connection-tls)

Ponieważ klienci działają na tym samym urządzeniu co broker MQTT w powyższym przykładzie, te same kroki mają zastosowanie do włączenia obsługi TLS przez zmianę numeru portu z 1883 (MQTT) na 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publikowanie i subskrybowanie IoT Hub tematach

Zestawy [SDK urządzeń Azure IoT](https://github.com/Azure/azure-iot-sdks) już umożliwiają klientom wykonywanie operacji IoT Hub, ale nie zezwalają na publikowanie/subskrybowanie tematów zdefiniowanych przez użytkownika. IoT Hub operacje mogą być wykonywane przy użyciu dowolnych klientów MQTT korzystających z semantyki publikowania i subskrybowania, o ile są przestrzegane protokoły pierwotne usługi IoT Hub. Aby zrozumieć, jak działają te protokoły, przejdmy przez szczegółowe informacje.

### <a name="send-telemetry-data-to-iot-hub"></a>Wysyłanie danych telemetrycznych do IoT Hub

Wysyłanie danych telemetrycznych do IoT Hub jest podobne do publikowania w temacie zdefiniowanym przez użytkownika, ale przy użyciu określonego IoT Hub tematu:

- W przypadku urządzenia dane telemetryczne są wysyłane w temacie: `devices/<device_name>/messages/events/`
- W przypadku modułu dane telemetryczne są wysyłane w temacie: `devices/<device_name>/<module_name>/messages/events/`

Ponadto utwórz trasę, taką jak , aby wysyłać dane telemetryczne z brokera `FROM /messages/* INTO $upstream` IoT Edge MQTT do centrum IoT. Aby dowiedzieć się więcej na temat routingu, zobacz [Deklarowanie tras](module-composition.md#declare-routes).

### <a name="get-twin"></a>Uzyskiwanie bliźniaczej reprezentacji

Pobieranie bliźniaczej reprezentacji urządzenia/modułu nie jest typowym wzorcem MQTT. Klient musi wydawać żądanie dotyczące bliźniaczej IoT Hub, która będzie obsługiwać.

Aby odbierać bliźniacze reprezentacji, klient musi zasubskrybować IoT Hub tematu `$iothub/twin/res/#` . Ta nazwa tematu jest dziedziczona z IoT Hub i wszyscy klienci muszą subskrybować ten sam temat. Nie oznacza to, że urządzenia lub moduły otrzymują bliźniacze reprezentacji. IoT Hub i IoT Edge wie, która bliźniacze reprezentacji powinna zostać dostarczona, nawet jeśli wszystkie urządzenia nasłuchują tej samej nazwy tematu.

Po zakończeniu subskrypcji klient musi poprosić o bliźniacze reprezentacji, publikując komunikat w IoT Hub określonym temacie, gdzie jest `$iothub/twin/GET/?rid=<request_id>/#`  `<request_id>` dowolnym identyfikatorem. Następnie centrum IoT hub wyśle odpowiedź z żądanymi danymi w temacie `$iothub/twin/res/200/?rid=<request_id>` , które klient subskrybuje. W ten sposób klient może sparować swoje żądania z odpowiedziami.

### <a name="receive-twin-patches"></a>Otrzymywanie poprawek bliźniaczych reprezentacji

Aby otrzymywać poprawki bliźniaczych reprezentacji, klient musi zasubskrybować specjalny temat usługi IoTHub. `$iothub/twin/PATCH/properties/desired/#` Po zakończeniu subskrypcji klient otrzymuje poprawki bliźniaczej reprezentacji IoT Hub w tym temacie.

### <a name="receive-direct-methods"></a>Odbieranie metod bezpośrednich

Odbieranie metody bezpośredniej jest podobne do odbierania pełnych bliźniaczych reprezentacji z dodaniem, że klient musi potwierdzić, że odebrał wywołanie. Najpierw klient subskrybuje specjalny temat usługi IoT `$iothub/methods/POST/#` Hub. Następnie po otrzymaniu metody bezpośredniej w tym temacie klient musi wyodrębnić identyfikator żądania z podtematu, w którym została odebrana metoda bezpośrednia, a na koniec opublikować komunikat potwierdzający w specjalnym temacie centrum `rid` `$iothub/methods/res/200/<request_id>` IoT.

### <a name="send-direct-methods"></a>Wysyłanie metod bezpośrednich

Wysyłanie metody bezpośredniej jest wywołaniem HTTP i dlatego nie przechodzi przez brokera MQTT. Aby wysłać metodę bezpośrednią do centrum IoT Hub, zobacz Understand and invoke direct methods (Opis metod bezpośrednich i [wywoływanie ich).](../iot-hub/iot-hub-devguide-direct-methods.md) Aby wysłać metodę bezpośrednią lokalnie do innego modułu, zobacz ten przykład wywołania metody bezpośredniej zestawu SDK języka C# usługi [Azure IoT.](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publikowanie i subskrybowanie między brokerami MQTT

Aby połączyć dwóch brokerów MQTT, IoT Edge koncentrator zawiera mostek MQTT. Mostek MQTT jest często używany do łączenia brokera MQTT uruchomionego z innym brokerem MQTT. Tylko podzbiór ruchu lokalnego jest zwykle wypychany do innego brokera.

> [!NOTE]
> Mostek IoT Edge można obecnie używać tylko między zagnieżdżoną IoT Edge urządzeniami. Nie można jej używać do wysyłania danych do centrum IoT, ponieważ centrum IoT nie jest w pełni funkcjonalnym brokerem MQTT. Aby dowiedzieć się więcej na temat obsługi funkcji brokera MQTT centrum IoT, zobacz Komunikacja z centrum [IoT przy użyciu protokołu MQTT.](../iot-hub/iot-hub-mqtt-support.md) Aby dowiedzieć się więcej na temat zagnieżdżania IoT Edge, zobacz Connect a downstream IoT Edge device to an Azure IoT Edge gateway (Łączenie urządzenia podrzędnego [z bramą Azure IoT Edge podrzędnej).](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

W konfiguracji zagnieżdżonych mostek MQTT centrum IoT Edge działa jako klient nadrzędnego brokera MQTT, dlatego reguły autoryzacji muszą być ustawione w nadrzędnej witrynie EdgeHub, aby umożliwić podrzędnej aplikacji EdgeHub publikowanie i subskrybowanie określonych tematów zdefiniowanych przez użytkownika, dla których skonfigurowano mostek.

Mostek IoT Edge MQTT jest konfigurowany za pośrednictwem struktury JSON, która jest wysyłana do centrum IoT Edge za pośrednictwem bliźniaczej reprezentacji. Edytuj bliźniacze IoT Edge centrum danych, aby skonfigurować jego mostek MQTT.

> [!NOTE]
> W publicznej wersji zapoznawczej tylko interfejs wiersza polecenia platformy Azure obsługuje wdrożenia zawierające konfiguracje mostka MQTT. Obecnie Azure Portal edycji bliźniaczej reprezentacji centrum IoT Edge i konfiguracji mostka MQTT.

Mostek MQTT można skonfigurować tak, aby łączył brokera MQTT IoT Edge hub z wieloma zewnętrznymi brokerami. Dla każdego brokera zewnętrznego wymagane są następujące ustawienia:

- `endpoint` to adres zdalnego brokera MQTT, z który ma nawiązać połączenie. Obecnie obsługiwane IoT Edge nadrzędne są definiowane przez zmienną `$upstream` .
- `settings` definiuje tematy, które mają być mostkowe dla punktu końcowego. Istnieje wiele ustawień dla każdego punktu końcowego, a do jego skonfigurowania są używane następujące wartości:
  - `direction`: `in` subskrybowanie tematów zdalnego brokera lub publikowanie `out` w tematach zdalnego brokera
  - `topic`: wzorzec tematu podstawowego do dopasowania. Do zdefiniowania tego wzorca można użyć symboli wieloznacznych [MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) Do tego wzorca tematu można zastosować różne prefiksy na lokalnym brokerze i zdalnym brokerze.
  - `outPrefix`: prefiks stosowany do wzorca `topic` w zdalnym brokerze.
  - `inPrefix`: prefiks stosowany do wzorca `topic` w brokerze lokalnym.

Poniżej znajduje się przykład konfiguracji mostka MQTT IoT Edge, która ponownie wysyła wszystkie komunikaty odebrane w tematach nadrzędnego urządzenia IoT Edge do podrzędnego urządzenia IoT Edge w tych samych tematach i ponownie wysyła wszystkie komunikaty dotyczące tematów podrzędnego urządzenia IoT Edge do nadrzędnego urządzenia IoT Edge w `alerts/#` `/local/telemetry/#` tematach `/remote/messages/#` .

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
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
Inne uwagi dotyczące mostka MQTT IoT Edge centrum danych:
- Protokół MQTT będzie automatycznie używany jako protokół nadrzędny, gdy jest używany broker MQTT, IoT Edge jest używany w konfiguracji zagnieżdżone, na przykład z `parent_hostname` określonym. Aby dowiedzieć się więcej na temat protokołów nadrzędnych, zobacz [Komunikacja w chmurze](iot-edge-runtime.md#cloud-communication). Aby dowiedzieć się więcej na temat konfiguracji zagnieżdżonych, zobacz Connect a downstream IoT Edge device to an Azure IoT Edge gateway (Łączenie urządzenia podrzędnego z [bramą Azure IoT Edge podrzędnego).](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

## <a name="next-steps"></a>Następne kroki

[Opis centrum IoT Edge danych](iot-edge-runtime.md#iot-edge-hub)
