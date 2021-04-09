---
title: Informacje o modelach urządzeń Plug and Play IoT | Microsoft Docs
description: Poznaj język modelowania języka Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzeń IoT Plug and Play. W tym artykule opisano pierwotne i złożone typy danych, wielokrotne użycie wzorców korzystających ze składników i dziedziczenia oraz typów semantycznych. Ten artykuł zawiera wskazówki dotyczące wyboru identyfikatora modelu i narzędzi do tworzenia modeli bliźniaczych urządzeń.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057477"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Przewodnik modelowania Plug and Play IoT

Podstawą Plug and Play IoT jest _model_ urządzeń, który opisuje możliwości urządzenia w aplikacji z obsługą Plug and Play IoT. Ten model jest strukturalny jako zestaw interfejsów, które definiują:

- _Właściwości_ reprezentujące stan tylko do odczytu lub do zapisu urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a docelowa temperatura w termostatie może być właściwością umożliwiającą zapis.
- Pola _telemetrii_ , które definiują dane emitowane przez urządzenie, czy dane są regularnym strumieniem odczytów czujników, okazjonalnym błędem lub komunikatem informacyjnym.
- _Polecenia_ opisujące funkcję lub operację, którą można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie przy użyciu zdalnej kamery.

Aby dowiedzieć się więcej o tym, jak usługa IoT Plug and Play korzysta z modeli urządzeń, zobacz [Przewodnik dla deweloperów urządzeń iot Plug and Play](concepts-developer-guide-device.md) i [IoT Plug and Play Service Guide](concepts-developer-guide-service.md).

Aby zdefiniować model, należy użyć języka Digital bliźniaczych reprezentacji Definition Language (DTDL). DTDL używa elementu JSON o nazwie [JSON-LD](https://json-ld.org/). Poniższy fragment kodu przedstawia model dla urządzenia z termostatem:

- Ma unikatowy identyfikator modelu: `dtmi:com:example:Thermostat;1` .
- Wysyła dane telemetryczne dotyczące temperatury.
- Ma modyfikowalną właściwość do ustawiania temperatury docelowej.
- Ma właściwość tylko do odczytu, aby zgłosić maksymalną temperaturę od ostatniego ponownego uruchomienia.
- Odpowiada na polecenie, które żąda maksymalnej, minimalnej i średniej temperatury w przedziale czasu.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
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

Model termostatu ma jeden interfejs. W dalszych przykładach w tym artykule przedstawiono bardziej złożone modele wykorzystujące składniki i dziedziczenie.

W tym artykule opisano sposób projektowania i tworzenia własnych modeli oraz tematyki, takie jak typy danych, struktura modelu i narzędzia.

Aby dowiedzieć się więcej, zobacz specyfikację [Digital bliźniaczych reprezentacji Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="model-structure"></a>Struktura modelu

Właściwości, dane telemetryczne i polecenia są pogrupowane w interfejsy. W tej sekcji opisano, jak można używać interfejsów do opisywania prostych i złożonych modeli przy użyciu składników i dziedziczenia.

### <a name="model-ids"></a>Identyfikatory modelu

Każdy interfejs ma unikatowy identyfikator modelu przędzy cyfrowej (DTMI). Modele złożone używają DTMIs do identyfikowania składników. Aplikacje mogą używać DTMIs wysyłanych przez urządzenia do lokalizowania definicji modelu w repozytorium.

DTMIs powinien postępować zgodnie z konwencją nazewnictwa wymaganą przez [repozytorium modelu Plug and Play IoT](https://github.com/Azure/iot-plugandplay-models):

- Prefiks DTMI ma wartość `dtmi:` .
- Sufiks DTMI jest numerem wersji dla modelu, takiego jak `;2` .
- Treść DTMI jest mapowana do folderu i pliku w repozytorium modelu, w którym jest przechowywany model. Numer wersji jest częścią nazwy pliku.

Na przykład model identyfikowany przez DTMI `dtmi:com:Example:Thermostat;2` jest przechowywany w pliku *DTMI/com/example/thermostat-2.js* .

Poniższy fragment kodu przedstawia konspekt definicji interfejsu z jego unikatowym DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Brak składników

Prosty model, taki jak termostat przedstawiony wcześniej, nie używa składników osadzonych ani kaskadowych. Dane telemetryczne, właściwości i polecenia są zdefiniowane w `contents` węźle interfejsu.

Poniższy przykład przedstawia część prostego modelu, który nie używa składników:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
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
...
```

Narzędzia takie jak Azure IoT Explorer i Projektant szablonów urządzeń IoT Central są oznaczone jako składnikiem autonomicznym, takim jak termostat jako _składnik domyślny_.

Poniższy zrzut ekranu przedstawia sposób wyświetlania modelu w narzędziu Azure IoT Explorer:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Składnik domyślny w programie Azure IoT Explorer":::

Poniższy zrzut ekranu pokazuje, jak model jest wyświetlany jako składnik domyślny w projektancie szablonów urządzeń IoT Central. Wybierz pozycję **Wyświetl tożsamość** , aby zobaczyć DTMI modelu:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Zrzut ekranu przedstawiający model termostatu w programie IoT Central Designer":::

Identyfikator modelu jest przechowywany we właściwości przędzy urządzenia, jak pokazano na poniższym zrzucie ekranu:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Identyfikator modelu we właściwości cyfrowej przędzy":::

Model DTDL bez składników to przydatne uproszczenie dla urządzeń lub modułu IoT Edge z pojedynczym zestawem danych telemetrycznych, właściwościami i poleceniami. Model, który nie używa składników, ułatwia Migrowanie istniejącego urządzenia lub modułu jako urządzenia Plug and Play IoT lub modułu — tworzysz model DTDL, który opisuje rzeczywiste urządzenie lub moduł bez konieczności definiowania składników.

> [!TIP]
> Moduł może [być modułem urządzenia lub](../iot-hub/iot-hub-devguide-module-twins.md) [modułem IoT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Ponowne użycie

Istnieją dwa sposoby ponownego użycia definicji interfejsu. Użyj wielu składników w modelu, aby odwoływać się do innych definicji interfejsu. Użyj dziedziczenia, aby rozłożyć istniejące definicje interfejsu.

### <a name="multiple-components"></a>Wiele składników

Składniki umożliwiają skompilowanie interfejsu modelu jako zestawu innych interfejsów.

Na przykład interfejs [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) jest zdefiniowany jako model. Ten interfejs można dołączyć jako co najmniej jeden składnik podczas definiowania [modelu kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). W poniższym przykładzie te składniki są wywoływane `thermostat1` i `thermostat2` .

W przypadku modelu DTDL z wieloma składnikami istnieją co najmniej dwie sekcje składników. Każda sekcja ma `@type` ustawioną wartość `Component` i jawnie odwołuje się do schematu, jak pokazano w poniższym fragmencie kodu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Ten model ma trzy składniki zdefiniowane w sekcji zawartości — dwa `Thermostat` składniki i `DeviceInformation` składnik. Sekcja zawartość zawiera również definicje właściwości, danych telemetrycznych i poleceń.

Poniższe zrzuty ekranu pokazują, jak ten model pojawia się w IoT Central. Definicje właściwości, danych telemetrycznych i poleceń w kontrolerze temperatury pojawiają się w **domyślnym składniku** najwyższego poziomu. Definicje właściwości, danych telemetrycznych i poleceń dla każdego termostatu są wyświetlane w definicjach składników:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Zrzut ekranu przedstawiający szablon urządzenia kontrolera temperatury w IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Zrzut ekranu przedstawiający składniki termostatu w szablonie urządzenia kontrolera temperatury w IoT Central.":::

Aby dowiedzieć się, jak napisać kod urządzenia, który współdziała ze składnikami, zobacz [Przewodnik dla deweloperów urządzeń IoT Plug and Play](concepts-developer-guide-device.md).

Aby dowiedzieć się, jak napisać kod usługi, który zawiera informacje o składnikach na urządzeniu, zobacz [przewodnik dewelopera usługi IoT Plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Dziedziczenie

Dziedziczenie umożliwia ponowne użycie funkcji w interfejsach podstawowych w celu zwiększenia możliwości interfejsu. Na przykład kilka modeli urządzeń może współużytkować typowe funkcje, takie jak numer seryjny:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Przykładem dziedziczenia w modelu urządzenia z termostatem i kontrolerem przepływu, który udostępnia możliwości z interfejsu podstawowego." border="false":::

Poniższy fragment kodu przedstawia model DTML, który używa `extends` słowa kluczowego do definiowania relacji dziedziczenia pokazanej na poprzednim diagramie:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Poniższy zrzut ekranu przedstawia ten model w IoT Central środowisku szablonów urządzeń:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Zrzut ekranu przedstawiający dziedziczenie interfejsu w IoT Central":::

Gdy piszesz kod po stronie urządzenia lub serwisu, kod nie musi wykonywać żadnych specjalnych czynności do obsługi dziedziczonych interfejsów. W przykładzie przedstawionym w tej sekcji kod urządzenia zgłasza numer seryjny tak, jakby był częścią interfejsu termostatu.

### <a name="tips"></a>Porady

Podczas tworzenia modelu można łączyć składniki i dziedziczenie. Na poniższym diagramie przedstawiono `thermostat` model dziedziczenia z `baseDevice` interfejsu. `baseDevice`Interfejs ma składnik, który sam dziedziczy z innego interfejsu:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagram przedstawiający model, który używa zarówno składników, jak i dziedziczenia." border="false":::

Poniższy fragment kodu przedstawia model DTML, który używa `extends` `component` słów kluczowych i, aby zdefiniować relację dziedziczenia i użycie składników pokazanej na poprzednim diagramie:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Typy danych

Użyj typów danych, aby zdefiniować dane telemetryczne, właściwości i parametry polecenia. Typy danych mogą być pierwotne lub złożone. Złożone typy danych używają prymitywów lub innych typów złożonych. Maksymalna głębokość typów złożonych to pięć poziomów.

### <a name="primitive-types"></a>Typy pierwotne

W poniższej tabeli przedstawiono zestaw typów pierwotnych, których można użyć:

| Typ pierwotny | Opis |
| --- | --- |
| `boolean` | Wartość logiczna |
| `date` | Pełna Data zgodnie z definicją w [sekcji 5,6 specyfikacji RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Data i godzina zgodnie z definicją w [dokumencie RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Dwubajtowa liczba zmiennoprzecinkowa IEEE 8 |
| `duration` | Czas trwania w formacie ISO 8601 |
| `float` | Dwubajtowa liczba zmiennoprzecinkowa IEEE 4 |
| `integer` | Podpisana 4-bajtowa liczba całkowita |
| `long` | 8-bajtowa liczba całkowita ze znakiem |
| `string` | Ciąg UTF8 |
| `time` | Pełny czas zgodnie z definicją w [sekcji 5,6 specyfikacji RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która używa `double` typu w `schema` polu:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Złożone typy danych

Złożone typy danych to jedna z *tablic*, *Wyliczenie*, *Mapa*, *obiekt* lub jeden z typów geoprzestrzennych.

#### <a name="arrays"></a>Tablice

Tablica jest indeksowanym typem danych, w którym wszystkie elementy są tego samego typu. Typ elementu może być typem pierwotnym lub złożonym.

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która używa `Array` typu w `schema` polu. Elementy tablicy są logicznymi:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Wyliczenia

Wyliczenie opisuje typ z zestawem nazwanych etykiet, które mapują na wartości. Wartości mogą być liczbami całkowitymi lub ciągami, ale etykiety są zawsze ciągami.

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która używa `Enum` typu w `schema` polu. Wartości w wyliczeniu są liczbami całkowitymi:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

Mapa jest typem z parami klucz-wartość, gdzie wszystkie wartości mają ten sam typ. Klucz w mapie musi być ciągiem. Wartości w mapie mogą być dowolnego typu, w tym innego typu złożonego.

Poniższy fragment kodu przedstawia przykładową definicję właściwości, która używa `Map` typu w `schema` polu. Wartości w mapie są ciągami:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Obiekty

Typ obiektu składa się z nazwanych pól. Typy pól w mapie obiektów mogą być typami pierwotnymi lub złożonymi.

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która używa `Object` typu w `schema` polu. Pola w obiekcie to `dateTime` , `duration` i, `string` typy:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Typy geograficzne

DTDL udostępnia zestaw typów geoprzestrzennych opartych na [GEOJSON](https://geojson.org/)dla modelowania struktur danych geograficznych:,,,, `point` `multiPoint` `lineString` `multiLineString` `polygon` i `multiPolygon` . Te typy są wstępnie zdefiniowanymi zagnieżdżonymi strukturami tablic, obiektów i wyliczeń.

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która używa `point` typu w `schema` polu:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Ponieważ typy geograficzne są oparte na tablicy, nie mogą być obecnie używane w definicjach właściwości.

## <a name="semantic-types"></a>Typy semantyczne

Typ danych w definicji właściwości lub telemetrii określa format informacji, które urządzenie wymienia z usługą. Typ semantyczny zawiera informacje o telemetrii i właściwościach, których aplikacja może użyć do określenia sposobu przetwarzania lub wyświetlania wartości. Każdy typ semantyczny ma co najmniej jedną skojarzoną jednostkę. Na przykład c i Fahrenheita są jednostkami dla typu semantyki temperatury. IoT Central pulpity nawigacyjne i analizy mogą korzystać z informacji o typie semantycznym, aby określić sposób kreślenia wartości telemetrycznych lub właściwości oraz jednostek wyświetlania. Aby dowiedzieć się, jak można użyć analizatora modeli do odczytu typów semantyki, zobacz [Omówienie analizatora modeli cyfrowych bliźniaczych reprezentacji](concepts-model-parser.md).

Poniższy fragment kodu przedstawia przykładową definicję telemetrii, która zawiera informacje o typie semantycznym. Typ semantyczny `Temperature` jest dodawany do `@type` tablicy, a `unit` wartość `degreeCelsius` jest jedną z prawidłowych jednostek dla typu semantycznego:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Lokalizacja

Aplikacje, takie jak IoT Central, wykorzystują informacje w modelu do dynamicznego kompilowania interfejsu użytkownika wokół danych wymienianych z urządzeniem IoT Plug and Play. Na przykład kafelki na pulpicie nawigacyjnym mogą wyświetlać nazwy i opisy dla danych telemetrycznych, właściwości i poleceń.

Pola opcjonalne `description` i `displayName` w modelu przechowują ciągi przeznaczone do użycia w interfejsie użytkownika. Te pola mogą przechowywać zlokalizowane ciągi, których aplikacja może używać do renderowania zlokalizowanego interfejsu użytkownika.

Poniższy fragment kodu przedstawia przykładową definicję telemetrii temperatury, która zawiera zlokalizowane ciągi:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Dodawanie zlokalizowanych ciągów jest opcjonalne. W poniższym przykładzie jest tylko jeden język domyślny:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Cykl życia i narzędzia

Cztery etapy cyklu życia dla modelu urządzenia to tworzenie, publikowanie, używanie i przechowywanie wersji:

### <a name="author"></a>Autor

Modele urządzeń DTML to dokumenty JSON, które można utworzyć w edytorze tekstu. Jednak w IoT Central można użyć środowiska graficznego interfejsu użytkownika szablonu urządzenia do utworzenia modelu DTML. W IoT Central można:

- Utwórz interfejsy, które definiują właściwości, dane telemetryczne i polecenia.
- Użyj składników, aby połączyć wiele interfejsów jednocześnie.
- Zdefiniuj relacje dziedziczenia między interfejsami.
- Importuj i Eksportuj pliki modelu DTML.

Aby dowiedzieć się więcej, zobacz [Definiowanie nowego typu urządzenia IoT w aplikacji IoT Central platformy Azure](../iot-central/core/howto-set-up-template.md).

[Edytor DTDL dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) zapewnia oparte na tekście środowisko edycji z walidacją składni i autouzupełnianiem w celu zapewnienia dokładniejszej kontroli nad środowiskiem tworzenia modelu.

### <a name="publish"></a>Publikowanie

Aby umożliwić udostępnianie modeli DTML i odnajdowanie, Opublikuj je w repozytorium modele urządzeń.

Przed opublikowaniem modelu w repozytorium publicznym można `dmr-client` sprawdzić model przy użyciu narzędzi.

Aby dowiedzieć się więcej, zobacz [repozytorium modele urządzeń](concepts-model-repository.md).

### <a name="use"></a>Zastosowanie

Aplikacje, takie jak IoT Central, korzystają z modeli urządzeń. W IoT Central model jest częścią szablonu urządzenia opisującego możliwości urządzenia. IoT Central używa szablonu urządzenia do dynamicznego kompilowania interfejsu użytkownika dla urządzenia, w tym pulpitów nawigacyjnych i analiz.

Rozwiązanie niestandardowe może używać [analizatora modeli cyfrowych bliźniaczych reprezentacji](concepts-model-parser.md) do zrozumienia możliwości urządzenia implementującego model. Aby dowiedzieć się więcej, zobacz [Korzystanie z modeli iot Plug and Play w rozwiązaniu IoT](concepts-model-discovery.md).

### <a name="version"></a>Wersja

Aby zapewnić, że urządzenia i rozwiązania po stronie serwera, które używają modeli, nadal działają, opublikowane modele są niezmienne.

DTMI zawiera numer wersji, którego można użyć do utworzenia wielu wersji modelu. Urządzenia i rozwiązania po stronie serwera mogą korzystać z określonej wersji, która została zaprojektowana do użycia.

IoT Central implementuje więcej reguł dotyczących wersji dla modeli urządzeń. W przypadku wersji szablonu urządzenia i jego modelu w IoT Central można migrować urządzenia z poprzednich wersji do nowszych wersji. Jednak zmigrowane urządzenia nie mogą korzystać z nowych możliwości bez uaktualnienia oprogramowania układowego. Aby dowiedzieć się więcej, zobacz [Tworzenie nowej wersji szablonu urządzenia](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Limity i ograniczenia

Poniższa lista podsumowuje niektóre ograniczenia dotyczące kluczy i limity dla modeli:

- Obecnie maksymalna głębokość dla tablic, map i obiektów ma pięć poziomów głębokości.
- Nie można używać tablic w definicjach właściwości.
- Można rozłożyć interfejsy na głębokość 10 poziomów.
- Interfejs może rozciągnąć maksymalnie dwa inne interfejsy.
- Składnik nie może zawierać innego składnika.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak modelowanie urządzeń, Oto kilka dodatkowych zasobów:

- [Instalowanie narzędzi autorskich DTDL i korzystanie z nich](howto-use-dtdl-authoring-tools.md)
- [Digital bliźniaczych reprezentacji Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repozytoria modelu](./concepts-model-repository.md)
