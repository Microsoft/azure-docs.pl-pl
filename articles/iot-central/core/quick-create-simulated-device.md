---
title: Szybki start — dodawanie symulowanego urządzenia do Azure IoT Central
description: W tym przewodniku Szybki start pokazano, jak utworzyć szablon urządzenia i dodać symulowane urządzenie do IoT Central aplikacji.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 2eb0e3ce2ac20c89d9c0176ca3e7b33dc839c923
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718831"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Szybki start: dodawanie symulowanego urządzenia do aplikacji IoT Central aplikacji

Szablon urządzenia definiuje możliwości urządzenia, które łączy się z twoją IoT Central aplikacji. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które reaguje urządzenie. Za pomocą szablonu urządzenia można dodać do aplikacji zarówno rzeczywiste, jak i symulowane urządzenia. Symulowane urządzenia są przydatne do testowania zachowania aplikacji IoT Central przed podłączeniem rzeczywistych urządzeń.

W tym przewodniku Szybki start dodasz szablon urządzenia dla tablicy dewelopera ESP32-Azure zestawu IoT Kit i utworzysz urządzenie symulowane. Aby ukończyć ten przewodnik Szybki start, nie potrzebujesz rzeczywistego urządzenia, pracujesz z symulacją urządzenia. Urządzenie ESP32:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak maksymalna temperatura od momentu ponownego uruchomienia urządzenia.
* Odpowiada na polecenia, takie jak ponowne uruchomienie.
* Raportuje ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ przewodnik [Szybki start Tworzenie Azure IoT Central aplikacji,](./quick-deploy-iot-central.md) aby utworzyć aplikację IoT Central przy użyciu szablonu aplikacji niestandardowej > aplikacji **niestandardowej.**

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Aby dodać nowy szablon urządzenia do aplikacji, wybierz kartę **Szablony** urządzeń w okienku po lewej stronie.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Zrzut ekranu przedstawiający pustą listę szablonów urządzeń":::

Szablon urządzenia zawiera model urządzenia, który definiuje:

* Dane telemetryczne wysyłane przez urządzenie.
* Właściwości urządzenia.
* Polecenia, na które reaguje urządzenie.

### <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

Istnieje kilka opcji dodawania modelu urządzenia do IoT Central aplikacji. Możesz utworzyć model od podstaw, zaimportować model z pliku lub wybrać urządzenie z katalogu urządzeń. IoT Central obsługuje również podejście oparte na *urządzeniu,* w którym aplikacja automatycznie importuje model z repozytorium, gdy rzeczywiste urządzenie łączy się po raz pierwszy.

W tym przewodniku Szybki start wybierzesz urządzenie z katalogu urządzeń, aby utworzyć szablon urządzenia.

Poniższe kroki pokazują, jak zaimportować model dla urządzenia **ESP32** za pomocą wykazu urządzeń. Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon urządzenia, wybierz **pozycję + Nowe** na stronie **Szablony** urządzeń.

1. Na stronie **Wybierz typ** przewiń w dół do kafelka **ESP32-Azure IoT Kit** w sekcji Korzystanie ze wstępnie skonfigurowanego **szablonu** urządzenia.

1. Wybierz **kafelek ESP32-Azure IoT Kit,** a następnie wybierz pozycję **Dalej: Przegląd**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz.**

1. Po kilku sekundach zobaczysz nowy szablon urządzenia:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Zrzut ekranu przedstawiający szablon urządzenia dla urządzenia ESP32":::

    Nazwa szablonu to **Sensor Controller**. Model zawiera składniki, takie jak **sensor Controller,** **SensorTemp** i **interfejs informacji o urządzeniu.** Składniki definiują możliwości urządzenia ESP32. Możliwości obejmują telemetrię, właściwości i polecenia.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do urządzenia ani z nich odbierane. Aby dodać dwie właściwości chmury:

1. Wybierz **pozycję Właściwości chmury,** a **następnie pozycję + Dodaj właściwość chmury.** Skorzystaj z informacji w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia:

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date (Data)   |
    | Nazwa klienta     | Brak          | Ciąg |

1. Wybierz **pozycję Zapisz,** aby zapisać zmiany:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Zrzut ekranu przedstawiający dwie właściwości chmury":::

## <a name="views"></a>Widoki

Możesz dostosować aplikację, aby wyświetlała odpowiednie informacje o urządzeniu. Dostosowania umożliwiają innym użytkownikom zarządzanie urządzeniami połączonymi z aplikacją. Możesz utworzyć dwa typy widoków do interakcji z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń, w tym danych telemetrycznych, które wysyłają.

### <a name="default-views"></a>Widoki domyślne

Widoki domyślne to szybki sposób na wprowadzenie do wizualizowania ważnych informacji o urządzeniu. Szablon urządzenia może mieć maksymalnie trzy widoki domyślne:

* Widok **Polecenia** umożliwia wysyłanie poleceń do urządzenia.
* Widok **Przegląd** używa wykresów i metryk do wyświetlania danych telemetrycznych urządzenia.
* Widok **Informacje** zawiera właściwości urządzenia.

Wybierz węzeł **Widoki** w szablonie urządzenia. Po dodaniu IoT Central są generowane widoki  **Przegląd,** Informacje i Dane pierwotne. 

Aby dodać nowy formularz do zarządzania urządzeniem:

1. Wybierz węzeł **Widoki,** a następnie wybierz kafelek Edytowanie danych urządzenia i **chmury,** aby dodać nowy widok.

1. Zmień nazwę formularza na **Zarządzaj urządzeniem.**

1. Wybierz właściwości **chmury Customer Name (Nazwa** **klienta) i Last Service Date (Data ostatniej** usługi) i **Target Temperature (Temperatura docelowa).** Następnie wybierz **pozycję Dodaj sekcję**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Zrzut ekranu przedstawiający nowy formularz dodany do szablonu urządzenia":::

1. Wybierz **pozycję Zapisz,** aby zapisać nowy formularz.

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Zanim będzie można utworzyć urządzenie symulowane lub połączyć rzeczywiste urządzenie, należy opublikować szablon urządzenia. Chociaż IoT Central szablon został opublikowany podczas jego pierwszego tworzenia, należy opublikować zaktualizowaną wersję.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu **urządzenia Sensor Controller** na stronie **Szablony** urządzeń.

1. Wybierz **pozycję Publikuj** na pasku poleceń w górnej części strony.

1. W wyświetlonym oknie dialogowym wybierz pozycję **Opublikuj**.

Po opublikowaniu szablonu urządzenia będzie on widoczny na **stronie** Urządzenia. W opublikowanym szablonie urządzenia nie można edytować modelu urządzenia bez tworzenia nowej wersji. Można jednak modyfikować właściwości, dostosowania i widoki chmury w opublikowanym szablonie urządzenia bez użycia wersji. Po w związku z wprowadzeniem jakichkolwiek zmian wybierz pozycję **Publikuj,**  aby wypchnąć te zmiany do użycia dla rzeczywistych i symulowanych urządzeń.

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać symulowane urządzenie do aplikacji, należy użyć utworzonego szablonu **urządzenia ESP32.**

1. Aby dodać nowe urządzenie, wybierz **pozycję Urządzenia** w okienku po lewej stronie. Karta **Urządzenia** zawiera **wszystkie urządzenia i** szablon urządzenia **Kontroler** czujnika dla urządzenia ESP32. Wybierz **pozycję Kontroler czujnika.**

1. Aby dodać symulowane urządzenie DevKit, wybierz **pozycję + Nowe.** Użyj sugerowanego identyfikatora **urządzenia** lub wprowadź własny. Identyfikator urządzenia może zawierać litery, cyfry i `-` znak. Możesz również wprowadzić nazwę nowego urządzenia. Upewnij się, **że dla opcji Symulowanie tego urządzenia** ustawiono wartość **Tak,** a następnie wybierz pozycję **Utwórz.**

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Zrzut ekranu przedstawiający symulowane urządzenie Sensor Controller":::

Teraz możesz wchodzić w interakcje z widokami utworzonymi wcześniej przy użyciu symulowanych danych:

1. Wybieranie symulowanego urządzenia na **stronie** Urządzenia

    * Widok **Przegląd** przedstawia wykres symulowanych danych telemetrycznych:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądu dla urządzenia symulowanego":::

    * Widok **Informacje** zawiera wartości właściwości.

    * Widok **Polecenia** umożliwia uruchamianie poleceń, takich jak **ponowne** uruchomienie na urządzeniu.

    * Widok **Zarządzaj urządzeniami** to formularz utworzony w celu zarządzania urządzeniem.

    * Widok **Nieprzetworzone** dane umożliwia wyświetlanie nieprzetworzonych danych telemetrycznych i wartości właściwości wysyłanych przez urządzenie. Ten widok jest przydatny w przypadku debugowania urządzeń.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia szablonu urządzenia **kontrolera** czujnika dla urządzenia ESP32 i dodawania symulowanego urządzenia do aplikacji.

Aby dowiedzieć się więcej na temat monitorowania urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji](./quick-configure-rules.md)
