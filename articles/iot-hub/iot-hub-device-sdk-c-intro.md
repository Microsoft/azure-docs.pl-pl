---
title: Zestaw SDK urządzeń Azure IoT dla języka C | Microsoft Docs
description: Rozpocznij pracę z zestawem SDK urządzeń Azure IoT dla języka C i Dowiedz się, jak tworzyć aplikacje dla urządzeń, które komunikują się z Centrum IoT.
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
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 9b870e21ffd5c6a8261b6731b939b5dff558256d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501196"
---
# <a name="azure-iot-device-sdk-for-c"></a>Zestaw SDK urządzeń Azure IoT dla języka C

**Zestaw SDK urządzeń Azure IoT** to zestaw bibliotek zaprojektowanych w celu uproszczenia procesu wysyłania komunikatów do i otrzymywania komunikatów z usługi **Azure IoT Hub** . Istnieją różne warianty zestawu SDK, które są przeznaczone dla konkretnej platformy, ale w tym artykule opisano **zestaw SDK urządzeń Azure IoT dla języka C**.

> [!NOTE]
> Osadzony zestaw SDK języka C jest alternatywą dla urządzeń z ograniczeniami, które obsługują podejście do dołączenia do sieci (BYON). Deweloperzy IoT mogą korzystać z możliwości MQTT klienta, TLS i wybranego gniazda, aby utworzyć rozwiązanie dla urządzeń. [Dowiedz się więcej o osadzonym zestawie SDK języka C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zestaw SDK urządzeń Azure IoT dla języka C jest zapisywana w języku ANSI C (C99) w celu zmaksymalizowania przenośności. Ta funkcja sprawia, że biblioteki dobrze pasują do działania na wielu platformach i urządzeniach, szczególnie w przypadku zminimalizowania rozmiaru dysku i pamięci.

Istnieje szeroka gama platform, na których przetestowano zestaw SDK (Aby uzyskać szczegółowe informacje, zobacz [katalog urządzeń z certyfikatem platformy Azure dla IoT](https://catalog.azureiotsolutions.com/) ). Mimo że ten artykuł zawiera wskazówki dotyczące przykładowego kodu działającego na platformie Windows, kod opisany w tym artykule jest identyczny między zakresem obsługiwanych platform.

Poniższy film wideo przedstawia przegląd zestawu Azure IoT SDK dla języka C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

W tym artykule przedstawiono architekturę zestawu SDK urządzeń Azure IoT dla języka C. Pokazano, jak zainicjować bibliotekę urządzeń, wysłać dane do IoT Hub i odbierać od niej komunikaty. Informacje zawarte w tym artykule powinny być wystarczające, aby rozpocząć korzystanie z zestawu SDK, ale również zawiera wskaźniki do dodatkowych informacji o bibliotekach.

## <a name="sdk-architecture"></a>Architektura zestawu SDK

[**Zestaw SDK urządzeń Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium w serwisie GitHub i widok szczegółów interfejsu API można znaleźć w [dokumentacji interfejsu API języka c](/azure/iot-hub/iot-c-sdk-ref/).

Najnowszą wersję bibliotek można znaleźć w gałęzi **głównej** repozytorium:

  ![Zrzut ekranu przedstawiający główną gałąź repozytorium](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Podstawowa implementacja zestawu SDK znajduje się w folderze **iothub \_ Client** zawierającym implementację najniższej warstwy interfejsu API w zestawie SDK: Biblioteka **usługi iothubclient** . Biblioteka **usługi iothubclient** zawiera interfejsy API implementujące nieprzetworzone wiadomości w celu wysyłania komunikatów do IoT Hub i otrzymywania komunikatów z IoT Hub. W przypadku korzystania z tej biblioteki użytkownik jest odpowiedzialny za implementację serializacji wiadomości, ale inne szczegóły komunikacji z IoT Hub są obsługiwane.

* Folder **serializatorów** zawiera funkcje pomocnika i przykłady, które pokazują, jak serializować dane przed wysłaniem do platformy Azure IoT Hub przy użyciu biblioteki klienckiej. Użycie serializatora nie jest obowiązkowe i jest udostępniane jako wygoda. Aby użyć biblioteki **serializatorów** , należy zdefiniować model, który określa dane do wysłania do IoT Hub i komunikaty, które powinny być od niego wysyłane. Po zdefiniowaniu modelu zestaw SDK udostępnia powierzchnię interfejsu API, która umożliwia łatwą współpracę z komunikatami z urządzenia do chmury i z chmury do urządzenia, bez konieczności pojmowania się szczegółami serializacji. Biblioteka jest zależna od innych bibliotek typu "open source", które implementują transport przy użyciu protokołów takich jak MQTT i AMQP.

* Biblioteka **usługi iothubclient** zależy od innych bibliotek typu "open source":

  * [Udostępniona biblioteka narzędzi platformy Azure C](https://github.com/Azure/azure-c-shared-utility) , która zapewnia typowe funkcje podstawowych zadań (takich jak ciągi, manipulowanie list i we/wy), które są odpowiednie dla kilku zestawów SDK języka C związanych z platformą Azure.

  * Biblioteka [usługi Azure uAMQP](https://github.com/Azure/azure-uamqp-c) , która jest implementacją klienta AMQP zoptymalizowaną pod kątem urządzeń z ograniczoną ilością zasobów.

  * Biblioteka [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) , która jest biblioteką ogólnego przeznaczenia implementującą protokół MQTT i zoptymalizowana pod kątem urządzeń z ograniczeniami zasobów.

Korzystanie z tych bibliotek jest łatwiejsze w zrozumieniu, przeglądając przykład kodu. W poniższych sekcjach omówiono kilka przykładowych aplikacji, które są zawarte w zestawie SDK. Ten przewodnik powinien dać dobry wpływ na różne możliwości warstw architektury zestawu SDK oraz wprowadzenie do działania interfejsów API.

## <a name="before-you-run-the-samples"></a>Przed uruchomieniem przykładów

Aby można było uruchomić przykłady w zestawie SDK urządzeń Azure IoT dla języka C, należy [utworzyć wystąpienie usługi IoT Hub](iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure. Następnie wykonaj następujące zadania:

* Przygotowywanie środowiska projektowego
* Uzyskaj poświadczenia urządzenia.

### <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Pakiety są udostępniane dla typowych platform (takich jak NuGet dla systemu Windows lub apt_get dla Debian i Ubuntu), a próbki używają tych pakietów, jeśli są dostępne. W niektórych przypadkach należy skompilować zestaw SDK dla lub na urządzeniu. Jeśli musisz skompilować zestaw SDK, zobacz [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w repozytorium GitHub.

Aby uzyskać przykładowy kod aplikacji, Pobierz kopię zestawu SDK z usługi GitHub. Pobierz swoją kopię źródła z **głównej** gałęzi [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Uzyskaj poświadczenia urządzenia

Teraz, gdy masz przykładowy kod źródłowy, następnym krokiem jest uzyskanie zestawu poświadczeń urządzeń. Aby urządzenie mogło uzyskać dostęp do usługi IoT Hub, należy najpierw dodać urządzenie do rejestru tożsamości IoT Hub. Po dodaniu urządzenia uzyskasz zestaw poświadczeń urządzenia, które są potrzebne, aby urządzenie mogło nawiązać połączenie z usługą IoT Hub. Przykładowe aplikacje omówione w następnej sekcji oczekują tych poświadczeń w formie **parametrów połączenia urządzenia**.

Istnieje kilka narzędzi "open source", które ułatwiają zarządzanie Centrum IoT.

* Aplikacja systemu Windows o nazwie [Eksplorator IoT Azure](https://github.com/Azure/azure-iot-explorer).

* Międzyplatformowe rozszerzenie Visual Studio Code o nazwie [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Międzyplatformowy interfejs wiersza polecenia python o nazwie [IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

W tym samouczku jest stosowane narzędzie graficzne *Eksploratora urządzeń* . Możesz użyć *narzędzi Azure IoT Tools for vs Code* , Jeśli opracowujesz program w vs Code. Jeśli wolisz korzystać z narzędzia interfejsu wiersza polecenia, możesz również użyć *rozszerzenia IoT dla narzędzia Azure CLI 2,0* .

Narzędzie Eksplorator urządzeń używa bibliotek usługi Azure IoT do wykonywania różnych funkcji na IoT Hub, w tym dodawania urządzeń. W przypadku dodawania urządzenia za pomocą narzędzia Eksplorator urządzeń uzyskasz parametry połączenia dla urządzenia. Te parametry połączenia są potrzebne do uruchomienia przykładowych aplikacji.

Jeśli nie masz doświadczenia z narzędziem Eksplorator urządzeń, Poniższa procedura opisuje sposób użycia go w celu dodania urządzenia i uzyskania parametrów połączenia urządzenia.

1. Aby zainstalować narzędzie Eksplorator urządzeń, zobacz [jak używać Device Explorer dla IoT Hub urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/).

1. Po uruchomieniu programu zostanie wyświetlony następujący interfejs:

   ![Zrzut ekranu Device Explorer bliźniaczy](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Wprowadź **Parametry połączenia IoT Hub** w pierwszym polu, a następnie kliknij przycisk **Aktualizuj**. Ten krok umożliwia skonfigurowanie narzędzia w taki sposób, aby mógł komunikować się z IoT Hub. 

**Parametry połączenia** można znaleźć w obszarze **IoT Hub**  >  **Ustawienia** usługi  >  **zasady dostępu współdzielonego**  >  **iothubowner**.

1. Po skonfigurowaniu parametrów połączenia IoT Hub kliknij kartę **Zarządzanie** :

   ![Zrzut ekranu przedstawiający Device Explorer ze przędzą/zarządzaniem](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Na tej karcie można zarządzać urządzeniami zarejestrowanymi w usłudze IoT Hub.

1. Aby utworzyć urządzenie, kliknij przycisk **Utwórz** . Zostanie wyświetlone okno dialogowe z zestawem wstępnie wypełnionych kluczy (podstawowa i pomocnicza). Wprowadź **Identyfikator urządzenia** , a następnie kliknij przycisk **Utwórz**.

   ![Zrzut ekranu przedstawiający tworzenie urządzenia](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Po utworzeniu urządzenia na liście zostaną zaktualizowane wszystkie zarejestrowane urządzenia, w tym właśnie utworzone. Jeśli klikniesz prawym przyciskiem myszy nowe urządzenie, zobaczysz to menu:

   ![Device Explorer dwuosiowy z prawym przyciskiem myszy](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. W przypadku wybrania opcji **Kopiuj parametry połączenia dla wybranego urządzenia** parametry połączenia urządzenia zostaną skopiowane do Schowka. Zachowaj kopię parametrów połączenia urządzenia. Jest on potrzebny podczas uruchamiania przykładowych aplikacji opisanych w poniższych sekcjach.

Po wykonaniu powyższych kroków wszystko jest gotowe do rozpoczęcia uruchamiania kodu. Większość przykładów ma stałą w górnej części głównego pliku źródłowego, która umożliwia wprowadzanie parametrów połączenia. Na przykład odpowiedni wiersz ze **iothub_client \_ przykładów \_ iothub_convenience_sample** aplikacji pojawia się w następujący sposób.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Korzystanie z biblioteki usługi iothubclient

W folderze **\_ Client iothub** w repozytorium [Azure-IoT-SDK-c](https://github.com/azure/azure-iot-sdk-c) istnieje folder **Samples** zawierający aplikację o nazwie **iothub \_ Client \_ Sample \_ MQTT**.

Wersja systemu Windows **iothub_client \_ przykładów \_ iothub_convenience_sample** aplikacji zawiera następujące rozwiązanie programu Visual Studio:

  ![Eksplorator rozwiązań programu Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio wyświetli monit o przekierowanie projektu do najnowszej wersji, zaakceptuj monit.

To rozwiązanie zawiera pojedynczy projekt. W tym rozwiązaniu są zainstalowane cztery pakiety NuGet:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. usługi iothubclient
* Microsoft. Azure. umqtt

Pakiet **Microsoft. Azure. C. SharedUtility** jest zawsze potrzebny podczas pracy z zestawem SDK. Ten przykład korzysta z protokołu MQTT, dlatego należy uwzględnić pakiety **Microsoft. Azure. umqtt** i **Microsoft. Azure. IoTHub. MqttTransport** (istnieją równoważne pakiety dla AMQP i https). Ponieważ przykład korzysta z biblioteki **usługi iothubclient** , w rozwiązaniu należy również uwzględnić pakiet **Microsoft. Azure. IoTHub. usługi iothubclient** .

Implementację przykładowej aplikacji można znaleźć w **iothub_client \_ próbkach \_ iothub_convenience_sample** pliku źródłowym.

Poniższe kroki umożliwiają korzystanie z tej przykładowej aplikacji w celu przechodzenia przez elementy wymagane do korzystania z biblioteki **usługi iothubclient** .

### <a name="initialize-the-library"></a>Zainicjuj bibliotekę

> [!NOTE]
> Przed rozpoczęciem pracy z bibliotekami może być konieczne wykonanie pewnej inicjalizacji specyficznej dla platformy. Na przykład jeśli planujesz używać AMQP w systemie Linux, musisz zainicjować bibliotekę OpenSSL. Przykłady w [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c) wywołują funkcję narzędzia **\_ init platformy** funkcji podczas uruchamiania klienta i wywołają funkcję **\_ DEINIT platformy** przed zamknięciem. Te funkcje są zadeklarowane w pliku nagłówkowym platform. h. Sprawdź definicje tych funkcji dla docelowej platformy w [repozytorium](https://github.com/Azure/azure-iot-sdk-c) , aby określić, czy w kliencie należy uwzględnić kod inicjalizacji specyficzny dla danej platformy.

Aby rozpocząć pracę z bibliotekami, najpierw Przydziel IoT Hub obsługę klienta:

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

Do tej funkcji jest przekazywany kopia parametrów połączenia urządzenia uzyskanych w narzędziu Eksplorator urządzeń. Należy również wyznaczyć protokół komunikacyjny do użycia. Ten przykład używa MQTT, ale AMQP i HTTPS są również opcjami.

Jeśli masz prawidłowe **\_ \_ dojście klienta IOTHUB**, możesz rozpocząć wywoływanie interfejsów API, aby wysyłać i odbierać komunikaty do i z IoT Hub.

### <a name="send-messages"></a>Wysyłanie komunikatów

Przykładowa aplikacja konfiguruje pętlę do wysyłania komunikatów do centrum IoT. Następujący fragment kodu:

- Tworzy komunikat.
- Dodaje właściwość do komunikatu.
- Wysyła komunikat.

Najpierw utwórz komunikat:

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

Za każdym razem, gdy wysyłasz komunikat, należy określić odwołanie do funkcji wywołania zwrotnego, która jest wywoływana, gdy dane są wysyłane. W tym przykładzie funkcja wywołania zwrotnego jest nazywana **SendConfirmationCallback**. Poniższy fragment kodu przedstawia tę funkcję wywołania zwrotnego:

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

Zwróć uwagę na wywołanie funkcji **\_ Destroy IoTHubMessage** po zakończeniu pracy z komunikatem. Ta funkcja zwalnia zasoby przydzieloną podczas tworzenia komunikatu.

### <a name="receive-messages"></a>Odbieranie komunikatów

Odebranie komunikatu jest operacją asynchroniczną. Najpierw należy zarejestrować wywołanie zwrotne do wywołania, gdy urządzenie odbiera komunikat:

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

Ostatni parametr jest wskaźnikiem typu void do dowolnych żądanych wartości. W przykładzie jest wskaźnikiem do liczby całkowitej, ale może być wskaźnikiem do bardziej złożonej struktury danych. Ten parametr umożliwia działanie funkcji wywołania zwrotnego w stanie udostępnionym z obiektem wywołującym tej funkcji.

Gdy urządzenie odbiera komunikat, wywoływana jest zarejestrowana funkcja wywołania zwrotnego. Ta funkcja wywołania zwrotnego pobiera:

* Identyfikator komunikatu i identyfikator korelacji z komunikatu.
* Zawartość komunikatu.
* Wszystkie właściwości niestandardowe z wiadomości.

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

Użyj funkcji **\_ getbytearray IoTHubMessage** , aby pobrać komunikat, który w tym przykładzie jest ciągiem.

### <a name="uninitialize-the-library"></a>Odinicjuj bibliotekę

Po zakończeniu wysyłania zdarzeń i otrzymywania komunikatów można odzainicjować bibliotekę IoT. Aby to zrobić, należy wydać następujące wywołanie funkcji:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

To wywołanie zwalnia zasoby, które zostały wcześniej przydzielone przez funkcję **usługi iothubclient \_ CreateFromConnectionString** .

Jak widać, można łatwo wysyłać i odbierać komunikaty z biblioteką **usługi iothubclient** . Biblioteka obsługuje szczegóły komunikacji z IoT Hub, w tym protokół, który ma być używany (z perspektywy dewelopera, to jest prosta opcja konfiguracji).

Biblioteka **usługi iothubclient** zapewnia również precyzyjną kontrolę nad sposobem serializacji danych wysyłanych przez urządzenie do IoT Hub. W niektórych przypadkach ten poziom kontroli jest zaletą, ale w innych jest to szczegółowe informacje o implementacji, których nie chcesz używać. W takim przypadku można rozważyć użycie biblioteki **serializatorów** , która została opisana w następnej sekcji.

## <a name="use-the-serializer-library"></a>Korzystanie z biblioteki serializatorów

Koncepcyjnie Biblioteka **serializatorów** znajduje się na szczycie biblioteki **usługi iothubclient** w zestawie SDK. Używa biblioteki **usługi iothubclient** dla podstawowej komunikacji z IoT Hub, ale dodaje możliwości modelowania, które usuwają obciążenie związane z serializacji wiadomości od dewelopera. Jak działa ta biblioteka najlepiej ilustruje przykład.

Wewnątrz folderu **serializatorów** w [repozytorium Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c)jest folderem **Samples** zawierającym aplikację o nazwie **simplesample \_ MQTT**. Wersja tego przykładu systemu Windows zawiera następujące rozwiązanie programu Visual Studio:

  ![Rozwiązanie Visual Studio dla przykładu MQTT](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio wyświetli monit o przekierowanie projektu do najnowszej wersji, zaakceptuj monit.

Tak jak w poprzednim przykładzie, to obejmuje kilka pakietów NuGet:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. usługi iothubclient
* Microsoft. Azure. IoTHub. serializator
* Microsoft. Azure. umqtt

Większość z tych pakietów była widoczna w poprzednim przykładzie, ale **pakiet Microsoft. Azure. IoTHub. Serializer** jest nowy. Ten pakiet jest wymagany w przypadku korzystania z biblioteki **serializatorów** .

Implementację przykładowej aplikacji można znaleźć w pliku **iothub_client \_ samples \_ iothub_convenience_sample** .

W poniższych sekcjach omówiono najważniejsze części tego przykładu.

### <a name="initialize-the-library"></a>Zainicjuj bibliotekę

Aby rozpocząć pracę z biblioteką **serializatorów** , wywołaj interfejsy API inicjacji:

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

Wywołanie funkcji **\_ init serializatora** to jednorazowe wywołanie i inicjuje podstawową bibliotekę. Następnie należy wywołać funkcję **usługi iothubclient \_ ll \_ CreateFromConnectionString** , która jest tym samym interfejsem API jak w przykładzie **usługi iothubclient** . To wywołanie ustawia parametry połączenia urządzenia (to wywołanie jest również używane w przypadku wybrania protokołu, który ma być używany). Ten przykład używa MQTT jako transportu, ale może użyć AMQP lub HTTPS.

Na koniec wywołaj funkcję **create \_ model \_ instance** . **WeatherStation** jest przestrzenią nazw modelu, a **ContosoAnemometer** jest nazwą modelu. Po utworzeniu wystąpienia modelu można go użyć do rozpoczęcia wysyłania i otrzymywania komunikatów. Ważne jest jednak, aby zrozumieć, co to jest model.

### <a name="define-the-model"></a>Zdefiniuj model

Model w bibliotece **serializatorów** definiuje komunikaty, które urządzenie może wysłać do IoT Hub i komunikatów nazywanych *akcjami* w języku modelowania, które mogą być odbierane. Definiujesz model przy użyciu zestawu makr języka C, jak w **iothub_client \_ samples \_ iothub_convenience_sample** Przykładowa aplikacja:

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

Makra **Begin \_ Namespace** i **End \_ Namespaces** przyjmują przestrzeń nazw modelu jako argument. Oczekuje się, że wszystkie te makra są definicjami modelu lub modeli oraz strukturą danych używaną przez modele.

W tym przykładzie istnieje jeden model o nazwie **ContosoAnemometer**. Ten model definiuje dwie fragmenty danych, które urządzenie może wysłać do IoT Hub: **DeviceID** i **WindSpeed**. Definiuje również trzy akcje (komunikaty), które urządzenie może odbierać: **TurnFanOn**, **TurnFanOff** i **SetAirResistance**. Każdy element danych ma typ, a każda akcja ma nazwę (i opcjonalnie zestaw parametrów).

Dane i akcje zdefiniowane w modelu definiują powierzchnię interfejsu API, za pomocą której można wysyłać komunikaty do IoT Hub i odpowiadać na komunikaty wysyłane do urządzenia. Korzystanie z tego modelu jest najlepszym rozwiązaniem na przykład.

### <a name="send-messages"></a>Wysyłanie komunikatów

Model definiuje dane, które można wysłać do IoT Hub. W tym przykładzie oznacza to jeden z dwóch elementów danych zdefiniowanych przy użyciu makra **WITH_DATA** . Aby wysłać wartości **DeviceID** i **WindSpeed** do centrum IoT, należy wykonać kilka kroków. Najpierw należy ustawić dane, które mają zostać wysłane:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Zdefiniowany wcześniej model umożliwia ustawianie wartości przez ustawienie elementów członkowskich **struktury**. Następnie serializować komunikat, który chcesz wysłać:

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

Ten kod serializacji urządzenia do chmury do bufora (do którego odwołuje się **obiekt docelowy**). Następnie kod wywołuje funkcję **SendMessage** , aby wysłać komunikat do IoT Hub:

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

Drugi do ostatniego parametru **usługi iothubclient \_ ll \_ SendEventAsync** jest odwołaniem do funkcji wywołania zwrotnego, która jest wywoływana, gdy dane są pomyślnie wysyłane. Oto funkcja wywołania zwrotnego w przykładzie:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Drugi parametr jest wskaźnikiem do kontekstu użytkownika; ten sam wskaźnik przeszedł do **usługi iothubclient \_ szystkie \_ SendEventAsync**. W tym przypadku kontekst jest prostym licznikiem, ale może być dowolny.

To wszystko, co jest konieczne do wysyłania komunikatów z urządzenia do chmury. Jedyną czynnością, którą należy uwzględnić, jest to, jak odbierać wiadomości.

### <a name="receive-messages"></a>Odbieranie komunikatów

Odebranie komunikatu działa podobnie do sposobu, w jaki komunikaty działają w bibliotece **usługi iothubclient** . Najpierw należy zarejestrować funkcję wywołania zwrotnego wiadomości:

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

Następnie napiszesz funkcję wywołania zwrotnego, która jest wywoływana po odebraniu komunikatu:

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

Jest to ten sam kod, który jest taki sam dla każdego rozwiązania. Ta funkcja otrzymuje komunikat i bierze pod uwagę kierowanie do odpowiedniej funkcji za pomocą wywołania **\_ polecenia Execute**. Funkcja wywołana w tym punkcie zależy od definicji akcji w modelu.

Podczas definiowania akcji w modelu wymagane jest zaimplementowanie funkcji, która jest wywoływana, gdy urządzenie otrzyma odpowiedni komunikat. Na przykład jeśli model definiuje tę akcję:

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

Należy zauważyć, jak nazwa funkcji jest zgodna z nazwą akcji w modelu i że parametry funkcji pasują do parametrów określonych dla akcji. Pierwszy parametr jest zawsze wymagany i zawiera wskaźnik do wystąpienia modelu.

Gdy urządzenie odbiera komunikat pasujący do tej sygnatury, wywoływana jest odpowiednia funkcja. W związku z tym oprócz konieczności dołączenia kodu standardowego z **IoTHubMessage**, otrzymywanie komunikatów jest tylko kwestią zdefiniowania prostej funkcji dla każdej akcji zdefiniowanej w modelu.

### <a name="uninitialize-the-library"></a>Odinicjuj bibliotekę

Gdy skończysz wysyłać dane i odbierać komunikaty, możesz odzainicjować bibliotekę IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Każda z tych trzech funkcji jest wyrównana z trzema wcześniej opisanymi funkcjami inicjalizacji. Wywołanie tych interfejsów API zapewnia zwolnienie wcześniej przyznanych zasobów.

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono podstawowe zagadnienia dotyczące korzystania z bibliotek dostępnych w **zestawie SDK urządzeń Azure IoT dla języka C**. Zapewnia ona wystarczającą ilość informacji, aby zrozumieć, co obejmuje zestaw SDK, jego architekturę oraz jak zacząć pracę z przykładami systemu Windows. Następny artykuł kontynuuje opis zestawu SDK, wyjaśniając [więcej o bibliotece usługi iothubclient](iot-hub-device-sdk-c-iothubclient.md).

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
