---
title: Opis wersji szablonu urządzenia dla aplikacji Azure IoT Central i | Microsoft Docs
description: Iteruj szablony urządzeń, tworząc nowe wersje bez wpływu na połączone urządzenia na żywo
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715050"
---
# <a name="create-a-new-device-template-version"></a>Tworzenie nowej wersji szablonu urządzenia

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Szablon urządzenia zawiera schemat, który opisuje sposób interakcji urządzenia z IoT Central. Te interakcje obejmują telemetrię, właściwości i polecenia. Zarówno urządzenie, jak i aplikacja IoT Central korzystają ze wspólnego zrozumienia tego schematu w celu wymiany informacji. Można wprowadzać tylko ograniczone zmiany schematu bez przerywania kontraktu, dlatego większość zmian schematu wymaga nowej wersji szablonu urządzenia. Wersja szablonu urządzenia umożliwia starszym urządzeniem kontynuowanie pracy ze zrozumiałą wersją schematu, podczas gdy nowsze lub zaktualizowane urządzenia używają nowszej wersji schematu.

Schemat w szablonie urządzenia jest definiowany w modelu urządzenia i jego interfejsach. Szablony urządzeń zawierają inne informacje, takie jak właściwości chmury, dostosowania wyświetlania i widoki. Jeśli wprowadzasz zmiany w tych częściach szablonu urządzenia, które nie definiują sposobu, w jaki urządzenie wymienia dane z IoT Central, nie musisz wersji szablonu.

Należy zawsze opublikować zaktualizowany szablon urządzenia, zanim operator będzie z niego korzystać. IoT Central umożliwia publikowanie zmian, które nie są istotne, w szablonie urządzenia bez konieczności wcześniejszego wersjonarowania szablonu.

> [!NOTE]
> Aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia, zobacz Konfigurowanie szablonu [urządzenia i zarządzanie nim](howto-set-up-template.md)

## <a name="versioning-rules"></a>Reguły wersji

Ta sekcja zawiera podsumowanie reguł dotyczących wersji, które mają zastosowanie do szablonów urządzeń. Zarówno modele urządzeń, jak i interfejsy mają numery wersji. Poniższy fragment kodu przedstawia model urządzenia dla urządzenia termostatu. Model urządzenia ma jeden interfejs. Numer wersji jest wyświetlony na końcu `@id` pola.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Aby wyświetlić te informacje w interfejsie IoT Central użytkownika, wybierz pozycję **Wyświetl tożsamość** w edytorze szablonów urządzeń:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Wyświetlanie tożsamości interfejsu w celu wyświetlenia numeru wersji":::

Na poniższej liście przedstawiono reguły, które określają, kiedy należy utworzyć nową wersję:

* Po opublikowaniu modelu urządzenia nie można usunąć żadnych interfejsów, nawet w nowej wersji szablonu urządzenia.
* Po opublikowaniu modelu urządzenia możesz dodać interfejs, jeśli utworzysz nową wersję szablonu urządzenia.
* Po opublikowaniu modelu urządzenia możesz zastąpić interfejs nowszą wersją, jeśli utworzysz nową wersję szablonu urządzenia. Jeśli na przykład szablon urządzenia sensor v1 używa interfejsu termostatu v1, można utworzyć szablon urządzenia czujnika w wersji 2, który używa interfejsu termostatu v2.
* Po opublikowaniu interfejsu nie można usunąć żadnej zawartości interfejsu, nawet w nowej wersji szablonu urządzenia.
* Po opublikowaniu interfejsu możesz dodać elementy do zawartości interfejsu, jeśli utworzysz nową wersję interfejsu i szablonu urządzenia. Elementy, które można dodać do interfejsu, obejmują dane telemetryczne, właściwości i polecenia.
* Po opublikowaniu interfejsu można wprowadzać zmiany inne niż schematu w istniejących elementach interfejsu, jeśli utworzysz nową wersję interfejsu i szablonu urządzenia. Części elementu interfejsu, które nie są schematami, obejmują nazwę wyświetlaną i typ semantyczny. Części schematu elementu interfejsu, których nie można zmienić, to nazwa, typ możliwości i schemat.

W poniższych sekcjach przedstawiono kilka przykładów modyfikowania szablonów urządzeń w IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Dostosowywanie szablonu urządzenia bez użycia wersji

Niektóre elementy możliwości urządzenia można edytować bez konieczności zmieniania wersji szablonu i interfejsów urządzenia. Na przykład niektóre z tych pól obejmują nazwę wyświetlaną, typ semantyczny, wartość minimalną, wartość maksymalną, miejsca dziesiętne, kolor, jednostkę, jednostkę wyświetlania, komentarz i opis. Aby dodać jedno z tych dostosowań:

1. Przejdź do **strony Szablony** urządzeń.
1. Wybierz szablon urządzenia, który chcesz dostosować.
1. Wybierz **kartę** Dostosowywanie.
1. Wszystkie możliwości zdefiniowane w modelu urządzenia są wymienione tutaj. Możesz edytować, zapisywać i używać wszystkich tych pól bez konieczności zmieniania wersji szablonu urządzenia. Jeśli istnieją pola, które chcesz edytować, są tylko do odczytu, musisz zmienić wersję szablonu urządzenia. Wybierz pole, które chcesz edytować, i wprowadź dowolne nowe wartości.
1. Wybierz pozycję **Zapisz**. Teraz te wartości zastępują wszystkie wartości, które zostały początkowo zapisane w szablonie urządzenia i są używane w całej aplikacji.

## <a name="version-a-device-template"></a>Wersja szablonu urządzenia

Utworzenie nowej wersji szablonu urządzenia powoduje utworzenie wersji roboczej szablonu, w której można edytować model urządzenia. Wszystkie opublikowane interfejsy pozostają opublikowane do momentu ich indywidualnej wersji. Aby zmodyfikować opublikowany interfejs, najpierw utwórz nową wersję szablonu urządzenia.

Wersją szablonu urządzenia jest tylko próba edytowania części modelu urządzenia, których nie można edytować w sekcji dostosowań.

Aby wersji szablonu urządzenia:

1. Przejdź do **strony Szablony** urządzeń.
1. Wybierz szablon urządzenia, który chcesz wersjonarować.
1. Wybierz przycisk **Wersja** w górnej części strony i nadaj szablonowi nową nazwę. IoT Central sugeruje nową nazwę, którą można edytować.
1. Wybierz przycisk **Utwórz**.
1. Teraz szablon urządzenia jest w trybie wersji roboczej. Możesz zobaczyć, że interfejsy są nadal zablokowane. Wersją wszystkich interfejsów, które chcesz zmodyfikować.

## <a name="version-an-interface"></a>Wersja interfejsu

Wersja interfejsu umożliwia dodawanie i aktualizowanie możliwości wewnątrz interfejsu, który został już utworzony.

Aby wersji interfejsu:

1. Przejdź do **strony Szablony** urządzeń.
1. Wybierz szablon urządzenia w trybie wersji roboczej.
1. Wybierz interfejs, który jest w trybie publikowanym, którego wersję i edycję chcesz edytować.
1. Wybierz przycisk **Wersja** w górnej części strony interfejsu.
1. Wybierz przycisk **Utwórz**.
1. Teraz interfejs jest w trybie wersji roboczej. Do interfejsu można dodawać lub edytować możliwości bez przerywania istniejących dostosowań i widoków.

## <a name="migrate-a-device-across-versions"></a>Migrowanie urządzenia między wersjami

Można utworzyć wiele wersji szablonu urządzenia. Z czasem będziesz mieć wiele połączonych urządzeń przy użyciu tych szablonów urządzeń. Urządzenia można migrować z jednej wersji szablonu urządzenia do innej. W poniższych krokach opisano sposób migrowania urządzenia:

1. Przejdź do strony **Urządzenia**.
1. Wybierz urządzenie, które chcesz migrować do innej wersji.
1. Wybierz **pozycję**  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Migruj: wybierz opcję, aby rozpocząć migrację urządzenia":::
1. Wybierz szablon urządzenia z numerem wersji, do którego chcesz przeprowadzić migrację urządzenia, a następnie wybierz pozycję **Migruj.**

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś operatorem lub konstruktorem rozwiązań, sugerowanym następnym krokiem jest nauczenia się zarządzania [urządzeniami.](./howto-manage-devices.md)

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest przeczytanie informacji o urządzeniach Azure IoT Edge [i Azure IoT Central](./concepts-iot-edge.md).
