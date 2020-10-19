---
title: Szybki Start — Dodawanie symulowanego urządzenia do usługi Azure IoT Central
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia szablonu urządzenia i dodawania symulowanego urządzenia do aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 757575b31bde1a0e25cc3e70aad0cca0fdd45953
ms.sourcegitcommit: 94ca9e89501e65f4dcccc3789249357c7d5e27e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92170324"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Szybki Start: Dodawanie symulowanego urządzenia do aplikacji IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Szablon urządzenia definiuje możliwości urządzenia, które nawiązują połączenie z aplikacją IoT Central. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje. Z szablonu urządzenia Konstruktor lub operator mogą dodawać do aplikacji zarówno rzeczywiste, jak i symulowane urządzenia. Symulowane urządzenia są przydatne do testowania zachowania aplikacji IoT Central przed połączeniem rzeczywistych urządzeń.

W tym przewodniku szybki start dodasz szablon urządzenia do tablicy usługi *zestawu deweloperskiego IoT DevKit* (DevKit) i utworzysz symulowane urządzenie. Aby ukończyć ten przewodnik Szybki Start, nie jest potrzebne rzeczywiste urządzenie, pracujesz z symulacją urządzenia. Urządzenie DevKit:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak poziom jasności.
* Reaguje na polecenia, takie jak Włączanie i wyłączanie.
* Raporty ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > aplikacji niestandardowej** .

## <a name="create-a-template"></a>Tworzenie szablonu

Jako Konstruktor można tworzyć i edytować szablony urządzeń w aplikacji IoT Central. Po opublikowaniu szablonu urządzenia można wygenerować symulowane urządzenie lub połączyć rzeczywiste urządzenia z szablonu urządzenia. Symulowane urządzenia umożliwiają testowanie zachowania aplikacji przed połączeniem rzeczywistego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, wybierz kartę **Szablony urządzeń** w lewym okienku.

![Strona Szablony urządzeń](./media/quick-create-simulated-device/device-definitions.png)

Szablon urządzenia zawiera model możliwości urządzenia, który definiuje dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które odpowiada urządzenie.

### <a name="add-a-device-capability-model"></a>Dodawanie modelu możliwości urządzenia

Istnieje kilka opcji dodawania modelu możliwości urządzenia do aplikacji IoT Central. Można utworzyć model od podstaw, zaimportować model z pliku lub wybrać urządzenie z katalogu urządzeń. IoT Central obsługuje również podejście *pierwszego urządzenia* , które automatycznie importuje model z repozytorium, gdy urządzenie jest nawiązywane po raz pierwszy. W tym przewodniku Szybki Start wybierz urządzenie z katalogu urządzeń, aby zaimportować jego model możliwości urządzenia.

Poniższe kroki pokazują, jak używać katalogu urządzeń do importowania modelu możliwości urządzenia **zestawu deweloperskiego IoT DevKit** . Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon urządzenia, wybierz pozycję **+** na stronie **Szablony urządzeń** .

1. Na stronie **Wybieranie typu szablonu** przewiń w dół do momentu znalezienia kafelka **zestawu deweloperskiego IoT DevKit** .

1. Wybierz kafelek **zestawu deweloperskiego IoT DevKit** , a następnie wybierz przycisk **Next (dalej): Dostosuj**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

1. Po kilku sekundach zobaczysz nowy szablon urządzenia:

    ![Szablon urządzenia zestawu deweloperskiego IoT DevKit](./media/quick-create-simulated-device/devkit-template.png)

    Model możliwości zestawu deweloperskiego IoT DevKit obejmuje interfejsy, takie jak **mxchip_sensor**, **Mxchip_settings**i **Informacje o urządzeniu**. Interfejsy definiują możliwości urządzenia zestawu deweloperskiego IoT DevKit. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia. Aby dodać właściwość chmury:

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia:

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date   |
    | Nazwa klienta     | Brak          | Ciąg |

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

    ![Właściwości chmury](media/quick-create-simulated-device/cloud-properties.png)

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

1. Wybierz **nazwę klienta** i właściwości chmury **Data ostatniej usługi** oraz Właściwość **szybkość wentylatora** . Następnie wybierz pozycję **Dodaj sekcję**:

    ![Utwórz nowy formularz](media/quick-create-simulated-device/new-form.png)

1. Wybierz pozycję **Zapisz** , aby zapisać nowy formularz.

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowane urządzenie lub połączyć rzeczywiste urządzenie, należy opublikować szablon urządzenia. Mimo że IoT Central opublikowanie szablonu podczas jego pierwszego tworzenia, należy opublikować zaktualizowaną wersję.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

1. Wybierz pozycję **Publikuj**:

    ![Opublikowany model](media/quick-create-simulated-device/published-model.png)

1. W oknie dialogowym **publikowanie tego szablonu urządzenia w aplikacji** wybierz pozycję **Publikuj**. 

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** . W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj**  , aby wypchnąć te zmiany do operatora.

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać symulowane urządzenie do aplikacji, należy użyć utworzonego szablonu urządzenia **zestawu deweloperskiego IoT DevKit** .

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w lewym okienku. Na karcie **urządzenia** są wyświetlane **wszystkie urządzenia** i szablon urządzenia **zestawu deweloperskiego IoT DevKit** . Wybierz pozycję **zestawu deweloperskiego IoT DevKit**.

1. Aby dodać symulowane urządzenie DevKit, wybierz opcję **+** . Użyj sugerowanego **identyfikatora urządzenia** lub wprowadź własny. Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki. Możesz również wprowadzić nazwę nowego urządzenia. Upewnij się, że **symulowany** przełącznik jest **włączony** , a następnie wybierz pozycję **Utwórz**.

    ![Symulowane urządzenie](./media/quick-create-simulated-device/simulated-device.png)

Teraz można korzystać z widoków, które zostały utworzone przez konstruktora dla szablonu urządzenia przy użyciu symulowanych danych:

1. Wybierz urządzenie symulowane na stronie **urządzenia**

    * Widok **przeglądu** przedstawia wykres symulowanych danych telemetrycznych:

        ![Widok przeglądu](./media/quick-create-simulated-device/simulated-telemetry.png)

    * Widok **informacje** zawiera wartości właściwości, w tym właściwości chmury dodane do widoku.

    * Widok **poleceń** umożliwia uruchamianie poleceń, takich jak **miganie** na urządzeniu.

    * Widok **Zarządzanie urządzeniami** jest formularzem utworzonym dla operatora, który ma zarządzać urządzeniem.

    * Widok **nieprzetworzonych danych** umożliwia wyświetlanie nieprzetworzonych wartości danych telemetrycznych i właściwości wysyłanych przez urządzenie. Ten widok jest przydatny w przypadku debugowania urządzeń.

## <a name="use-a-simulated-device-to-improve-views"></a>Ulepszanie widoków przy użyciu symulowanego urządzenia

Po utworzeniu nowego symulowanego urządzenia, Konstruktor może używać tego urządzenia do dalszego ulepszania i kompilowania widoków dla szablonu urządzenia.

1. W lewym okienku wybierz pozycję **Szablony urządzeń** , a następnie wybierz szablon **zestawu deweloperskiego IoT DevKit** .

1. Wybierz dowolne z widoków, które chcesz edytować, lub Utwórz nowy widok. Wybierz pozycję **Konfiguruj urządzenie w wersji zapoznawczej**, a następnie **Wybierz pozycję z uruchomionego urządzenia**. W tym miejscu możesz wybrać opcję braku urządzenia do wyświetlania podglądu, rzeczywistego urządzenia skonfigurowanego do testowania lub istniejącego urządzenia, które zostało dodane do IoT Central.

1. Na liście Wybierz swoje symulowane urządzenie. Następnie wybierz przycisk **Zastosuj**. Teraz można zobaczyć to samo symulowane urządzenie w widokach szablonów urządzeń. Ten widok jest przydatny w przypadku wykresów i innych wizualizacji.

    ![Konfigurowanie urządzenia w wersji zapoznawczej](./media/quick-create-simulated-device/configure-preview.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia szablonu urządzenia **zestawu deweloperskiego IoT DevKit** i dodawania symulowanego urządzenia do aplikacji.

Aby dowiedzieć się więcej o monitorowaniu urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji](./quick-configure-rules.md)
