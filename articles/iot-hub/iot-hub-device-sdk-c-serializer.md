---
title: Zestaw SDK urządzenia Usługi Azure IoT dla języka C — serializator | Dokumenty firmy Microsoft
description: Jak używać biblioteki Serializer w SDK urządzenia Usługi Azure IoT dla języka C do tworzenia aplikacji urządzeń, które komunikują się z centrum IoT hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d4916d651638f0d1dbb4f10e0e0732f5c330d300
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767013"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Zestaw SDK urządzenia Usługi Azure IoT dla języka C — więcej o serializatorze

Pierwszy artykuł z tej serii wprowadzono [wprowadzenie do urządzenia Azure IoT SDK dla języka C](iot-hub-device-sdk-c-intro.md). Następny artykuł zawiera bardziej szczegółowy opis [SDK urządzenia Usługi Azure IoT dla języka C - IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). W tym artykule uzupełnia pokrycie SDK, zapewniając bardziej szczegółowy opis pozostałego składnika: biblioteki **serializatora.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

W artykule wprowadzającym opisano, jak używać biblioteki **serializatora** do wysyłania zdarzeń i odbierania wiadomości z usługi IoT Hub. W tym artykule rozszerzamy tę dyskusję, zapewniając pełniejsze wyjaśnienie sposobu modelowania danych za pomocą języka makr **serializatora.** Artykuł zawiera również więcej szczegółów na temat sposobu serializacji wiadomości biblioteki (a w niektórych przypadkach, jak można kontrolować zachowanie serializacji). Opiszemy również niektóre parametry, które można zmodyfikować, które określają rozmiar utworzonych modeli.

Na koniec artykuł powraca do niektórych tematów omówionych w poprzednich artykułach, takich jak wiadomości i obsługi właściwości. Jak dowiemy się, te funkcje działają w taki sam sposób przy użyciu biblioteki **serializatora,** jak w przypadku biblioteki **IoTHubClient.**

Wszystko opisane w tym artykule jest oparte na przykładach SDK **serializatora.** Jeśli chcesz śledzić, zobacz **simplesample\_amqp** i **simplesample\_http** aplikacji zawartych w zestawie SDK urządzenia Usługi Azure IoT dla języka C.

Zestaw [**SDK urządzenia Usługi Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium C GitHub można znaleźć i wyświetlić szczegóły interfejsu API w [odwołaniu do interfejsu API języka C.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-modeling-language"></a>Język modelowania

W [artykule Zestaw SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md) w tej serii wprowadzono zestaw **SDK urządzenia Usługi Azure IoT dla** języka modelowania języka C za pomocą przykładu przedstawionego w aplikacji **simplesample\_amqp:**

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Jak widać, język modelowania jest oparty na makrach C. Zawsze zaczynasz swoją definicję **od BEGIN\_NAMESPACE** i zawsze kończysz na END **\_NAMESPACE**. Często nazywasz obszar nazw dla firmy lub, jak w tym przykładzie, projekt, nad który pracujesz.

To, co trafia do przestrzeni nazw, to definicje modelu. W tym przypadku istnieje jeden model anemometru. Po raz kolejny model można nazwać wszystko, ale zazwyczaj model jest nazwany dla urządzenia lub typu danych, które mają być wymieniane z Usługi IoT Hub.  

Modele zawierają definicję zdarzeń, które można przychodzących do Usługi IoT Hub *(dane),* a także wiadomości, które można odbierać z Usługi IoT Hub *(akcje).* Jak widać na przykładzie, zdarzenia mają typ i nazwę; akcje mają nazwę i parametry opcjonalne (każdy z typem).

Co nie jest wykazane w tym przykładzie są dodatkowe typy danych, które są obsługiwane przez zestaw SDK. Omówimy to dalej.

> [!NOTE]
> Usługa IoT Hub odnosi się do danych wysyłanych do niego przez urządzenie jako *zdarzeń,* podczas gdy język modelowania odnosi się do niego jako *danych* (zdefiniowanych przy użyciu **WITH_DATA**). Podobnie Usługa IoT Hub odnosi się do danych wysyłanych do urządzeń jako *wiadomości,* podczas gdy język modelowania odnosi się do niego jako *akcje* (zdefiniowane przy użyciu **WITH_ACTION**). Należy pamiętać, że terminy te mogą być używane zamiennie w tym artykule.
> 
> 

## <a name="supported-data-types"></a>Obsługiwane typy danych

Następujące typy danych są obsługiwane w modelach utworzonych za pomocą biblioteki **serializatora:**

| Typ | Opis |
| --- | --- |
| double |podwójna precyzja liczba zmiennoprzecinkowych |
| int |32-bitowa całkowitej liczby |
| float |pojedyncza precyzja liczba zmiennoprzecinkowego |
| long |długa ć całkowita |
| int8\_t |8-bitowa czkawce całkowitej |
| int16\_t |16-bitowa całkowitej liczby |
| int32\_t |32-bitowa całkowitej liczby |
| int64\_t |64-bitowa całkowitej liczby |
| bool |wartość logiczna |
| ascii\_\_char ptr |Ciąg ASCII |
| PRZESUNIĘCIE\_DATY\_\_EDM |przesunięcie godziny daty |
| Identyfikator EDM\_ |Identyfikator GUID |
| EDM\_BINARNY |binarny |
| DEKLARUJ\_STRUKTURĘ |złożony typ danych |

Zacznijmy od ostatniego typu danych. **\_DECLARE STRUCT** umożliwia definiowanie złożonych typów danych, które są grupowania innych typów pierwotnych. Te grupy pozwalają nam zdefiniować model, który wygląda następująco:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nasz model zawiera pojedyncze zdarzenie danych typu **TestType**. **TestType** jest typ złożony, który zawiera kilka elementów członkowskich, które łącznie zademonstrować typy pierwotne obsługiwane przez język modelowania **serializatora.**

W takim modelu możemy napisać kod do wysyłania danych do usługi IoT Hub, który jest wyświetlany w następujący sposób:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Zasadniczo przypisujemy wartość do każdego członka **struktury testowej,** a następnie **wywołujemy SendAsync,** aby wysłać zdarzenie **testu** danych do chmury. **SendAsync** jest funkcją pomocniczą, która wysyła pojedyncze zdarzenie danych do Usługi IoT Hub:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Ta funkcja serializuje dane i wysyła je do Centrum IoT przy użyciu **usługi IoTHubClient\_SendEventAsync**. Jest to ten sam kod omówione w poprzednich artykułach (**SendAsync** hermetyzuje logiki do wygodnej funkcji).

Inną inną funkcją pomocnika używaną w poprzednim kodzie jest **GetDateTimeOffset**. Ta funkcja przekształca dany czas w wartość typu **PRZESUNIĘCIE CZASU\_\_\_DATY EDM:**

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Po uruchomieniu tego kodu do usługi IoT Hub zostanie wysłana następująca wiadomość:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Należy zauważyć, że serializacji jest w JSON, który jest format generowany przez bibliotekę **serializatora.** Należy również zauważyć, że każdy element członkowski serializowanego obiektu JSON pasuje do członków **TestType,** które zdefiniowaliśmy w naszym modelu. Wartości również dokładnie zgodne z wartościami używanymi w kodzie. Należy jednak zauważyć, że dane binarne są zakodowane w formacie base64: "AQID" jest kodowaniem base64 {0x01, 0x02, 0x03}.

W tym przykładzie pokazano zalety przy użyciu biblioteki **serializatora** — umożliwia nam wysyłanie JSON do chmury, bez konieczności jawnie radzić sobie z serializacji w naszej aplikacji. Jedyne, o co musimy się martwić, to ustawienie wartości zdarzeń danych w naszym modelu, a następnie wywołanie prostych interfejsów API w celu wysłania tych zdarzeń do chmury.

Dzięki tym informacjom możemy zdefiniować modele, które obejmują zakres obsługiwanych typów danych, w tym typów złożonych (możemy nawet uwzględnić typy złożone w innych złożonych typach). Jednak serializowane JSON generowane przez przykład powyżej wywołuje ważny punkt. *Sposób* wysyłania danych z biblioteki **serializatora** określa dokładnie, jak powstaje JSON. Ten szczególny punkt jest tym, co omówimy dalej.

## <a name="more-about-serialization"></a>Więcej o serializacji

W poprzedniej sekcji przedstawiono przykład danych wyjściowych generowanych przez bibliotekę **serializatora.** W tej sekcji wyjaśnimy, jak biblioteka serializuje dane i jak można kontrolować to zachowanie za pomocą interfejsów API serializacji.

Aby przyspieszyć dyskusję na temat serializacji, będziemy pracować z nowym modelem opartym na termostatze. Najpierw podajmy pewne tło dotyczące scenariusza, który próbujemy rozwiązać.

Chcemy modelować termostat, który mierzy temperaturę i wilgotność. Każdy fragment danych zostanie wysłany do usługi IoT Hub w inny sposób. Domyślnie termostat wnika zdarzenie temperatury raz na 2 minuty; zdarzenie wilgotności jest wnikane raz na 15 minut. Gdy którekolwiek z tych zdarzeń jest wnikanie, musi zawierać sygnaturę czasową, która pokazuje czas, który odpowiedniej temperatury lub wilgotności został zmierzony.

W tym scenariuszu zademonstrujemy dwa różne sposoby modelowania danych, a my wyjaśnimy wpływ modelowania na dane wyjściowe serializowane.

### <a name="model-1"></a>Model 1

Oto pierwsza wersja modelu, który obsługuje poprzedni scenariusz:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Należy zauważyć, że model zawiera dwa zdarzenia danych: **temperatura** i **wilgotność**. W przeciwieństwie do poprzednich przykładów, typ każdego zdarzenia jest strukturą zdefiniowaną przy użyciu **\_DECLARE STRUCT**. **TemperatureEvent** obejmuje pomiar temperatury i sygnaturę czasową; **WilgotnośćWydajnik** zawiera pomiar wilgotności i znacznik czasu. Ten model daje nam naturalny sposób modelowania danych dla scenariusza opisanego powyżej. Gdy wyślemy zdarzenie do chmury, wyślemy parę sygnatura temperatury/sygnatury czasowej lub pary wilgotności/sygnatury czasowej.

Możemy wysłać zdarzenie temperatury do chmury przy użyciu kodu, takiego jak:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

W przykładowym kodzie użyjemy zakodowanych wartości temperatury i wilgotności, ale wyobraźmy sobie, że pobieramy te wartości, pobierając próbki odpowiednich czujników na termostatze.

Powyższy kod używa pomocnika **GetDateTimeOffset,** który został wprowadzony wcześniej. Z powodów, które staną się jasne później, ten kod jawnie oddziela zadanie serializacji i wysyłania zdarzenia. Poprzedni kod serializuje zdarzenie temperatury do buforu. Następnie **sendMessage** jest funkcją pomocnika (zawartą w **simplesample\_amqp),** która wysyła zdarzenie do Centrum IoT Hub:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ten kod jest podzbiorem **pomocnika SendAsync** opisanego w poprzedniej sekcji, więc nie przejdziemy go ponownie tutaj.

Po uruchomieniu poprzedniego kodu, aby wysłać zdarzenie Temperature, ten serializowany formularz zdarzenia jest wysyłany do usługi IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Wysyłamy temperaturę, która jest typu **TemperatureEvent**, a ta struktura zawiera element członkowski **Temperatury** i **Czasu.** Jest to bezpośrednio odzwierciedlone w danych szeregowych.

Podobnie możemy wysłać zdarzenie wilgotności z tego kodu:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Formularz serializowany, który jest wysyłany do Usługi IoT Hub jest wyświetlany w następujący sposób:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ponownie, jest to zgodnie z oczekiwaniami.

Dzięki temu modelowi możesz sobie wyobrazić, jak można łatwo dodać dodatkowe zdarzenia. Można zdefiniować więcej struktur za pomocą **\_DECLARE STRUCT**i dołączyć odpowiednie zdarzenie w modelu przy użyciu **z\_danymi**.

Teraz zmodyfikujmy model tak, aby zawiera te same dane, ale z inną strukturą.

### <a name="model-2"></a>Model 2

Należy wziąć pod uwagę ten alternatywny model do powyższego:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

W takim przypadku wyeliminowaliśmy makra **STRUCT DECLARE\_** i po prostu definiujemy elementy danych z naszego scenariusza przy użyciu prostych typów z języka modelowania.

Tylko na razie zignoruj zdarzenie **Time.** Z tym na bok, oto kod do wnikania **Temperatura:**

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła następujące zdarzenie serializowane do Usługi IoT Hub:

```C
{"Temperature":75}
```

A kod do wysyłania zdarzenia Wilgotność jest wyświetlany w następujący sposób:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła to do Usługi IoT Hub:

```C
{"Humidity":45}
```

Do tej pory nadal nie ma niespodzianek. Teraz zmieńmy sposób korzystania z makra SERIALIZE.

Makro **SERIALIZE** może przyjmować wiele zdarzeń danych jako argumenty. Dzięki temu możemy serializować **zdarzenie temperatura** i **wilgotność** razem i wysłać je do Usługi IoT Hub w jednym wywołaniu:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Można się domyślać, że wynikiem tego kodu jest, że dwa zdarzenia danych są wysyłane do Usługi IoT Hub:

[ {"Temperature":75}, {"Wilgotność":45} ]

Innymi słowy można oczekiwać, że ten kod jest taki sam jak wysyłanie **temperatury** i **wilgotności** oddzielnie. To tylko wygoda, aby przekazać oba zdarzenia do **SERIALIZE** w tym samym wywołaniu. Tak się jednak nie stało. Zamiast tego powyższy kod wysyła to zdarzenie pojedynczych danych do usługi IoT Hub:

{"Temperatura":75, "Wilgotność":45}

Może się to wydawać dziwne, ponieważ nasz model definiuje **temperaturę** i **wilgotność** jako dwa *oddzielne* zdarzenia:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Bardziej do rzeczy, nie model tych zdarzeń, gdzie **temperatura** i **wilgotność** są w tej samej strukturze:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Gdybyśmy użyli tego modelu, łatwiej byłoby zrozumieć, jak **temperatura** i **wilgotność** będą wysyłane w tej samej wiadomości serializowanej. Jednak może nie być jasne, dlaczego działa w ten sposób, gdy przekazujesz oba zdarzenia danych do **serialize** przy użyciu modelu 2.

To zachowanie jest łatwiejsze do zrozumienia, jeśli znasz założenia, które tworzy biblioteka **serializatora.** Aby to zrozumieć, wróćmy do naszego modelu:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pomyśl o tym modelu w kategoriach obiektowych. W tym przypadku modelujemy urządzenie fizyczne (termostat) i to urządzenie zawiera **atrybuty, takie** jak temperatura i **wilgotność.**

Możemy wysłać cały stan naszego modelu z kodem, takich jak następujące:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Przy założeniu, że wartości temperatury, wilgotności i czasu są ustawione, zobaczymy zdarzenie takie jak ten wysłany do Usługi IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Czasami można wysłać tylko *niektóre* właściwości modelu do chmury (jest to szczególnie ważne, jeśli model zawiera dużą liczbę zdarzeń danych). Warto wysłać tylko podzbiór zdarzeń danych, na przykład w naszym wcześniejszym przykładzie:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Generuje to dokładnie to samo zdarzenie serializowane, tak jakbyśmy zdefiniowali **Zdarzenie temperatury** z elementem członkowskim **Temperatury** i **Czasu,** tak jak zrobiliśmy to w przypadku modelu 1. W takim przypadku udało nam się wygenerować dokładnie to samo zdarzenie serializowane przy użyciu innego modelu (model 2), ponieważ nazwaliśmy **SERIALIZE** w inny sposób.

Ważnym punktem jest to, że jeśli przekażesz wiele zdarzeń danych do **serialize,** a następnie zakłada, że każde zdarzenie jest właściwością w jednym obiekcie JSON.

Najlepsze podejście zależy od Ciebie i jak myślisz o swoim modelu. Jeśli wysyłasz "zdarzenia" do chmury, a każde zdarzenie zawiera zdefiniowany zestaw właściwości, pierwsze podejście ma wiele sensu. W takim przypadku należy użyć **DECLARE\_STRUCT** do zdefiniowania struktury każdego zdarzenia, a następnie dołączyć je do modelu z makra **\_WITH DATA.** Następnie wysyłasz każde zdarzenie tak jak w pierwszym powyższym przykładzie. W tym podejściu można przekazać tylko pojedyncze zdarzenie danych do **serializer**.

Jeśli myślisz o swoim modelu w sposób obiektowy, to drugie podejście może ci odpowiadać. W takim przypadku elementy zdefiniowane przy użyciu **WITH\_DATA** są "właściwości" obiektu. Możesz przekazać dowolny podzbiór zdarzeń do **serialize,** który chcesz, w zależności od tego, ile stanu "obiektu", który chcesz wysłać do chmury.

Podejście Nether jest dobre lub złe. Wystarczy pamiętać, jak działa biblioteka **serializatora** i wybierz podejście modelowania, które najlepiej odpowiada Twoim potrzebom.

## <a name="message-handling"></a>Obsługa komunikatów

Do tej pory w tym artykule omówiono tylko wysyłanie zdarzeń do usługi IoT Hub i nie został rozwiązany odbieranie wiadomości. Powodem tego jest to, że to, co musimy wiedzieć o odbieraniu wiadomości, zostało w dużej mierze omówione w artykule [Zestaw SDK urządzenia Usługi Azure IoT dla języka C.](iot-hub-device-sdk-c-intro.md) Przypomnij sobie z tego artykułu, że wiadomości przetwarzają, rejestrując funkcję wywołania zwrotnego wiadomości:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Następnie napisz funkcję wywołania zwrotnego, która jest wywoływana po odebraniu wiadomości:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Ta implementacja **IoTHubMessage** wywołuje określoną funkcję dla każdej akcji w modelu. Jeśli na przykład model definiuje tę akcję:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Należy zdefiniować funkcję z tym podpisem:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** jest następnie wywoływana, gdy ta wiadomość jest wysyłana do urządzenia.

Nie wyjaśniliśmy jeszcze, jak wygląda serializowana wersja wiadomości. Innymi słowy, jeśli chcesz wysłać wiadomość **SetAirResistance** do urządzenia, jak to wygląda?

Jeśli wysyłasz wiadomość do urządzenia, można to zrobić za pośrednictwem usługi Azure IoT SDK. Nadal musisz wiedzieć, jaki ciąg wysłać, aby wywołać określoną akcję. Ogólny format wysyłania wiadomości jest wyświetlany w następujący sposób:

```C
{"Name" : "", "Parameters" : "" }
```

Wysyłasz serializowany obiekt JSON z dwiema właściwościami: **Nazwa** jest nazwą akcji (komunikat) i **Parametry** zawiera parametry tej akcji.

Na przykład, aby wywołać **SetAirResistance** można wysłać tę wiadomość do urządzenia:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Nazwa akcji musi dokładnie odpowiadać akcji zdefiniowanej w modelu. Nazwy parametrów również muszą być zgodne. Zwróć również uwagę na wielkość liter. **Nazwa** i **parametry** są zawsze wielkie litery. Upewnij się, że pasuje do przypadku nazwy akcji i parametrów w modelu. W tym przykładzie nazwa akcji to "SetAirResistance", a nie "setairresistance".

Dwie inne akcje **TurnFanOn** i **TurnFanOff** mogą być wywoływane przez wysłanie tych wiadomości do urządzenia:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

W tej sekcji opisano wszystko, co musisz wiedzieć podczas wysyłania zdarzeń i odbierania wiadomości z biblioteki **serializatora.** Przed przejściem, przyjmijmy niektóre parametry, które można skonfigurować, które kontrolują, jak duży jest model.

## <a name="macro-configuration"></a>Konfiguracja makr

Jeśli używasz biblioteki **Serializer** ważną część SDK, aby pamiętać, znajduje się w bibliotece azure-c-shared-utility.

Jeśli sklonowałeś repozytorium Azure-iot-sdk-c z gitHub `git submodule update --init` i wydałeś polecenie, znajdziesz tę udostępnioną bibliotekę narzędzi tutaj:

```C
.\\c-utility
```

Jeśli biblioteka nie została sklonowana, można ją znaleźć [tutaj](https://github.com/Azure/azure-c-shared-utility).

W udostępnionej bibliotece narzędziowej znajduje się następujący folder:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ten folder zawiera rozwiązanie programu Visual Studio o nazwie **macro\_utils\_h\_generator.sln**:

  ![Zrzut ekranu przedstawiający rozwiązanie programu Visual Studio maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Program w tym rozwiązaniu generuje makro **\_utils.h** pliku. Istnieje domyślny plik\_makro utils.h dołączony do SDK. To rozwiązanie umożliwia zmodyfikowanie niektórych parametrów, a następnie ponownetworzenie pliku nagłówka na podstawie tych parametrów.

Dwa kluczowe parametry, którymi należy się z nimi zwrócić, to **nArchityczne** i **nMacroParameters**, które są zdefiniowane w tych dwóch liniach występujących w utils.tt makro:\_

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Wartości te są domyślnymi parametrami dołączonymi do zestawu SDK. Każdy parametr ma następujące znaczenie:

* nMacroParameters – Określa, ile parametrów\_można mieć w jednej definicji makra MODELU DECLARE.
* nArytmetyczne — steruje całkowitą liczbą elementów członkowskich dozwolonych w modelu.

Powodem, dla którego te parametry są ważne, jest to, że kontrolują one, jak duży może być model. Rozważmy na przykład tę definicję modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak wspomniano wcześniej, **\_DECLARE MODEL** jest tylko makro C. Nazwy modelu i instrukcji **\_WITH DATA** (jeszcze jedno makro) są parametrami **DECLARE\_MODEL**. **nMacroParameters** określa, ile parametrów można uwzględnić w **MODELU DECLARE\_**. Skutecznie określa, ile zdarzeń danych i deklaracji akcji można mieć. W związku z tym z domyślnym limitem 124 oznacza to, że można zdefiniować model z kombinacją około 60 akcji i zdarzeń danych. Jeśli spróbujesz przekroczyć ten limit, otrzymasz błędy kompilatora, które wyglądają podobnie do tego:

  ![Zrzut ekranu przedstawiający błędy kompilatora parametrów makr](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

Parametr **nArithmetic** dotyczy bardziej wewnętrznego funkcjonowania języka makr niż aplikacji.  Kontroluje całkowitą liczbę członków, które można mieć w modelu, w tym **DECLARE_STRUCT** makr. Jeśli zaczniesz widzieć błędy kompilatora, takie jak ten, należy spróbować zwiększenie **nArithmetic:**

   ![Zrzut ekranu przedstawiający błędy kompilatora arytmetycznego](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Jeśli chcesz zmienić te parametry, zmodyfikuj wartości w\_pliku utils.tt makra, ponownie skompiluj rozwiązanie macro\_utils\_h\_generator.sln i uruchom skompilowany program. Gdy to zrobisz, nowy\_plik makra utils.h jest generowany i umieszczany w pliku . \\wspólnego\\katalogu inc.

Aby użyć nowej wersji makra\_utils.h, usuń pakiet **NuGet serializatora** z rozwiązania i w jego miejsce obejmują projekt **serializator** visual studio. Dzięki temu kod do kompilacji z kodem źródłowym biblioteki serializatora. Obejmuje to zaktualizowane makro\_utils.h. Jeśli chcesz to zrobić dla **simplesample\_amqp**, zacznij od usunięcia pakietu NuGet dla biblioteki serializatora z rozwiązania:

   ![Zrzut ekranu przedstawiający usunięcie pakietu NuGet dla biblioteki serializatora](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Następnie dodaj ten projekt do rozwiązania programu Visual Studio:

> . \\c\\\\serializator\\\\budować windows serializer.vcxproj
> 
> 

Po zakończeniu rozwiązanie powinno wyglądać następująco:

   ![Zrzut ekranu przedstawiający rozwiązanie simplesample_amqp Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Teraz po skompilowaniu rozwiązania\_zaktualizowane makro utils.h jest zawarte w pliku binarnym.

Należy zauważyć, że zwiększenie tych wartości wystarczająco wysokie może przekroczyć limity kompilatora. Do tego momentu **nMacroParameters** jest głównym parametrem, którego należy się z tym zwrócić. Specyfikacja C99 określa, że w definicji makra dozwolone są co najmniej 127 parametrów. Kompilator firmy Microsoft dokładnie podąża za specyfikacją (i ma limit 127), więc nie będzie można zwiększyć **nMacroParameters** poza wartością domyślną. Inne kompilatory mogą ci na to pozwolić (na przykład kompilator GNU obsługuje wyższy limit).

Do tej pory omówiliśmy prawie wszystko, co musisz wiedzieć o tym, jak napisać kod z biblioteki **serializatora.** Przed zawarciem, przyjrzajmy się niektórym tematom z poprzednich artykułów, nad którymi możesz się zastanawiać.

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu
Przykładowa aplikacja, na której koncentruje się ten artykuł jest **simplesample\_amqp**. W tym przykładzie używane są interfejsy API wyższego poziomu (inne niż**LL)** do wysyłania zdarzeń i odbierania wiadomości. Jeśli używasz tych interfejsów API, wątek w tle uruchamia się, który zajmuje się zarówno wysyłanie zdarzeń i odbieranie wiadomości. Jednak można użyć interfejsów API niższego poziomu (LL), aby wyeliminować ten wątek w tle i przejąć jawną kontrolę nad podczas wysyłania zdarzeń lub odbierania wiadomości z chmury.

Jak opisano w [poprzednim artykule,](iot-hub-device-sdk-c-iothubclient.md)istnieje zestaw funkcji, który składa się z interfejsów API wyższego poziomu:

* Tworzenie ioTHubClientOdłączanie\_połączenia
* IoTHubClient\_SendEventAsync
* Zestaw IoTHubClientMessageCallback\_
* IoTHubClient\_Destroy

Te interfejsy API są zademonstrowane w **simplesample\_amqp**.

Istnieje również analogiczny zestaw interfejsów API niższego poziomu.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* Zestaw IoTHubClient\_LLMessageCallback\_
* IoTHubClient\_\_LL Zniszczyć

Należy zauważyć, że interfejsy API niższego poziomu działają dokładnie w taki sam sposób, jak opisano w poprzednich artykułach. Można użyć pierwszego zestawu interfejsów API, jeśli chcesz wątku w tle do obsługi wysyłania zdarzeń i odbierania wiadomości. Drugi zestaw interfejsów API jest używany, jeśli chcesz jawnie kontrolować podczas wysyłania i odbierania danych z usługi IoT Hub. Każdy zestaw interfejsów API działa równie dobrze z biblioteką **serializatora.**

Na przykład, jak interfejsy API niższego poziomu są używane z biblioteki **serializatora,** zobacz **simplesample\_aplikacji http.**

## <a name="additional-topics"></a>Tematy dodatkowe
Kilka innych tematów, o których warto wspomnieć ponownie, to obsługa właściwości, przy użyciu alternatywnych poświadczeń urządzenia i opcje konfiguracji. Są to wszystkie tematy omówione w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md). Głównym punktem jest to, że wszystkie te funkcje działają w taki sam sposób z biblioteki **serializatora,** jak w **bibliotece IoTHubClient.** Na przykład, jeśli chcesz dołączyć właściwości do zdarzenia z modelu, należy użyć **właściwości\_IoTHubMessage** i **Map**\_**AddorUpdate**, w taki sam sposób, jak opisano wcześniej:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Czy zdarzenie zostało wygenerowane z biblioteki **serializatora** lub utworzone ręcznie przy użyciu biblioteki **IoTHubClient** nie ma znaczenia.

W przypadku poświadczeń urządzenia alternatywnego przy użyciu **funkcji IoTHubClient\_LL\_Create** działa tak samo dobrze, jak **IoTHubClient\_CreateFromConnectionString** do przydzielania **uchwytu klienta\_\_IotHUB**.

Na koniec, jeśli używasz biblioteki **serializatora,** można ustawić opcje konfiguracji z **IoTHubClient\_LL\_SetOption** tak samo, jak podczas korzystania z biblioteki **IoTHubClient.**

Funkcja, która jest unikatowa dla biblioteki **serializatora** są inicjowania interfejsów API inicjowania. Przed rozpoczęciem pracy z biblioteką należy wywołać **program serializatora\_init:**

```C
serializer_init(NULL);
```

Odbywa się to tuż przed **wywołaniem\_IoTHubClient CreateFromConnectionString**.

Podobnie, po zakończeniu pracy z biblioteką, ostatnie połączenie zrobisz jest **serializer\_deinit:**

```C
serializer_deinit();
```

W przeciwnym razie wszystkie inne funkcje wymienione powyżej działają tak samo w bibliotece **serializatora,** jak w **bibliotece IoTHubClient.** Aby uzyskać więcej informacji na temat któregokolwiek z tych tematów, zobacz [poprzedni artykuł](iot-hub-device-sdk-c-iothubclient.md) w tej serii.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano szczegółowo unikatowe aspekty biblioteki **serializatora** zawarte w **SDK urządzenia Usługi Azure IoT dla języka C**. Dzięki dostarczonym informacjom powinieneś dobrze zrozumieć, jak używać modeli do wysyłania zdarzeń i odbierania wiadomości z usługi IoT Hub.

Kończy się również trzyczęściowa seria o tym, jak tworzyć aplikacje za pomocą **SDK urządzenia Usługi Azure IoT dla języka C.** Powinno to być wystarczające informacje, aby nie tylko rozpocząć, ale daje dokładne zrozumienie, jak działają interfejsy API. Aby uzyskać dodatkowe informacje, istnieje kilka przykładów w sdk nie objęte tutaj. W przeciwnym razie [dokumentacja zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) jest dobrym zasobem dla dodatkowych informacji.

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [zestawów SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz [Wdrażanie sztucznej inteligencji na urządzeniach brzegowych za pomocą usługi Azure IoT Edge.](../iot-edge/tutorial-simulate-device-linux.md)