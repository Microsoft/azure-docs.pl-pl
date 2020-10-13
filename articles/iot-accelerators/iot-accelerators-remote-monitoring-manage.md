---
title: Konfigurowanie urządzeń w rozwiązaniu do zdalnego monitorowania — Azure | Microsoft Docs
description: W tym samouczku pokazano, w jaki sposób konfigurować urządzenia połączone z akceleratorem rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 432386809596fb2ef040a05d1fe0d12294a1abef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534526"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Samouczek: konfigurowanie urządzeń połączonych z rozwiązaniem do monitorowania

W tym samouczku użyjesz akceleratora rozwiązania do monitorowania zdalnego, aby skonfigurować połączone urządzenia IoT i nimi zarządzać. Dodasz nowe urządzenie do akceleratora rozwiązań i skonfigurujesz je.

Firma Contoso zamówiła nowe maszyny, aby rozwinąć jedną ze swoich placówek. Czekając na dostawę nowych maszyn, chcesz uruchomić symulację, aby przetestować działanie rozwiązania. Aby uruchomić symulację, dodasz nowy symulowany silnik do akceleratora rozwiązania do monitorowania zdalnego i sprawdzisz, czy symulowane urządzenie prawidłowo reaguje na aktualizacje konfiguracji. W tym samouczku używane są urządzenia symulowane, ale deweloper urządzeń może wdrażać metody bezpośrednie na [rzeczywistym urządzeniu połączonym z akceleratorem rozwiązania do monitorowania zdalnego](iot-accelerators-connecting-devices.md).

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Aprowizowanie symulowanego urządzenia.
> * Testowanie symulowanego urządzenia.
> * Zmiana konfiguracji urządzenia.
> * Organizowanie urządzeń.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Przejdź do strony **Device Explorer** w rozwiązaniu, a następnie kliknij przycisk **+ Nowe urządzenie**:

[![Udostępnianie symulowanego urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Na panelu **Nowe urządzenie** wybierz pozycję **Symulowane**, pozostaw liczbę urządzeń do aprowizacji **1**, wybierz model urządzenia **Faulty Engine** (Wadliwy silnik), a następnie wybierz polecenie **Zastosuj**, aby utworzyć symulowane urządzenie:

[![Udostępnianie symulowanego urządzenia z aparatem](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testowanie symulowanego urządzenia

Aby sprawdzić, czy symulowany silnik wysyła dane telemetryczne i zgłasza wartości właściwości, wybierz go z listy urządzeń na stronie **Device Explorer**. Bieżące informacje o silniku są wyświetlane na panelu **Szczegóły urządzenia**:

[![Wyświetlanie nowego symulowanego urządzenia z aparatem](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

W polu **Szczegóły urządzenia** sprawdź, czy nowe urządzenie wysyła dane telemetryczne. Aby wyświetlić przesyłany z urządzenia strumień danych telemetrycznych o wibracjach, kliknij pozycję **Vibration** (Wibracje):

[![Wybierz strumień danych telemetrycznych do wyświetlenia](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Panel **Szczegóły urządzenia** zawiera inne informacje dotyczące urządzenia, takie jak wartości tagów, obsługiwane metody oraz właściwości zgłaszane przez urządzenie.

Aby wyświetlić szczegółowe dane diagnostyczne, przewiń w dół zawartość panelu **Szczegóły urządzenia** aż do sekcji **Diagnostyka**.

## <a name="reconfigure-a-device"></a>Zmiana konfiguracji urządzenia

Aby przetestować aktualizowanie właściwości konfiguracji silnika, wybierz go z listy urządzeń na stronie **Device Explorer**. Kliknij przycisk **Zadania**, a następnie wybierz pozycję **Właściwości**. Na panelu Zadania wyświetlane są wartości właściwości, które można zaktualizować dla wybranego urządzenia:

[![Zmiana konfiguracji urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Aby zaktualizować lokalizację silnika, ustaw nazwę zadania **UpdateEngineLocation**, ustaw wartość długości geograficznej **-122,15**, ustaw lokalizację **Factory 2** (Fabryka 2), ustaw wartość szerokości geograficznej **47,62** i kliknij pozycję **Zastosuj**:

[![Zrzut ekranu przedstawiający stronę "Device Explorer" z wyróżnionym oknem "jobs".](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Aby śledzić stan zadania, kliknij pozycję **Wyświetl stan zadania**:

[![Aktualizowanie wartości właściwości urządzenia](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Po zakończeniu zadania przejdź do strony **Pulpit nawigacyjny**. Urządzenie zostanie wyświetlone na mapie w nowej lokalizacji:

[![Wyświetl lokalizację aparatu](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizowanie urządzeń

Aby ułatwić operatorom organizowanie urządzeń i zarządzanie nimi, warto je otagować przy użyciu nazwy zespołu. Firma Contoso ma dwa różne zespoły do wykonywania zadań serwisowych w terenie:

* Zespół Smart Vehicle zajmuje się ciężarówkami i urządzeniami do tworzenia prototypów.
* Zespół Smart Building zajmuje się chłodniami, podnośnikami i silnikami.

Aby wyświetlić wszystkie urządzenia, przejdź do strony **Device Explorer** i wybierz filtr **Wszystkie urządzenia**:

[![Pokaż wszystkie urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Dodawanie tagów

Zaznacz wszystkie urządzenia typu **Truck** (Ciężarówka) i **Prototyping** (Prototypy). Następnie kliknij pozycję **zadania**.

Na panelu **Zadania** wybierz pozycję **Tag**, ustaw nazwę zadania **AddConnectedVehicleTag**, a następnie dodaj tag tekstowy o nazwie **FieldService** z wartością **ConnectedVehicle**. Następnie kliknij pozycję **Zastosuj**:

[![Dodawanie tagu do urządzeń prototypowych i samochodowych](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na stronie Urządzenia zaznacz wszystkie urządzenia **Chiller** (Chłodnia), **Elevator** (Podnośnik) i **Engine** (Silnik). Następnie kliknij pozycję **zadania**.

Na panelu **Zadania** wybierz pozycję **Tag**, ustaw nazwę zadania **AddSmartBuildingTag**, a następnie dodaj tag tekstowy o nazwie **FieldService** z wartością **SmartBuilding**. Następnie kliknij pozycję **Zastosuj**:

[![Dodawanie tagu do urządzeń chłodniczych, wind i Engine](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Tworzenie filtrów

Teraz możesz użyć wartości tagów do utworzenia filtrów. Na stronie **Device Explorer** kliknij pozycję **Zarządzaj grupami urządzeń**:

[![Zarządzanie grupami urządzeń](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Utwórz filtr tekstowy, używając w warunku nazwy tagu **FieldService** i wartości **SmartBuilding**. Zapisz filtr jako **Smart Building**:

[![Utwórz inteligentny filtr konstrukcyjny](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Utwórz filtr tekstowy, używając w warunku nazwy tagu **FieldService** i wartości **ConnectedVehicle**. Zapisz filtr jako **Connected Vehicle**.

[![Zrzut ekranu przedstawiający stronę "Device Explorer" z wyróżnionym oknem "Zarządzaj grupami urządzeń".](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Teraz operator firmy Contoso może wykonać zapytanie dotyczące urządzeń według zespołu operacyjnego:

[![Utwórz filtr połączonego pojazdu](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, w jaki sposób skonfigurować urządzenia połączone z akceleratorem rozwiązania do monitorowania zdalnego i zarządzać nimi. Aby dowiedzieć się, jak używać akceleratora rozwiązań do przeprowadzania analizy głównej przyczyny w przypadku nieoczekiwanego alertu, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Przeprowadzanie analizy głównej przyczyny po wystąpieniu alertu](iot-accelerators-remote-monitoring-root-cause-analysis.md)
