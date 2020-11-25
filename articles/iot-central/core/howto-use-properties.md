---
title: Korzystanie z właściwości w rozwiązaniu IoT Central platformy Azure
description: Dowiedz się, jak korzystać z właściwości tylko do odczytu i z możliwością zapisu w rozwiązaniu IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 992fb4422974dad7a48e5fea953ac4530bb52d5c
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122591"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Korzystanie z właściwości w rozwiązaniu IoT Central platformy Azure

W tym artykule pokazano, jak używać właściwości urządzenia, które są zdefiniowane w szablonie urządzenia w aplikacji IoT Central platformy Azure.

Właściwości reprezentują wartości punktu w czasie. Na przykład urządzenie może użyć właściwości do raportowania temperatury docelowej, do której próbujesz uzyskać dostęp. Domyślnie właściwości urządzenia są tylko do odczytu w IoT Central. Właściwości z możliwością zapisu pozwalają synchronizować stan między urządzeniem i aplikacją IoT Central platformy Azure.

Właściwości chmury można także definiować w aplikacji IoT Central platformy Azure. Wartości właściwości chmury nigdy nie są wymieniane z urządzeniem i znajdują się poza zakresem tego artykułu.

## <a name="define-your-properties"></a>Definiowanie właściwości

Właściwości to pola danych, które reprezentują stan urządzenia. Użyj właściwości, aby reprezentować stan trwały urządzenia, taki jak stan włączania/wyłączania urządzenia. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania urządzenia. Właściwości deklaruje się jako tylko do odczytu lub do zapisu.

Poniższy zrzut ekranu przedstawia definicję właściwości w aplikacji IoT Central platformy Azure.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Zrzut ekranu pokazujący definicję właściwości w aplikacji IoT Central platformy Azure.":::

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji właściwości.

| Pole           | Opis                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwa wyświetlana    | Nazwa wyświetlana wartości właściwości używanej na pulpitach nawigacyjnych i formularzach.                                                                                                                                                              |
| Nazwa            | Nazwa właściwości. Usługa Azure IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi zawierać znaki alfanumeryczne.                                                 |
| Typ możliwości | Wartość.                                                                                                                                                                                                                          |
| Typ semantyczny   | Typ semantyczny właściwości, taki jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne.                                                                       |
| Schemat          | Typ danych właściwości, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu.                                               |
| Zapisywalne       | Jeśli właściwość nie jest zapisywalna, urządzenie może raportować wartości właściwości do usługi Azure IoT Central. Jeśli właściwość jest zapisywalna, urządzenie może raportować wartości właściwości do usługi Azure IoT Central. Następnie usługa Azure IoT Central może wysyłać do urządzenia aktualizacje właściwości. |
| Ważność        | Dostępne tylko dla typu semantycznego zdarzenia. Te informacje dotyczą **błędu**, **informacji** lub **ostrzeżenia**.                                                                                                                         |
| Wartości stanu    | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość.                                                                                   |
| Jednostka            | Jednostka wartości właściwości, takiej jak **mph**, **%** lub **&deg; C**.                                                                                                                                                              |
| Jednostka wyświetlania    | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach.                                                                                                                                                                                    |
| Komentarz         | Wszelkie komentarze dotyczące możliwości właściwości.                                                                                                                                                                                        |
| Opis     | Opis możliwości właściwości.                                                                                                                                                                                          |

Właściwości można także definiować w interfejsie w szablonie urządzenia, jak pokazano poniżej:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Ten przykład przedstawia dwie właściwości. Te właściwości odnoszą się do definicji właściwości w interfejsie użytkownika:

* `@type` Określa typ możliwości: `Property` . W poprzednim przykładzie przedstawiono również typ semantyczny `Temperature` dla obu właściwości.
* `name` dla właściwości.
* `schema` Określa typ danych właściwości. Ta wartość może być typem pierwotnym, takim jak Double, Integer, Boolean lub String. Obsługiwane są również złożone typy obiektów, tablice i mapy.
* `writable` Domyślnie właściwości są tylko do odczytu. Można oznaczyć właściwość jako zapisywalną przy użyciu tego pola.

Opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

Podczas tworzenia właściwości można określić złożone typy schematów, takie jak **Object** i **enum**.

![Zrzut ekranu pokazujący sposób dodawania funkcji.](./media/howto-use-properties/property.png)

Po wybraniu **schematu** złożonego, takiego jak **obiekt**, należy również zdefiniować obiekt.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Zrzut ekranu pokazujący sposób definiowania obiektu":::

Poniższy kod przedstawia definicję typu właściwości obiektu. Ten obiekt ma dwa pola z typem String i liczbą całkowitą.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
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

## <a name="implement-read-only-properties"></a>Implementowanie właściwości tylko do odczytu

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu umożliwiają aktualizowanie wartości właściwości raportu urządzenia do aplikacji IoT Central platformy Azure. Aplikacja platformy Azure IoT Central nie może ustawić wartości właściwości tylko do odczytu.

Usługa Azure IoT Central używa bliźniaczych reprezentacji urządzeń do synchronizowania wartości właściwości między urządzeniem a aplikacją IoT Central platformy Azure. Wartości właściwości urządzenia używają właściwości zgłaszanych przez sznurek urządzeń. Aby uzyskać więcej informacji, zobacz [bliźniaczych reprezentacji urządzeń](../../iot-hub/tutorial-device-twins.md).

Poniższy fragment kodu z modelu urządzenia pokazuje definicję typu właściwości tylko do odczytu:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Aktualizacje właściwości są wysyłane przez urządzenie jako ładunek JSON. Aby uzyskać więcej informacji, zobacz [ładunki](./concepts-telemetry-properties-commands.md).

Możesz użyć zestawu SDK urządzeń Azure IoT, aby wysłać aktualizację właściwości do aplikacji IoT Central platformy Azure.

Właściwości sznurka urządzenia mogą być wysyłane do aplikacji IoT Central platformy Azure przy użyciu następującej funkcji:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

W tym artykule użyto Node.js dla uproszczenia. Aby uzyskać pełne informacje na temat przykładów aplikacji urządzeń, zobacz następujące instrukcje: [Tworzenie i łączenie aplikacji klienckiej z samouczkiem aplikacji platformy Azure IoT Central](tutorial-connect-device.md) .

Poniższy widok aplikacji IoT Central platformy Azure przedstawia właściwości, które można wyświetlić. Widok automatycznie powoduje, że właściwość **model urządzenia** jest _właściwością urządzenia tylko do odczytu_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Zrzut ekranu pokazujący widok właściwości tylko do odczytu":::

## <a name="implement-writable-properties"></a>Implementowanie właściwości zapisywalnych

Właściwości z możliwością zapisu są ustawiane przez operator w aplikacji IoT Central platformy Azure w formularzu. Usługa Azure IoT Central wysyła właściwość na urządzenie. Usługa Azure IoT Central oczekuje potwierdzenia z urządzenia.

Poniższy fragment kodu z modelu urządzenia pokazuje definicję typu właściwości z możliwością zapisu:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Aby zdefiniować i obsłużyć zapisywalne właściwości, na które odpowiada urządzenie, można użyć następującego kodu:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Komunikat odpowiedzi powinien zawierać `ac` `av` pola i. Pole `ad` jest opcjonalne. Zobacz następujące fragmenty kodu, aby zapoznać się z przykładami:

* `ac` to pole liczbowe, które używa wartości z poniższej tabeli.
* `av` jest numerem wersji wysyłanym do urządzenia.
* `ad` jest opisem ciągu opcji.

| Wartość | Etykieta | Opis |
| ----- | ----- | ----------- |
| `'ac': 200` | Ukończone | Operacja zmiany właściwości została pomyślnie ukończona. |
| `'ac': 202` lub `'ac': 201` | Oczekiwanie | Operacja zmiany właściwości jest w stanie oczekiwania lub w toku. |
| `'ac': 4xx` | Błąd | Żądana zmiana właściwości nie jest prawidłowa lub wystąpił błąd. |
| `'ac': 5xx` | Błąd | Urządzenie napotkało nieoczekiwany błąd podczas przetwarzania żądanej zmiany. |

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Konfigurowanie urządzeń z poziomu usługi zaplecza](../../iot-hub/tutorial-device-twins.md).

Gdy operator ustawia właściwość zapisywalną w aplikacji IoT Central platformy Azure, aplikacja używa odpowiedniej właściwości przędzy urządzenia do wysłania wartości do urządzenia. Następnie urządzenie odpowiada za pomocą właściwości zgłoszonej przez urządzenie. Gdy usługa Azure IoT Central otrzymuje raportowaną wartość właściwości, aktualizuje widok właściwości ze stanem **zaakceptowane**.

W poniższym widoku są wyświetlane właściwości z możliwością zapisu. Po wprowadzeniu wartości i wybraniu opcji **Zapisz** stan początkowy jest **oczekujący**. Gdy urządzenie zaakceptuje zmiany, stan zmieni się na **zaakceptowane**.

![Zrzut ekranu pokazujący stan oczekiwania.](./media/howto-use-properties/status-pending.png)

![Zrzut ekranu przedstawiający zaakceptowaną właściwość.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać właściwości w aplikacji IoT Central platformy Azure, zobacz:

* [Ładunków](concepts-telemetry-properties-commands.md)
* [Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central](tutorial-connect-device.md)