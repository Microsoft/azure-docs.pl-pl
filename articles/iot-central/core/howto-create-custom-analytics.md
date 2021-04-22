---
title: Rozszerzanie Azure IoT Central za pomocą niestandardowych narzędzi analitycznych | Microsoft Docs
description: Jako deweloper rozwiązań skonfiguruj aplikację IoT Central do niestandardowych analiz i wizualizacji. To rozwiązanie używa Azure Databricks.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872771"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Rozszerzanie usługi Azure IoT Central o niestandardowe analizy przy użyciu usługi Azure Databricks

W tym przewodniku przedstawiono, jako dewelopera rozwiązań, jak rozszerzyć aplikację IoT Central o niestandardowe analizy i wizualizacje. W przykładzie użyto [Azure Databricks](/azure/azure-databricks/) obszaru roboczego do analizowania strumienia IoT Central danych telemetrycznych i generowania wizualizacji, takich jak [wykresy skrzynkowe.](https://wikipedia.org/wiki/Box_plot)  

W tym przewodniku pokazano, jak rozszerzyć IoT Central poza to, co już może zrobić za pomocą wbudowanych [narzędzi analitycznych.](./howto-create-custom-analytics.md)

Z tego przewodnika dowiesz się, jak:

* Przesyłanie strumieniowe danych telemetrycznych z IoT Central przy użyciu *ciągłego eksportu danych.*
* Utwórz środowisko Azure Databricks do analizowania i kreślenia danych telemetrycznych urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central aplikacji

Utwórz aplikację IoT Central w witrynie [Azure IoT Central menedżer aplikacji](https://aka.ms/iotcentral) przy użyciu następujących ustawień:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa (Standard) |
| Szablon aplikacji | Analiza w sklepie — monitorowanie warunków |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj wartość domyślną lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Twoja Azure Active Directory dzierżawy |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region (Region) | Najbliższy region |

Przykłady i zrzuty ekranu w tym artykule używają **Stany Zjednoczone** regionu. Wybierz lokalizację w pobliżu i upewnij się, że wszystkie zasoby są w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia termostatów, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [Azure Portal, aby utworzyć grupę](https://portal.azure.com/#create/Microsoft.ResourceGroup) zasobów o nazwie **IoTCentralAnalysis,** która będzie zawierać inne zasoby, które utworzysz. Utwórz zasoby platformy Azure w tej samej lokalizacji, w IoT Central aplikacji.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [Azure Portal, aby utworzyć Event Hubs nazw](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybieranie nazwy przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks roboczy

Użyj [Azure Portal, aby utworzyć usługę Azure Databricks Service](https://portal.azure.com/#create/Microsoft.Databricks) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa obszaru roboczego    | Wybieranie nazwy obszaru roboczego |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Warstwa cenowa | Standardowa (Standard) |

Po utworzeniu wymaganych zasobów grupa zasobów **IoTCentralAnalysis** wygląda jak na poniższym zrzucie ekranu:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="obraz IoT Central grupy zasobów analizy danych.":::

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Możesz skonfigurować aplikację do IoT Central, aby stale eksportowała dane telemetryczne do centrum zdarzeń. W tej sekcji utworzysz centrum zdarzeń do odbierania danych telemetrycznych z IoT Central aplikacji. Centrum zdarzeń dostarcza dane telemetryczne do Stream Analytics przetwarzania.

1. W Azure Portal przejdź do przestrzeni nazw usługi Event Hubs i wybierz **pozycję + Centrum zdarzeń**.
1. Nadaj centrum zdarzeń **nazwę centralexport**.
1. Na liście centrów zdarzeń w przestrzeni nazw wybierz pozycję **centralexport**. Następnie wybierz **pozycję Zasady dostępu współużytkowego.**
1. Wybierz pozycję **+ Dodaj**. Utwórz zasady o nazwie **SendListen z** **oświadczeniami Wysyłanie** **i nasłuchiwać.**
1. Gdy zasady będą gotowe, wybierz je z listy, a następnie skopiuj **wartość Connection string-primary key (Ciąg** połączenia — klucz podstawowy).
1. Zanotuj te ciągi połączenia. Użyjesz ich później podczas konfigurowania notesu usługi Databricks do odczytu z centrum zdarzeń.

Twoja Event Hubs przestrzeń nazw wygląda jak na poniższym zrzucie ekranu:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="obraz Event Hubs przestrzeni nazw.":::

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w IoT Central

W tej sekcji skonfigurujesz aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń.

W [witrynie Azure IoT Central menedżer aplikacji](https://aka.ms/iotcentral) przejdź do utworzonej IoT Central aplikacji. Aby skonfigurować eksport, najpierw utwórz miejsce docelowe:

1. Przejdź do strony **Eksportowanie danych,** a następnie wybierz pozycję **Miejsca docelowe.**
1. Wybierz **pozycję + Nowe miejsce docelowe.**
1. Użyj wartości z poniższej tabeli, aby utworzyć miejsce docelowe:

    | Ustawienie | Wartość |
    | ----- | ----- |
    | Nazwa miejsca docelowego | Centrum zdarzeń telemetrii |
    | Typ docelowy | Azure Event Hubs |
    | Parametry połączenia | Zanotuj wcześniej zanotowe wartości parametrów połączenia centrum zdarzeń |

    Centrum **zdarzeń jest** wyeksportowywuj **centralnie**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Zrzut ekranu przedstawiający miejsce docelowe eksportu danych.":::

1. Wybierz pozycję **Zapisz**.

Aby utworzyć definicję eksportu:

1. Przejdź do strony **Eksportowanie danych** i wybierz **pozycję + Nowy eksport.**

1. Użyj wartości w poniższej tabeli, aby skonfigurować eksport:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa eksportu | Eksportowanie centrum zdarzeń |
    | Enabled (Włączony) | Włączone |
    | Typ danych do wyeksportowania | Telemetria |
    | Miejsca docelowe | Wybierz **pozycję + Miejsce docelowe,** a następnie wybierz pozycję Centrum zdarzeń **telemetrii** |

1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Zrzut ekranu przedstawiający definicję eksportu danych.":::

Przed kontynuowaniem poczekaj, aż stan eksportu na stronie **Eksport** danych będzie w dobrej kondycji. 

## <a name="configure-databricks-workspace"></a>Konfigurowanie obszaru roboczego usługi Databricks

W Azure Portal przejdź do usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy.** W przeglądarce zostanie otwarta nowa karta, która spowoduje twoje otwarcie w obszarze roboczym.

### <a name="create-a-cluster"></a>Tworzenie klastra

Na stronie **Azure Databricks** wybierz pozycję Nowy klaster na liście typowych **zadań.**

Skorzystaj z informacji w poniższej tabeli, aby utworzyć klaster:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa klastra | centralanalysis |
| Tryb klastra | Standardowa (Standard) |
| Databricks Runtime wersji | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Wersja języka Python | 3 |
| Włączanie skalowania automatycznego | Nie |
| Zakończ po min braku aktywności | 30 |
| Typ procesu roboczego | Standardowa_DS3_v2 |
| Pracowników | 1 |
| Typ sterownika | Tak samo jak w przypadku procesu roboczego |

Tworzenie klastra może potrwać kilka minut. Przed kontynuowaniem poczekaj na ukończenie tworzenia klastra.

### <a name="install-libraries"></a>Instalowanie bibliotek

Na stronie **Klastry** zaczekaj na stan klastra **Uruchomiony.**

Poniższe kroki pokazują, jak zaimportować bibliotekę potrzeb przykładowych do klastra:

1. Na stronie **Klastry** poczekaj, aż stanem klastra interaktywnego **centralanalysis** będzie **Uruchomiony**.

1. Wybierz klaster, a następnie wybierz **kartę Biblioteki.**

1. Na karcie **Biblioteki** wybierz pozycję **Zainstaluj nową.**

1. Na stronie **Instalowanie biblioteki** wybierz **pozycję Maven** jako źródło biblioteki.

1. W polu **tekstowym Współrzędne** wprowadź następującą wartość: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Wybierz **pozycję Zainstaluj,** aby zainstalować bibliotekę w klastrze.

1. Stan biblioteki to teraz **Zainstalowano:**

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Zrzut ekranu przedstawiający zainstalowaną bibliotekę.":::

### <a name="import-a-databricks-notebook"></a>Importowanie notesu databricks

Aby zaimportować notes usługi Databricks zawierający kod IoT Central języka Python do analizowania i wizualizacji danych telemetrycznych usługi Databricks, należy wykonać następujące kroki:

1. Przejdź do strony **Obszar roboczy** w środowisku databricks. Wybierz menu rozwijane obok nazwy konta, a następnie wybierz pozycję **Importuj**.

1. Wybierz importowanie z adresu URL i wprowadź następujący adres: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Aby zaimportować notes, wybierz pozycję **Importuj**.

1. Wybierz obszar **roboczy,** aby wyświetlić zaimportowany notes:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Zrzut ekranu przedstawiający zaimportowany notes.":::

5. Edytuj kod w pierwszej komórce języka Python, aby dodać Event Hubs parametrów połączenia, które zostały zapisane wcześniej:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Uruchamianie analizy

Aby uruchomić analizę, musisz dołączyć notes do klastra:

1. Wybierz **pozycję Odłączone,** a następnie wybierz **klaster centralanalysis.**
1. Jeśli klaster nie jest uruchomiony, uruchom go.
1. Aby uruchomić notes, wybierz przycisk uruchamiania.

W ostatniej komórce może zostać wyświetlony błąd. Jeśli tak, sprawdź, czy poprzednie komórki są uruchomione, poczekaj chwilę na zapisane dane w magazynie, a następnie ponownie uruchom ostatnią komórkę.

### <a name="view-smoothed-data"></a>Wyświetlanie wygładzonej danych

W notesie przewiń w dół do komórki 14, aby zobaczyć wykres średniej wilgotności kroczącej według typu urządzenia. Ten wykres jest stale aktualizowany w przypadku nasyłania danych telemetrycznych przesyłania strumieniowego:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Zrzut ekranu przedstawiający wykres danych telemetrycznych Smoothed.":::

Rozmiar wykresu można zmienić w notesie.

### <a name="view-box-plots"></a>Wyświetlanie wykresów skrzynkowych

W notesie przewiń w dół do komórki 20, aby zobaczyć [wykresy skrzynkowe](https://en.wikipedia.org/wiki/Box_plot). Wykresy box są oparte na danych statycznych, więc aby je zaktualizować, należy ponownie uruchomić komórkę:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Zrzut ekranu przedstawiający wykresy box.":::

Rozmiar wykresów w notesie można zmienić.

## <a name="tidy-up"></a>Uporządkowanie

Aby uporządkować po tym how-to i uniknąć niepotrzebnych kosztów, usuń grupę zasobów **IoTCentralAnalysis** w Azure Portal.

Możesz usunąć aplikację IoT Central na **stronie** Zarządzanie w aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku po tym przewodniku opisano, jak:

* Przesyłanie strumieniowe danych telemetrycznych z IoT Central przy użyciu *ciągłego eksportu danych.*
* Utwórz środowisko Azure Databricks do analizowania i wykreślenia danych telemetrycznych.

Teraz, gdy wiesz już, jak utworzyć niestandardową analizę, sugerowanym następnym krokiem jest nauczenia się, jak wizualizować i analizować dane Azure IoT Central [na pulpicie nawigacyjnym Power BI nawigacyjnym.](howto-connect-powerbi.md)
