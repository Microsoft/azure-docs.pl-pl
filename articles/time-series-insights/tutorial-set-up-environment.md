---
title: 'Samouczek: Konfigurowanie środowiska Gen2 — Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Samouczek: informacje dotyczące konfigurowania środowiska w Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464676"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Samouczek: Konfigurowanie środowiska Azure Time Series Insights Gen2

Ten samouczek przeprowadzi Cię przez proces tworzenia Azure Time Series Insights Gen2 *w trybie płatność zgodnie z rzeczywistym* użyciem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz środowisko Azure Time Series Insights Gen2.
> * Połącz środowisko Azure Time Series Insights Gen2 z IoT Hub.
> * Uruchom przykład akceleratora rozwiązania, aby przesłać strumieniowo dane do środowiska Azure Time Series Insights Gen2.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Co najmniej musisz mieć rolę **współautor** dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, przeczytaj temat [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).

* Utwórz środowisko przy użyciu [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) lub [interfejsu wiersza polecenia](how-to-create-environment-using-cli.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Zaloguj się przy użyciu konta platformy Azure, a następnie wybierz opcję **symulacja urządzenia**.

   [![Akcelerator rozwiązań usługi Azure IoT.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Przewiń w dół, aby przeczytać sekcję [Przegląd](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) i [wprowadzenie](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) .

1. Postępuj zgodnie z [instrukcjami dotyczącymi wdrożenia](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) w sekcji wprowadzenie.

   Ukończenie tego procesu może potrwać do 20 minut.

1. Po zakończeniu wdrażania otrzymasz adres URL do symulacji. Nie otwieraj tej strony, ponieważ wrócisz do niej później.

   >[!IMPORTANT]
   > Nie wprowadzaj jeszcze akceleratora rozwiązania. Pozostaw Tę stronę sieci Web otwartą, ponieważ wrócisz do niej później.

   [![Ukończono Inicjowanie obsługi rozwiązania symulacji urządzeń.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Teraz Sprawdź nowo utworzone zasoby w Azure Portal. Na stronie **grupy zasobów** Zwróć uwagę, że nowa grupa zasobów została utworzona przy użyciu `solutionName` podanej w pliku parametrów szablonu usługi ARM. Zanotuj zasoby, które zostały utworzone dla symulacji urządzenia.

   [![Zasoby symulacji urządzenia.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Tworzenie środowiska Azure Time Series Insights Gen2

W tej sekcji opisano sposób tworzenia środowiska Azure Time Series Insights Gen2 i łączenia go z usługą IoT Hub utworzoną przez Akcelerator rozwiązania IoT przy użyciu [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta subskrypcji platformy Azure.
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**.

   [![Wybierz zasób środowiska Time Series Insights.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. W okienku **Tworzenie środowiska Time Series Insights** na karcie **podstawowe** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska Azure Time Series Insights Gen2. |
    | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć Azure Time Series Insights środowisko Gen2. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji jako reszty zasobów IoT, które są tworzone przez symulatora urządzeń. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure Time Series Insights Gen2. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów co inne zasoby IoT, które są tworzone przez symulatora urządzeń. |
    | **Lokalizacja** | Wybierz region centrum danych dla środowiska Azure Time Series Insights Gen2. Aby uniknąć dodatkowego opóźnienia, najlepszym rozwiązaniem jest utworzenie środowiska Azure Time Series Insights Gen2 w tym samym regionie, w którym znajduje się centrum IoT utworzone w symulatorze urządzeń. |
    | **Warstwa** |  Wybierz pozycję **Gen2 (L1)**. Jest to jednostka SKU dla produktu Azure Time Series Insights Gen2. |
    | **Nazwa właściwości identyfikatora szeregów czasowych** | Wprowadź nazwę właściwości, która zawiera wartości, które jednoznacznie identyfikują wystąpienia szeregów czasowych. Nie można później zmienić wartości wprowadzonej w polu **Nazwa właściwości** jako identyfikatora szeregów czasowych. Na potrzeby tego samouczka wprowadź **_iothub-Connection-Device-ID_**. Aby dowiedzieć się więcej na temat identyfikatora szeregów czasowych, w tym złożonego identyfikatora szeregów czasowych, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./how-to-select-tsid.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę nowego konta magazynu.|
    | **Rodzaj konta magazynu** | Wybierz rodzaj magazynu dla nowego konta magazynu. Zalecamy StorageV2|
    | **Replikacja konta magazynu** | Wybierz rodzaj magazynu dla nowego konta magazynu. W zależności od wybranej lokalizacji można wybrać jedną z LRS, GRS i ZRS. Na potrzeby tego samouczka można wybrać LRS|
    | **Hierarchiczna przestrzeń nazw** |Ta opcja jest zaznaczona, po wybraniu rodzaju magazynu, który ma zostać StorageV2. Domyślnie jest on wyłączony. W tym samouczku można pozostawić go w domyślnym stanie *Disabled*|
    |**Włącz sklep ciepły**|Wybierz pozycję **tak** , aby włączyć sklep ciepły. To ustawienie można wyłączyć i włączyć je również po utworzeniu środowiska. |
    |**Przechowywanie danych (w dniach)**|Wybierz opcję domyślną 7 dni. |

    [![Nowa konfiguracja środowiska Azure Time Series Insights.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![Nowa konfiguracja środowiska Azure Time Series Insights, kontynuacja.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. Wybierz pozycję **Dalej: Źródło zdarzenia**.

   [![Skonfiguruj identyfikator szeregów czasowych dla środowiska.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Utworzyć źródło zdarzenia?** | Wybierz pozycję **Tak**.|
   | **Typ źródła** | Wybierz **IoT Hub**. |
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzenia. |
   | **Wybierz centrum** | Wybierz **pozycję Wybierz istniejące**. |
   | **Subskrypcja** | Wybierz subskrypcję używaną dla symulatora urządzeń. |
   | **Nazwa IoT Hub** | Wybierz nazwę usługi IoT Hub utworzoną dla symulatora urządzeń. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów IoT Hub** | Wybierz pozycję **Nowy**, wprowadź unikatową nazwę, a następnie wybierz pozycję **+ Dodaj**. Grupa konsumentów musi być unikatową wartością w Azure Time Series Insights Gen2. |
   | **Właściwość znacznika czasu** | Ta wartość służy do identyfikowania właściwości **timestamp** w przychodzących danych telemetrycznych. Na potrzeby tego samouczka pozostaw to pole puste. Ten symulator używa przychodzącej sygnatury czasowej z IoT Hub, która Azure Time Series Insights wartość domyślna Gen2. |

1. Wybierz pozycję **Przejrzyj i utwórz**.

   [![Skonfiguruj utworzone Centrum IoT Hub jako źródło zdarzenia.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. Wybierz pozycję **Przejrzyj i utwórz**.

    [![Przejrzyj i Utwórz stronę przy użyciu przycisku Utwórz.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Stan wdrożenia można sprawdzić:

    [![Powiadomienie o zakończeniu wdrażania.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Rozwiń węzeł Szczegóły wdrożenia.

## <a name="stream-data"></a>Strumieniowe przesyłanie danych

Po wdrożeniu środowiska Azure Time Series Insights Gen2 Rozpocznij przesyłanie strumieniowe danych na potrzeby analizy.

1. Po zakończeniu wdrażania akceleratora rozwiązań otrzymasz adres URL.

1. Kliknij adres URL, aby uruchomić symulację urządzenia.

1. Wybierz pozycję **+ Nowa symulacja**.

    1. Po załadowaniu strony **konfiguracji symulacji** wprowadź wymagane parametry.

        | Parametr | Akcja |
        | --- | --- |
        | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
        | **Opis** | Wprowadź definicję. |
        | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
        | **Model urządzenia** | Kliknij pozycję + **Dodaj typ urządzenia** <br />**Nazwa**: wprowadź **windę**. <br />**Kwota**: wprowadź wartość **3**. <br /> Pozostaw pozostałe wartości domyślne |
        | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

        [![Skonfiguruj parametry i Uruchom.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. Wybierz pozycję **Rozpocznij symulację**. Na pulpicie nawigacyjnym symulacji urządzenia są wyświetlane **aktywne urządzenia** i **całkowita liczba komunikatów** .

        [![Pulpit nawigacyjny symulacji usługi Azure IoT.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analizowanie danych

W tej sekcji przeprowadzasz podstawową analizę danych szeregów czasowych przy użyciu [Azure Time Series Insights Explorer Gen2](./concepts-ux-panels.md).

1. Przejdź do Azure Time Series Insights Gen2 Explorer, wybierając adres URL na stronie zasobów w [Azure Portal](https://portal.azure.com/).

    [![Adres URL Azure Time Series Insights Gen2 Explorer.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. W Eksploratorze Azure Time Series Insights Gen2 zostanie wyświetlony pasek z górną częścią ekranu. Jest to Twój selektor dostępności. Upewnij się, że wybrano co najmniej 2 2 m i w razie potrzeby rozwiń przedział czasu, zaznaczając i przeciągając uchwyty selektora w lewo i w prawo.

1. **Wystąpienia szeregów czasowych** będą wyświetlane po lewej stronie.

    [![Lista niewidocznych wystąpień.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. Wybierz wystąpienie serii pierwszy-czas. Następnie wybierz pozycję **Pokaż temperaturę**.

    [![Wybrano wystąpienie szeregów czasowych z poleceniem menu, aby wyświetlić średnią temperaturę.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Zostanie wyświetlony wykres szeregów czasowych. Zmień **Interwał** na **30 s**.

1. Powtórz poprzedni krok z innymi dwoma wystąpieniami szeregów czasowych, aby wyświetlić wszystkie trzy, jak pokazano na tym wykresie:

    [![Wykres dla wszystkich szeregów czasowych.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. Wybierz selektor przedziałów czasu w prawym górnym rogu. W tym miejscu możesz wybrać określone czasy rozpoczęcia i zakończenia w milisekundach lub wybrać spośród wstępnie skonfigurowanych opcji, takich jak **ostatnie 30 minut**. Możesz również zmienić domyślną strefę czasową.

    [![Ustaw zakres czasu na ostatni 30 minut.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    Postęp akceleratora rozwiązania w ciągu **ostatnich 30 minut** jest teraz wyświetlany w Eksploratorze Azure Time Series Insights Gen2.

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [model szeregów czasowych](./concepts-model-overview.md).

1. W Eksploratorze wybierz kartę **model** :

   [![Wyświetl kartę Model w Eksploratorze.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   Na karcie **typy** wybierz pozycję **+ Dodaj**.

1. Wprowadź następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wprowadź **windę** |
    | **Opis** | Wprowadź **tę definicję typu dla wind** |

1. Następnie wybierz kartę **zmienne** .

    1. Wybierz pozycję **+ Dodaj zmienną** i wypełnij następujące wartości dla pierwszej zmiennej typu Wind. W sumie są tworzone trzy zmienne.

        | Parametr | Akcja |
        | --- | --- |
        | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
        | **Rodzaj** | Wybierz wartość **numeryczną** |
        | **Wartość** | Wybierz jedną z ustawień predefiniowanych: wybierz pozycję **temperatura (Podwójna precyzja)**. <br /> Uwaga: może upłynąć kilka minut, zanim **wartość** zostanie automatycznie wypełniona, gdy Azure Time Series Insights Gen2 zacznie otrzymywać zdarzenia.|
        | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    1. Wybierz przycisk **Zastosuj**. Następnie ponownie **Dodaj zmienną** i ustaw następujące wartości:

        | Parametr | Akcja |
        | --- | --- |
        | **Nazwa** | Wprowadź **średnie wibracje**. |
        | **Rodzaj** | Wybierz wartość **numeryczną** |
        | **Wartość** | Wybierz spośród ustawień predefiniowanych: wybierz pozycję **drgania (Double)**. <br /> Uwaga: może upłynąć kilka minut, zanim **wartość** zostanie automatycznie wypełniona, gdy Azure Time Series Insights Gen2 zacznie otrzymywać zdarzenia.|
        | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    1. Wybierz przycisk **Zastosuj**. Następnie ponownie **Dodaj zmienną** i ustaw następujące wartości dla zmiennej trzeciej i końcowej:

        | Parametr | Akcja |
        | --- | --- |
        | **Nazwa** | Wprowadź **piętro**. |
        | **Rodzaj** | Wybierz **kategorii** |
        | **Wartość** | Wybierz z ustawień predefiniowanych: wybierz pozycję **piętro (Double)**. <br /> Uwaga: może upłynąć kilka minut, zanim **wartość** zostanie automatycznie wypełniona, gdy Azure Time Series Insights Gen2 zacznie otrzymywać zdarzenia.|
        | **Kategorie** | <span style="text-decoration: underline">Etykieta</span>   -  <span style="text-decoration: underline">Wartości</span> <br /> Niższy: 1, 2, 3, 4 <br /> Środek: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
        | **Kategoria domyślna** | Wprowadź **nieznany** |

        [![Dodaj zmienne typu.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. Wybierz przycisk **Zastosuj**.
    1. Wybierz pozycję **Zapisz**. Zostaną utworzone i wyświetlone trzy zmienne.

        [![Po dodaniu typu Sprawdź go w widoku model.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. Wybierz kartę **hierarchie** . Następnie wybierz pozycję **+ Dodaj**.

   1. W okienku **Edytuj hierarchię** ustaw następujące parametry:

        | Parametr | Akcja |
        | --- | ---|
        | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
        |**Poziomy**| Wprowadź **kraj** jako nazwę pierwszego poziomu<br />Wybierz pozycję **+ Dodaj poziom**<br />Wprowadź **miasto** dla drugiego poziomu, a następnie wybierz pozycję **+ Dodaj poziom**<br />Wprowadź wartość **konstrukcyjną** jako nazwę trzeciego i końcowego poziomu |

   1. Wybierz pozycję **Zapisz**.

        [![Wyświetl nową hierarchię w widoku model.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. Przejdź do **wystąpień**.

    1. W obszarze **Akcje** po prawej stronie i wybierz ikonę ołówka, aby zmodyfikować pierwsze wystąpienie o następujące wartości:

        | Parametr | Akcja |
        | --- | --- |
        | **Typ** | Wybierz pozycję **windy**. |
        | **Nazwa** | Wprowadź **windę 1**|
        | **Opis** | Wprowadź **wystąpienie dla wind 1** |

    1. Przejdź do **pól wystąpień** i wprowadź następujące wartości:

        | Parametr | Akcja |
        | --- | --- |
        | **Hierarchie** | Wybierz **hierarchię lokalizacji** |
        | **Kraj** | Wprowadź **Stan USA** |
        | **City (Miasto)** | Wprowadź **Seattle** |
        | **Budowaniu** | Wprowadź **wskazówkę miejsca** |

    1. Wybierz pozycję **Zapisz**.

1. Powtórz poprzedni krok z innymi dwoma wystąpieniami przy użyciu następujących wartości:

    **Dla wind 2:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 2**|
    | **Opis** | Wprowadź **wystąpienie dla wind 2** |
    | **Hierarchie** | Wybierz **hierarchię lokalizacji** |
    | **Kraj** | Wprowadź **Stan USA** |
    | **City (Miasto)** | Wprowadź **Seattle** |
    | **Budowaniu** | Wprowadź **centrum nauki Pacyfiku** |

    **Dla windy 3:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 3**|
    | **Opis** | Wprowadź **wystąpienie dla wind 3** |
    | **Hierarchie** | Wybierz **hierarchię lokalizacji** |
    | **Kraj** | Wprowadź **Stan USA** |
    | **City (Miasto)** | Wprowadź **Nowy Jork** |
    | **Budowaniu** | Wprowadź **kompilację stanu Empire** |

    [![Wyświetl zaktualizowane wystąpienia.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Przejdź z powrotem do karty **Analiza** , aby wyświetlić okienko wykresów. W obszarze **Hierarchia lokalizacji** rozwiń węzeł wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

    [![Wyświetl wszystkie hierarchie w widoku wykresu.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. W obszarze **centrum nauki pacyficznego** wybierz pozycję przenośnik szeregów czasowych **2**, a następnie wybierz pozycję **Pokaż średnią temperaturę**.

1. Dla tego samego wystąpienia, **Wind 2**, wybierz pozycję **Pokaż piętro**.

    Za pomocą zmiennej kategorii można określić, jak długo winda zajmuje się górną, niższą i środkową podłogą.

    [![Wizualizuje windę 2 z hierarchią i danymi.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź grupę zasobów Azure Time Series Insights Gen2.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Utwórz środowisko Azure Time Series Insights Gen2 PAYG.
* Podłącz środowisko Azure Time Series Insights Gen2 do centrum IoT Hub.
* Uruchom przykład akceleratora rozwiązania, aby przesłać strumieniowo dane do środowiska Azure Time Series Insights Gen2.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

Teraz, gdy wiesz już, jak utworzyć własne środowisko Azure Time Series Insights Gen2, Dowiedz się więcej na temat kluczowych pojęć w Azure Time Series Insights Gen2.

Przeczytaj o Azure Time Series Insights pozyskiwania Gen2:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 pozyskiwania danych](./concepts-ingestion-overview.md)

Przeczytaj o Azure Time Series Insights Gen2 Storage:

> [!div class="nextstepaction"]
> [Magazyn danych Gen2 Azure Time Series Insights](./concepts-storage.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Azure Time Series Insights modelowania danych Gen2](./concepts-model-overview.md)

Dowiedz się więcej o łączeniu środowiska z Power BI:

> [!div class="nextstepaction"]
> [Wizualizuj dane z Azure Time Series Insights Gen2 w Power BI](./how-to-connect-power-bi.md)
