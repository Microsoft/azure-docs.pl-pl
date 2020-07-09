---
title: Korzystanie z urządzenia usługi IoT Plug and Play w wersji zapoznawczej w ramach rozwiązania Azure IoT | Microsoft Docs
description: Deweloperzy rozwiązań dowiesz się, jak używać zestawu SDK usługi do współpracy z urządzeniami Plug and Play IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159221"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Łączenie się z urządzeniem Plug and Play IoT w wersji zapoznawczej i korzystanie z niego

W tym przewodniku opisano sposób korzystania z przykładów w zestawie SDK usługi Node Service, które pokazują, jak rozwiązanie IoT może współdziałać z urządzeniami w wersji zapoznawczej IoT Plug and Play.

Jeśli nie zostało to jeszcze zrobione, [Połącz urządzenie IoT Plug and Play z rozwiązaniem](quickstart-connect-pnp-device-solution-node.md) przewodnika Szybki Start. Przewodnik Szybki Start przedstawia sposób pobierania i instalowania zestawu SDK oraz uruchamiania niektórych przykładów.

Przed uruchomieniem przykładów usługi Otwórz nowy terminal, przejdź do folderu głównego sklonowanego repozytorium, przejdź do folderu **digitaltwins/szybkiego startu/usługi** , a następnie uruchom następujące polecenie, aby zainstalować zależności:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Uruchamianie przykładów usługi

Poniższe przykłady umożliwiają zapoznanie się z możliwościami usługi Node.js Service SDK. Upewnij się, że `IOTHUB_CONNECTION_STRING` zmienna środowiskowa jest ustawiona w używanej powłoce:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Pobierz dwuosiową cyfrę i Utwórz listę interfejsów

**get_digital_twin.js** pobiera cyfrowe sznurki skojarzone z urządzeniem i drukuje jego składnik w wierszu polecenia. Nie wymaga to pomyślnego uruchomienia przykładowego urządzenia.

**get_digital_twin_interface_instance.js** pobiera jedno wystąpienie interfejsu dwuosiowego skojarzone z urządzeniem i drukuje je w wierszu polecenia. Nie wymaga to uruchomienia próbki urządzenia.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Pobieranie i Ustawianie właściwości przy użyciu zestawu SDK usługi Node

**update_digital_twin.js** aktualizuje zapisywalną Właściwość urządzenia Digital bliźniaczy przy użyciu pełnej poprawki. Jeśli chcesz, możesz zaktualizować wiele właściwości w wielu interfejsach. Aby pomyślnie zadziałała, próbka urządzenia musi być uruchomiona w tym samym czasie. Pomyślna informacja o tym, że przykład urządzenia drukuje coś o aktualizowaniu właściwości, przykładowa usługa drukuje zaktualizowaną dwuosiową cyfrę w terminalu.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Wyślij polecenie i Pobierz odpowiedź przy użyciu zestawu SDK usługi Node Service

**invoke_command.js** wywołuje synchroniczne polecenie na cyfrowym bliźniaczym urządzeniu. Aby pomyślnie zadziałała, próbka urządzenia musi być uruchomiona w tym samym czasie. Pomyślnym wyglądem urządzenia jest wydrukowanie czegoś o potwierdzenie polecenia, a klient usługi drukuje wynik polecenia w terminalu.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Nawiązywanie połączenia z repozytorium publicznym i pobieranie definicji modelu przy użyciu zestawu SDK usługi Node Service

Korzystając z tych samych instrukcji jak w przypadku przykładów usług i urządzeń, należy ustawić następującą zmienną środowiskową:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Te parametry połączenia można znaleźć w [portalu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na karcie **Parametry połączenia** dla **repozytorium firmy**.

Parametry połączenia wyglądają podobnie jak w poniższym przykładzie:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Po ustawieniu tych czterech zmiennych środowiskowych Uruchom próbkę tak samo jak w przypadku innych przykładów:

```cmd/sh
node model_repo.js
```

Ten przykład pobiera interfejs **ModelDiscovery** i drukuje ten model w terminalu.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Uruchamianie zapytań w IoT Hub na podstawie modeli możliwości i interfejsów

Język zapytań IoT Hub obsługuje `HAS_INTERFACE` i `HAS_CAPABILITYMODEL` jak pokazano w następujących przykładach:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Tworzenie tras Digital bliźniaczy

Twoje rozwiązanie może odbierać powiadomienia o zdarzeniach zmiany cyfrowych sznurów. Aby subskrybować te powiadomienia, użyj [funkcji routingu IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) , aby wysyłać powiadomienia do punktu końcowego, takiego jak BLOB storage, Event Hubs lub kolejki Service Bus.

Aby utworzyć trasę Digital bliźniaczy:

1. W Azure Portal przejdź do zasobu IoT Hub.
1. Wybierz pozycję **Routing komunikatów**.
1. Na karcie **trasy** wybierz pozycję **Dodaj**.
1. Wprowadź wartość w polu **Nazwa** , a następnie wybierz **punkt końcowy**. Jeśli nie skonfigurowano punktu końcowego, wybierz pozycję **Dodaj punkt końcowy**.
1. Z listy rozwijanej **Źródło danych** wybierz pozycję **wielocyfrowe zdarzenia zmiany**.
1. Wybierz pozycję **Zapisz**.

Poniższy kod JSON przedstawia przykład wielocyfrowego zdarzenia zmiany:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już rozwiązania usług, które współdziałają z urządzeniami Plug and Play IoT, sugerowanym następnym krokiem jest zapoznanie się z [odnajdywaniem modeli](concepts-model-discovery.md).
