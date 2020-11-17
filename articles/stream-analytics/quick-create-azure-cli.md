---
title: Szybki Start — Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start pokazano, jak utworzyć zadanie Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: fa7919f54663387ddef811d02137da6d3ffb9d9b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646631"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure w celu zdefiniowania zadania Stream Analytics, które filtruje komunikaty czujnika w czasie rzeczywistym z odczytem temperatury większym niż 27. Zadanie Stream Analytics będzie odczytywać dane z IoT Hub, przekształcać dane i zapisywać dane z powrotem do kontenera w usłudze BLOB Storage. Dane wejściowe używane w tym przewodniku Szybki start są generowany przez symulator online Raspberry Pi.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Utwórz grupę zasobów. Wszystkie zasoby platformy Azure muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

   W tym przewodniku szybki start Utwórz grupę zasobów o nazwie *streamanalyticsrg* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania Stream Analytics Przygotuj dane, które są używane na potrzeby danych wejściowych zadania.

Poniższe bloki kodu interfejsu wiersza polecenia platformy Azure to polecenia przygotowujące dane wejściowe wymagane przez zadanie. Przejrzyj sekcje, aby zapoznać się z kodem.

1. Utwórz centrum IoT Hub za pomocą polecenia [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). W tym przykładzie tworzone jest centrum IoT Hub o nazwie **MyASAIoTHub**. Ponieważ nazwy centrów IoT Hub są unikatowe, musisz utworzyć własną nazwę centrum IoT Hub. Ustaw jednostkę SKU F1, aby skorzystać z warstwy bezpłatnej, jeśli jest dostępna w ramach subskrypcji. W przeciwnym razie wybierz kolejną najniższą warstwę.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Po utworzeniu centrum IoT Hub pobierz parametry połączenia tego centrum IoT Hub za pomocą polecenia [az iot hub show-connection-string](/cli/azure/iot/hub). Skopiuj całe parametry połączenia i zapisz je, ponieważ będą używane podczas dodawania centrum IoT Hub w danych wejściowych zadania usługi Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Dodaj urządzenie do centrum IoT Hub za pomocą polecenia [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). W tym przykładzie zostanie utworzone urządzenie o nazwie **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Pobierz parametry połączenia tego urządzenia za pomocą polecenia [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Skopiuj całe parametry połączenia i zapisz je, ponieważ będą używane podczas tworzenia symulatora urządzenia Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Przykładowe dane wyjściowe:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Tworzenie konta magazynu obiektów BLOB

Poniższy blok kodu interfejsu wiersza polecenia platformy Azure tworzy konto usługi BLOB Storage, które jest używane na potrzeby danych wyjściowych zadania. Przejrzyj sekcje, aby zapoznać się z kodem.

1. Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami.

   Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Pobierz klucz dla konta magazynu, uruchamiając polecenie [AZ Storage account Keys list](/cli/azure/storage/account/keys) . Zapisz ten klucz do użycia w następnym kroku.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Do tworzenia kontenera do przechowywania obiektów blob służy polecenie [az storage container create](/cli/azure/storage/container). Klucz konta magazynu jest używany do autoryzacji operacji tworzenia kontenera. Aby uzyskać więcej informacji na temat autoryzacji operacji na danych za pomocą interfejsu wiersza polecenia platformy Azure, zobacz temat [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](../storage/common/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Poniższe bloki kodu interfejsu wiersza polecenia platformy Azure tworzą zadanie Stream Analytics. Zapoznaj się z sekcją, aby zrozumieć kod

1. Utwórz zadanie Stream Analytics za pomocą polecenia [AZ Stream-Analytics Job Create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) .

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Konfigurowanie danych wejściowych zadania

Dodaj dane wejściowe do zadania za pomocą polecenia [AZ Stream-Analytics Input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) . To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wejściowych zadania, nazwę grupy zasobów i definicję danych wejściowych zadania. Definicja danych wejściowych zadania to plik JSON, który zawiera właściwości wymagane do skonfigurowania danych wejściowych zadania. W tym przykładzie utworzysz IoT Hub jako dane wejściowe.

Na maszynie lokalnej utwórz plik o nazwie `datasource.json` i dodaj do niego następujące dane JSON. Pamiętaj o zastąpieniu wartości `sharedAccessPolicyKey` częścią `SharedAccessKey` z parametrów połączenia centrum IoT Hub zapisanych w poprzedniej sekcji.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Na maszynie lokalnej utwórz plik o nazwie `serialization.json` i dodaj do niego następujące dane JSON.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Następnie uruchom polecenie cmdlet `az stream-analytics input create`. Pamiętaj, aby zastąpić wartość `datasource` zmiennej ścieżką, w której zapisano plik JSON definicji danych wejściowych zadania, oraz wartość `serialization` zmiennej z ścieżką, w której ZAPISANO plik JSON serializacji.

```azurecli
az stream-analytics input create 
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Konfigurowanie danych wyjściowych zadania

Dodawanie danych wyjściowych do zadania za pomocą polecenia [AZ Stream-Analytics Output Create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) . To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wyjściowych zadania, nazwę grupy zasobów i definicję danych wyjściowych zadania. Definicja danych wyjściowych zadania to plik JSON, który zawiera właściwości wymagane do skonfigurowania danych wyjściowych zadania. W tym przykładzie jako dane wyjściowe używany jest magazyn obiektów blob.

Na maszynie lokalnej utwórz plik o nazwie `datasink.json` i dodaj do niego następujące dane JSON. Pamiętaj, aby zastąpić wartość dla `accountKey` klucza dostępu konta magazynu, który jest wartością przechowywaną w $storageAccountKey wartość.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Następnie uruchom polecenie cmdlet `az stream-analytics output`. Pamiętaj, aby zastąpić wartość `datasource` zmiennej ścieżką, w której zapisano plik JSON definicji danych wyjściowych zadania, oraz wartość `serialization` zmiennej z ścieżką, w której zapisano plik serializacji JSON.

```azurecli
az stream-analytics output create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Dodaj przekształcenie zadania za pomocą polecenia [AZ Stream-Analytics Transformation Create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) . To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę przekształcenia zadania, nazwę grupy zasobów i definicję przekształcenia zadania. 

Uruchom `az stream-analytics transformation create` polecenie cmdlet.

```azurecli
az stream-analytics transformation create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Uruchamianie symulatora IoT

1. Otwórz symulator [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Zastąp symbol zastępczy w wierszu 15 całymi parametrami połączenia urządzenia usługi Azure IoT Hub, które zostały zapisane w poprzedniej sekcji.

3. Kliknij przycisk **Uruchom**. Dane wyjściowe powinny pokazywać dane z czujników i komunikaty, które są wysyłane do usługi IoT Hub.

    ![Symulator Raspberry Pi Azure IoT Online Simulator](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Uruchom zadanie przy użyciu polecenia [AZ Stream-Analytics Job Start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) . To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę grupy zasobów, tryb uruchamiania danych wyjściowych i czas rozpoczęcia. Parametr `OutputStartMode` przyjmuje wartość `JobStartTime`, `CustomTime` lub `LastOutputEventTime`.

Po uruchomieniu poniższego polecenia cmdlet zwróci ono wartość `True` jako dane wyjściowe, jeśli zadanie zostało uruchomione. W kontenerze magazynu zostanie utworzony folder wyjściowy z przekształconymi danymi.

```azurecli
az stream-analytics job start 
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz pominąć jego usuwanie i na razie je zatrzymać. Jeśli nie planujesz używania tego zadania w przyszłości, usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki start, uruchamiając następujące polecenie cmdlet:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono proste zadanie Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz też wdrażać zadania usługi Stream Analytics przy użyciu witryny [Azure Portal](stream-analytics-quick-create-portal.md) i programu [Visual Studio](stream-analytics-quick-create-vs.md).

Aby dowiedzieć się więcej o konfigurowaniu innych źródeł danych wejściowych i wykonywaniu wykrywania w czasie rzeczywistym, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Wykrywanie oszustw w czasie rzeczywistym za pomocą usługi Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)