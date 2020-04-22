---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą języka Python | Dokumenty firmy Microsoft
description: Jak przekazać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzenia Usługi Azure IoT dla języka Python. Przekazane pliki są przechowywane w kontenerze obiektów blob magazynu platformy Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 47fb7c615389e24322450ed1785aa7da9ec50db6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759690"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym artykule pokazano, jak użyć [możliwości przekazywania plików usługi IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania pliku do [magazynu obiektów blob platformy Azure](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpiecznie podaj kontener magazynu do przekazywania pliku.

* Użyj klienta języka Python, aby przekazać plik za pośrednictwem centrum IoT Hub.

Funkcja [Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT](quickstart-send-telemetry-python.md) hub pokazuje podstawowe funkcje obsługi wiadomości między urządzeniami w chmurze usługi IoT Hub. Jednak w niektórych scenariuszach nie można łatwo mapować danych wysyłanych przez urządzenia do komunikatów stosunkowo małych urządzeń do chmury akceptowanych przez usługę IoT Hub. Gdy potrzebujesz plików wyżynnych z urządzenia, nadal możesz korzystać z zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka uruchomisz aplikację konsoli Python:

* **FileUpload.py**, który przesyła plik do magazynu przy użyciu SDK urządzenia Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia, aby przekazać plik do centrum IoT Hub.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **urządzenia azure-iot.** Ten pakiet służy do koordynowania przekazywania plików z centrum IoT hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Ten pakiet służy do przekazywania plików.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Utwórz plik testowy, który zostanie przekazany do magazynu obiektów blob.

1. Za pomocą edytora tekstu utwórz plik **FileUpload.py** w folderze roboczym.

1. Dodaj następujące `import` instrukcje i zmienne na początku pliku **FileUpload.py.**

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. W pliku zastąp `[Device Connection String]` ciągiem połączenia urządzenia centrum IoT. Zamień `[Full path to local file]` ścieżkę do utworzonego pliku testowego lub dowolnego pliku na urządzeniu, który chcesz przekazać.

1. Utwórz funkcję przekazywania pliku do magazynu obiektów blob:

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

    Ta funkcja analizuje *blob_info* strukturę przekazywana do niej w celu utworzenia adresu URL używanego do inicjowania [elementu azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Następnie przekazuje plik do magazynu obiektów blob platformy Azure przy użyciu tego klienta.

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

    Ten kod tworzy asynchroniczne **IoTHubDeviceClient** i używa następujących interfejsów API do zarządzania przekazywania plików z centrum IoT hub:

    * **get_storage_info_for_blob** pobiera z centrum IoT informacje o utworzonym wcześniej połączonym koncie magazynu. Te informacje obejmują nazwę hosta, nazwę kontenera, nazwę obiektu blob i token sygnatury dostępu Współdzielonego. Informacje o magazynie są przekazywane do funkcji **store_blob** (utworzonej w poprzednim kroku), więc **obiekt BlobClient** w tej funkcji może uwierzytelniać się za pomocą magazynu platformy Azure. Metoda **get_storage_info_for_blob** zwraca również correlation_id, który jest używany w **notify_blob_upload_status** metody. correlation_id jest sposób IoT Hub oznaczania, który obiekt blob pracujesz nad.

    * **notify_blob_upload_status** powiadamia Centrum IoT o stanie operacji magazynu obiektów blob. Należy przekazać go correlation_id uzyskanych metodą **get_storage_info_for_blob.** Usługa IoT Hub jest używana przez usługę IoT Hub do powiadamiania dowolnej usługi, która może nasłuchiwać powiadomienia o stanie zadania przekazywania pliku.

1. Zapisz i zamknij plik **UploadFile.py.**

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz możesz przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia w folderze roboczym uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

2. Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **FileUpload:**

    ![Dane wyjściowe z aplikacji symulowanego urządzenia](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Za pomocą portalu można wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

    ![Przesłany plik](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z możliwości przekazywania plików usługi IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz kontynuować eksplorowanie funkcji i scenariuszy centrum IoT za pomocą następujących artykułów:

* [Programowo tworzenie centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Dowiedz się więcej o usłudze Azure Blob Storage, wykonując następujące łącza:

* [Dokumentacja usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/)

* [Dokumentacja usługi Azure Blob Storage dla interfejsu API języka Python](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
