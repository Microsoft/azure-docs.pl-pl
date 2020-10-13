---
title: Co to są szablony urządzeń na platformie Azure IoT Central | Microsoft Docs
description: Szablony urządzeń IoT Central platformy Azure pozwalają określić zachowanie urządzeń podłączonych do aplikacji. Szablon urządzenia określa dane telemetryczne, właściwości i polecenia, które urządzenie musi zaimplementować. Szablon urządzenia definiuje również interfejs użytkownika dla urządzenia w IoT Central, takich jak formularze i pulpity nawigacyjne, które są stosowane przez operatora.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91813172"
---
# <a name="what-are-device-templates"></a>Co to są szablony urządzeń?

_Ten artykuł ma zastosowanie do deweloperów urządzeń i konstruktorów rozwiązań._

Szablon urządzenia w usłudze Azure IoT Central jest planem, który definiuje cechy i zachowania typu urządzenia, które nawiązuje połączenie z Twoją aplikacją. Na przykład szablon urządzenia definiuje dane telemetryczne wysyłane przez urządzenie, dzięki czemu IoT Central może tworzyć wizualizacje, które używają prawidłowych jednostek i typów danych.

Konstruktor rozwiązań dodaje szablony urządzeń do aplikacji IoT Central. Deweloper urządzenia zapisuje kod urządzenia, który implementuje zachowania zdefiniowane w szablonie urządzenia.

Szablon urządzenia zawiera następujące sekcje:

- _Model możliwości urządzenia (DCM)_. Ta część szablonu urządzenia określa, jak urządzenie współdziała z Twoją aplikacją. Deweloper urządzenia implementuje zachowania zdefiniowane w DCM.
    - _Interfejsy_. DCM zawiera jeden lub więcej interfejsów, które definiują dane telemetryczne, właściwości i polecenia, które urządzenie musi zaimplementować.
- _Właściwości chmury_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania określenie wszelkich metadanych urządzeń do przechowywania. Właściwości chmury nigdy nie są synchronizowane z urządzeniami i istnieją tylko w aplikacji. Właściwości chmury nie wpływają na kod, który Projektant urządzenia zapisuje w celu wdrożenia DCM.
- _Dostosowania_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania zastąpienie niektórych definicji w DCM. Dostosowania są przydatne, jeśli deweloper rozwiązania chce udoskonalić, jak aplikacja obsługuje wartość, na przykład zmieniając nazwę wyświetlaną właściwości lub kolor używany do wyświetlania wartości telemetrii. Dostosowania nie wpływają na kod, który Projektant urządzenia zapisuje w celu wdrożenia DCM.
- _Widoki_. Ta część szablonu urządzenia umożliwia deweloperowi rozwiązania Definiowanie wizualizacji do wyświetlania danych z urządzenia oraz formularzy służących do zarządzania urządzeniami i ich kontrolowania. Widoki używają właściwości DCM, Cloud i customizations. Widoki nie wpływają na kod, który Projektant urządzenia zapisuje w celu wdrożenia DCM.

## <a name="device-capability-models"></a>Modele możliwości urządzeń

DCM definiuje, jak urządzenie współdziała z aplikacją IoT Central. Deweloper urządzenia musi upewnić się, że urządzenie implementuje zachowania zdefiniowane w DCM, aby IoT Central mógł monitorować urządzenie i zarządzać nim. DCM składa się z jednego lub kilku _interfejsów_, a każdy interfejs może definiować zbiór typów _telemetrii_ , _Właściwości urządzenia_i _poleceń_. Deweloper rozwiązania może zaimportować plik JSON, który definiuje DCM w szablonie urządzenia, lub użyć internetowego interfejsu użytkownika w IoT Central, aby utworzyć lub edytować DCM. Zmiany DCM wprowadzone przy użyciu interfejsu użytkownika sieci Web wymagają, [Aby szablon urządzenia był w wersji](./howto-version-device-template.md).

Deweloperzy rozwiązań mogą również eksportować plik JSON, który zawiera DCM. Deweloper urządzenia może użyć tego dokumentu JSON, aby zrozumieć, w jaki sposób urządzenie ma komunikować się z aplikacją IoT Central.

Plik JSON, który definiuje DCM, używa [języka Digital bliźniaczy Definition Language (DTDL) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central oczekuje, że plik JSON będzie zawierać DCM z interfejsami zdefiniowanymi w tekście, a nie w oddzielnych plikach.

Typowe urządzenie IoT składa się z:

- Części niestandardowe, które są elementami, które sprawiają, że urządzenie jest unikatowe.
- Standardowe części, które są wspólne dla wszystkich urządzeń.

Te części nazywają _interfejsy_ w DCM. Interfejsy definiują szczegóły każdej części implementującej Twoje urządzenie. Interfejsy są wielokrotnego użytku w DCMs.

Poniższy przykład przedstawia kontur modelu możliwości urządzenia dla urządzenia czujnika środowiska z dwoma interfejsami:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Model możliwości ma niektóre wymagane pola:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest modelem możliwości.
- `@context`: określa wersję DTDL używaną dla modelu możliwości.
- `implements`: Wyświetla listę interfejsów, które implementuje urządzenie.

Każdy wpis na liście interfejsów w sekcji Implements ma:

- `name`: Nazwa programowania interfejsu.
- `schema`: interfejs implementujący model możliwości.

Interfejs ma niektóre wymagane pola:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest interfejsem.
- `@context`: określa wersję DTDL użytą dla interfejsu.
- `contents`: Wyświetla właściwości, dane telemetryczne i polecenia wchodzące w skład Twojego urządzenia.

Istnieją opcjonalne pola, za pomocą których można dodać więcej szczegółów do modelu możliwości, takie jak nazwa wyświetlana i opis.

## <a name="interfaces"></a>Interfejsy

DTDL umożliwia opisywanie możliwości urządzenia. Powiązane funkcje są pogrupowane w interfejsy. Interfejsy opisują właściwości, dane telemetryczne i polecenia, które są implementowane przez część urządzenia:

- `Properties`. Właściwości to pola danych, które reprezentują stan urządzenia. Użyj właściwości, aby reprezentować stan trwały urządzenia, taki jak stan włączony pompy chłodzącej. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania układowego urządzenia. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Tylko urządzenia mogą aktualizować wartość właściwości tylko do odczytu. Operator może ustawić wartość właściwości zapisywalnej do wysłania do urządzenia.
- `Telemetry`. Pola telemetrii przedstawiają pomiary z czujników. Za każdym razem, gdy urządzenie przyjmuje pomiar czujnika, należy wysłać zdarzenie telemetrii zawierające dane czujnika.
- `Commands`. Polecenia reprezentują metody, które użytkownicy urządzenia mogą wykonywać na urządzeniu. Na przykład polecenie Reset lub polecenie umożliwiające włączenie lub wyłączenie wentylatora.

W poniższym przykładzie przedstawiono definicję interfejsu czujnika środowiska:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Ten przykład przedstawia dwie właściwości (jeden tylko do odczytu i jeden zapisywalny), typ telemetrii i dwa polecenia. Minimalny opis pola ma:

- `@type` Aby określić typ możliwości: `Telemetry` , `Property` , lub `Command` .  W niektórych przypadkach typ zawiera typ semantyczny, aby umożliwić IoT Central w celu wprowadzenia pewnych założeń dotyczących obsługi wartości.
- `name` wartość telemetrii.
- `schema` Aby określić typ danych telemetrii lub właściwości. Ta wartość może być typem pierwotnym, takim jak Double, Integer, Boolean lub String. Obsługiwane są również złożone typy obiektów, tablice i mapy.
- `commandType` , aby określić sposób obsługi polecenia.

Opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

## <a name="properties"></a>Właściwości

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu oznaczają, że urządzenie zgłasza aktualizacje wartości właściwości do aplikacji IoT Central. Aplikacja IoT Central nie może ustawić wartości właściwości tylko do odczytu.

Można także oznaczyć właściwość jako zapisywalną w interfejsie. Urządzenie może odebrać aktualizację właściwości z możliwością zapisu z aplikacji IoT Central, a także zgłaszać aktualizacje wartości właściwości do aplikacji.

Urządzenia nie muszą być połączone, aby ustawić wartości właściwości. Zaktualizowane wartości są transferowane, gdy urządzenie dalej nawiązuje połączenie z aplikacją. To zachowanie dotyczy zarówno właściwości tylko do odczytu, jak i do zapisu.

Nie używaj właściwości, aby wysyłać dane telemetryczne z urządzenia. Na przykład właściwość ReadOnly, taka jak `temperatureSetting=80` powinna oznaczać, że temperatura urządzenia została ustawiona na 80, a urządzenie próbuje uzyskać dostęp do tej temperatury lub pozostać w tej temperaturze.

W przypadku właściwości zapisywalnych aplikacja urządzenia zwraca kod stanu żądanego stanu, wersję i opis, aby wskazać, czy został on odebrany i zastosował wartość właściwości.

## <a name="telemetry"></a>Telemetria

IoT Central umożliwia wyświetlanie danych telemetrycznych na pulpitach nawigacyjnych i na wykresach oraz stosowanie reguł do wyzwalania akcji po osiągnięciu progów. IoT Central używa informacji w DCM, takich jak typy danych, jednostki i nazwy wyświetlane, aby określić sposób wyświetlania wartości telemetrii.

Za pomocą funkcji eksportu danych IoT Central można przesyłać dane telemetryczne do innych miejsc docelowych, takich jak magazyn lub Event Hubs.

## <a name="commands"></a>Polecenia

Polecenia są synchroniczne lub asynchroniczne. Polecenie synchroniczne musi być wykonywane w ciągu 30 sekund domyślnie, a urządzenie musi być połączone po nadejściu polecenia. Jeśli urządzenie jest w czasie odpowiedzi lub urządzenie nie jest połączone, polecenie kończy się niepowodzeniem.

Korzystaj z poleceń asynchronicznych dla długotrwałych operacji. Urządzenie wysyła informacje o postępie przy użyciu komunikatów telemetrycznych. Te komunikaty postępu mają następujące właściwości nagłówka:

- `iothub-command-name`: nazwa polecenia, na przykład `UpdateFirmware` .
- `iothub-command-request-id`: Identyfikator żądania wygenerowany po stronie serwera i wysyłany do urządzenia w wywołaniu początkowym.
- `iothub-interface-id`: Identyfikator interfejsu, na którym jest zdefiniowane to polecenie, na przykład `urn:example:AssetTracker:1` .
 `iothub-interface-name`: nazwa wystąpienia tego interfejsu, na przykład `myAssetTracker` .
- `iothub-command-statuscode`: kod stanu zwrócony z urządzenia, na przykład `202` .

## <a name="cloud-properties"></a>Właściwości chmury

Właściwości chmury są częścią szablonu urządzenia, ale nie są częścią DCM. Właściwości chmury umożliwiają deweloperom rozwiązania Określanie metadanych urządzeń, które mają być przechowywane w aplikacji IoT Central. Właściwości chmury nie wpływają na kod, który Projektant urządzenia zapisuje w celu wdrożenia DCM.

Deweloper rozwiązań może dodawać właściwości chmury do pulpitów nawigacyjnych i widoków wraz z właściwościami urządzenia, aby umożliwić operatorowi zarządzanie urządzeniami podłączonymi do aplikacji. Deweloper rozwiązania może również użyć właściwości chmury jako części definicji reguły, aby umożliwić edytowanie wartości progowej przez operatora.

## <a name="customizations"></a>Dostosowania

Dostosowania są częścią szablonu urządzenia, ale nie są częścią DCM. Dostosowania pozwalają deweloperom rozwiązania ulepszyć lub zastąpić niektóre definicje w DCM. Na przykład deweloper rozwiązania może zmienić nazwę wyświetlaną dla typu telemetrii lub właściwości. Deweloper rozwiązania może również użyć dostosowań, aby dodać sprawdzanie poprawności, takie jak minimalna lub maksymalna długość właściwości urządzenia typu String.

Dostosowania mogą mieć wpływ na kod, który Projektant urządzenia zapisuje w celu wdrożenia DCM. Na przykład dostosowanie może ustawić minimalną i maksymalną długość ciągu lub minimalną i maksymalną wartość liczbową dla telemetrii.

## <a name="views"></a>Widoki

Deweloper rozwiązania tworzy widoki, które umożliwiają operatorom monitorowanie podłączonych urządzeń i zarządzanie nimi. Widoki są częścią szablonu urządzenia, więc widok jest skojarzony z określonym typem urządzenia. Widok może obejmować:

- Wykresy do kreślenia danych telemetrycznych.
- Kafelki, aby wyświetlić właściwości urządzenia tylko do odczytu.
- Kafelki, aby umożliwić operatorowi Edytowanie właściwości zapisywalnego urządzenia.
- Kafelki, aby umożliwić operatorowi Edytowanie właściwości chmury.
- Kafelki, które umożliwiają wywoływanie poleceń operatora, w tym poleceń, które oczekują ładunku.
- Kafelki do wyświetlania etykiet, obrazów lub tekstu promocji.

Dane telemetryczne, właściwości i polecenia, które można dodać do widoku, są określane na podstawie właściwości DCM, chmury i dostosowań w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

Jako deweloper dla urządzeń już wiesz już, jak można zapoznać się z szablonami urządzeń, sugerowane następne kroki polegają na odczytaniu danych [telemetrycznych, właściwości i poleceń](./concepts-telemetry-properties-commands.md) , aby dowiedzieć się więcej o tym, które urządzenia wymieniają IoT Central.

Jako deweloper rozwiązań zalecanym następnym krokiem jest zapoznanie się z artykułem [Definiowanie nowego typu urządzenia IoT w aplikacji IoT Central platformy Azure](./howto-set-up-template.md) , aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia.
