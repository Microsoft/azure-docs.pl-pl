---
title: Korzystanie z danych lokalizacji w rozwiązaniu IoT Central platformy Azure
description: Dowiedz się, jak używać danych lokalizacji wysyłanych z urządzenia połączonego z aplikacją IoT Central. Kreśl dane lokalizacji na mapie lub utwórz reguły geofencingu.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128024"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Korzystanie z danych lokalizacji w rozwiązaniu IoT Central platformy Azure

W tym artykule pokazano, jak używać danych lokalizacji w aplikacji IoT Central. Urządzenie podłączone do IoT Central może wysyłać dane lokalizacji jako strumień danych telemetrycznych lub używać właściwości urządzenia do raportowania danych lokalizacji.

Konstruktor rozwiązań może używać danych lokalizacji, aby:

* Wykreślanie zgłoszonej lokalizacji na mapie.
* Wykreśl mapę obiektów telemetrycznych Historia lokalizacji.
* Utwórz reguły geofencingu w celu powiadomienia operatora, gdy urządzenie przejdzie do określonego obszaru lub opuści je.

## <a name="add-location-capabilities-to-a-device-template"></a>Dodawanie możliwości lokalizacji do szablonu urządzenia

Poniższy zrzut ekranu przedstawia szablon urządzenia z przykładami właściwości urządzenia i typu telemetrii, które używają danych lokalizacji. Definicje używają typu semantyki **lokalizacji** i typu schematu **geolokalizacji** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Zrzut ekranu przedstawiający definicję właściwości lokalizacji w szablonie urządzenia":::

W przypadku dokumentacji definicje [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) dla tych funkcji wyglądają jak w poniższym fragmencie kodu:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Typ schematu **punktu geopunktowego** nie jest częścią [specyfikacji DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central obecnie obsługuje typ **schematu i** typ semantyczny **lokalizacji** w celu zapewnienia zgodności z poprzednimi wersjami.

## <a name="send-location-data-from-a-device"></a>Wysyłanie danych lokalizacji z urządzenia

Gdy urządzenie wysyła dane dla właściwości **DeviceLocation** pokazanej w poprzedniej sekcji, ładunek wygląda podobnie do następującego fragmentu kodu JSON:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Gdy urządzenie wysyła dane dla danych telemetrycznych **śledzenia** pokazanych w poprzedniej sekcji, ładunek wygląda podobnie do następującego fragmentu kodu JSON:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Wyświetl lokalizację urządzenia

Dane lokalizacji można wyświetlać w wielu miejscach w aplikacji IoT Central. Na przykład w widokach skojarzonych z konkretnym urządzeniem lub na pulpitach nawigacyjnych.

Podczas tworzenia widoku dla urządzenia można wybrać opcję wykreślania lokalizacji na mapie lub pokazać poszczególne wartości:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Zrzut ekranu przedstawiający przykładowy widok z danymi lokalizacji":::

Kafelki mapy można dodać do pulpitu nawigacyjnego, aby wykreślić lokalizację jednego lub kilku urządzeń. Po dodaniu kafelka mapy w celu wyświetlenia telemetrii lokalizacji można wykreślić lokalizację w danym okresie czasu. Poniższy zrzut ekranu przedstawia lokalizację zgłoszoną przez symulowane urządzenie w ciągu ostatnich 30 minut:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Zrzut ekranu przedstawiający przykładowy pulpit nawigacyjny z danymi lokalizacji":::

## <a name="create-a-geofencing-rule"></a>Tworzenie reguły geofencingu

Za pomocą telemetrii lokalizacji można utworzyć regułę geofencingu, która generuje alert, gdy urządzenie przejdzie do lub z prostokątnego obszaru. Poniższy zrzut ekranu przedstawia regułę, która używa czterech warunków do definiowania prostokątnego obszaru przy użyciu wartości szerokości i długości geograficznej. Reguła generuje wiadomość e-mail po przeniesieniu urządzenia w prostokątny obszar:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Zrzut ekranu pokazujący definicję reguły geogeofencinguj":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać właściwości w aplikacji IoT Central platformy Azure, zobacz:

* [Ładunków](concepts-telemetry-properties-commands.md)
* [Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central](tutorial-connect-device.md)