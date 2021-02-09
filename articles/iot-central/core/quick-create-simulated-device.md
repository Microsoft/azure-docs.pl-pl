---
title: Szybki Start — Dodawanie symulowanego urządzenia do usługi Azure IoT Central
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia szablonu urządzenia i dodawania symulowanego urządzenia do aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 488de7114d80e6a88cc619ba3b42f867c985ea11
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833919"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Szybki Start: Dodawanie symulowanego urządzenia do aplikacji IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Szablon urządzenia definiuje możliwości urządzenia, które nawiązują połączenie z aplikacją IoT Central. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje. Z szablonu urządzenia Konstruktor lub operator mogą dodawać do aplikacji zarówno rzeczywiste, jak i symulowane urządzenia. Symulowane urządzenia są przydatne do testowania zachowania aplikacji IoT Central przed połączeniem rzeczywistych urządzeń.

W tym przewodniku szybki start dodasz szablon urządzenia dla programu ESP32-Azure IoT Development i utworzysz symulowane urządzenie. Aby ukończyć ten przewodnik Szybki Start, nie jest potrzebne rzeczywiste urządzenie, pracujesz z symulacją urządzenia. Urządzenie ESP32:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak Maksymalna temperatura od momentu ponownego uruchomienia urządzenia.
* Reaguje na polecenia, takie jak ponowny rozruch.
* Raporty ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > aplikacji niestandardowej** .

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Jako Konstruktor można tworzyć i edytować szablony urządzeń w aplikacji IoT Central. Po opublikowaniu szablonu urządzenia można wygenerować symulowane urządzenie lub połączyć rzeczywiste urządzenia z szablonu urządzenia. Symulowane urządzenia umożliwiają testowanie zachowania aplikacji przed połączeniem rzeczywistego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, wybierz kartę **Szablony urządzeń** w lewym okienku.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Zrzut ekranu przedstawiający pustą listę szablonów urządzeń":::

Szablon urządzenia zawiera model urządzenia, który definiuje:

* Dane telemetryczne wysyłane przez urządzenie.
* Właściwości urządzenia.
* Polecenia, na które urządzenie reaguje.

### <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

Istnieje kilka opcji dodawania modelu urządzenia do aplikacji IoT Central. Można utworzyć model od podstaw, zaimportować model z pliku lub wybrać urządzenie z katalogu urządzeń. IoT Central obsługuje również *urządzenia* , w których aplikacja automatycznie importuje model z repozytorium, gdy rzeczywiste urządzenie nawiązuje połączenie po raz pierwszy.

W tym przewodniku Szybki Start wybierz urządzenie z katalogu urządzeń, aby utworzyć szablon urządzenia.

Poniższe kroki pokazują, jak używać katalogu urządzeń do importowania modelu dla urządzenia **ESP32** . Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon urządzenia, wybierz pozycję **+ Nowy** na stronie **Szablony urządzeń** .

1. Na stronie **Wybieranie typu** przewiń w dół do momentu znalezienia kafelka **ESP32-Azure IoT Kit** w sekcji **Korzystanie ze wstępnie skonfigurowanego szablonu urządzenia** .

1. Wybierz kafelek **ESP32-Azure IoT Kit** , a następnie wybierz pozycję **Next (dalej): przegląd**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

1. Po kilku sekundach zobaczysz nowy szablon urządzenia:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Zrzut ekranu przedstawiający szablon urządzenia dla urządzenia ESP32":::

    Nazwa szablonu to **kontroler czujnika**. Model zawiera składniki, takie jak **kontroler czujnika**, **SensorTemp** i **interfejs informacji o urządzeniu**. Składniki definiują możliwości urządzenia ESP32. Możliwości obejmują dane telemetryczne, właściwości i polecenia.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia. Aby dodać dwie właściwości chmury:

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia:

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date (Data)   |
    | Nazwa klienta     | Brak          | Ciąg |

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Zrzut ekranu przedstawiający dwie właściwości chmury":::

## <a name="views"></a>Widoki

Jako Konstruktor można dostosować aplikację w taki sposób, aby wyświetlała odpowiednie informacje o urządzeniu do operatora. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń, w tym dane telemetryczne, które wysyłają.

### <a name="default-views"></a>Widoki domyślne

Widoki domyślne są szybkim sposobem na rozpoczęcie wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia można zdefiniować maksymalnie trzy widoki domyślne:

* Widok **poleceń** umożliwia operatorowi Wysyłanie poleceń do urządzenia.
* Widok **Przegląd** używa wykresów i metryk do wyświetlania danych telemetrycznych urządzenia.
* W widoku **informacje** są wyświetlane właściwości urządzenia.

Wybierz węzeł **widoki** w szablonie urządzenia. Możesz zobaczyć, że IoT Central wygenerował **Przegląd** i widok **informacje** dla Ciebie po dodaniu szablonu.

Aby dodać nowy formularz **zarządzania urządzeniem** , którego operator może używać do zarządzania urządzeniem:

1. Wybierz węzeł **widoki** , a następnie wybierz kafelek **Edytowanie urządzenia i danych w chmurze** , aby dodać nowy widok.

1. Zmień nazwę formularza na, aby **zarządzać urządzeniem**.

1. Wybierz **nazwę klienta** i właściwości chmury **Data ostatniej usługi** oraz Właściwość **temperatury docelowej** . Następnie wybierz pozycję **Dodaj sekcję**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Zrzut ekranu przedstawiający nowy formularz dodany do szablonu urządzenia":::

1. Wybierz pozycję **Zapisz** , aby zapisać nowy formularz.

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowane urządzenie lub połączyć rzeczywiste urządzenie, należy opublikować szablon urządzenia. Mimo że IoT Central opublikowanie szablonu podczas jego pierwszego tworzenia, należy opublikować zaktualizowaną wersję.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia **kontrolera czujnika** ze strony **Szablony urządzeń** .

1. Wybierz pozycję **Publikuj**:

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="Zrzut ekranu przedstawiający lokalizację ikony publikowania":::

1. W oknie dialogowym **publikowanie tego szablonu urządzenia w aplikacji** wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** . W opublikowanym szablonie urządzenia nie można edytować modelu urządzenia bez tworzenia nowej wersji. Można jednak zmodyfikować właściwości, dostosowania i widoki chmury w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj**  , aby wypchnąć te zmiany do operatora.

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać symulowane urządzenie do aplikacji, należy użyć utworzonego szablonu urządzenia **ESP32** .

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w lewym okienku. Na karcie **urządzenia** są wyświetlane **wszystkie urządzenia** i szablon urządzenia **kontrolera czujnika** dla urządzenia ESP32. Wybierz pozycję **kontroler czujnika**.

1. Aby dodać symulowane urządzenie DevKit, wybierz pozycję **+ Nowy**. Użyj sugerowanego **identyfikatora urządzenia** lub wprowadź własny. Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki. Możesz również wprowadzić nazwę nowego urządzenia. Upewnij się, że **to urządzenie** ma ustawioną wartość **tak** , a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Zrzut ekranu przedstawiający urządzenie kontrolera czujnika symulowanego":::

Teraz można korzystać z widoków, które zostały utworzone przez konstruktora dla szablonu urządzenia przy użyciu symulowanych danych:

1. Wybierz urządzenie symulowane na stronie **urządzenia**

    * Widok **przeglądu** przedstawia wykres symulowanych danych telemetrycznych:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądu symulowanego urządzenia":::

    * W widoku **informacje** są wyświetlane wartości właściwości.

    * Widok **poleceń** umożliwia uruchamianie poleceń, takich jak **Ponowne uruchamianie** na urządzeniu.

    * Widok **Zarządzanie urządzeniami** jest formularzem utworzonym dla operatora, który ma zarządzać urządzeniem.

    * Widok **nieprzetworzonych danych** umożliwia wyświetlanie nieprzetworzonych wartości danych telemetrycznych i właściwości wysyłanych przez urządzenie. Ten widok jest przydatny w przypadku debugowania urządzeń.

## <a name="use-a-simulated-device-to-improve-views"></a>Ulepszanie widoków przy użyciu symulowanego urządzenia

Po utworzeniu nowego symulowanego urządzenia, Konstruktor może używać tego urządzenia do dalszego ulepszania i kompilowania widoków dla szablonu urządzenia.

1. W lewym okienku wybierz pozycję **Szablony urządzeń** , a następnie wybierz szablon **kontroler czujnika** .

1. Wybierz dowolne z widoków, które chcesz edytować, takie jak **Przegląd**, lub Utwórz nowy widok. Wybierz pozycję **Konfiguruj urządzenie w wersji zapoznawczej**, a następnie **Wybierz pozycję z uruchomionego urządzenia**. W tym miejscu możesz wybrać opcję braku urządzenia do wyświetlania podglądu, rzeczywistego urządzenia skonfigurowanego do testowania lub istniejącego urządzenia, które zostało dodane do IoT Central.

1. Na liście Wybierz swoje symulowane urządzenie. Następnie wybierz przycisk **Zastosuj**. Teraz można zobaczyć to samo symulowane urządzenie w widokach szablonów urządzeń. Ten widok jest przydatny w przypadku wykresów i innych wizualizacji.

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="Zrzut ekranu przedstawiający skonfigurowane urządzenie podglądu":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia szablonu urządzenia **kontrolera czujnika** dla urządzenia ESP32 i dodawania symulowanego urządzenia do aplikacji.

Aby dowiedzieć się więcej o monitorowaniu urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji](./quick-configure-rules.md)
