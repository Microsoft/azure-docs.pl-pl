---
title: Mostek Plug and Play IoT | Microsoft Docs
description: Zapoznaj się z mostkiem Plug and Play IoT i sposobami korzystania z niego do łączenia istniejących urządzeń podłączonych do bramy systemu Windows lub Linux jako urządzeń Plug and Play IoT.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 05b9df2c5083d24ae6a78212bdd29cd1c740e1d1
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746496"
---
# <a name="iot-plug-and-play-bridge"></a>Mostek IoT Plug and Play

Mostek Plug and Play IoT to aplikacja Open Source służąca do łączenia istniejących urządzeń podłączonych do bramy systemu Windows lub Linux jako urządzeń Plug and Play IoT. Po zainstalowaniu i skonfigurowaniu aplikacji na komputerze z systemem Windows lub Linux można użyć jej do nawiązania połączenia z urządzeniami podłączonymi do centrum IoT Hub. Za pomocą mostka można mapować interfejsy Plug and Play IoT na telemetrię, które dołączone urządzenia wysyłają, pracują z właściwościami urządzenia i wywoływać polecenia.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Po lewej stronie istnieje kilka istniejących czujników dołączonych (zarówno przewodowych, jak i bezprzewodowych) do komputera z systemem Windows lub Linux zawierającym mostek Plug and Play IoT. Mostek Plug and Play IoT łączy się następnie z Centrum IoT po prawej stronie":::

Program IoT Plug and Play Bridge można wdrożyć jako autonomiczny plik wykonywalny na dowolnym urządzeniu IoT, komputerze stacjonarnym, serwerze lub bramie z systemem Windows 10 lub Linux. Można go również skompilować do kodu aplikacji. Prosty plik JSON konfiguracji informuje mostek Plug and Play IoT, które dołączone urządzenia/urządzenia peryferyjne powinny być uwidocznione na platformie Azure.

## <a name="supported-protocols-and-sensors"></a>Obsługiwane protokoły i czujniki

Program IoT Plug and Play Bridge domyślnie obsługuje następujące typy urządzeń peryferyjnych z linkami do dokumentacji karty:

|Urządzenia|Windows|Linux|
|---------|---------|---------|
|[Adapter czujnika Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) łączy wykryte czujniki z włączonymi technologiami Bluetooth Low Energy (beli).       |Tak|Nie|
|[Karta aparatu fotograficznego](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) łączy kamery na urządzeniu z systemem Windows 10.               |Tak|Nie|
|[Adapter Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) łączy czujniki na urządzeniu Modbus.              |Tak|Tak|
|[Karta MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) łączy urządzenia korzystające z brokera MQTT.                  |Tak|Tak|
|[Karta SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) łączy urządzenia, które komunikują się za pośrednictwem połączenia szeregowego.               |Tak|Tak|
|Urządzenia [peryferyjne USB z systemem Windows](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) używają listy klas interfejsów urządzeń obsługiwanych przez adapter do łączenia urządzeń z określonym identyfikatorem sprzętu.  |Tak|Nie dotyczy|

Aby dowiedzieć się, jak zwiększyć możliwości mostka Plug and Play IoT w celu obsługi dodatkowych protokołów urządzeń, zobacz sekcję [zwiększanie poziomu mostka Plug and Play IoT](howto-author-pnp-bridge-adapter.md). Aby dowiedzieć się, jak skompilować i wdrożyć mostek Plug and Play IoT, zobacz [Kompilowanie i wdrażanie mostka Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Architektura mostka Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Po lewej stronie znajduje się kilka pól wskazujących różne urządzenia peryferyjne podłączone do komputera z systemem Windows lub Linux zawierającego mostek Plug and Play IoT. W górnej części pole z oznaczeniem punktów konfiguracji do mostka. Następnie mostek łączy się z Centrum IoT Hub po prawej stronie diagramu.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Karty mostków Plug and Play IoT

Program IoT Plug and Play Bridge obsługuje zestaw kart usługi IoT Plug and Play dla różnych typów urządzeń. *Manifest karty* statycznie definiuje karty do mostka.

Menedżer adapterów mostka używa manifestu, aby identyfikować i wywoływać funkcje adaptera. Menedżer adapterów wywołuje tylko funkcję Create na kartach mostkowych, które są wymagane przez składniki interfejsu wymienione w pliku konfiguracji. Dla każdego składnika usługi IoT Plug and Play tworzone jest wystąpienie karty.

Adapter mostka tworzy i uzyskuje dojście do interfejsu z podpisem cyfrowym. Adapter używa tego uchwytu, aby powiązać funkcje urządzenia z dwuosiową cyfrą.

Korzystając z informacji w pliku konfiguracyjnym, karta mostka używa następujących technik, aby zapewnić pełną komunikację między urządzeniami cyfrowymi i cyfrowymi za pośrednictwem mostka:

- Bezpośrednio ustanawia kanał komunikacyjny.
- Tworzy obserwatora urządzenia do oczekiwania na udostępnienie kanału komunikacyjnego.

### <a name="configuration-file"></a>Plik konfiguracji

Mostek Plug and Play IoT korzysta z pliku konfiguracji opartego na notacji JSON, który określa:

- Jak nawiązać połączenie z usługą IoT Hub lub IoT Central aplikacji: opcje obejmują parametry połączenia, parametry uwierzytelniania lub usługę aprowizacji urządzeń (DPS).
- Lokalizacja modeli możliwości Plug and Play IoT, z których korzysta mostek. Model definiuje możliwości urządzenia IoT Plug and Play i jest statyczny i niezmienny.
- Lista składników interfejsu usługi IoT Plug and Play oraz następujące informacje dotyczące poszczególnych składników:
- Identyfikator interfejsu i nazwa składnika.
- Karta mostku wymagana do współpracy ze składnikiem.
- Informacje o urządzeniu, które karta mostkowa musi nawiązać komunikację z urządzeniem. Na przykład identyfikator sprzętu lub określone informacje dla karty, interfejsu lub protokołu.
- Opcjonalny podtyp karty mostka lub Konfiguracja interfejsu, jeśli karta obsługuje wiele typów komunikacji z podobnymi urządzeniami. W przykładzie pokazano, jak można skonfigurować składnik czujnika Bluetooth:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Opcjonalna lista globalnych parametrów karty mostka. Na przykład adapter mostku czujnika Bluetooth ma słownik obsługiwanych konfiguracji. Składnik interfejsu, który wymaga karty czujnika Bluetooth, może wybrać jedną z następujących konfiguracji `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Pobierz mostek Plug and Play IoT

Możesz pobrać wstępnie zbudowaną wersję mostka z obsługiwanymi kartami w usłudze [IoT Plug and Play w wersji Bridge](https://github.com/Azure/iot-plug-and-play-bridge/releases) i rozwinąć listę zasobów dla najnowszej wersji. Pobierz najnowszą wersję aplikacji dla danego systemu operacyjnego.

Możesz również pobrać i wyświetlić kod źródłowy usługi [IoT Plug and Play Bridge w serwisie GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz Przegląd architektury usługi IoT Plug and Play Bridge, następne kroki zawierają informacje na temat:

- [Jak podłączyć przykład IoT Plug and Play Bridge działającego w systemie Linux lub Windows do IoT Hub](./howto-use-iot-pnp-bridge.md)
- [Kompilowanie i wdrażanie mostka Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md)
- [Poszerzenie mostka Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md)
- [Mostek Plug and Play IoT w serwisie GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
