---
title: Przesyłanie strumieniowe danych ze zdalnego monitorowania do Data Lake Store na platformie Azure | Microsoft Docs
description: Dowiedz się, jak zintegrować rozwiązanie do zdalnego monitorowania z Azure Data Lake Store przy użyciu zadania Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73889233"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Zintegruj rozwiązanie do zdalnego monitorowania z usługą Azure Data Lake Store

Możesz mieć zaawansowane wymagania dotyczące analiz wykraczające poza to, co jest oferowane w rozwiązaniu do zdalnego monitorowania. Azure Data Lake Store jest idealnym rozwiązaniem dla tej aplikacji, ponieważ może przechowywać dane z ogromnych i różnorodnych zestawów danych oraz integrować je z usługą Azure Data Lake Analytics w celu zapewnienia analizy na żądanie.

W tej metodzie można użyć zadania Azure Stream Analytics do przesyłania strumieniowego danych z Centrum IoT Hub w rozwiązaniu do zdalnego monitorowania do Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten sposób, potrzebne są następujące elementy:

* [Wdróż Akcelerator rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md).
  * Rozwiązanie do monitorowania zdalnego spowoduje wdrożenie w ramach subskrypcji platformy Azure zadania usługi IoT Hub i Azure Stream Analytics użytego w tym artykule.
* [Wdrażanie Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store należy wdrożyć w tym samym regionie, w którym znajduje się rozwiązanie do monitorowania zdalnego.
  * [Utwórz folder](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) o nazwie "streaming" na Twoim koncie.

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Utwórz dedykowaną grupę odbiorców w centrum IoT w rozwiązaniu do monitorowania zdalnego. Będzie on używany przez zadanie Stream Analytics do przesyłania strumieniowego danych do Data Lake Store.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do ściągania danych z usługi Azure IoT Hub. Dla każdego pięciu odbiorców wyjściowych należy utworzyć nową grupę odbiorców. Można utworzyć maksymalnie 32 grup odbiorców.

1. Zaloguj się do Portalu Azure.

1. W Azure Portal kliknij przycisk **Cloud Shell** .

    ![Ikona uruchamiania portalu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Wykonaj to polecenie, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Użyj nazw grupy zasobów i usługi IoT Hub z rozwiązania do monitorowania zdalnego.

## <a name="create-stream-analytics-job"></a>Tworzenie zadania Stream Analytics

Utwórz zadanie Azure Stream Analytics w celu przesyłania strumieniowego danych z Centrum IoT do magazynu Azure Data Lake.

1. Kliknij pozycję **Utwórz zasób**, wybierz Internet rzeczy z portalu Marketplace, a następnie kliknij pozycję **zadanie Stream Analytics**.

    ![Nowe zadanie Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Wprowadź nazwę zadania i wybierz odpowiednią subskrypcję i grupę zasobów.

1. Wybierz lokalizację w sąsiedztwie lub w tym samym regionie, w którym znajduje się Data Lake Store. W tym miejscu używamy Wschodnie stany USA.

1. Upewnij się, że środowisko hostingu ma pozostać w **chmurze**domyślnej.

1. Kliknij przycisk **Utwórz**.

    ![Tworzenie zadania Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurowanie zadania Stream Analytics

1. Przejdź do **zadania Stream Analytics** w grupie zasobów rozwiązania do monitorowania zdalnego.

1. Na stronie Przegląd kliknij pozycję **dane wejściowe**.

    ![Strona przeglądu](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kliknij pozycję **Dodaj strumień wejściowy** i wybierz pozycję **IoT Hub** z listy rozwijanej.

    ![Dodaj dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na karcie nowe dane wejściowe wprowadź alias wejściowy **IoTHub**.

1. Z listy rozwijanej Grupa odbiorców wybierz utworzoną wcześniej grupę odbiorców. Tutaj korzystamy z usługi **streamanalyticsjob**.

    ![Wybierz dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kliknij przycisk **Zapisz**.

1. Na stronie Przegląd kliknij pozycję dane **wyjściowe**.

    ![Dodaj Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kliknij przycisk **Dodaj** i wybierz pozycję **Data Lake Store** z listy rozwijanej.

    ![Dodawanie danych wyjściowych](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na karcie nowe dane wyjściowe wprowadź alias wyjściowy elementu **kontach datalakestore**.

1. Wybierz konto Data Lake Store utworzone w poprzednich krokach i podaj strukturę folderów do przesyłania strumieniowego danych do magazynu.

1. W polu Format daty wprowadź **/Streaming/{Date}/{Time}**. Pozostaw domyślny format daty RRRR/MM/DD i godziny w formacie HH.

    ![Podaj strukturę folderów](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kliknij przycisk **Autoryzuj**.

    Konieczne będzie autoryzowanie przy użyciu Data Lake Store, aby umożliwić usłudze Stream Analytics dostęp do zapisu w systemie plików.

    ![Autoryzuj Stream Analytics do Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobaczysz okno podręczne, a po zakończeniu autoryzacji przycisk wyskakujący autoryzacja zostanie zamknięty.

    > [!NOTE]
    > Jeśli w oknie podręcznym zostanie wyświetlony błąd, Otwórz nowe okno przeglądarki w trybie incognito i spróbuj ponownie.

1. Kliknij przycisk **Zapisz**.

## <a name="edit-the-stream-analytics-query"></a>Edytuj zapytanie Stream Analytics

Azure Stream Analytics używa języka zapytań przypominających SQL, aby określić źródło danych wejściowych, które przesyła strumieniowo dane, przekształca te dane zgodnie z potrzebami oraz dane wyjściowe do różnych miejsc do magazynowania lub przetwarzania.

1. Na karcie Przegląd kliknij pozycję **Edytuj zapytanie**.

    ![Edytuj zapytanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. W edytorze zapytań Zastąp symbole zastępcze [YourOutputAlias] i [YourInputAlias] wartościami zdefiniowanymi wcześniej.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Zapytanie Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kliknij przycisk **Zapisz**.
1. Kliknij przycisk **tak** , aby zatwierdzić zmiany.

## <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania Stream Analytics

1. Na karcie Przegląd kliknij przycisk **Uruchom**.

    ![Uruchom zadanie Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na karcie Uruchamianie zadania kliknij pozycję **niestandardowa**.

1. Ustaw czas niestandardowy, aby wycofać kilka godzin, aby pobrać dane z momentu rozpoczęcia przesyłania strumieniowego na urządzeniu.

1. Kliknij przycisk **Uruchom**.

    ![Wybierz niestandardową datę](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Zaczekaj, aż zadanie przejdzie w stan uruchomienia, Jeśli zobaczysz błędy, które może być z tego zapytania, upewnij się, że składnia jest poprawna.

    ![Uruchomiono zadanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Zadanie przesyłania strumieniowego rozpocznie odczytywanie danych z IoT Hub i przechowywanie danych w Data Lake Store. Wyświetlenie danych w Data Lake Store może potrwać kilka minut.

## <a name="explore-the-streaming-data"></a>Eksplorowanie danych przesyłanych strumieniowo

1. Przejdź do Data Lake Store.

1. Na karcie Przegląd kliknij pozycję **Eksplorator danych**.

1. W Eksploratorze danych przejdź do szczegółów folderu **/Streaming** . Zostaną wyświetlone foldery utworzone przy użyciu formatu RRRR/MM/DD/HH.

    ![Eksplorowanie danych przesyłanych strumieniowo](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Będą widoczne pliki JSON z jednym plikiem na godzinę.

    ![Eksplorowanie danych przesyłanych strumieniowo](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Następne kroki

Azure Data Lake Analytics można użyć do przeprowadzenia analizy danych Big Data na Data Lake Store zbiorach danych. Więcej informacji znajduje się w [dokumentacji Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
