---
title: Ładunki telemetryczne, właściwości i poleceń na platformie Azure IoT Central | Microsoft Docs
description: Szablony urządzeń IoT Central platformy Azure pozwalają określić dane telemetryczne, właściwości i polecenia urządzenia, które muszą zostać wdrożone. Zapoznaj się z formatem danych, z którymi urządzenie może wymieniać IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 554079ddec3332ced2817d18ea55ce1260d68817
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291611"
---
# <a name="telemetry-property-and-command-payloads"></a>Ładunki telemetrii, właściwości i polecenia

_Ten artykuł ma zastosowanie do deweloperów urządzeń._

Szablon urządzenia w usłudze Azure IoT Central to plan, który definiuje:

* Dane telemetryczne wysyłane przez urządzenie do IoT Central.
* Właściwości synchronizowane z urządzeniem IoT Central.
* Polecenia, które IoT Central wywołań na urządzeniu.

W tym artykule opisano, w przypadku deweloperów urządzeń, ładunków JSON wysyłanych i odbieranych przez urządzenia dla danych telemetrycznych, właściwości i poleceń zdefiniowanych w szablonie urządzenia.

Artykuł nie zawiera opisu każdego możliwego typu telemetrii, właściwości i ładunku poleceń, ale przykłady ilustrują wszystkie typy kluczy.

W każdym przykładzie przedstawiono fragment kodu z modelu możliwości urządzenia (DCM), który definiuje typ i przykładowe ładunki JSON, aby zilustrować, jak urządzenie ma korzystać z aplikacji IoT Central.

> [!NOTE]
> IoT Central akceptuje dowolny prawidłowy kod JSON, ale może być używany tylko w przypadku wizualizacji, jeśli jest zgodny z definicją w DCM. Możesz eksportować dane, które nie pasują do definicji, zobacz [Eksportowanie danych IoT do miejsc docelowych na platformie Azure](howto-export-data.md).

Plik JSON, który definiuje DCM, używa [języka Digital bliźniaczy Definition Language (DTDL) v1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md). Ta specyfikacja obejmuje definicję `@id` formatu właściwości.

Aby zapoznać się z przykładowym kodem urządzenia, który pokazuje niektóre z tych ładunków, zobacz temat [Tworzenie i łączenie aplikacji klienta w aplikacji IoT Central platformy Azure (Node.js)](tutorial-connect-device-nodejs.md) i [Tworzenie i łączenie aplikacji klienckiej z samouczkami aplikacji platformy Azure IoT Central](tutorial-connect-device-python.md) .

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

IoT Central umożliwia wyświetlenie danych pierwotnych wysyłanych przez urządzenie do aplikacji. Ten widok jest przydatny do rozwiązywania problemów z ładunkiem wysyłanym z urządzenia. Aby wyświetlić dane pierwotne wysyłane przez urządzenie:

1. Przejdź do urządzenia ze strony **urządzenia** .

1. Wybierz kartę **dane surowe** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Nieprzetworzony widok danych":::

    W tym widoku można wybrać kolumny do wyświetlenia i ustawić zakres czasu do wyświetlenia. W kolumnie **dane niemodelowane** są wyświetlane dane z urządzenia, które nie są zgodne z żadną definicją właściwości lub telemetrii w szablonie urządzenia.

## <a name="telemetry"></a>Telemetria

### <a name="primitive-types"></a>Typy pierwotne

W tej sekcji przedstawiono przykłady typów danych telemetrycznych pierwotnych, które urządzenie strumieniuje do aplikacji IoT Central.

Poniższy fragment kodu z DCM zawiera definicję `boolean` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu:

```json
{ "BooleanTelemetry": true }
```

Poniższy fragment kodu z DCM zawiera definicję `string` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Poniższy fragment kodu z DCM zawiera definicję `integer` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu:

```json
{ "IntegerTelemetry": 23 }
```

Poniższy fragment kodu z DCM zawiera definicję `double` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu:

```json
{ "DoubleTelemetry": 56.78 }
```

Poniższy fragment kodu z DCM zawiera definicję `dateTime` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako dane JSON, które wyglądają podobnie jak w przypadku następujących przykładowych `DateTime` typów, muszą być zgodne z normą ISO 8061:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Poniższy fragment kodu z DCM zawiera definicję `duration` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu — czas trwania musi być zgodny ze standardem ISO 8601:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Typy złożone

W tej sekcji przedstawiono przykłady złożonych typów telemetrii, które urządzenie przesyła strumieniowo do aplikacji IoT Central.

Poniższy fragment kodu z DCM zawiera definicję `geopoint` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do poniższego przykładu. IoT Central wyświetla wartość jako numer PIN na mapie:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Poniższy fragment kodu z DCM zawiera definicję `Enum` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do poniższego przykładu. Możliwe wartości to `0` , `1` i, `2` które są wyświetlane w IoT Central jako `Item1` , `Item2` i `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Poniższy fragment kodu z DCM zawiera definicję `Object` typu telemetrii. Ten obiekt ma trzy pola z typami `dateTime` , `integer` i `Enum` :

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do poniższego przykładu. `DateTime` typy muszą być zgodne ze standardem ISO 8061. Możliwe wartości dla programu `Property3` to `0` , i, które są `1` wyświetlane w IoT Central jako `Item1` , `Item2` i `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Poniższy fragment kodu z DCM zawiera definicję `vector` typu telemetrii:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Klient urządzenia powinien wysłać dane telemetryczne jako kod JSON, który wygląda podobnie do następującego przykładu:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Typy zdarzeń i Stanów

W tej sekcji przedstawiono przykłady zdarzeń telemetrii i Stany wysyłane przez urządzenie do aplikacji IoT Central.

Poniższy fragment kodu z DCM zawiera definicję `integer` typu zdarzenia:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Klient urządzenia powinien wysłać dane zdarzenia w formacie JSON, który wygląda podobnie do następującego przykładu:

```json
{ "IntegerEvent": 74 }
```

Poniższy fragment kodu z DCM zawiera definicję `integer` typu stanu:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Klient urządzenia powinien wysłać stan jako kod JSON, który wygląda podobnie do poniższego przykładu. Możliwe wartości stanu całkowitego to `1` , `2` , lub `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Właściwości

> [!NOTE]
> Formaty ładunku dla właściwości mają zastosowanie do aplikacji utworzonych w dniu lub po 07/14/2020.

### <a name="primitive-types"></a>Typy pierwotne

W tej sekcji przedstawiono przykłady typów właściwości pierwotnych wysyłanych przez urządzenie do aplikacji IoT Central.

Poniższy fragment kodu z DCM zawiera definicję `boolean` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{ "BooleanProperty": false }
```

Poniższy fragment kodu z DCM zawiera definicję `boolean` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{ "LongProperty": 439 }
```

Poniższy fragment kodu z DCM zawiera definicję `date` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda jak w poniższym przykładzie jako raportowaną właściwość w bliźniaczych urządzeniach. `Date` typy muszą być zgodne ze standardem ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

Poniższy fragment kodu z DCM zawiera definicję `duration` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako raportowana właściwość w przypadku urządzeń, które muszą być zgodne z normą ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Poniższy fragment kodu z DCM zawiera definicję `float` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{ "FloatProperty": 1.9 }
```

Poniższy fragment kodu z DCM zawiera definicję `string` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Typy złożone

W tej sekcji przedstawiono przykłady typów właściwości złożonych, które urządzenie wysyła do aplikacji IoT Central.

Poniższy fragment kodu z DCM zawiera definicję `geopoint` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Poniższy fragment kodu z DCM zawiera definicję `Enum` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda jak w poniższym przykładzie jako raportowaną właściwość w bliźniaczych urządzeniach. Możliwe wartości to `0` , `1` i, które są wyświetlane w IoT Central jako `Item1` , `Item2` i `Item3` :

```json
{ "EnumProperty": 1 }
```

Poniższy fragment kodu z DCM zawiera definicję `Object` typu właściwości. Ten obiekt ma dwa pola z typami `string` i `integer` :

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Poniższy fragment kodu z DCM zawiera definicję `vector` typu właściwości:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Zapisywalne typy właściwości

W tej sekcji przedstawiono przykłady typów właściwości zapisywalnych odbieranych przez urządzenie z aplikacji IoT Central.

IoT Central oczekuje odpowiedzi z urządzenia na zapisywalne aktualizacje właściwości. Komunikat odpowiedzi powinien zawierać `ac` `av` pola i. Pole `ad` jest opcjonalne. Przykłady można znaleźć w następujących fragmentach kodu.

`ac` to pole liczbowe, które używa wartości z następującej tabeli:

| Wartość | Etykieta | Opis |
| ----- | ----- | ----------- |
| `'ac': 200` | Zakończone | Operacja zmiany właściwości została pomyślnie ukończona. |
| `'ac': 202`  oraz `'ac': 201` | Oczekiwanie | Operacja zmiany właściwości jest w stanie oczekiwania lub w toku |
| `'ac': 4xx` | Błąd | Żądana zmiana właściwości jest nieprawidłowa lub wystąpił błąd |
| `'ac': 5xx` | Błąd | Urządzenie napotkało nieoczekiwany błąd podczas przetwarzania żądanej zmiany. |

`av` jest numerem wersji wysyłanym do urządzenia.

`ad` jest opisem ciągu opcji.

Poniższy fragment kodu z DCM pokazuje definicję `string` typu właściwości do zapisu:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Urządzenie otrzymuje następujący ładunek z IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Urządzenie powinno wysłać następujący ładunek JSON do IoT Central po przetworzeniu aktualizacji. Ten komunikat zawiera numer wersji oryginalnej aktualizacji otrzymanej z IoT Central. Gdy IoT Central otrzymuje ten komunikat, oznacza właściwość jako **zsynchronizowaną** w interfejsie użytkownika:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Poniższy fragment kodu z DCM pokazuje definicję `Enum` typu właściwości do zapisu:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Urządzenie otrzymuje następujący ładunek z IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Urządzenie powinno wysłać następujący ładunek JSON do IoT Central po przetworzeniu aktualizacji. Ten komunikat zawiera numer wersji oryginalnej aktualizacji otrzymanej z IoT Central. Gdy IoT Central otrzymuje ten komunikat, oznacza właściwość jako **zsynchronizowaną** w interfejsie użytkownika:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Polecenia

### <a name="synchronous-command-types"></a>Typy poleceń synchronicznych

Poniższy fragment kodu z DCM pokazuje definicję polecenia synchronicznego, które nie ma parametrów i nie oczekuje, że urządzenie zwróci coś:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

Urządzenie odbiera pusty ładunek w żądaniu i powinien zwrócić pusty ładunek w odpowiedzi z `200` kodem odpowiedzi HTTP, aby wskazać powodzenie.

Poniższy fragment kodu z DCM pokazuje definicję polecenia synchronicznego, które ma parametr integer i oczekuje, że urządzenie zwróci wartość całkowitą:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

Urządzenie odbiera wartość całkowitą jako ładunek żądania. Urządzenie powinno zwrócić wartość całkowitą jako ładunek odpowiedzi z `200` kodem odpowiedzi HTTP, aby wskazać powodzenie.

Poniższy fragment kodu z DCM pokazuje definicję polecenia synchronicznego, które ma parametr obiektu i oczekuje, że urządzenie zwróci obiekt. W tym przykładzie oba obiekty zawierają liczby całkowite i pola ciągów:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

Poniższy fragment kodu przedstawia przykładowy ładunek żądania wysyłany do urządzenia:

```json
{ "Field1": 56, "Field2": "A string value" }
```

Poniższy fragment kodu przedstawia przykładowy ładunek odpowiedzi wysłany z urządzenia. Użyj `200` kodu odpowiedzi HTTP w celu wskazania sukcesu:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>Typy poleceń asynchronicznych

Poniższy fragment kodu z DCM zawiera definicję polecenia asynchronicznego. Polecenie ma parametr integer i oczekuje, że urządzenie zwróci wartość całkowitą:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

Urządzenie odbiera wartość całkowitą jako ładunek żądania. Urządzenie powinno zwrócić pusty ładunek odpowiedzi z `202` kodem odpowiedzi HTTP, aby wskazać, że urządzenie zaakceptowało żądanie przetwarzania asynchronicznego.

Po zakończeniu przetwarzania żądania przez urządzenie należy wysłać właściwość do IoT Central, która wygląda podobnie do poniższego przykładu. Nazwa właściwości musi być taka sama jak nazwa polecenia:

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Następne kroki

Jako deweloper dla urządzeń, teraz "warto zapoznać się z szablonami urządzeń, sugerowanym następnym etapem jest zapoznawanie się z [usługą Azure IoT Central](./concepts-get-connected.md) , aby dowiedzieć się więcej na temat rejestrowania urządzeń w usłudze IoT Central i sposobu, w jaki IoT Central zabezpiecza połączenia urządzeń.
