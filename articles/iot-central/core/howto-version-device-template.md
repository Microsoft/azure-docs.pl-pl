---
title: Informacje o wersji szablonów urządzeń dla aplikacji IoT Central platformy Azure | Microsoft Docs
description: Wykonaj iterację szablonów urządzeń, tworząc nowe wersje i bez wpływu na urządzenia połączone na żywo
author: philmea
ms.author: philmea
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 3c13c0b8cb118df877642328fa1b5512be31cffa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014430"
---
# <a name="create-a-new-device-template-version"></a>Utwórz nową wersję szablonu urządzenia

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Szablon urządzenia zawiera schemat opisujący sposób interakcji urządzenia z IoT Central. Interakcje obejmują dane telemetryczne, właściwości i polecenia. Zarówno urządzenie, jak i IoT Central aplikacji polegają na współużytkowanym zrozumieniu tego schematu do wymiany informacji. Można wprowadzać tylko ograniczone zmiany w schemacie bez przerywania kontraktu, dlatego większość zmian schematu wymaga nowej wersji szablonu urządzenia. Przechowywanie wersji szablonu urządzenia umożliwia dalszą pracę ze starszymi wersjami schematu, które rozumieją, a nowsze lub zaktualizowane urządzenia używają nowszej wersji schematu.

Schemat w szablonie urządzenia jest zdefiniowany w modelu możliwości urządzenia (DCM) i jego interfejsach. Szablony urządzeń obejmują inne informacje, takie jak właściwości chmury, ustawienia wyświetlania i widoki. W przypadku wprowadzenia zmian w tych częściach szablonu urządzenia, które nie definiują sposobu, w jaki urządzenie wymienia dane z IoT Central, nie trzeba zmieniać wersji szablonu.

Należy opublikować wszystkie zmiany szablonów urządzeń, niezależnie od tego, czy wymagają one aktualizacji wersji, zanim operator będzie mógł z niego korzystać. IoT Central uniemożliwia publikowanie istotnych zmian w szablonie urządzenia bez wcześniejszej wersji szablonu.

> [!NOTE]
> Aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia [, zobacz Konfigurowanie szablonu urządzenia i zarządzanie nim](howto-set-up-template.md)

## <a name="versioning-rules"></a>Reguły obsługi wersji

Ta sekcja zawiera podsumowanie reguł dotyczących wersji, które są stosowane do szablonów urządzeń. Zarówno DCMs, jak i interfejsy mają numery wersji. Poniższy fragment kodu przedstawia DCM dla urządzenia czujnika środowiska. DCM ma dwa interfejsy: **DeviceInformation** i **EnvironmentalSensor**. Numery wersji na końcu pól są widoczne `@id` . Aby wyświetlić te informacje w interfejsie użytkownika IoT Central, wybierz pozycję **Wyświetl tożsamość** w edytorze szablonów urządzeń.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Po opublikowaniu DCM nie można usunąć żadnych interfejsów, nawet w nowej wersji szablonu urządzenia.
* Po opublikowaniu DCM można dodać interfejs, jeśli tworzysz nową wersję szablonu urządzenia.
* Po opublikowaniu DCM można zastąpić interfejs nowszą wersją, jeśli zostanie utworzona nowa wersja szablonu urządzenia. Na przykład jeśli szablon urządzenia czujnika V1 korzysta z interfejsu EnvironmentalSensor V1, można utworzyć szablon urządzenia z czujnikiem v2, który korzysta z interfejsu EnvironmentalSensor v2.
* Po opublikowaniu interfejsu nie można usunąć żadnej zawartości interfejsu, nawet w nowej wersji szablonu urządzenia.
* Po opublikowaniu interfejsu można dodać elementy do zawartości interfejsu, jeśli tworzysz nową wersję interfejsu i szablonu urządzenia. Elementy, które można dodać do interfejsu, obejmują dane telemetryczne, właściwości i polecenia.
* Po opublikowaniu interfejsu można wprowadzić zmiany niebędące schematem istniejących elementów w interfejsie, jeśli tworzysz nową wersję interfejsu i szablonu urządzenia. Elementy niebędące schematem elementu interfejsu obejmują nazwę wyświetlaną i typ semantyczny. Części schematu elementu interfejsu, którego nie można zmienić, to nazwa, typ możliwości i schemat.

W poniższych sekcjach przedstawiono kilka przykładów modyfikowania szablonów urządzeń w programie IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Dostosowywanie szablonu urządzenia bez obsługi wersji

Niektóre elementy możliwości urządzenia można edytować bez konieczności korzystania z wersji szablonu i interfejsów urządzeń. Na przykład niektóre z tych pól obejmują nazwę wyświetlaną, typ semantyczny, wartość minimalną, wartość maksymalną, miejsca dziesiętne, kolor, jednostkę, jednostkę wyświetlania, komentarz i opis. Aby dodać jedno z następujących dostosowań:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia, który chcesz dostosować.
1. Wybierz kartę **Dostosowywanie** .
1. Wszystkie możliwości zdefiniowane w modelu możliwości urządzenia są wymienione tutaj. Można edytować, zapisywać i korzystać ze wszystkich tych pól bez konieczności podania wersji szablonu urządzenia. Jeśli istnieją pola, które chcesz edytować, są przeznaczone tylko do odczytu, aby je zmienić, musisz mieć wersję szablonu urządzenia. Wybierz pole, które chcesz edytować, a następnie wprowadź nowe wartości.
1. Kliknij pozycję **Zapisz**. Teraz te wartości zastępują wszystkie elementy, które zostały początkowo zapisane w szablonie urządzenia i są używane w całej aplikacji.

## <a name="version-a-device-template"></a>Wersja szablonu urządzenia

Utworzenie nowej wersji szablonu urządzenia spowoduje utworzenie wersji roboczej szablonu, w którym można edytować model możliwości urządzenia. Wszystkie opublikowane interfejsy pozostają opublikowane, dopóki nie zostaną one powiązane z wersjami. Aby zmodyfikować opublikowany interfejs, najpierw utwórz nową wersję szablonu urządzenia.

Tylko wersja szablonu urządzenia tylko wtedy, gdy próbujesz edytować część modelu możliwości urządzenia, którego nie można edytować w sekcji dostosowania.

Aby uzyskać wersję szablonu urządzenia:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia, do którego próbujesz uzyskać wersję.
1. Kliknij przycisk **wersji** w górnej części strony i nadaj szablonowi nową nazwę. IoT Central sugeruje nową nazwę, którą można edytować.
1. Kliknij pozycję **Utwórz**.
1. Teraz Twój szablon urządzenia jest w trybie wersji roboczej. Twoje interfejsy są nadal zablokowane. Wersje interfejsów, które chcesz zmodyfikować.

## <a name="version-an-interface"></a>Wersja interfejsu

Wersja interfejsu pozwala na dodawanie, aktualizowanie i usuwanie funkcji w interfejsie, który został już utworzony.

Aby uzyskać wersję interfejsu:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia w trybie wersji roboczej.
1. Wybierz interfejs, który znajduje się w trybie opublikowanym, który chcesz wersji i edytować.
1. Kliknij przycisk **wersji** w górnej części strony interfejsu.
1. Kliknij pozycję **Utwórz**.
1. Interfejs jest teraz w trybie wersji roboczej. Możesz dodawać lub edytować możliwości w interfejsie bez przerywania istniejących dostosowań i widoków.

## <a name="migrate-a-device-across-versions"></a>Migrowanie urządzenia w różnych wersjach

Można utworzyć wiele wersji szablonu urządzenia. Z biegiem czasu będziesz mieć wiele połączonych urządzeń korzystających z tych szablonów urządzeń. Możliwe jest Migrowanie urządzeń z jednej wersji szablonu urządzenia do innej. W poniższych krokach opisano sposób migrowania urządzenia:

1. Przejdź do strony **Device Explorer** .
1. Wybierz urządzenie, które ma być migrowane do innej wersji.
1. Wybierz pozycję **Migruj**.
1. Wybierz szablon urządzenia z numerem wersji, z którym chcesz migrować urządzenie, i wybierz pozycję **Migruj**.

![Jak migrować urządzenie](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś operatorem lub konstruktorem rozwiązań, sugerowanym następnym krokiem jest zapoznanie się z [tematem jak zarządzać urządzeniami](./howto-manage-devices.md).

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [Azure IoT Edge urządzeń i IoT Central platformy Azure](./concepts-iot-edge.md).
