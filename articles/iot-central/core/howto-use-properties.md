---
title: Korzystanie z właściwości w rozwiązaniu IoT Central platformy Azure
description: Dowiedz się, jak korzystać z właściwości tylko do odczytu i z możliwością zapisu w rozwiązaniu IoT Central platformy Azure.
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: eb949f6f0895743250ead0276692497432bfeed5
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940569"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Korzystanie z właściwości w rozwiązaniu IoT Central platformy Azure

W tym artykule pokazano, jak używać właściwości urządzenia, które są zdefiniowane w szablonie urządzenia w aplikacji IoT Central platformy Azure.

Właściwości reprezentują wartości punktu w czasie. Na przykład urządzenie może użyć właściwości do raportowania temperatury docelowej, do której próbujesz uzyskać dostęp. Właściwości pozwalają również synchronizować stan między urządzeniem i aplikacją IoT Central platformy Azure. Można ustawić właściwości z możliwością zapisu z usługi Azure IoT Central.

Właściwości chmury można także definiować w aplikacji IoT Central platformy Azure. Wartości właściwości chmury nigdy nie są wymieniane z urządzeniem i znajdują się poza zakresem tego artykułu.

## <a name="define-your-properties"></a>Definiowanie właściwości

Właściwości to pola danych, które reprezentują stan urządzenia. Użyj właściwości, aby reprezentować stan trwały urządzenia, taki jak stan włączania/wyłączania urządzenia. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania urządzenia. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu.

Poniższy zrzut ekranu przedstawia definicję właściwości w aplikacji IoT Central platformy Azure.

![Zrzut ekranu pokazujący definicję właściwości w aplikacji IoT Central platformy Azure.](./media/howto-use-properties/property-definition.png)

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji właściwości.

| Pole           | Opis                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwa wyświetlana    | Nazwa wyświetlana wartości właściwości używanej na pulpitach nawigacyjnych i formularzach.                                                                                                                                                              |
| Nazwa            | Nazwa właściwości. Usługa Azure IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi zawierać znaki alfanumeryczne.                                                 |
| Typ możliwości | Wartość.                                                                                                                                                                                                                          |
| Typ semantyczny   | Typ semantyczny właściwości, taki jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne.                                                                       |
| Schemat          | Typ danych właściwości, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu.                                               |
| Z możliwością zapisu       | Jeśli właściwość nie jest zapisywalna, urządzenie może raportować wartości właściwości do usługi Azure IoT Central. Jeśli właściwość jest zapisywalna, urządzenie może raportować wartości właściwości do usługi Azure IoT Central. Następnie usługa Azure IoT Central może wysyłać do urządzenia aktualizacje właściwości. |
| Ważność        | Dostępne tylko dla typu semantycznego zdarzenia. Te informacje dotyczą **błędu**, **informacji**lub **ostrzeżenia**.                                                                                                                         |
| Wartości stanu    | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość.                                                                                   |
| Jednostka            | Jednostka wartości właściwości, takiej jak **mph**, **%** lub ** &deg; C**.                                                                                                                                                              |
| Jednostka wyświetlania    | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach.                                                                                                                                                                                    |
| Komentarz         | Wszelkie komentarze dotyczące możliwości właściwości.                                                                                                                                                                                        |
| Opis     | Opis możliwości właściwości.                                                                                                                                                                                          |

Właściwości można także definiować w interfejsie w szablonie urządzenia, jak pokazano poniżej:

``` json
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
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Ten przykład pokazuje pięć właściwości. Te właściwości mogą być powiązane z definicją właściwości w interfejsie użytkownika, jak pokazano poniżej:

* `@type` Aby określić typ możliwości: `Property`
* `name` dla wartości właściwości.
* `schema` Określ typ danych właściwości. Ta wartość może być typem pierwotnym, takim jak Double, Integer, Boolean lub String. Obsługiwane są również złożone typy obiektów, tablice i mapy.
* `writable` Domyślnie właściwości są tylko do odczytu. Można oznaczyć właściwość jako zapisywalną przy użyciu tego pola.

Opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

Podczas tworzenia właściwości można określić złożone typy **schematów** , takie jak Object i enum.

![Zrzut ekranu pokazujący sposób dodawania funkcji.](./media/howto-use-properties/property.png)

Po wybraniu **schematu**złożonego, takiego jak **obiekt**, należy również zdefiniować obiekt.

![Zrzut ekranu pokazujący sposób definiowania obiektu.](./media/howto-use-properties/object.png)

Poniższy kod przedstawia definicję typu właściwości obiektu. Ten obiekt ma dwa pola z typem String i liczbą całkowitą.

``` json
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

## <a name="implement-read-only-properties"></a>Implementowanie właściwości tylko do odczytu

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu oznaczają, że urządzenie zgłasza aktualizacje wartości właściwości do aplikacji IoT Central platformy Azure. Aplikacja platformy Azure IoT Central nie może ustawić wartości właściwości tylko do odczytu.

Usługa Azure IoT Central używa bliźniaczych reprezentacji urządzeń do synchronizowania wartości właściwości między urządzeniem a aplikacją IoT Central platformy Azure. Wartości właściwości urządzenia używają właściwości zgłaszanych przez sznurek urządzeń. Aby uzyskać więcej informacji, zobacz [bliźniaczych reprezentacji urządzeń](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Poniższy fragment kodu z modelu możliwości urządzenia pokazuje definicję typu właściwości tylko do odczytu:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Urządzenie do usługi Azure IoT Central wysyła właściwości tylko do odczytu. Właściwości są wysyłane jako ładunek JSON. Aby uzyskać więcej informacji, zobacz [ładunki](./concepts-telemetry-properties-commands.md).

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

W tym artykule użyto Node.js dla uproszczenia. Aby uzyskać pełne informacje na temat przykładów aplikacji urządzeń, zobacz następujące samouczki:

* [Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
* [Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Python)](tutorial-connect-device-python.md)

Poniższy widok aplikacji IoT Central platformy Azure przedstawia właściwości, które można wyświetlić. Widok automatycznie powoduje, że właściwość **model urządzenia** jest _właściwością urządzenia tylko do odczytu_.

![Zrzut ekranu pokazujący widok właściwości tylko do odczytu.](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementowanie właściwości zapisywalnych

Właściwości z możliwością zapisu są ustawiane przez operator w aplikacji IoT Central platformy Azure w formularzu. Usługa Azure IoT Central wysyła właściwość na urządzenie. Usługa Azure IoT Central oczekuje potwierdzenia z urządzenia.

Poniższy fragment kodu z modelu możliwości urządzenia pokazuje definicję typu właściwości możliwej do zapisu:

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

Klient urządzenia powinien wysłać ładunek JSON, który wygląda podobnie jak w poniższym przykładzie jako zgłoszonej właściwości w bliźniaczych urządzeniach:

``` json
{ "Brightness Level": 2 }
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
| `'ac': 200` | Zakończone | Operacja zmiany właściwości została pomyślnie ukończona. |
| `'ac': 202` lub `'ac': 201` | Oczekiwanie | Operacja zmiany właściwości jest w stanie oczekiwania lub w toku. |
| `'ac': 4xx` | Błąd | Żądana zmiana właściwości nie jest prawidłowa lub wystąpił błąd. |
| `'ac': 5xx` | Błąd | Urządzenie napotkało nieoczekiwany błąd podczas przetwarzania żądanej zmiany. |


Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Konfigurowanie urządzeń z poziomu usługi zaplecza](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Gdy operator ustawia właściwość zapisywalną w aplikacji IoT Central platformy Azure, aplikacja używa odpowiedniej właściwości przędzy urządzenia do wysłania wartości do urządzenia. Następnie urządzenie odpowiada za pomocą właściwości zgłoszonej przez urządzenie. Gdy usługa Azure IoT Central otrzymuje raportowaną wartość właściwości, aktualizuje widok właściwości ze stanem **zaakceptowane**.

W poniższym widoku są wyświetlane właściwości z możliwością zapisu. Po wprowadzeniu wartości i wybraniu opcji **Zapisz**stan początkowy jest **oczekujący**. Gdy urządzenie zaakceptuje zmiany, stan zmieni się na **zaakceptowane**.

![Zrzut ekranu pokazujący stan oczekiwania.](./media/howto-use-properties/status-pending.png)

![Zrzut ekranu przedstawiający zaakceptowaną właściwość.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać właściwości w aplikacji IoT Central platformy Azure, zobacz:

* [Ładunków](concepts-telemetry-properties-commands.md)
* [Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
