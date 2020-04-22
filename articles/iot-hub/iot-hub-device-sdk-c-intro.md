---
title: Zestaw SDK urządzenia Usługi Azure IoT dla języka C | Dokumenty firmy Microsoft
description: Wprowadzenie do zestawu SDK urządzenia Usługi Azure IoT dla języka C i dowiedz się, jak tworzyć aplikacje urządzeń komunikujące się z centrum IoT hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b9b27bb142cb729536a3b7a561ed8b8ff5e0ccf5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731319"
---
# <a name="azure-iot-device-sdk-for-c"></a>Zestaw SDK urządzenia Usługi Azure IoT dla języka C

Zestaw **SDK urządzenia Usługi Azure IoT** to zestaw bibliotek zaprojektowany w celu uproszczenia procesu wysyłania wiadomości do i odbierania wiadomości z usługi **Azure IoT Hub.** Istnieją różne odmiany zestawu SDK, z których każdy jest przeznaczony dla określonej platformy, ale w tym artykule opisano **zestaw SDK urządzenia Usługi Azure IoT dla języka C.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zestaw SDK urządzenia Azure IoT dla języka C jest napisany w języku ANSI C (C99), aby zmaksymalizować przenośność. Ta funkcja sprawia, że biblioteki dobrze nadaje się do pracy na wielu platformach i urządzeniach, zwłaszcza, gdy minimalizacja dysku i pamięci jest priorytetem.

Istnieje szeroki zakres platform, na których zestaw SDK został przetestowany (zobacz [wykaz urządzeń certyfikatu platformy Azure dla IoT,](https://catalog.azureiotsolutions.com/) aby uzyskać szczegółowe informacje). Mimo że ten artykuł zawiera instruktaże przykładowego kodu uruchomionego na platformie Windows, kod opisany w tym artykule jest identyczny w zakresie obsługiwanych platform.

Poniższy klip wideo przedstawia omówienie zestawu SDK usługi Azure IoT dla języka C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

W tym artykule przedstawiono architekturę zestawu SDK urządzenia Usługi Azure IoT dla języka C. Pokazuje, jak zainicjować bibliotekę urządzeń, wysłać dane do usługi IoT Hub i odbierać wiadomości z niej. Informacje zawarte w tym artykule powinny wystarczyć, aby rozpocząć korzystanie z sdk, ale również zawiera wskazówki do dodatkowych informacji o bibliotekach.

## <a name="sdk-architecture"></a>Architektura SDK

Zestaw [**SDK urządzenia Usługi Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium C GitHub można znaleźć i wyświetlić szczegóły interfejsu API w [odwołaniu do interfejsu API języka C.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Najnowszą wersję bibliotek można znaleźć w **gałęzi głównej** repozytorium:

  ![Zrzut ekranu przedstawiający główną gałąź repozytorium](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Podstawowa implementacja SDK znajduje się w folderze **klienta\_iothub,** który zawiera implementację najniższej warstwy interfejsu API w SDK: biblioteki **IoTHubClient.** Biblioteka **IoTHubClient** zawiera interfejsy API implementujące nieprzetworzone wiadomości do wysyłania wiadomości do Centrum IoT i odbierania wiadomości z usługi IoT Hub. Podczas korzystania z tej biblioteki, jesteś odpowiedzialny za implementowanie serializacji wiadomości, ale inne szczegóły komunikacji z Usługi IoT Hub są obsługiwane dla Ciebie.

* Folder **serializatora** zawiera funkcje pomocnicze i przykłady, które pokazują, jak serializować dane przed wysłaniem do usługi Azure IoT Hub przy użyciu biblioteki klienta. Korzystanie z serializatora nie jest obowiązkowe i jest zapewnione jako udogodnienie. Aby użyć biblioteki **serializatora,** należy zdefiniować model, który określa dane do wysłania do Usługi IoT Hub i wiadomości, które mają otrzymać od niego. Po zdefiniowaniu modelu, SDK zapewnia powierzchni interfejsu API, który umożliwia łatwą pracę z urządzenia do chmury i chmury do urządzenia wiadomości bez martwienia się o szczegóły serializacji. Biblioteka zależy od innych bibliotek typu open source, które implementują transport przy użyciu protokołów, takich jak MQTT i AMQP.

* Biblioteka **IoTHubClient** zależy od innych bibliotek typu open source:

  * Biblioteka [narzędzi udostępnionych usługi Azure C,](https://github.com/Azure/azure-c-shared-utility) która zapewnia typowe funkcje dla podstawowych zadań (takich jak ciągi, manipulowanie listami i we/wy) potrzebne w kilku zestawach SDK C związanych z platformą Azure.

  * Biblioteka [uAMQP platformy Azure,](https://github.com/Azure/azure-uamqp-c) która jest implementacją po stronie klienta usługi AMQP zoptymalizowaną pod kątem urządzeń o ograniczonym zakresie zasobów.

  * Biblioteka [azure uMQTT,](https://github.com/Azure/azure-umqtt-c) która jest biblioteką ogólnego przeznaczenia implementującą protokół MQTT i zoptymalizowaną pod kątem urządzeń o ograniczonym zasobach.

Korzystanie z tych bibliotek jest łatwiejsze do zrozumienia, patrząc na przykładowy kod. W poniższych sekcjach przedstawiono kilka przykładowych aplikacji, które są zawarte w zestawie SDK. Ten instruktaż powinien dać dobre wyczucie dla różnych możliwości warstw architektonicznych sdk sdk i wprowadzenie do sposobu działania interfejsów API.

## <a name="before-you-run-the-samples"></a>Przed uruchomieniem próbek

Aby można było uruchomić przykłady w SDK urządzenia Usługi Azure IoT dla języka C, należy [utworzyć wystąpienie usługi IoT Hub](iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure. Następnie wykonaj następujące zadania:

* Przygotowywanie środowiska projektowego
* Uzyskiwanie poświadczeń urządzenia.

### <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Pakiety są dostarczane dla typowych platform (takich jak NuGet dla Windows lub apt_get dla Debiana i Ubuntu), a przykłady używają tych pakietów, jeśli są dostępne. W niektórych przypadkach należy skompilować zestaw SDK dla lub na urządzeniu. Jeśli chcesz skompilować zestaw SDK, zobacz [Przygotowywanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w repozytorium GitHub.

Aby uzyskać przykładowy kod aplikacji, pobierz kopię zestawu SDK z usługi GitHub. Pobierz kopię źródła z **gałęzi głównej** [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Uzyskiwanie poświadczeń urządzenia

Teraz, gdy masz przykładowy kod źródłowy, następną rzeczą do zrobienia jest uzyskanie zestawu poświadczeń urządzenia. Aby urządzenie mogło uzyskać dostęp do centrum IoT hub, należy najpierw dodać urządzenie do rejestru tożsamości usługi IoT Hub. Po dodaniu urządzenia otrzymasz zestaw poświadczeń urządzenia, które są potrzebne do tego urządzenia, aby móc połączyć się z centrum IoT hub. Przykładowe aplikacje omówione w następnej sekcji oczekują tych poświadczeń w postaci **ciągu połączenia urządzenia**.

Istnieje kilka narzędzi typu open source ułatwiających zarządzanie centrum IoT Hub.

* Aplikacja systemu Windows o nazwie [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Wieloplatformowe rozszerzenie kodu programu Visual Studio o nazwie [Narzędzia Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

* Wieloplatformowy interfejs wiersza polecenia języka Python o nazwie [rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

W tym samouczku użyto narzędzia *do eksplorowania urządzeń graficznych.* Narzędzia *IoT azure dla programu VS Code* można użyć, jeśli opracujesz w programie VS Code. Można również użyć *rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure 2.0,* jeśli wolisz użyć narzędzia interfejsu wiersza polecenia.

Narzędzie Eksploratora urządzeń używa bibliotek usługi Azure IoT do wykonywania różnych funkcji w centrum IoT Hub, w tym dodawania urządzeń. Jeśli używasz narzędzia Eksploratora urządzeń do dodania urządzenia, otrzymasz ciąg połączenia dla urządzenia. Ten ciąg połączenia jest potrzebny do uruchamiania przykładowych aplikacji.

Jeśli nie znasz narzędzia eksploratora urządzeń, w poniższej procedurze opisano sposób używania go do dodania urządzenia i uzyskania ciągu połączenia urządzenia.

1. Aby zainstalować narzędzie Eksploratora urządzeń, zobacz [Jak używać Eksploratora urządzeń dla urządzeń IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Po uruchomieniu programu jest widoczny ten interfejs:

   ![— dwuszenasze— dwuosobowe](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Wprowadź **ciąg połączenia usługi IoT Hub** w pierwszym polu i kliknij przycisk **Aktualizuj**. Ten krok konfiguruje narzędzie tak, aby mógł komunikować się z Centrum IoT Hub. 

The **Connection String** can be found under **IoT Hub Service** > **Settings** > **Shared Access Policy** > **iothubowner**.

1. Po skonfigurowaniu ciągu połączenia usługi IoT Hub kliknij kartę **Zarządzanie:**

   ![Dwu- zdań z funkcją Badacz urządzeń / Zarządzanie](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Ta karta służy do zarządzania urządzeniami zarejestrowanymi w centrum IoT Hub.

1. Urządzenie można utworzyć, klikając przycisk **Utwórz.** Zostanie wyświetlone okno dialogowe z zestawem wstępnie wypełnionych kluczy (podstawowych i pomocniczych). Wprowadź **identyfikator urządzenia,** a następnie kliknij przycisk **Utwórz**.

   ![Zrzut ekranu przedstawiający tworzenie urządzenia](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Po utworzeniu urządzenia lista Urządzenia jest aktualizowana ze wszystkimi zarejestrowanymi urządzeniami, w tym z utworzonym właśnie urządzeniem. Po kliknięciu prawym przyciskiem myszy nowego urządzenia zostanie wyświetle się to menu:

   ![Wynik bliźniaczego kliknięcia bliźniaczego eksploratora urządzeń](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. W przypadku **wybrania opcji Kopiuj ciąg połączenia dla wybranego urządzenia**ciąg połączenia urządzenia zostanie skopiowany do schowka. Zachowaj kopię ciągu połączenia urządzenia. Jest on potrzebny podczas uruchamiania przykładowych aplikacji opisanych w poniższych sekcjach.

Po wykonaniu powyższych kroków możesz rozpocząć uruchamianie kodu. Większość próbek ma stałą u góry głównego pliku źródłowego, która umożliwia wprowadzenie ciągu połączenia. Na przykład odpowiedni wiersz z **iothub_client\_próbek\_iothub_convenience_sample** aplikacji jest wyświetlany w następujący sposób.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Korzystanie z biblioteki IoTHubClient

W folderze **klienta iothub\_** w repozytorium [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) znajduje się folder **przykładów zawierający** aplikację o nazwie **przykładowe\_\_\_mqtt klienta iothub**.

Wersja systemu Windows **iothub_client\_przykładów\_iothub_convenience_sample** aplikacji zawiera następujące rozwiązanie programu Visual Studio:

  ![Eksplorator rozwiązań programu Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio prosi o przekierowanie projektu do najnowszej wersji, zaakceptuj monit.

To rozwiązanie zawiera jeden projekt. Istnieją cztery pakiety NuGet zainstalowane w tym rozwiązaniu:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Podczas pracy z zestawem SDK zawsze potrzebujesz pakietu **Microsoft.Azure.C.SharedUtility.** W tym przykładzie użyto protokołu MQTT, w związku z czym należy dołączyć pakiety **Microsoft.Azure.umqtt** i **Microsoft.Azure.IoTHub.MqttTransport** (istnieją równoważne pakiety dla pakietów AMQP i HTTPS). Ponieważ w przykładzie używa biblioteki **IoTHubClient,** należy również dołączyć **pakiet Microsoft.Azure.IoTHubHubClient** w rozwiązaniu.

Implementację przykładowej aplikacji można znaleźć w **\_iothub_client\_przykładach iothub_convenience_sample** pliku źródłowego.

Poniższe kroki używają tej przykładowej aplikacji, aby przejść przez to, co jest wymagane do korzystania z biblioteki **IoTHubClient.**

### <a name="initialize-the-library"></a>Inicjowanie biblioteki

> [!NOTE]
> Przed rozpoczęciem pracy z bibliotekami może być konieczne wykonanie inicjowania specyficznego dla platformy. Na przykład, jeśli planujesz używać usługi AMQP w systemie Linux, należy zainicjować bibliotekę OpenSSL. Przykłady w [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c) wywołać **\_platformę** funkcji narzędzia init po uruchomieniu klienta i wywołać funkcję **deinit platformy\_** przed zamknięciem. Te funkcje są zadeklarowane w pliku nagłówka platform.h. Sprawdź definicje tych funkcji dla platformy docelowej w [repozytorium,](https://github.com/Azure/azure-iot-sdk-c) aby ustalić, czy należy dołączyć dowolny kod inicjalizacji specyficzne dla platformy w kliencie.

Aby rozpocząć pracę z bibliotekami, najpierw przydziel dojście klienta usługi IoT Hub:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Do tej funkcji należy przekazać kopię ciągu połączenia urządzenia uzyskanego z narzędzia eksploratora urządzeń. Należy również wyznaczyć protokół komunikacyjny do użycia. W tym przykładzie użyto MQTT, ale opcje są również opcje PROTOKOŁU AMQP i HTTPS.

Jeśli masz prawidłowy **uchwyt\_\_klienta IOTHUB,** można rozpocząć wywoływanie interfejsów API do wysyłania i odbierania wiadomości do i z Usługi IoT Hub.

### <a name="send-messages"></a>Wysyłanie komunikatów

Przykładowa aplikacja konfiguruje pętlę do wysyłania wiadomości do centrum IoT Hub. Następujący fragment kodu:

- Tworzy wiadomość.
- Dodaje właściwość do wiadomości.
- Wysyła wiadomość.

Najpierw utwórz wiadomość:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Za każdym razem, gdy wysyłasz wiadomość, należy określić odwołanie do funkcji wywołania zwrotnego, która jest wywoływana podczas wysyłania danych. W tym przykładzie funkcja wywołania zwrotnego nosi nazwę **SendConfirmationCallback**. We werżem jest następująca funkcja wywołania zwrotnego:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Zanotuj wywołanie **funkcji Zniszczenia\_IoTHubMessage** po zakończeniu pracy z komunikatem. Ta funkcja zwalnia zasoby przydzielone podczas tworzenia wiadomości.

### <a name="receive-messages"></a>Odbieranie komunikatów

Odbieranie wiadomości jest operacją asynchroniką. Najpierw zarejestruj wywołanie zwrotne, aby wywołać, gdy urządzenie otrzyma komunikat:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Ostatni parametr jest wskaźnik void do cokolwiek chcesz. W przykładzie jest to wskaźnik do liczby całkowitej, ale może to być wskaźnik do bardziej złożonej struktury danych. Ten parametr umożliwia funkcję wywołania zwrotnego do pracy w stanie udostępnionym z wywołującym tej funkcji.

Gdy urządzenie odbiera komunikat, wywoływana jest zarejestrowana funkcja wywołania zwrotnego. Ta funkcja wywołania zwrotnego pobiera:

* Identyfikator wiadomości i identyfikator korelacji z wiadomości.
* Treść wiadomości.
* Wszelkie właściwości niestandardowe z wiadomości.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Użyj **funkcji IoTHubMessage\_GetByteArray,** aby pobrać wiadomość, która w tym przykładzie jest ciągiem.

### <a name="uninitialize-the-library"></a>Niezajęcie biblioteki

Po zakończeniu wysyłania zdarzeń i odbierania wiadomości można niezainicjować bibliotekę IoT. Aby to zrobić, należy wykonać następujące wywołanie funkcji:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

To wywołanie zwalnia zasoby wcześniej przydzielone przez funkcję **IoTHubClient\_CreateFromConnectionString.**

Jak widać, jest łatwo wysyłać i odbierać wiadomości z biblioteki **IoTHubClient.** Biblioteka obsługuje szczegóły komunikacji z Centrum IoT, w tym, który protokół do użycia (z punktu widzenia dewelopera jest to prosta opcja konfiguracji).

Biblioteka **IoTHubClient** zapewnia również precyzyjną kontrolę nad serializowanie danych wysyłanych przez urządzenie do usługi IoT Hub. W niektórych przypadkach ten poziom kontroli jest zaletą, ale w innych jest to szczegół implementacji, który nie chcesz się zajmować. W takim przypadku można rozważyć użycie biblioteki **serializatora,** która jest opisana w następnej sekcji.

## <a name="use-the-serializer-library"></a>Korzystanie z biblioteki serializatora

Koncepcyjnie biblioteka **serializatora** znajduje się na szczycie biblioteki **IoTHubClient** w SDK. Używa biblioteki **IoTHubClient** dla podstawowej komunikacji z Centrum IoT, ale dodaje możliwości modelowania, które usuwają ciężar radzenia sobie z serializacji wiadomości od dewelopera. Jak działa ta biblioteka najlepiej wykazać przykład.

Wewnątrz folderu **serializatora** w [repozytorium azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c)znajduje się folder **przykładów zawierający** aplikację o nazwie **simplesample\_mqtt**. Wersja systemu Windows w tym przykładzie zawiera następujące rozwiązanie programu Visual Studio:

  ![Rozwiązanie programu Visual Studio dla przykładu mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio prosi o przekierowanie projektu do najnowszej wersji, zaakceptuj monit.

Podobnie jak w poprzednim przykładzie, ten zawiera kilka pakietów NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Widziałeś większość z tych pakietów w poprzednim przykładzie, ale **Microsoft.Azure.IoTHub.Serializer** jest nowy. Ten pakiet jest wymagany podczas korzystania z biblioteki **serializatora.**

Implementację przykładowej aplikacji można znaleźć w **\_iothub_client\_przykładach iothub_convenience_sample** pliku.

W poniższych sekcjach przedstawiono kluczowe części tego przykładu.

### <a name="initialize-the-library"></a>Inicjowanie biblioteki

Aby rozpocząć pracę z biblioteką **serializatora,** wywołanie interfejsów API inicjowania:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

Wywołanie funkcji **init serializatora\_** jest wywołaniem jednorazowym i inicjuje podstawową bibliotekę. Następnie należy wywołać funkcję **IoTHubClient\_\_LL CreateFromConnectionString,** która jest tym samym interfejsem API, co w przykładzie **IoTHubClient.** To wywołanie ustawia parametry połączenia urządzenia (to wywołanie jest również w miejscu, w którym można wybrać protokół, którego chcesz użyć). W tym przykładzie używa MQTT jako transportu, ale można użyć PROTOKOŁU AMQP lub HTTPS.

Na koniec wywołać CREATE **\_\_MODEL WYSTĄPIENIE** funkcji. **WeatherStation** jest obszar nazw modelu i **ContosoAnemometer** jest nazwą modelu. Po utworzeniu wystąpienia modelu można go użyć do rozpoczęcia wysyłania i odbierania wiadomości. Jednak ważne jest, aby zrozumieć, co to jest model.

### <a name="define-the-model"></a>Definiowanie modelu

Model w bibliotece **serializatora** definiuje komunikaty, które urządzenie może wysyłać do usługi IoT Hub i komunikaty, nazywane *akcjami* w języku modelowania, które może odbierać. Model można zdefiniować przy użyciu zestawu makr C, jak w **\_iothub_client przykładów\_iothub_convenience_sample** przykładowej aplikacji:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Makra **BEGIN\_NAMESPACE** i **\_END NAMESPACE** przyjmują obszar nazw modelu jako argument. Oczekuje się, że wszystko między tymi makrami jest definicja modelu lub modeli i struktur danych, które używają modeli.

W tym przykładzie istnieje pojedynczy model o nazwie **ContosoAnemometer**. Ten model definiuje dwa fragmenty danych, które urządzenie może wysyłać do centrum IoT Hub: **DeviceId** i **WindSpeed**. Definiuje również trzy akcje (wiadomości), które urządzenie może odbierać: **TurnFanOn**, **TurnFanOff**i **SetAirResistance**. Każdy element danych ma typ, a każda akcja ma nazwę (i opcjonalnie zestaw parametrów).

Dane i akcje zdefiniowane w modelu definiują powierzchnię interfejsu API, której można używać do wysyłania wiadomości do centrum IoT Hub i odpowiadania na wiadomości wysyłane do urządzenia. Użycie tego modelu jest najlepiej rozumiane przez przykład.

### <a name="send-messages"></a>Wysyłanie komunikatów

Model definiuje dane, które można wysłać do Centrum IoT Hub. W tym przykładzie oznacza to jeden z dwóch elementów danych zdefiniowanych przy użyciu **makra WITH_DATA.** Istnieje kilka kroków wymaganych do **wysyłania DeviceId** i **WindSpeed** wartości do centrum IoT hub. Pierwszym z nich jest ustawienie danych, które chcesz wysłać:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model zdefiniowany wcześniej umożliwia ustawienie wartości przez ustawienie elementów członkowskich **struktury**. Następnie zseralizuj wiadomość, którą chcesz wysłać:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Ten kod serializuje urządzenie do chmury do buforu (odwołuje się do **miejsca docelowego).** Kod następnie wywołuje funkcję **sendMessage,** aby wysłać wiadomość do Centrum IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Drugi do ostatniego parametru **IoTHubClient\_LL\_SendEventAsync** jest odwołanie do funkcji wywołania zwrotnego, który jest wywoływany, gdy dane są pomyślnie wysyłane. Oto funkcja wywołania zwrotnego w przykładzie:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Drugi parametr jest wskaźnikiem do kontekstu użytkownika; ten sam wskaźnik przekazany do **IoTHubClient\_LL\_SendEventAsync**. W tym przypadku kontekst jest prostym licznikiem, ale może to być wszystko, co chcesz.

To wszystko, co jest do wysyłania wiadomości z urządzenia do chmury. Jedyne, co pozostało do pokrycia, to jak odbierać wiadomości.

### <a name="receive-messages"></a>Odbieranie komunikatów

Odbieranie wiadomości działa podobnie do sposobu pracy wiadomości w **bibliotece IoTHubClient.** Najpierw rejestrujesz funkcję wywołania zwrotnego wiadomości:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Następnie należy napisać funkcję wywołania zwrotnego, która jest wywoływana po odebraniu wiadomości:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ten kod jest standardowy - jest taki sam dla każdego rozwiązania. Ta funkcja odbiera komunikat i zajmuje się kierowaniem go do odpowiedniej funkcji za pośrednictwem wywołania **polecenia EXECUTE\_**. Funkcja wywoływana w tym momencie zależy od definicji akcji w modelu.

Podczas definiowania akcji w modelu, należy zaimplementować funkcję, która jest wywoływana, gdy urządzenie odbiera odpowiedni komunikat. Jeśli na przykład model definiuje tę akcję:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Zdefiniuj funkcję z tym podpisem:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Należy zauważyć, jak nazwa funkcji pasuje do nazwy akcji w modelu i że parametry funkcji odpowiadają parametrom określonym dla akcji. Pierwszy parametr jest zawsze wymagany i zawiera wskaźnik do wystąpienia modelu.

Gdy urządzenie otrzyma komunikat, który pasuje do tego podpisu, wywoływana jest odpowiednia funkcja. W związku z tym oprócz konieczności dołączania kodu standardowego z **IoTHubMessage**, odbieranie wiadomości jest tylko kwestią definiowania prostej funkcji dla każdej akcji zdefiniowanej w modelu.

### <a name="uninitialize-the-library"></a>Niezajęcie biblioteki

Po zakończeniu wysyłania danych i odbierania wiadomości można niezainicjować bibliotekę IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Każda z tych trzech funkcji jest zgodna z trzema opisanymi wcześniej funkcjami inicjowania. Wywoływanie tych interfejsów API gwarantuje, że można zwolnić wcześniej przydzielone zasoby.

## <a name="next-steps"></a>Następne kroki

W tym artykule okryją się podstawy korzystania z bibliotek w **sdk urządzenia Usługi Azure IoT dla języka C**. Dostarczyła wystarczającej ilości informacji, aby zrozumieć, co jest zawarte w zestawie SDK, jego architektura i jak rozpocząć pracę z przykładami systemu Windows. W następnym artykule kontynuuje opis SDK, wyjaśniając [więcej na temat biblioteki IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [zestawów SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
