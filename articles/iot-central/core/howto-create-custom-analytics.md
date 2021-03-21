---
title: Zwiększanie IoT Central platformy Azure przy użyciu analizy niestandardowej | Microsoft Docs
description: Jako deweloper rozwiązań Skonfiguruj aplikację IoT Central, aby wykonywać niestandardowe analizy i wizualizacje. To rozwiązanie używa Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/18/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 11e5ba3c0700cc9b29b8a11c0f9aa20cb5adb132
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551321"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Rozszerzając IoT Central platformy Azure z analizą niestandardową przy użyciu Azure Databricks

Ten przewodnik zawiera informacje o sposobie rozbudowywania aplikacji IoT Central przy użyciu niestandardowych analiz i wizualizacji. W przykładzie przedstawiono użycie [Azure Databricks](/azure/azure-databricks/) obszaru roboczego do analizowania IoT Central strumienia telemetrii i generowania wizualizacji, takich jak [pola](https://wikipedia.org/wiki/Box_plot).  

W tym przewodniku opisano sposób, w jaki można rozbudować IoT Central wykraczające poza to, co można już zrobić przy użyciu [wbudowanych narzędzi analitycznych](./howto-create-custom-analytics.md).

W tym przewodniku krok po kroku dowiesz się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportu danych*.
* Utwórz środowisko Azure Databricks, aby przeanalizować i wykreolić dane telemetryczne urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>Aplikacja IoT Central

Utwórz aplikację IoT Central w witrynie programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) przy użyciu następujących ustawień:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa |
| Szablon aplikacji | Analiza warunków w sklepie — monitorowanie |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj domyślny lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawa Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region (Region) | Najbliższy region |

Przykłady i zrzuty ekranu w tym artykule używają regionu **Stany Zjednoczone** . Wybierz lokalizację blisko siebie i upewnij się, że wszystkie zasoby zostały utworzone w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia z termostatem, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [Azure Portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **IoTCentralAnalysis** , aby zawierała inne utworzone zasoby. Utwórz zasoby platformy Azure w tej samej lokalizacji, w której znajduje się aplikacja IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [Azure Portal, aby utworzyć przestrzeń nazw Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks obszar roboczy

Użyj [Azure Portal, aby utworzyć usługę Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa obszaru roboczego    | Wybierz nazwę obszaru roboczego |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Warstwa cenowa | Standardowa |

Po utworzeniu wymaganych zasobów Grupa zasobów **IoTCentralAnalysis** wygląda podobnie do poniższego zrzutu ekranu:

![Grupa zasobów analizy IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Można skonfigurować aplikację IoT Central, aby ciągle eksportować dane telemetryczne do centrum zdarzeń. W tej sekcji utworzysz centrum zdarzeń, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń dostarcza dane telemetryczne do zadania Stream Analytics do przetworzenia.

1. W Azure Portal przejdź do przestrzeni nazw Event Hubs i wybierz pozycję **+ centrum zdarzeń**.
1. Nazwij **centralexport** centrum zdarzeń, a następnie wybierz pozycję **Utwórz**.
1. Na liście centrów zdarzeń w przestrzeni nazw wybierz pozycję **centralexport**. Następnie wybierz pozycję **zasady dostępu współdzielonego**.
1. Wybierz pozycję **+ Dodaj**. Utwórz zasady o nazwie **Nasłuchuj** przy użyciu roszczeń **nasłuchujących** .
1. Gdy zasady są gotowe, wybierz je z listy, a następnie skopiuj **Parametry połączenia — wartość klucza podstawowego** .
1. Zanotuj te parametry połączenia, które są używane później podczas konfigurowania notesu datakostki do odczytu z centrum zdarzeń.

Przestrzeń nazw Event Hubs wygląda następująco:

![Przestrzeń nazw usługi Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w IoT Central

W witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) przejdź do aplikacji IoT Central utworzonej na podstawie szablonu contoso. W tej sekcji skonfigurujesz aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń. Aby skonfigurować eksport:

1. Przejdź do strony **eksport danych** , wybierz pozycję **+ Nowy**, a następnie opcję **Azure Event Hubs**.
1. Aby skonfigurować eksport, użyj następujących ustawień, a następnie wybierz pozycję **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportuj do Event Hubs |
    | Enabled (Włączony) | Włączone |
    | Przestrzeń nazw usługi Event Hubs | Nazwa przestrzeni nazw Event Hubs |
    | Centrum zdarzeń | centralexport |
    | Miary | Włączone |
    | Urządzenia | Wyłączone |
    | Szablony urządzeń | Wyłączone |

![Konfiguracja eksportu danych](media/howto-create-custom-analytics/cde-configuration.png)

Przed kontynuowaniem Zaczekaj, aż stan eksportu zostanie **uruchomiony** .

## <a name="configure-databricks-workspace"></a>Skonfiguruj obszar roboczy datakosteks

W Azure Portal przejdź do usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**. Zostanie otwarta nowa karta w przeglądarce i zalogowanie do obszaru roboczego.

### <a name="create-a-cluster"></a>Tworzenie klastra

Na stronie **Azure Databricks** w obszarze listy typowych zadań wybierz pozycję **nowy klaster**.

Skorzystaj z informacji w poniższej tabeli, aby utworzyć klaster:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa klastra | centralanalysis |
| Tryb klastra | Standardowa |
| Wersja Databricks Runtime | 5,5 LTS (Scala 2,11, Spark 2.4.5) |
| Wersja języka Python | 3 |
| Włącz Skalowanie automatyczne | Nie |
| Przerwij po minutach braku aktywności | 30 |
| Typ procesu roboczego | Standardowa_DS3_v2 |
| Ochotnik | 1 |
| Typ sterownika | Taki sam jak proces roboczy |

Utworzenie klastra może potrwać kilka minut. przed kontynuowaniem Zaczekaj na ukończenie tworzenia klastra.

### <a name="install-libraries"></a>Zainstaluj biblioteki

Na stronie **klastry** poczekaj, aż stan klastra zostanie **uruchomiony**.

W poniższych krokach pokazano, jak zaimportować bibliotekę potrzebną w ramach przykładu do klastra:

1. Na stronie **klastry** poczekaj, aż zostanie **uruchomiony** stan **centralanalysis** Interactive.

1. Wybierz klaster, a następnie wybierz kartę **biblioteki** .

1. Na karcie **biblioteki** wybierz pozycję **Zainstaluj nowe**.

1. Na stronie **Instalowanie biblioteki** wybierz **Maven** jako źródło biblioteki.

1. W polu tekstowym **współrzędne** Wprowadź następującą wartość: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę w klastrze.

1. Stan biblioteki jest teraz **zainstalowany**:

    ![Zainstalowana Biblioteka](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importowanie notesu datacegły

Wykonaj następujące kroki, aby zaimportować Notes datacegły zawierający kod języka Python, aby analizować i wizualizować dane telemetryczne IoT Central:

1. Przejdź do strony **obszaru roboczego** w środowisku datakostks. Wybierz listę rozwijaną obok swojej nazwy konta, a następnie wybierz pozycję **Importuj**.

1. Wybierz opcję importowania z adresu URL i wprowadź następujący adres: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Aby zaimportować Notes, wybierz pozycję **Importuj**.

1. Wybierz **obszar roboczy** , aby wyświetlić zaimportowany Notes:

    ![Zaimportowany Notes](media/howto-create-custom-analytics/import-notebook.png)

1. Edytuj kod w pierwszej komórce w języku Python, aby dodać Event Hubs parametry połączenia, które zostały zapisane wcześniej:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Uruchom analizę

Aby uruchomić analizę, należy dołączyć Notes do klastra:

1. Wybierz opcję **odłączane** , a następnie wybierz klaster **centralanalysis** .
1. Jeśli klaster nie jest uruchomiony, należy go uruchomić.
1. Aby rozpocząć pracę z notesem, wybierz przycisk Uruchom.

W ostatniej komórce może zostać wyświetlony błąd. Jeśli tak jest, sprawdź, czy poprzednie komórki są uruchomione, Zaczekaj chwilę na zapisanie niektórych danych w magazynie, a następnie ponownie uruchom ostatnią komórkę.

### <a name="view-smoothed-data"></a>Wyświetl dane gładkie

W notesie przewiń w dół do komórki 14, aby wyświetlić wykres średniej wartości wilgotnej według typu urządzenia. Ten wykres ciągle aktualizuje się w miarę nadejścia danych telemetrycznych przesyłania strumieniowego:

![Wygładzony wykres telemetrii](media/howto-create-custom-analytics/telemetry-plot.png)

Można zmienić rozmiar wykresu w notesie.

### <a name="view-box-plots"></a>Wyświetl wykresy

W notesie przewiń w dół do komórki 20, aby zobaczyć [pola wykresów](https://en.wikipedia.org/wiki/Box_plot). Wykresy skrzynkowe są oparte na danych statycznych, więc aby je zaktualizować, należy ponownie uruchomić komórkę:

![Wykresy skrzynkowe](media/howto-create-custom-analytics/box-plots.png)

Można zmienić rozmiar wykresów w notesie.

## <a name="tidy-up"></a>Uporządkowanego

Aby uporządkowanego się po tym, jak to zrobić, i uniknąć niepotrzebnych kosztów, Usuń grupę zasobów **IoTCentralAnalysis** w Azure Portal.

Aplikację IoT Central można usunąć ze strony **zarządzania** w ramach aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku krok po kroku przedstawiono następujące informacje:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportu danych*.
* Utwórz środowisko Azure Databricks, aby analizować i wykreślić dane telemetryczne.

Teraz, gdy wiesz już, jak utworzyć analizę niestandardową, sugerowanym następnym krokiem jest zapoznanie się z tematem [wizualizacji i analizowania danych IoT Central platformy Azure na pulpicie nawigacyjnym Power BI](howto-connect-powerbi.md).