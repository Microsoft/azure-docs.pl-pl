---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu języka Python | Microsoft Docs
description: Jak przekazywać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla języka Python. Przekazane pliki są przechowywane w kontenerze obiektów BLOB usługi Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 1112df382fb10051ac06c4461a9e4328e1663388
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139308"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym artykule pokazano, jak używać [funkcji przekazywania plików IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania plików do [usługi Azure Blob Storage](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpieczne udostępnianie kontenera magazynu na potrzeby przekazywania pliku.

* Użyj klienta języka Python, aby przekazać plik za pomocą Centrum IoT.

[Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) przedstawia podstawowe funkcje obsługi komunikatów z urządzenia do chmury IoT Hub. Jednak w niektórych scenariuszach nie można łatwo zmapować danych wysyłanych przez urządzenia do bezwzględnie niewielkich komunikatów z urządzenia do chmury, które IoT Hub akceptowane. W przypadku konieczności przewożenia plików z urządzenia można nadal korzystać z zabezpieczeń i niezawodności IoT Hub.

Na końcu tego samouczka uruchomisz aplikację konsolową języka Python:

* **FileUpload.py**, który przekazuje plik do magazynu przy użyciu zestawu SDK urządzenia w języku Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia w celu przekazania pliku do centrum IoT Hub.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Device** . Ten pakiet służy do koordynowania przekazywania plików z Centrum IoT Hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet [**Azure. Storage. blob**](https://pypi.org/project/azure-storage-blob/) . Ten pakiet jest używany do przekazywania pliku.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Utwórz plik testowy, który zostanie przekazany do magazynu obiektów BLOB.

1. Za pomocą edytora tekstów Utwórz plik **FileUpload.py** w folderze roboczym.

1. Dodaj następujące `import` instrukcje i zmienne na początku pliku **FileUpload.py** .

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. W pliku Zastąp `[Device Connection String]` ciąg połączeniem urządzenia usługi IoT Hub. Zamień na `[Full path to local file]` ścieżkę do pliku testowego utworzonego lub dowolnego pliku na urządzeniu, które chcesz przekazać.

1. Utwórz funkcję do przekazania pliku do magazynu obiektów blob:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Ta funkcja analizuje przekazaną w niej strukturę *blob_info* , aby utworzyć adres URL, którego używa do zainicjowania [usługi Azure. Storage. blob. BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Następnie przekazuje plik do magazynu obiektów blob platformy Azure przy użyciu tego klienta.

1. Dodaj następujący kod, aby połączyć klienta i przekazać plik:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Ten kod tworzy asynchroniczny **IoTHubDeviceClient** i używa następujących interfejsów API do zarządzania przekazywaniem plików za pomocą Centrum IoT:

    * **get_storage_info_for_blob** pobiera z Centrum IoT informacje o utworzonym wcześniej koncie magazynu. Te informacje obejmują nazwy hosta, nazwę kontenera, nazwę obiektu BLOB i token sygnatury dostępu współdzielonego. Informacje o magazynie są przekazywane do funkcji **store_blob** (utworzonej w poprzednim kroku), więc **BlobClient** w tej funkcji może uwierzytelniać się w usłudze Azure Storage. Metoda **get_storage_info_for_blob** zwraca również correlation_id, który jest używany w metodzie **notify_blob_upload_status** . Correlation_id jest IoT Hub sposób oznaczenia obiektu BLOB, nad którym pracujesz.

    * **notify_blob_upload_status** powiadamia IoT Hub o stanie operacji magazynu obiektów BLOB. Przekazujesz go correlation_id uzyskany przez metodę **get_storage_info_for_blob** . Jest on używany przez IoT Hub do powiadamiania każdej usługi, która może nasłuchiwać powiadomienia o stanie zadania przekazywania pliku.

1. Zapisz i zamknij plik **UploadFile.py** .

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. W wierszu polecenia w folderze roboczym Uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

2. Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **FileUpload** :

    ![Wyjście z aplikacji symulowanej — urządzenie](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Możesz użyć portalu, aby wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

    ![Przekazany plik](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz w dalszym ciągu eksplorować funkcje i scenariusze dotyczące programu IoT Hub z następującymi artykułami:

* [Programistyczne tworzenie Centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Dowiedz się więcej o usłudze Azure Blob Storage, korzystając z następujących linków:

* [Dokumentacja usługi Azure Blob Storage](../storage/blobs/index.yml)

* [Dokumentacja interfejsu API platformy Azure Blob Storage dla języka Python](/python/api/overview/azure/storage-blob-readme?view=azure-python)