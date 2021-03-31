---
title: Planowanie zadań przy użyciu usługi Azure IoT Hub (Python) | Microsoft Docs
description: Jak zaplanować zadanie IoT Hub platformy Azure w celu wywołania metody bezpośredniej na wielu urządzeniach. Korzystając z zestawów SDK usługi Azure IoT dla języka Python, można zaimplementować aplikacje symulowanych urządzeń i aplikację usługi w celu uruchomienia zadania.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876686"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Planowanie i emitowanie zadań (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza tworzenie i śledzenie zadań, które umożliwiają planowanie i aktualizowanie milionów urządzeń.  Zadania mogą służyć do wykonywania następujących czynności:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

Koncepcyjnie, zadanie otacza jedną z tych akcji i śledzi postęp wykonywania w odniesieniu do zestawu urządzeń, który jest zdefiniowany przez zapytanie o pojedyncze urządzenie.  Na przykład aplikacja zaplecza może użyć zadania do wywołania metody ponownego rozruchu na urządzeniach 10 000, określonych przez zapytanie o sznurki urządzenia i zaplanowane w przyszłości.  Następnie aplikacja może śledzić postęp, ponieważ każdy z tych urządzeń otrzyma i wykona metodę ponownego uruchomienia.

Dowiedz się więcej o każdej z tych funkcji w następujących artykułach:

* Sznurki i właściwości urządzenia: [wprowadzenie do bliźniaczych reprezentacjii urządzeń](iot-hub-python-twin-getstarted.md) i [Samouczek: jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [przewodnik dewelopera IoT Hub — bezpośrednie metody](iot-hub-devguide-direct-methods.md) i [Samouczek: metody bezpośrednie](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację symulowanego urządzenia w języku Python, która ma metodę bezpośrednią, która umożliwia **lockDoor**, który może być wywoływany przez zaplecze rozwiązania.

* Utwórz aplikację konsolową języka Python, która wywołuje metodę **lockDoor** Direct w aplikacji symulowanego urządzenia przy użyciu zadania i aktualizuje odpowiednie właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka będziesz mieć dwie aplikacje w języku Python:

**simDevice.py**, który łączy się z Centrum IoT Hub przy użyciu tożsamości urządzenia i otrzymuje metodę **lockDoor** Direct.

**scheduleJobService.py**, która wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia i aktualizuje odpowiednie właściwości sznurka urządzenia przy użyciu zadania.

> [!NOTE]
> **Zestaw SDK usługi Azure IoT dla języka Python** nie obsługuje bezpośrednio funkcji **zadań** . W zamian ten samouczek oferuje alternatywne rozwiązanie wykorzystujące asynchroniczne wątki i czasomierze. Aby uzyskać więcej aktualizacji, zobacz listę funkcji **zestawu SDK klienta usługi** na stronie [zestawu Azure IoT SDK dla języka Python](https://github.com/Azure/azure-iot-sdk-python) .
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową w języku Python, która reaguje na bezpośrednią metodę wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor** .

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstów Utwórz nowy plik **simDevice.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **simDevice.py** . Zamień na `deviceConnectionString` Parametry połączenia utworzonego powyżej urządzenia:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Dodaj następujące wywołanie zwrotne funkcji, aby obsłużyć metodę **lockDoor** :

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Dodaj kolejne wywołanie zwrotne funkcji w celu obsługi aktualizacji bliźniaczych reprezentacji urządzeń:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Dodaj następujący kod, aby zarejestrować procedurę obsługi dla metody **lockDoor** . Uwzględnij również `main` procedurę:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Zapisz i zamknij plik **simDevice.py** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza, która wywołuje metodę bezpośrednią na urządzeniu i aktualizuje sznurek urządzeń. Usługa musi mieć uprawnienia do **połączenia z usługą** , aby wywołać metodę bezpośrednią na urządzeniu. Usługa wymaga również uprawnienia do **odczytu** rejestru i **zapisu** rejestru w celu odczytu i zapisu rejestru tożsamości. Nie ma domyślnych zasad dostępu współdzielonego, które zawierają tylko te uprawnienia, więc należy ją utworzyć.

Aby utworzyć zasady dostępu współdzielonego, które przyznają uprawnienia do **nawiązywania połączenia z usługą**, **odczytu rejestru** i **zapisu rejestru** oraz do uzyskiwania parametrów połączenia dla tych zasad, wykonaj następujące kroki:

1. Otwórz Centrum IoT Hub w [Azure Portal](https://portal.azure.com). Najprostszym sposobem, aby uzyskać dostęp do usługi IoT Hub, jest wybranie opcji **grupy zasobów**, wybranie grupy zasobów, w której znajduje się centrum IoT Hub, a następnie wybranie Centrum IoT Hub z listy zasobów.

2. W okienku po lewej stronie Centrum IoT wybierz pozycję **zasady dostępu współdzielonego**.

3. W górnym menu powyżej listy zasad wybierz pozycję **Dodaj**.

4. W okienku **Dodawanie zasad dostępu współdzielonego** Wprowadź opisową nazwę zasad. na przykład: *serviceAndRegistryReadWrite*. W obszarze **uprawnienia** wybierz **pozycję Usługa Connect** i **Zapisz rejestr** (**odczyt rejestru** jest wybierany automatycznie po wybraniu pozycji **zapis w rejestrze**). Następnie wybierz pozycję **Utwórz**.

    ![Pokaż, jak dodać nowe zasady dostępu współdzielonego](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. W okienku **zasady dostępu współdzielonego** wybierz nowe zasady z listy zasad.

6. W obszarze **klucze dostępu współdzielonego** wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań związanych z wywoływaniem metody bezpośredniej i aktualizowaniem właściwości sznurka urządzenia

W tej sekcji utworzysz aplikację konsolową języka Python, która inicjuje zdalne **lockDoor** na urządzeniu przy użyciu metody bezpośredniej, a także aktualizuje odpowiednie właściwości sznurka urządzenia.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Za pomocą edytora tekstów Utwórz nowy plik **scheduleJobService.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **scheduleJobService.py** . Zastąp `{IoTHubConnectionString}` symbol zastępczy parametrami połączenia usługi IoT Hub skopiowanymi wcześniej w temacie [pobieranie parametrów połączenia usługi IoT Hub](#get-the-iot-hub-connection-string). Zastąp `{deviceId}` symbol zastępczy identyfikatorem urządzenia zarejestrowanym w zarejestrowaniu [nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub):

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Dodaj następującą funkcję, która jest używana do wykonywania zapytań dotyczących urządzeń:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Dodaj następujące metody, aby uruchomić zadania, które wywołują metodę bezpośrednią i sznurek urządzenia:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Dodaj następujący kod, aby zaplanować zadania i zaktualizować stan zadania. Uwzględnij również `main` procedurę:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Zapisz i zamknij plik **scheduleJobService.py** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia:

    ```cmd/sh
    python simDevice.py
    ```

2. W innym wierszu polecenia w katalogu roboczym Uruchom następujące polecenie, aby wyzwolić zadania w celu zablokowania drzwi i zaktualizowania sznurka:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. W konsoli programu są widoczne odpowiedzi urządzenia do metody bezpośredniej i aktualizacji urządzenia bliźniaczych reprezentacji.

    ![Przykład zadania IoT Hub 1 — dane wyjściowe urządzenia](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Przykład zadania IoT Hub 2 — dane wyjściowe urządzenia](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania do zaplanowania metody bezpośredniej dla urządzenia i aktualizacji właściwości sznurka urządzenia.

Aby nadal zacząć korzystać z wzorców IoT Hub i zarządzania urządzeniami, takimi jak zdalne przez aktualizację oprogramowania układowego, zobacz artykuł [Jak przeprowadzić aktualizację oprogramowania układowego](tutorial-firmware-update.md).
