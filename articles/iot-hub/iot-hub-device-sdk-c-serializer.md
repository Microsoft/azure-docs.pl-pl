---
title: Zestaw SDK urządzeń Azure IoT dla języka C — serializator | Microsoft Docs
description: Jak używać biblioteki serializatorów w zestawie SDK urządzeń Azure IoT dla języka C, aby tworzyć aplikacje dla urządzeń, które komunikują się z Centrum IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152240"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Zestaw SDK urządzeń Azure IoT dla języka C — więcej informacji na temat serializatora

W pierwszym artykule z tej serii wprowadzono [wprowadzenie do zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). W następnym artykule przedstawiono bardziej szczegółowy opis [zestawu SDK urządzeń Azure IoT dla języka C--usługi iothubclient](iot-hub-device-sdk-c-iothubclient.md). W tym artykule zakończył się pokrycie zestawu SDK, oferując bardziej szczegółowy opis składnika pozostałej części: biblioteki **serializatorów** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Artykuł wprowadzający opisany w temacie jak używać biblioteki **serializatorów** do wysyłania zdarzeń do i odbierania komunikatów z IoT Hub. W tym artykule rozszeram tę dyskusję, dostarczając dokładniejszy opis sposobu modelowania danych przy użyciu języka makr **serializatorów** . Artykuł zawiera również więcej szczegółowych informacji na temat sposobu serializacji komunikatów przez bibliotekę (a w niektórych przypadkach, w jaki sposób można kontrolować zachowanie serializacji). Opisujemy również niektóre parametry, które można zmodyfikować, aby określić rozmiar tworzonych modeli.

Na koniec artykuł odwiedza kilka tematów wymienionych w poprzednich artykułach, takich jak obsługa komunikatów i właściwości. Dzięki temu funkcje te działają w taki sam sposób, jak w przypadku biblioteki **serializatorów** , tak jak w przypadku biblioteki **usługi iothubclient** .

Wszystko, co opisano w tym artykule, jest oparte na przykładach zestawu SDK programu **szeregującego** . Jeśli chcesz się dowiedzieć, zobacz aplikacje http **simplesample \_ AMQP** i **simplesample \_** zawarte w zestawie SDK urządzeń Azure IoT dla języka C.

[**Zestaw SDK urządzeń Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium w serwisie GitHub i widok szczegółów interfejsu API można znaleźć w [dokumentacji interfejsu API języka c](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Język modelowania

W artykule [dotyczącym zestawu SDK urządzeń Azure IoT dla języka c](iot-hub-device-sdk-c-intro.md) w tej serii wprowadzono **zestaw SDK urządzeń Azure IoT for c** Modeling w ramach przykładu dostępnego w aplikacji **simplesample \_ AMQP** :

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

Jak widać, język modelowania jest oparty na makrach C. Należy zawsze rozpoczynać swoją definicję z **początkową \_ przestrzenią nazw** i kończyć się na **końcu \_ przestrzeni nazw**. Często należy nazwać przestrzeń nazw dla swojej firmy lub, jak w tym przykładzie, projekt, nad którym pracujesz.

Elementy w przestrzeni nazw są definicjami modelu. W takim przypadku istnieje jeden model dla anemometer. Po ponownym uruchomieniu model może mieć nazwę wszystko, ale zazwyczaj model jest nazwany dla urządzenia lub typu danych, które mają być wymieniane za pomocą IoT Hub.  

Modele zawierają definicję zdarzeń, które można przekierować do IoT Hub ( *dane*), a także komunikaty, które można odbierać od IoT Hub ( *Akcje*). Jak widać w przykładzie, zdarzenia mają typ i nazwę; akcje mają nazwę i opcjonalne parametry (z których każdy jest typem).

Nie przedstawiono tego przykładu w tym przykładzie są to dodatkowe typy danych, które są obsługiwane przez zestaw SDK. Zajmiemy się tym następnym.

> [!NOTE]
> IoT Hub odnosi się do danych wysyłanych przez urządzenie jako *zdarzenia*, natomiast język modelowania odwołuje się do niego jako *dane* (zdefiniowane przy użyciu **WITH_DATA**). Podobnie IoT Hub odnosi się do danych wysyłanych do urządzeń jako *wiadomości*, podczas gdy język modelowania odwołuje się do niego jako *Akcje* (zdefiniowane przy użyciu **WITH_ACTION**). Należy pamiętać, że te warunki mogą być używane zamiennie w tym artykule.
> 
> 

## <a name="supported-data-types"></a>Obsługiwane typy danych

Następujące typy danych są obsługiwane w modelach utworzonych przy użyciu biblioteki **serializatorów** :

| Typ | Opis |
| --- | --- |
| double |liczba zmiennoprzecinkowa o podwójnej precyzji |
| int |32-bitowa liczba całkowita |
| float |liczba zmiennoprzecinkowa o pojedynczej precyzji |
| długi |Długa liczba całkowita |
| Int8 \_ t |8-bitowa liczba całkowita |
| Int16 \_ t |16-bitowa liczba całkowita |
| Int32 \_ t |32-bitowa liczba całkowita |
| Int64 \_ t |64-bitowe liczby całkowite |
| bool |boolean |
| ASCII \_ char \_ PTR |Ciąg ASCII |
| \_przesunięcie daty i \_ godziny \_ dla modelu EDM |przesunięcie daty i godziny |
| \_Identyfikator GUID modelu EDM |GUID |
| \_plik binarny modelu EDM |binarny |
| Zadeklaruj \_ strukturę |Typ danych złożonych |

Zacznijmy od ostatniego typu danych. **Deklaracja DECLARE \_** umożliwia Definiowanie złożonych typów danych, które są grupami innych typów pierwotnych. Te grupowania umożliwiają zdefiniowanie modelu, który wygląda następująco:

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

Nasz model zawiera pojedyncze zdarzenie danych typu **TestType**. **TestType** jest typem złożonym, który zawiera kilka elementów członkowskich, które zbiorczo przedstawiają typy pierwotne obsługiwane przez język modelowania **serializatora** .

Dzięki takiemu modelowi możemy napisać kod, aby wysłać dane do IoT Hub, które są wyświetlane w następujący sposób:

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

Zasadniczo przypisujemy wartość do każdego elementu członkowskiego struktury **testowej** , a następnie wywołując **SendAsync** do wysyłania zdarzenia danych **testowych** do chmury. **SendAsync** jest funkcją pomocnika, która wysyła pojedyncze zdarzenie danych do IoT Hub:

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

Ta funkcja serializacji dane zdarzenie danych i wysyła je do IoT Hub przy użyciu **usługi iothubclient \_ SendEventAsync**. Jest to ten sam kod omówiony w poprzednich artykułach (**SendAsync** hermetyzuje logikę w dogodnej funkcji).

Jedną inną funkcją pomocnika używaną w poprzednim kodzie jest **GetDateTimeOffset**. Ta funkcja przekształca dany czas na wartość typu **\_ \_ \_ przesunięcia daty i godziny**:

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

W przypadku uruchomienia tego kodu do IoT Hub zostanie wysłany następujący komunikat:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Należy zauważyć, że Serializacja jest w formacie JSON, który jest formatem generowanym przez bibliotekę **serializatorów** . Należy również zauważyć, że każdy element członkowski serializowanego obiektu JSON jest zgodny z elementami członkowskimi elementu **TestType** zdefiniowanymi w naszym modelu. Wartości są również dokładnie zgodne z użytymi w kodzie. Należy jednak pamiętać, że dane binarne są kodowane algorytmem Base64: "AQID" jest kodowaniem Base64 {0x01, 0x02, 0x03}.

W tym przykładzie przedstawiono zalety korzystania z biblioteki **serializatorów** — pozwala nam wysyłać dane JSON do chmury bez konieczności jawnej obsługi serializacji w naszej aplikacji. Wszystko, co należy martwić się, jest ustawienie wartości zdarzeń danych w naszym modelu, a następnie wywołanie prostych interfejsów API w celu wysłania tych zdarzeń do chmury.

Korzystając z tych informacji, możemy definiować modele, które obejmują zakres obsługiwanych typów danych, w tym typy złożone (możemy nawet uwzględnić złożone typy w innych typach złożonych). Jednak serializowany kod JSON wygenerowany w powyższym przykładzie wywołuje ważny punkt. *Sposób* wysyłania danych za pomocą biblioteki **serializatorów** określa dokładnie sposób tworzenia kodu JSON. Tym konkretnym punktem jest to, co będziemy omawiać dalej.

## <a name="more-about-serialization"></a>Więcej informacji o serializacji

W poprzedniej sekcji przedstawiono przykład danych wyjściowych generowanych przez bibliotekę **serializatorów** . W tej sekcji wyjaśnimy, w jaki sposób biblioteka serializować dane oraz jak można kontrolować to zachowanie przy użyciu interfejsów API serializacji.

Aby przejść do dyskusji na temat serializacji, będziemy współpracować z nowym modelem opartym na termostatie. Najpierw podajmy kilka teł scenariusza, który próbujemy rozwiązać.

Chcemy modelować termostat, który mierzy temperaturę i wilgotność. Każdy element danych zostanie wysłany do IoT Hub w różny sposób. Domyślnie termostat ingresses zdarzenie temperatury co 2 minuty; zdarzenie wilgotności jest ingressed co 15 minut. Gdy każde zdarzenie jest ingressed, musi zawierać znacznik czasu, który pokazuje czas, w którym jest mierzona odpowiednia temperatura lub wilgotność.

W tym scenariuszu przedstawiono dwa różne sposoby modelowania danych, a my wyjaśnimy wpływ modelu na serializowane dane wyjściowe.

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

Należy zauważyć, że model zawiera dwa zdarzenia danych: **temperatury** i **wilgotności**. W przeciwieństwie do poprzednich przykładów, typ każdego zdarzenia jest strukturą zdefiniowaną przy użyciu **deklaracji DECLARE \_ struct**. **TemperatureEvent** zawiera pomiar temperatury i sygnaturę czasową; **HumidityEvent** zawiera pomiar wilgotności i sygnaturę czasową. Ten model umożliwia naszym naturalnym sposobem modelowania danych dla opisanego powyżej scenariusza. Po wysłaniu zdarzenia do chmury będziemy wysyłać do niej parę temperatury/sygnatur czasowych lub sygnatury czasowej.

Możemy wysłać zdarzenie dotyczące temperatury do chmury, korzystając z kodu, takiego jak:

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

Będziemy używać ustalonych wartości temperatury i wilgotności w przykładowym kodzie, ale Wyobraź sobie, że faktycznie pobieramy te wartości poprzez próbkowanie odpowiednich czujników na termostat.

W powyższym kodzie użyto pomocnika **GetDateTimeOffset** , który został wprowadzony wcześniej. Z powodów, które staną się bardziej jasne, ten kod jawnie oddziela zadanie serializacji i wysyłania zdarzenia. Poprzedni kod serializacji zdarzenia temperatury do buforu. Następnie **SendMessage** jest funkcją pomocnika (uwzględnioną w **simplesample \_ AMQP**), która wysyła zdarzenie do IoT Hub:

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

Ten kod jest podzbiorem pomocnika **SendAsync** opisanego w poprzedniej sekcji, więc nie przejdziemy go ponownie w tym miejscu.

Gdy uruchamiamy poprzedni kod w celu wysłania zdarzenia dotyczącego temperatury, ta serializowana postać zdarzenia jest wysyłana do IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Wysyłamy temperaturę, która jest typu **TemperatureEvent** i Ta struktura zawiera element członkowski **temperatury** i **czasu** . Jest to bezpośrednio odzwierciedlone w serializowanych danych.

Podobnie możemy wysłać zdarzenie wilgotności przy użyciu tego kodu:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializowany formularz, który jest wysyłany do IoT Hub wygląda następująco:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Jest to zgodnie z oczekiwaniami.

Przy użyciu tego modelu można przystąpić do sposobu, w jaki można łatwo dodawać dodatkowe zdarzenia. Można zdefiniować więcej struktur przy **użyciu \_ deklaracji DECLARE** i dołączyć odpowiednie zdarzenie do modelu przy użyciu **\_ danych**.

Teraz Zmodyfikujmy model tak, aby zawierał te same dane, ale z inną strukturą.

### <a name="model-2"></a>Model 2

Zastanów się nad tym alternatywnym modelem:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

W takim przypadku wyeliminowano makra **deklaruj \_ strukturę** i po prostu definiujemy elementy danych z naszego scenariusza przy użyciu typów prostych z języka modelowania.

Przez chwilę zignoruj zdarzenie **Time** . W tym miejscu jest to kod dotyczący **temperatury** transferu danych przychodzących:

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

Ten kod wysyła następujące serializowane zdarzenie do IoT Hub:

```C
{"Temperature":75}
```

I kod służący do wysyłania zdarzenia wilgotności pojawia się w następujący sposób:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła do IoT Hub:

```C
{"Humidity":45}
```

Do tej pory nadal nie ma żadnych niespodziewanych. Teraz Zmieńmy sposób użycia makra serializacji.

Makro **serializacji** może przyjmować wiele zdarzeń danych jako argumenty. Dzięki temu możemy serializować jednocześnie zdarzenia **temperatury** i **wilgotności** i wysyłać je do IoT Hub w jednym wywołaniu:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Można odgadnąć, że wynikiem tego kodu jest to, że dwa zdarzenia danych są wysyłane do IoT Hub:

[{"Temperatura": 75}, {"wilgotność: 45}]

Innymi słowy, można oczekiwać, że ten kod jest taki sam jak w przypadku wysyłania **temperatury** i **wilgotności** osobno. To tylko wygoda do przekazania obu zdarzeń do **serializacji** w tym samym wywołaniu. Nie ma to jednak żadnego przypadku. Zamiast tego, kod powyżej wysyła to pojedyncze zdarzenie danych do IoT Hub:

{"Temperatura": 75, "wilgotność": 45}

Może wydawać się dziwne, ponieważ nasz model definiuje **temperaturę** i **wilgotność** jako dwa *oddzielne* zdarzenia:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Więcej niż w przypadku nie modeluje tych zdarzeń w przypadku, gdy **temperatura** i **wilgotność** znajdują się w tej samej strukturze:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Jeśli ten model został użyty, łatwiej będzie zrozumieć, jak **temperatury** i **wilgotność** zostałyby przesłane w tym samym zserializowanym komunikacie. Niemniej jednak może nie być jasne, dlaczego działa w taki sposób, gdy przekazywane są oba zdarzenia danych do **serializacji** przy użyciu modelu 2.

Takie zachowanie jest łatwiejsze w zrozumieniu, czy znasz założenia, które trwa wykonywanie biblioteki **serializatorów** . Aby to sprawdzić, wróćmy do naszego modelu:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Zastanów się nad tym modelem w warunkach zorientowanych obiektowo. W tym przypadku modeluje urządzenie fizyczne (termostat) i to urządzenie obejmuje takie atrybuty jak **temperatury** i **wilgotności**.

Możemy wysłać cały stan naszego modelu, korzystając z kodu, takiego jak:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Zakładając, że wartości temperatury, wilgotności i czasu są ustawione, zobaczysz zdarzenie podobne do tego wysłanego do IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Czasami możesz chcieć wysłać tylko *niektóre* właściwości modelu do chmury (jest to szczególnie prawdziwe, jeśli model zawiera dużą liczbę zdarzeń danych). Warto wysyłać tylko podzbiór zdarzeń danych, na przykład w naszym wcześniej przykładzie:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Spowoduje to wygenerowanie dokładnie tego samego serializowanego zdarzenia, tak jakby **TemperatureEvent** z elementem członkowskim **temperatury** i **godziny** , podobnie jak w przypadku modelu 1. W takim przypadku mogliśmy wygenerować dokładnie to samo serializowane zdarzenie przy użyciu innego modelu (model 2), ponieważ nazywamy **serializacji** w inny sposób.

Ważnym punktem jest to, że jeśli przekażesz wiele zdarzeń danych do **serializacji,** zakłada każde zdarzenie jest właściwością w jednym obiekcie JSON.

Najlepsze podejście zależy od Ciebie i sposobu, w jaki myślisz o modelu. Jeśli wysyłasz "zdarzenia" do chmury, a każde zdarzenie zawiera zdefiniowany zestaw właściwości, to pierwsze podejście wykonuje wiele sensu. W takim przypadku należy użyć **Zadeklaruj \_** strukturę do zdefiniowania struktury każdego zdarzenia, a następnie uwzględnić je w modelu za pomocą makra **with \_ Data** . Następnie wysyłasz każde zdarzenie tak jak w pierwszym powyższym przykładzie. W tym podejściu do **serializatora** przekazywane jest tylko jedno zdarzenie danych.

Jeśli myślisz o modelu w sposób zorientowany na obiekt, to drugie podejście może być odpowiednie. W takim przypadku elementy zdefiniowane przy użyciu **z \_ danymi** są "właściwościami" obiektu. W zależności od tego, jak wiele zdarzeń "obiektu" ma być wysyłanych do chmury, można przekazać wszystkie zdarzenia do **serializacji** .

Podejście zweryfikowało jest prawidłowe lub nieprawidłowe. Wystarczy wiedzieć, jak działa Biblioteka **serializatorów** , i wybrać podejście modelowania, które najlepiej odpowiada Twoim potrzebom.

## <a name="message-handling"></a>Obsługa komunikatów

Do tej pory w tym artykule omówiono tylko wysyłanie zdarzeń do IoT Hub i brak odpowiedzi na komunikaty. Przyczyną tego problemu jest to, że musimy wiedzieć, co jest potrzebne do uzyskania informacji o otrzymywaniu komunikatów, w tym artykule [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Odwołaj się z tego artykułu, aby przetwarzać komunikaty przez zarejestrowanie funkcji wywołania zwrotnego komunikatu:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Następnie napiszesz funkcję wywołania zwrotnego, która jest wywoływana po odebraniu komunikatu:

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

Ta implementacja **IoTHubMessage** wywołuje konkretną funkcję dla każdej akcji w modelu. Na przykład jeśli model definiuje tę akcję:

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

**SetAirResistance** jest następnie wywoływana, gdy wiadomość jest wysyłana do urządzenia.

Jeszcze nie wyjaśniono, jak wygląda serializowana wersja komunikatu. Innymi słowy, jeśli chcesz wysłać wiadomość **SetAirResistance** do urządzenia, tak jak wygląda?

W przypadku wysyłania komunikatu do urządzenia można to zrobić za pomocą zestawu SDK usługi Azure IoT. Nadal musisz wiedzieć, jakie ciągi wysłać, aby wywołać określoną akcję. Ogólny format wysyłania komunikatu jest następujący:

```C
{"Name" : "", "Parameters" : "" }
```

Wysyłasz serializowany obiekt JSON z dwiema właściwościami: **name** to nazwa akcji (komunikat), a **Parametry** zawierają parametry tej akcji.

Na przykład, aby wywołać **SetAirResistance** można wysłać tę wiadomość do urządzenia:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Nazwa akcji musi być dokładnie zgodna z akcją zdefiniowaną w modelu. Nazwy parametrów muszą być również zgodne. Pamiętaj również o rozróżnieniu wielkości liter. **Nazwa** i **Parametry** są zawsze pisane wielką literą. Upewnij się, że w modelu jest zgodna wielkość liter i parametrów akcji. W tym przykładzie nazwa akcji to "SetAirResistance", a nie "SetAirResistance".

Dwie inne akcje **TurnFanOn** i **TurnFanOff** mogą być wywoływane przez wysyłanie tych komunikatów do urządzenia:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

W tej sekcji opisano wszystko, co należy wiedzieć podczas wysyłania zdarzeń i otrzymywania komunikatów z biblioteką **serializatorów** . Przed przejściem na ten temat należy określić parametry, które można skonfigurować, aby kontrolować, jak duży model.

## <a name="macro-configuration"></a>Konfiguracja makra

W przypadku korzystania z biblioteki **serializatorów** ważna część zestawu SDK, która ma być świadoma, znajduje się w bibliotece narzędzi do udostępniania platformy Azure-c.

Jeśli zostało Sklonowane repozytorium Azure-IoT-SDK-c z usługi GitHub i wydano `git submodule update --init` polecenie, ta udostępniona biblioteka narzędzi znajduje się tutaj:

```C
.\\c-utility
```

Jeśli biblioteka nie została sklonowana, możesz ją znaleźć [tutaj](https://github.com/Azure/azure-c-shared-utility).

W bibliotece narzędzi udostępnionych znajduje się następujący folder:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ten folder zawiera rozwiązanie programu Visual Studio o nazwie narzędzia **makr \_ \_ h \_ Generator. sln**:

  ![Zrzut ekranu przedstawiający rozwiązanie Visual Studio maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Program w tym rozwiązaniu generuje plik **. \_ h** . Istnieje domyślny plik narzędzia makra \_ . h dołączony do zestawu SDK. To rozwiązanie umożliwia modyfikację niektórych parametrów, a następnie ponowne utworzenie pliku nagłówkowego na podstawie tych parametrów.

Dwa kluczowe parametry, których dotyczy, to **nArithmetic** i **nMacroParameters**, które są zdefiniowane w tych dwóch wierszach znalezionych w makrze \_ utils.TT:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Te wartości są domyślnymi parametrami zawartymi w zestawie SDK. Każdy parametr ma następujące znaczenie:

* nMacroParameters — określa liczbę parametrów, które mogą znajdować się w jednej \_ definicji makra Zadeklaruj model.
* nArithmetic — określa łączną liczbę elementów członkowskich dozwolonych w modelu.

Przyczyną tych parametrów są ważne, ponieważ kontrolują one, jak duży model może być. Rozważmy na przykład tę definicję modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak wspomniano wcześniej, **Zadeklaruj \_ model** jest tylko makro C. Nazwy modelu i instrukcji **with \_ Data** (inne makro) są parametrami **DECLARE \_ model**. **nMacroParameters** definiuje liczbę parametrów, które można uwzględnić w **\_ modelu DECLARE**. Efektywnie definiuje to, ile może istnieć deklaracja zdarzeń danych i akcji. W związku z tym z domyślnym limitem 124 oznacza to, że można zdefiniować model z kombinacją akcji i zdarzeń danych programu 60. Jeśli spróbujesz przekroczyć ten limit, otrzymasz błędy kompilatora, które wyglądają podobnie do następującej:

  ![Zrzut ekranu z błędami kompilatora parametrów makra](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**NArithmetic** parametr zawiera więcej informacji o działaniach wewnętrznych języka makr niż aplikacja.  Kontroluje ona łączną liczbę elementów członkowskich, które mogą znajdować się w modelu, w tym **DECLARE_STRUCT** makra. Jeśli zaczniesz widzieć błędy kompilatora, takie jak to, należy spróbować zwiększyć **nArithmetic**:

   ![Zrzut ekranu błędów kompilatora arytmetycznego](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Jeśli chcesz zmienić te parametry, zmodyfikuj wartości w \_ pliku utils.tt makra, ponownie skompiluj \_ Narzędzia makr \_ h \_ Generator. sln i uruchom skompilowany program. Gdy to zrobisz, nowy \_ plik. h narzędzia makr zostanie wygenerowany i umieszczony w. \\ Common \\ Inc — katalog.

Aby można było użyć nowej wersji narzędzi makro \_ . h, Usuń pakiet NuGet **serializator** z rozwiązania i w jego miejscu Uwzględnij **serializator** programu Visual Studio. Umożliwia to kompilowanie kodu względem kodu źródłowego biblioteki serializatorów. Obejmuje to zaktualizowane narzędzia makr \_ . h. Jeśli chcesz to zrobić dla **simplesample \_ AMQP**, Zacznij od usunięcia pakietu NuGet dla biblioteki serializatorów z rozwiązania:

   ![Zrzut ekranu przedstawiający Usuwanie pakietu NuGet dla biblioteki serializatorów](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Następnie Dodaj ten projekt do rozwiązania programu Visual Studio:

> .\\ Serializator w języku c \\ \\ Kompiluj kompilację \\ systemu Windows \\ . vcxproj
> 
> 

Gdy wszystko będzie gotowe, rozwiązanie powinno wyglądać następująco:

   ![Zrzut ekranu przedstawiający rozwiązanie simplesample_amqp Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Teraz podczas kompilowania rozwiązania zaktualizowane narzędzia makr \_ . h są uwzględniane w danych binarnych.

Należy pamiętać, że zwiększenie ilości tych wartości może przekroczyć limity kompilatora. Do tego momentu **nMacroParameters** jest głównym parametrem, który ma być używany. Specyfikacja C99 określa, że w definicji makra można używać co najmniej 127 parametrów. Kompilator firmy Microsoft jest zgodny ze specyfikacją dokładnie (i ma limit 127), więc nie będzie można zwiększyć **nMacroParameters** poza wartością domyślną. Inne kompilatory mogą umożliwić wykonanie tej czynności (na przykład kompilator GNU obsługuje wyższy limit).

Do tej pory omówiono wszystko, czego potrzebujesz, aby dowiedzieć się, jak napisać kod przy użyciu biblioteki **serializatorów** . Przed zakończeniem należy ponownie odwiedzić niektóre tematy z poprzednich artykułów, które mogą być zastanawiane.

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu
Przykładowa aplikacja, dla której ten artykuł koncentruje się na **simplesample \_ AMQP**. Ten przykład używa interfejsów API wyższego poziomu (innych niż **pozostałe) do** wysyłania zdarzeń i odbierania komunikatów. W przypadku korzystania z tych interfejsów API działa wątek w tle, który bierze pod uwagę zarówno wysyłanie zdarzeń, jak i otrzymywanie komunikatów. Można jednak użyć interfejsów API niższego poziomu (szystkie), aby wyeliminować ten wątek w tle i przejąć jawną kontrolę w przypadku wysyłania zdarzeń lub odbierania komunikatów z chmury.

Zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md)istnieje zestaw funkcji, które składają się z interfejsów API wyższego poziomu:

* Usługi iothubclient \_ CreateFromConnectionString
* Usługi iothubclient \_ SendEventAsync
* Usługi iothubclient \_ SetMessageCallback
* Usługi iothubclient \_ niszczenia

Te interfejsy API są prezentowane w **simplesample \_ AMQP**.

Istnieje również analogiczny zestaw interfejsów API niższego poziomu.

* Usługi iothubclient \_ szystkie \_ CreateFromConnectionString
* Usługi iothubclient \_ szystkie \_ SendEventAsync
* Usługi iothubclient \_ szystkie \_ SetMessageCallback
* Usługi iothubclient \_ wszystkie \_ zniszczenia

Należy pamiętać, że interfejsy API niższego poziomu działają dokładnie tak samo, jak opisano w poprzednich artykułach. Możesz użyć pierwszego zestawu interfejsów API, jeśli chcesz, aby wątek w tle obsługiwał zdarzenia wysyłania i odbierać komunikaty. Drugi zestaw interfejsów API jest używany, jeśli chcesz jawnie kontrolować podczas wysyłania i odbierania danych z IoT Hub. Zestaw interfejsów API działa równie dobrze z biblioteką **serializatorów** .

Przykład sposobu używania interfejsów API niższego poziomu z biblioteką **serializatorów** można znaleźć w aplikacji **\_ http simplesample** .

## <a name="additional-topics"></a>Tematy dodatkowe
Kilka innych tematów cennych w tym przypadku jest obsługa właściwości, przy użyciu alternatywnych poświadczeń urządzenia i opcji konfiguracji. Są to wszystkie tematy omówione w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md). Głównym punktem jest to, że wszystkie te funkcje działają w taki sam sposób, jak w przypadku biblioteki **serializatorów** , tak jak w przypadku biblioteki **usługi iothubclient** . Na przykład jeśli chcesz dołączyć właściwości do zdarzenia z modelu, użyj **\_ Właściwości IoTHubMessage** i **Mapuj** \_ **AddorUpdate** w taki sam sposób jak opisano wcześniej:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Czy zdarzenie zostało wygenerowane z biblioteki **serializatorów** lub utworzone ręcznie przy użyciu biblioteki **usługi iothubclient** , nie ma znaczenia.

W przypadku alternatywnych poświadczeń urządzenia za pomocą **usługi iothubclient \_ ll \_ Create** działa tylko, a także **usługi iothubclient \_ CreateFromConnectionString** do alokowania **\_ \_ dojścia klienta IOTHUB**.

Na koniec, jeśli używasz biblioteki **serializatorów** , możesz ustawić opcje konfiguracji za pomocą **usługi iothubclient \_ szystkie \_** , tak jak w przypadku korzystania z biblioteki **usługi iothubclient** .

Funkcja, która jest unikatowa dla biblioteki **serializatorów** , to interfejsy API inicjacji. Przed rozpoczęciem pracy z biblioteką należy wywołać **\_ inicjalizację programu szeregującego**:

```C
serializer_init(NULL);
```

Jest to wykonywane tuż przed wywołaniem **usługi iothubclient \_ CreateFromConnectionString**.

Podobnie po zakończeniu pracy z biblioteką, ostatnie wywołanie będzie miało **\_ DEINIT serializator**:

```C
serializer_deinit();
```

W przeciwnym razie wszystkie inne funkcje wymienione powyżej działają tak samo w bibliotece **serializatorów** , jak w bibliotece **usługi iothubclient** . Aby uzyskać więcej informacji na temat tego tematu, zobacz [Poprzedni artykuł](iot-hub-device-sdk-c-iothubclient.md) w tej serii.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano unikatowe aspekty biblioteki **serializatorów** zawarte w **zestawie SDK urządzeń Azure IoT dla języka C**. Korzystając z tych informacji, należy dobrze zrozumieć, jak używać modeli do wysyłania zdarzeń i odbierania komunikatów z IoT Hub.

Zawiera również serie z serii trzech części na temat sposobu tworzenia aplikacji za pomocą **zestawu SDK urządzeń Azure IoT dla języka C**. Powinno to być wystarczającej ilości informacji, aby nie tylko rozpocząć pracę, ale zapoznaj się z dokładnym sposobem działania interfejsów API. Aby uzyskać dodatkowe informacje, w zestawie SDK nie omówiono kilku przykładów. W przeciwnym razie [Dokumentacja zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) jest dobrym zasobem, aby uzyskać dodatkowe informacje.

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz [wdrażanie AI na urządzeniach brzegowych z Azure IoT Edge](../iot-edge/quickstart-linux.md).