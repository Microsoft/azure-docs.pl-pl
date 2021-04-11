---
title: Co to są szablony urządzeń na platformie Azure IoT Central | Microsoft Docs
description: Szablony urządzeń IoT Central platformy Azure pozwalają określić zachowanie urządzeń podłączonych do aplikacji. Szablon urządzenia określa dane telemetryczne, właściwości i polecenia, które urządzenie musi zaimplementować. Szablon urządzenia definiuje również interfejs użytkownika dla urządzenia w IoT Central, takich jak formularze i pulpity nawigacyjne, które są stosowane przez operatora.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 2396768d87b93c4df16b6de78d03faf1d8d1cc2b
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492005"
---
# <a name="what-are-device-templates"></a>Co to są szablony urządzeń?

_Ten artykuł ma zastosowanie do deweloperów urządzeń i konstruktorów rozwiązań._

Szablon urządzenia w usłudze Azure IoT Central jest planem, który definiuje cechy i zachowania typu urządzenia, które nawiązuje połączenie z Twoją aplikacją. Na przykład szablon urządzenia definiuje dane telemetryczne wysyłane przez urządzenie, dzięki czemu IoT Central może tworzyć wizualizacje, które używają prawidłowych jednostek i typów danych.

Konstruktor rozwiązań dodaje szablony urządzeń do aplikacji IoT Central. Deweloper urządzenia zapisuje kod urządzenia, który implementuje zachowania zdefiniowane w szablonie urządzenia.

Szablon urządzenia zawiera następujące sekcje:

- _Model urządzenia_. Ta część szablonu urządzenia określa, jak urządzenie współdziała z Twoją aplikacją. Deweloper urządzenia implementuje zachowania zdefiniowane w modelu.
    - _Składnik domyślny_. Każdy model urządzenia ma składnik domyślny. Domyślny interfejs składnika zawiera opis możliwości specyficznych dla modelu urządzenia.
    - _Składniki_. Model urządzenia może obejmować składniki oprócz domyślnego składnika do opisywania możliwości urządzeń. Każdy składnik ma interfejs, który opisuje możliwości składnika. Interfejsy składników mogą być ponownie używane w innych modelach urządzeń. Przykładowo kilka modeli urządzeń telefonicznych może korzystać z tego samego interfejsu aparatu.
    - _Dziedziczone interfejsy_. Model urządzenia zawiera jeden lub więcej interfejsów, które zwiększają możliwości składnika domyślnego.
- _Właściwości chmury_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania określenie wszelkich metadanych urządzeń do przechowywania. Właściwości chmury nigdy nie są synchronizowane z urządzeniami i istnieją tylko w aplikacji. Właściwości chmury nie wpływają na kod, który Projektant urządzenia zapisuje w celu zaimplementowania modelu urządzenia.
- _Dostosowania_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania zastąpienie niektórych definicji w modelu urządzenia. Dostosowania są przydatne, jeśli deweloper rozwiązania chce udoskonalić, jak aplikacja obsługuje wartość, na przykład zmieniając nazwę wyświetlaną właściwości lub kolor używany do wyświetlania wartości telemetrii. Dostosowania nie wpływają na kod, który Projektant urządzenia zapisuje w celu wdrożenia modelu urządzenia.
- _Widoki_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania Definiowanie wizualizacji do wyświetlania danych z urządzenia oraz formularzy służących do zarządzania urządzeniami i ich kontrolowania. Widoki używają modelu urządzenia, właściwości chmury i dostosowań. Widoki nie wpływają na kod, który Projektant urządzenia zapisuje w celu zaimplementowania modelu urządzenia.

## <a name="device-models"></a>Modele urządzeń

Model urządzenia definiuje, jak urządzenie współdziała z aplikacją IoT Central. Deweloper urządzenia musi upewnić się, że na urządzeniu zaimplementowano zachowania zdefiniowane w modelu urządzenia, tak aby IoT Central mógł monitorować urządzenie i zarządzać nim. Model urządzenia składa się z jednego lub kilku _interfejsów_, a każdy interfejs może definiować zbiór typów _telemetrii_ , _Właściwości urządzenia_ i _poleceń_. Deweloper rozwiązania może zaimportować plik JSON definiujący model urządzenia do szablonu urządzenia lub użyć internetowego interfejsu użytkownika w IoT Central, aby utworzyć lub edytować model urządzenia. Zmiany w modelu urządzenia za pomocą interfejsu użytkownika sieci Web wymagają, [Aby szablon urządzenia był w wersji](./howto-version-device-template.md).

Deweloper rozwiązania może również wyeksportować plik JSON, który zawiera model urządzenia. Deweloper urządzenia może użyć tego dokumentu JSON, aby zrozumieć, w jaki sposób urządzenie ma komunikować się z aplikacją IoT Central.

Plik JSON, który definiuje model urządzenia, używa [języka Digital bliźniaczy Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central oczekuje, że plik JSON zawiera model urządzenia z interfejsami zdefiniowanymi w tekście, a nie w oddzielnych plikach. Aby dowiedzieć się więcej, zobacz artykuł [IoT Plug and Play Modeling Guide](../../iot-pnp/concepts-modeling-guide.md).

Typowe urządzenie IoT składa się z:

- Części niestandardowe, które są elementami, które sprawiają, że urządzenie jest unikatowe.
- Standardowe części, które są wspólne dla wszystkich urządzeń.

Te części nazywają się _interfejsami_ w modelu urządzenia. Interfejsy definiują szczegóły każdej części implementującej Twoje urządzenie. Interfejsy są wielokrotnego użytku między modelami urządzeń. W DTDL, składnik odwołuje się do innego interfejsu, który może być zdefiniowany w osobnym pliku DTDL lub w oddzielnej sekcji pliku.

Poniższy przykład przedstawia zarys modelu urządzenia dla [urządzenia kontrolera temperatury](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json). Składnik domyślny zawiera definicje dla `workingSet` , `serialNumber` i `reboot` . Model urządzenia obejmuje również dwa `thermostat` składniki i `deviceInformation` składnik. Zawartość trzech składników została usunięta ze względu na zwięzłości:

```json
[
  {
    "@context": [
      "dtmi:iotcentral:context;2",
      "dtmi:dtdl:context;2"
    ],
    "@id": "dtmi:com:example:TemperatureController;2",
    "@type": "Interface",
    "contents": [
      {
        "@type": [
          "Telemetry",
          "DataSize"
        ],
        "description": {
          "en": "Current working set of the device memory in KiB."
        },
        "displayName": {
          "en": "Working Set"
        },
        "name": "workingSet",
        "schema": "double",
        "unit": "kibibit"
      },
      {
        "@type": "Property",
        "displayName": {
          "en": "Serial Number"
        },
        "name": "serialNumber",
        "schema": "string",
        "writable": false
      },
      {
        "@type": "Command",
        "commandType": "synchronous",
        "description": {
          "en": "Reboots the device after waiting the number of seconds specified."
        },
        "displayName": {
          "en": "Reboot"
        },
        "name": "reboot",
        "request": {
          "@type": "CommandPayload",
          "description": {
            "en": "Number of seconds to wait before rebooting the device."
          },
          "displayName": {
            "en": "Delay"
          },
          "name": "delay",
          "schema": "integer"
        }
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat1"
        },
        "name": "thermostat1",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat2"
        },
        "name": "thermostat2",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "DeviceInfo"
        },
        "name": "deviceInformation",
        "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1"
      }
    ],
    "displayName": {
      "en": "Temperature Controller"
    }
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;2",
    "@type": "Interface",
    "displayName": "Thermostat",
    "description": "Reports current temperature and provides desired temperature control.",
    "contents": [
      ...
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:azure:DeviceManagement:DeviceInformation;1",
    "@type": "Interface",
    "displayName": "Device Information",
    "contents": [
      ...
    ]
  }
]
```

Interfejs ma niektóre wymagane pola:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest interfejsem.
- `@context`: określa wersję DTDL użytą dla interfejsu.
- `contents`: Wyświetla właściwości, dane telemetryczne i polecenia wchodzące w skład Twojego urządzenia. Możliwości można definiować w wielu interfejsach.

Istnieją opcjonalne pola, za pomocą których można dodać więcej szczegółów do modelu możliwości, takie jak nazwa wyświetlana i opis.

Każdy wpis na liście interfejsów w sekcji Implements ma:

- `name`: Nazwa programowania interfejsu.
- `schema`: interfejs implementujący model możliwości.

## <a name="interfaces"></a>Interfejsy

DTDL umożliwia opisywanie możliwości urządzenia. Powiązane funkcje są pogrupowane w interfejsy. Interfejsy opisują właściwości, dane telemetryczne i polecenia, które są implementowane przez część urządzenia:

- `Properties`. Właściwości to pola danych, które reprezentują stan urządzenia. Użyj właściwości, aby reprezentować stan trwały urządzenia, taki jak stan włączony pompy chłodzącej. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania układowego urządzenia. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Tylko urządzenia mogą aktualizować wartość właściwości tylko do odczytu. Operator może ustawić wartość właściwości zapisywalnej do wysłania do urządzenia.
- `Telemetry`. Pola telemetrii przedstawiają pomiary z czujników. Za każdym razem, gdy urządzenie przyjmuje pomiar czujnika, należy wysłać zdarzenie telemetrii zawierające dane czujnika.
- `Commands`. Polecenia reprezentują metody, które użytkownicy urządzenia mogą wykonywać na urządzeniu. Na przykład polecenie Reset lub polecenie umożliwiające włączenie lub wyłączenie wentylatora.

W poniższym przykładzie przedstawiono definicję interfejsu termostatu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Ten przykład przedstawia dwie właściwości (jeden tylko do odczytu i jeden zapisywalny), typ telemetrii i polecenie. Minimalny opis pola ma:

- `@type` Aby określić typ możliwości: `Telemetry` , `Property` , lub `Command` .  W niektórych przypadkach typ zawiera typ semantyczny, aby umożliwić IoT Central w celu wprowadzenia pewnych założeń dotyczących obsługi wartości.
- `name` wartość telemetrii.
- `schema` Aby określić typ danych telemetrii lub właściwości. Ta wartość może być typem pierwotnym, takim jak Double, Integer, Boolean lub String. Obsługiwane są również złożone typy i mapy obiektów.

Opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

## <a name="properties"></a>Właściwości

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu oznaczają, że urządzenie zgłasza aktualizacje wartości właściwości do aplikacji IoT Central. Aplikacja IoT Central nie może ustawić wartości właściwości tylko do odczytu.

Można także oznaczyć właściwość jako zapisywalną w interfejsie. Urządzenie może odebrać aktualizację właściwości z możliwością zapisu z aplikacji IoT Central, a także zgłaszać aktualizacje wartości właściwości do aplikacji.

Urządzenia nie muszą być połączone, aby ustawić wartości właściwości. Zaktualizowane wartości są transferowane, gdy urządzenie dalej nawiązuje połączenie z aplikacją. To zachowanie dotyczy zarówno właściwości tylko do odczytu, jak i do zapisu.

Nie używaj właściwości, aby wysyłać dane telemetryczne z urządzenia. Na przykład właściwość ReadOnly, taka jak `temperatureSetting=80` powinna oznaczać, że temperatura urządzenia została ustawiona na 80, a urządzenie próbuje uzyskać dostęp do tej temperatury lub pozostać w tej temperaturze.

W przypadku właściwości zapisywalnych aplikacja urządzenia zwraca kod stanu żądanego stanu, wersję i opis, aby wskazać, czy został on odebrany i zastosował wartość właściwości.

## <a name="telemetry"></a>Telemetria

IoT Central umożliwia wyświetlanie danych telemetrycznych na pulpitach nawigacyjnych i na wykresach oraz stosowanie reguł do wyzwalania akcji po osiągnięciu progów. IoT Central używa informacji w modelu urządzenia, takich jak typy danych, jednostki i nazwy wyświetlane, aby określić sposób wyświetlania wartości telemetrii.

Za pomocą funkcji eksportu danych IoT Central można przesyłać dane telemetryczne do innych miejsc docelowych, takich jak magazyn lub Event Hubs.

## <a name="commands"></a>Polecenia

Polecenie musi być wykonywane w ciągu 30 sekund domyślnie, a urządzenie musi być połączone po nadejściu polecenia. Jeśli urządzenie jest w czasie odpowiedzi lub urządzenie nie jest połączone, polecenie kończy się niepowodzeniem.

Polecenia mogą zawierać parametry żądania i zwracać odpowiedź.

### <a name="offline-commands"></a>Polecenia w trybie offline

Możesz wybrać polecenia kolejki, jeśli urządzenie jest obecnie w trybie offline, włączając opcję **kolejki w trybie offline** dla polecenia w szablonie urządzenia.

Polecenia w trybie offline to jednokierunkowe powiadomienia do urządzenia z rozwiązania. Polecenia w trybie offline mogą zawierać parametry żądania, ale nie zwracają odpowiedzi.

> [!NOTE]
> Ta opcja jest dostępna tylko w interfejsie użytkownika sieci Web IoT Central. To ustawienie nie jest uwzględniane w przypadku eksportowania modelu lub interfejsu z szablonu urządzenia.

## <a name="cloud-properties"></a>Właściwości chmury

Właściwości chmury są częścią szablonu urządzenia, ale nie są częścią modelu urządzenia. Właściwości chmury umożliwiają deweloperom rozwiązania Określanie metadanych urządzeń, które mają być przechowywane w aplikacji IoT Central. Właściwości chmury nie wpływają na kod, który Projektant urządzenia zapisuje w celu zaimplementowania modelu urządzenia.

Deweloper rozwiązań może dodawać właściwości chmury do pulpitów nawigacyjnych i widoków wraz z właściwościami urządzenia, aby umożliwić operatorowi zarządzanie urządzeniami podłączonymi do aplikacji. Deweloper rozwiązania może również użyć właściwości chmury jako części definicji reguły, aby umożliwić edytowanie wartości progowej przez operatora.

## <a name="customizations"></a>Dostosowania

Dostosowania są częścią szablonu urządzenia, ale nie są częścią modelu urządzenia. Dostosowania pozwalają deweloperom rozwiązania ulepszyć lub zastąpić niektóre definicje w modelu urządzenia. Na przykład deweloper rozwiązania może zmienić nazwę wyświetlaną dla typu telemetrii lub właściwości. Deweloper rozwiązania może również użyć dostosowań, aby dodać sprawdzanie poprawności, takie jak minimalna lub maksymalna długość właściwości urządzenia typu String.

Dostosowania mogą mieć wpływ na kod, który Projektant urządzenia zapisuje w celu wdrożenia modelu urządzenia. Na przykład dostosowanie może ustawić minimalną i maksymalną długość ciągu lub minimalną i maksymalną wartość liczbową dla telemetrii.

## <a name="views"></a>Widoki

Deweloper rozwiązania tworzy widoki, które umożliwiają operatorom monitorowanie podłączonych urządzeń i zarządzanie nimi. Widoki są częścią szablonu urządzenia, więc widok jest skojarzony z określonym typem urządzenia. Widok może obejmować:

- Wykresy do kreślenia danych telemetrycznych.
- Kafelki, aby wyświetlić właściwości urządzenia tylko do odczytu.
- Kafelki, aby umożliwić operatorowi Edytowanie właściwości zapisywalnego urządzenia.
- Kafelki, aby umożliwić operatorowi Edytowanie właściwości chmury.
- Kafelki, które umożliwiają wywoływanie poleceń operatora, w tym poleceń, które oczekują ładunku.
- Kafelki do wyświetlania etykiet, obrazów lub tekstu promocji.

Dane telemetryczne, właściwości i polecenia, które można dodać do widoku, są określane przez model urządzenia, właściwości chmury i dostosowania w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

Jako deweloper dla urządzeń już wiesz już, jak można zapoznać się z szablonami urządzeń, sugerowane następne kroki polegają na odczytaniu danych [telemetrycznych, właściwości i poleceń](./concepts-telemetry-properties-commands.md) , aby dowiedzieć się więcej o tym, które urządzenia wymieniają IoT Central.

Jako deweloper rozwiązań zalecanym następnym krokiem jest zapoznanie się z artykułem [Definiowanie nowego typu urządzenia IoT w aplikacji IoT Central platformy Azure](./howto-set-up-template.md) , aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia.
