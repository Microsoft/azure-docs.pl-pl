---
title: plik dołączany
description: plik dołączany
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877209"
---
### <a name="add-relationships"></a>Dodaj relacje

W szablonie urządzenia **bramy LVA Edge** w obszarze **moduły/LVA krawędź bramy**wybierz pozycję **relacje**. Wybierz pozycję **+ Dodaj relację** i Dodaj następujące dwie relacje:

|Nazwa wyświetlana               |Nazwa          |Cel |
|-------------------------- |------------- |------ |
|Detektor ruchu LVA Edge   |Używanie wartości domyślnej   |Urządzenie czujnika ruchu LVA Edge |
|LVA — detektor obiektów krawędzi   |Używanie wartości domyślnej   |Urządzenie do wykrywania obiektów brzegowych LVA |

Następnie wybierz przycisk **Zapisz**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Dodaj relacje":::

### <a name="add-views"></a>Dodawanie widoków

Szablon urządzenia **bramy LVA Edge** nie zawiera żadnych definicji widoku.

Aby dodać widok do szablonu urządzenia:

1. W szablonie urządzenia **bramy LVA Edge** przejdź do **widoku widoki** i wybierz kafelek **Wizualizacja urządzenia** .

1. Wprowadź *urządzenie bramy LVA Edge* jako nazwę widoku.

1. Dodaj następujące kafelki do widoku:

    * Kafelek z właściwościami **informacji o urządzeniu** : **model urządzenia**, **producent**, **system operacyjny**, **architektura procesora**, **wersja oprogramowania**, **Łączna ilość pamięci**i **łączny magazyn**.
    * Kafelek wykresu liniowego z **bezpłatną ilością pamięci** i wartościami telemetrycznymi **pulsu systemowego** .
    * Kafelek historia zdarzeń z następującymi zdarzeniami: **Tworzenie aparatu**, **usuwanie aparatu**, **Ponowne uruchamianie modułu**, **moduł rozpoczęty**, **moduł został zatrzymany**.
    * Kafelek "2x1 Ostatnia znana wartość" pokazujący telemetrię **stanu klienta IoT Central** .
    * Kafelek "2x1 Ostatnia znana wartość" pokazujący telemetrię **stanu modułu** .
    * Kafelek 1x1 Ostatnia znana wartość przedstawiająca dane telemetryczne **pulsu systemu** .
    * Kafelek "1x1 Ostatnia znana wartość" pokazujący telemetrię **podłączonych kamer** .

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Dodaj relacje":::

1. Wybierz pozycję **Zapisz**.

### <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Aby można było dodać urządzenie do aplikacji, należy opublikować szablon urządzenia:

1. W szablonie urządzenia **bramy LVA Edge** wybierz pozycję **Publikuj**.

1. Na stronie **Publikuj ten szablon urządzenia na potrzeby aplikacji** wybierz pozycję **Publikuj**.

**Brama LVA Edge** jest teraz dostępna jako typ urządzenia do użycia na stronie **urządzenia** w aplikacji.

## <a name="add-a-gateway-device"></a>Dodawanie urządzenia bramy

Aby dodać urządzenie **bramy LVA Edge** do aplikacji:

1. Przejdź do strony **urządzenia** i wybierz szablon urządzenia **bramy LVA Edge** .

1. Wybierz pozycję **+ Nowe**.

1. W oknie dialogowym **Tworzenie nowego urządzenia** Zmień nazwę urządzenia na *LVA Gateway 001*i zmień identyfikator urządzenia na *LVA-Gateway-001*.

    > [!NOTE]
    > Identyfikator urządzenia musi być unikatowy w aplikacji.

1. Wybierz przycisk **Utwórz**.

Stan urządzenia jest **zarejestrowany**.

### <a name="get-the-device-credentials"></a>Pobieranie poświadczeń urządzenia

Wymagane są poświadczenia zezwalające urządzeniu na łączenie się z aplikacją IoT Central. Pobierz poświadczenia urządzenia:

1. Na stronie **urządzenia** wybierz utworzone urządzenie **LVA-Gateway-001** .

1. Wybierz pozycję **Połącz**.

1. Na stronie **połączenie urządzenia** zanotuj *scratchpad.txt* pliku z **zakresem identyfikatorów**, **identyfikatorem urządzenia**i **kluczem podstawowym**urządzenia. Te wartości są używane później.

1. Upewnij się, że metoda połączenia jest ustawiona na **sygnaturę dostępu współdzielonego**.

1. Wybierz pozycję **Close** (Zamknij).

## <a name="next-steps"></a>Następne kroki

Aplikacja IoT Central została utworzona przy użyciu szablonu **wideo Analytics — obiekty i wykrywanie ruchu** , utworzyła szablon urządzenia dla urządzenia bramy i dodał urządzenie bramy do aplikacji.

Jeśli chcesz wypróbować aplikację do wykrywania obiektów wideo i wykrycia ruchu przy użyciu modułów IoT Edge, na których uruchomiono maszynę wirtualną w chmurze z symulowanymi strumieniami wideo:

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia IoT Edge dla analiz wideo (maszyna wirtualna z systemem Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Jeśli chcesz wypróbować aplikację do wykrywania obiektów wideo i wykrycia ruchu przy użyciu modułów IoT Edge, na których działa rzeczywiste urządzenie z **ONVIFą** kamerą:

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia IoT Edge dla analiz wideo (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
