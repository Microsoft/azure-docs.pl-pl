---
title: Zestaw SDK urządzenia Usługi Azure IoT dla języka C — IoTHubClient | Dokumenty firmy Microsoft
description: Jak używać biblioteki IoTHubClient w sdk urządzenia Usługi Azure IoT dla języka C do tworzenia aplikacji urządzeń, które komunikują się z centrum IoT hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732597"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Zestaw SDK urządzenia Usługi Azure IoT dla języka C — więcej informacji o aplikacji IoTHubClient

[Zestaw SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md) jest pierwszym artykułem z tej serii wprowadzającym **zestaw SDK urządzenia Usługi Azure IoT dla języka C.** W tym artykule wyjaśniono, że istnieją dwie warstwy architektoniczne w SDK. U podstawy znajduje się biblioteka **IoTHubClient,** która bezpośrednio zarządza komunikacją z Centrum IoT. Istnieje również biblioteka **serializatora,** który opiera się na tym, aby zapewnić usługi serializacji. W tym artykule udostępnimy dodatkowe szczegóły dotyczące biblioteki **IoTHubClient.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

W poprzednim artykule opisano, jak używać biblioteki **IoTHubClient** do wysyłania zdarzeń do usługi IoT Hub i odbierania wiadomości. W tym artykule opisano, jak dokładniej *zarządzać* danymi podczas wysyłania i odbierania danych, wprowadzając je do **interfejsów API niższego poziomu.** Wyjaśnimy również, jak dołączyć właściwości do zdarzeń (i pobrać je z wiadomości) przy użyciu funkcji obsługi właściwości w bibliotece **IoTHubClient.** Na koniec zapewnimy dodatkowe wyjaśnienie różnych sposobów obsługi wiadomości odebranych z usługi IoT Hub.

W artykule kończy się obejmujące kilka różnych tematów, w tym więcej informacji na temat poświadczeń urządzenia i jak zmienić zachowanie **IoTHubClient** za pomocą opcji konfiguracji.

Użyjemy przykładów zestawów SDK **IoTHubClient,** aby wyjaśnić te tematy. Jeśli chcesz śledzić, zobacz **przykładowy\_\_\_klient iothub http** i iothub **\_klienta\_przykładowe\_aplikacje amqp,** które są zawarte w zestawie SDK urządzenia Usługi Azure IoT dla języka C. Wszystko opisane w poniższych sekcjach jest wykazane w tych przykładach.

Zestaw [**SDK urządzenia Usługi Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium C GitHub można znaleźć i wyświetlić szczegóły interfejsu API w [odwołaniu do interfejsu API języka C.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu

W poprzednim artykule opisano podstawowe działanie **IotHubClient** w kontekście **aplikacji\_amqp przykładowego klienta\_\_iothub.** Na przykład wyjaśniono, jak zainicjować biblioteki przy użyciu tego kodu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Opisano również, jak wysyłać zdarzenia przy użyciu tej funkcji wywołania.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

W artykule opisano również sposób odbierania wiadomości przez zarejestrowanie funkcji wywołania zwrotnego.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

W artykule pokazano również, jak zwolnić zasoby przy użyciu kodu, takich jak następujące.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Istnieją funkcje towarzyszące dla każdego z tych interfejsów API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* Zestaw IoTHubClient\_LLMessageCallback\_
* IoTHubClient\_\_LL Zniszczyć

Wszystkie te funkcje obejmują **LL** w nazwie interfejsu API. Inne **LL** część nazwy, parametry każdej z tych funkcji są identyczne z ich odpowiedników innych niż LL. Jednak zachowanie tych funkcji jest inny w jeden ważny sposób.

Po wywołaniu **IoTHubClient\_CreateFromConnectionString,** podstawowe biblioteki utworzyć nowy wątek, który działa w tle. Ten wątek wysyła zdarzenia i odbiera wiadomości z Usługi IoT Hub. Nie taki wątek jest tworzony podczas pracy z interfejsami API **LL.** Tworzenie wątku tła jest udogodnieniem dla dewelopera. Nie musisz się martwić o jawne wysyłanie zdarzeń i odbieranie wiadomości z Usługi IoT Hub — dzieje się to automatycznie w tle. Z drugiej strony **interfejsy** API LL zapewniają jawną kontrolę nad komunikacją z Centrum IoT, jeśli jest to potrzebne.

Aby lepiej zrozumieć tę koncepcję, przyjrzyjmy się przykładowi:

Po wywołaniu **IoTHubClient\_SendEventAsync,** co faktycznie robisz jest umieszczenie zdarzenia w buforze. Wątek tła utworzony podczas wywoływania **IoTHubClient\_CreateFromConnectionString** stale monitoruje ten bufor i wysyła wszystkie dane, które zawiera do Centrum IoT Hub. Dzieje się tak w tle w tym samym czasie, że główny wątek wykonuje inne prace.

Podobnie podczas rejestrowania funkcji wywołania zwrotnego dla wiadomości przy użyciu **IoTHubClient\_SetMessageCallback**, instruujesz SDK, aby wątek w tle wywołał funkcję wywołania zwrotnego po odebraniu wiadomości, niezależnie od wątku głównego.

Interfejsy API **LL** nie tworzą wątku w tle. Zamiast tego nowy interfejs API musi być wywoływany jawnie wysyłać i odbierać dane z Usługi IoT Hub. Jest to zaprezentowane w poniższym przykładzie.

Przykładowy **\_plik\_\_HTTP iothub,** który jest zawarty w zestawie SDK demonstruje interfejsy API niższego poziomu. W tym przykładzie wysyłamy zdarzenia do Usługi IoT Hub z kodem, takimi jak:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Pierwsze trzy wiersze tworzą wiadomość, a ostatni wiersz wysyła zdarzenie. Jednak, jak wspomniano wcześniej, wysłanie zdarzenia oznacza, że dane są po prostu umieszczane w buforze. Nic nie jest przesyłane w sieci, gdy **nazywamy IoTHubClient\_LL\_SendEventAsync**. Aby faktycznie przystawić dane do Usługi IoT Hub, należy wywołać **IoTHubClient\_\_LL DoWork**, jak w tym przykładzie:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod (z **\_przykładowej\_\_aplikacji http klienta iothub)** wielokrotnie wywołuje **\_IoTHubClient\_LL DoWork**. Za każdym razem, gdy wywoływany jest **program IoTHubClient\_LL\_DoWork,** wysyła niektóre zdarzenia z bufora do usługi IoT Hub i pobiera wiadomość w kolejce wysyłaną do urządzenia. Ten ostatni przypadek oznacza, że jeśli zarejestrowaliśmy funkcję wywołania zwrotnego dla wiadomości, wywołanie zwrotne jest wywoływane (przy założeniu, że wszystkie wiadomości są umieszczane w kolejce). Zarejestrowalibyśmy taką funkcję wywołania zwrotnego z kodem, takim jak:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Powodem, dla którego **Usługa\_\_IoTHubClient LL DoWork** jest często wywoływana w pętli, jest to, że za każdym razem, gdy jest wywoływana, wysyła *niektóre* buforowane zdarzenia do usługi IoT Hub i pobiera *następną* wiadomość w kolejce do urządzenia. Każde wywołanie nie jest gwarantowane, aby wysłać wszystkie zdarzenia buforowane lub pobrać wszystkie wiadomości w kolejce. Jeśli chcesz wysłać wszystkie zdarzenia w buforze, a następnie kontynuować inne przetwarzanie, możesz zastąpić tę pętlę kodem, takim jak:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod wywołuje **IoTHubClient\_LL\_DoWork,** dopóki wszystkie zdarzenia w buforze nie zostały wysłane do usługi IoT Hub. Należy zauważyć, że nie oznacza to również, że wszystkie wiadomości w kolejce zostały odebrane. Jednym z powodów jest to, że sprawdzanie "wszystkich" wiadomości nie jest tak deterministyczne działanie. Co się stanie, jeśli pobierzesz "wszystkie" wiadomości, ale kolejna zostanie wysłana do urządzenia natychmiast po? Lepszym sposobem radzenia sobie z tym jest zaprogramowany limit czasu. Na przykład funkcja wywołania zwrotnego wiadomości można zresetować czasomierz za każdym razem, gdy jest wywoływany. Następnie można napisać logikę, aby kontynuować przetwarzanie, jeśli, na przykład, żadne wiadomości zostały odebrane w ciągu ostatnich *X* sekund.

Po zakończeniu wnikania w ruch przychodzący i odbieranie wiadomości, należy wywołać odpowiednią funkcję, aby oczyścić zasoby.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Zasadniczo istnieje tylko jeden zestaw interfejsów API do wysyłania i odbierania danych z wątku w tle i inny zestaw interfejsów API, który robi to samo bez wątku tła. Wielu deweloperów może preferować interfejsy API innego niż LL, ale interfejsy API niższego poziomu są przydatne, gdy deweloper chce jawnej kontroli nad transmisjami sieciowymi. Na przykład niektóre urządzenia zbierają dane w czasie i tylko zdarzenia przychodzące w określonych odstępach czasu (na przykład raz na godzinę lub raz dziennie). Interfejsy API niższego poziomu umożliwiają jawną kontrolę podczas wysyłania i odbierania danych z usługi IoT Hub. Inni po prostu wolą prostotę, że interfejsy API niższego poziomu zapewniają. Wszystko dzieje się w głównym wątku, a nie w jakiejś pracy dzieje się w tle.

Niezależnie od wybranego modelu, należy być spójne, w których interfejsów API używasz. Jeśli zaczniesz od wywołania **IoTHubClient\_LL\_CreateFromConnectionString**, upewnij się, że używasz tylko odpowiednich interfejsów API niższego poziomu dla każdej pracy uzupełniającej:

* IoTHubClient\_LL\_SendEventAsync
* Zestaw IoTHubClient\_LLMessageCallback\_
* IoTHubClient\_\_LL Zniszczyć
* Praca dopracowa\_IoTHubClient LL\_

Jest też odwrotnie. Jeśli zaczniesz od **IoTHubClient\_CreateFromConnectionString**, użyj interfejsów API innych niż LL dla dodatkowego przetwarzania.

W zestaw SDK urządzenia Usługi Azure IoT dla języka C zobacz **przykładową\_aplikację http klienta\_\_iothub,** aby uzyskać pełny przykład interfejsów API niższego poziomu. Do **przykładowej\_\_\_aplikacji amqp klienta iothub** można odwoływać się do pełnego przykładu interfejsów API innych niż LL.

## <a name="property-handling"></a>Obsługa nieruchomości

Do tej pory, kiedy opisywliśmy wysyłanie danych, odnosiliśmy się do treści wiadomości. Na przykład, rozważmy ten kod:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

W tym przykładzie wysyła wiadomość do Usługi IoT Hub z tekstem "Hello World". Jednak Usługa IoT Hub umożliwia również właściwości, które mają być dołączone do każdej wiadomości. Właściwości są pary nazw/wartości, które mogą być dołączone do wiadomości. Na przykład możemy zmodyfikować poprzedni kod, aby dołączyć właściwość do wiadomości:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Zaczynamy od wywołania **właściwości IoTHubMessage\_** i przekazania go dojście naszej wiadomości. Co możemy odzyskać jest **odwołanie DOJDA MAP,\_** który umożliwia nam rozpoczęcie dodawania właściwości. Ten ostatni jest realizowany przez **wywołanie Map\_AddOrUpdate**\_, który przyjmuje odwołanie do DOJŚCIA MAP, nazwę właściwości i wartość właściwości. Za pomocą tego interfejsu API możemy dodać tyle właściwości, ile chcemy.

Gdy zdarzenie jest odczytywane z **centrum zdarzeń,** odbiornik można wyliczyć właściwości i pobrać ich odpowiednie wartości. Na przykład w .NET można to osiągnąć, uzyskując dostęp do [kolekcji Właściwości w obiekcie EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

W poprzednim przykładzie dołączamy właściwości do zdarzenia, które wysyłamy do usługi IoT Hub. Właściwości można również dołączyć do wiadomości odebranych z Usługi IoT Hub. Jeśli chcemy pobrać właściwości z wiadomości, możemy użyć kodu, takiego jak w naszej funkcji wywołania zwrotnego wiadomości:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Wywołanie **właściwości IoTHubMessage\_** zwraca odwołanie **DOJDULUJ MAP.\_** Następnie przekazujemy to odwołanie do **Map\_GetInternals,** aby uzyskać odwołanie do tablicy par nazwa/wartość (a także liczbę właściwości). W tym momencie jest to prosta kwestia wyliczenia właściwości, aby uzyskać do wartości, które chcemy.

Nie trzeba używać właściwości w aplikacji. Jednak jeśli trzeba ustawić je na zdarzenia lub pobrać je z wiadomości, **biblioteka IoTHubClient** ułatwia.

## <a name="message-handling"></a>Obsługa komunikatów

Jak wspomniano wcześniej, gdy wiadomości przychodzą z Usługi IoT Hub biblioteki **IoTHubClient** odpowiada, wywołując zarejestrowaną funkcję wywołania zwrotnego. Istnieje parametr powrotu tej funkcji, który zasługuje na dodatkowe wyjaśnienie. Oto fragment funkcji wywołania zwrotnego w **\_przykładowej\_\_aplikacji http klienta iothub:**

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Należy zauważyć, że typem zwracanym jest **wynik dyspozycji\_\_IOTHUBMESSAGE** i w tym konkretnym przypadku zwracamy **IOTHUBMESSAGE\_ACCEPTED**. Istnieją inne wartości, które możemy zwrócić z tej funkcji, które zmieniają sposób biblioteki **IoTHubClient** reaguje na wywołanie zwrotne wiadomości. Oto opcje.

* **IOTHUBMESSAGE\_ZAAKCEPTOWANE** — wiadomość została pomyślnie przetworzona. Biblioteka **IoTHubClient** nie wywoła funkcji wywołania zwrotnego ponownie z tej samej wiadomości.

* **IOTHUBMESSAGE\_ODRZUCONE** - Wiadomość nie została przetworzona i nie ma chęci, aby to zrobić w przyszłości. Biblioteka **IoTHubClient** nie należy ponownie wywoływać funkcji wywołania zwrotnego z tej samej wiadomości.

* **IOTHUBMESSAGE\_ABANDONED** — wiadomość nie została pomyślnie przetworzona, ale **biblioteka IoTHubClient** powinna ponownie wywołać funkcję wywołania zwrotnego z tą samą wiadomością.

W przypadku pierwszych dwóch kodów zwrotnych **biblioteka IoTHubClient** wysyła do centrum IoT Hub komunikat wskazujący, że wiadomość powinna zostać usunięta z kolejki urządzenia i nie dostarczona ponownie. Efekt netto jest taki sam (wiadomość jest usuwana z kolejki urządzenia), ale czy wiadomość została zaakceptowana czy odrzucona, jest nadal rejestrowana.  Nagrywanie tego rozróżnienia jest przydatne dla nadawców wiadomości, którzy mogą nasłuchiwać opinii i dowiedzieć się, czy urządzenie zaakceptowało lub odrzuciło określoną wiadomość.

W ostatnim przypadku wiadomość jest również wysyłana do usługi IoT Hub, ale wskazuje, że wiadomość powinna zostać ponownie dostarczona. Zazwyczaj porzucisz wiadomość, jeśli napotkasz jakiś błąd, ale chcesz spróbować ponownie przetworzyć wiadomość. Z kolei odrzucenie wiadomości jest właściwe, gdy napotkasz nieodwracalny błąd (lub jeśli po prostu zdecydujesz, że nie chcesz przetwarzać wiadomości).

W każdym przypadku należy pamiętać o różnych kodów zwracanych, dzięki czemu można wywołać zachowanie, które chcesz z biblioteki **IoTHubClient.**

## <a name="alternate-device-credentials"></a>Poświadczenia urządzenia alternatywnego

Jak wyjaśniono wcześniej, pierwszą rzeczą do zrobienia podczas pracy z biblioteką **IoTHubClient** jest uzyskanie **dojścia klienta\_\_IOTHUB z** wywołaniem, takich jak następujące:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty **do IoTHubClient\_CreateFromConnectionString** są parametry połączenia urządzenia i parametr, który wskazuje protokół, którego używamy do komunikowania się z Centrum IoT. Parametry połączenia urządzenia mają format wyświetlany w następujący sposób:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

W tym ciągu znajdują się cztery informacje: nazwa centrum IoT, sufiks usługi IoT Hub, identyfikator urządzenia i udostępniony klucz dostępu. W pełni kwalifikowana nazwa domeny (FQDN) centrum IoT hub podczas tworzenia wystąpienia centrum IoT w witrynie Azure Portal — daje to nazwę centrum IoT (pierwsza część nazwy FQDN) i sufiks centrum IoT (pozostała część nazwy FQDN). Podczas rejestracji urządzenia w Urzędzie Obsługi Klienta (zgodnie z opisem w poprzednim [artykule)](iot-hub-device-sdk-c-intro.md)otrzymasz identyfikator urządzenia i udostępniony klucz dostępu.

**IoTHubClient\_CreateFromConnectionString** umożliwia zainicjowanie biblioteki jednym ze sposobów zainicjowania. Jeśli wolisz, możesz utworzyć nowy **IOTHUB\_CLIENT\_HANDLE** przy użyciu tych indywidualnych parametrów, a nie ciągu połączenia urządzenia. Osiąga się to za pomocą następującego kodu:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To osiąga to samo co **IoTHubClient\_CreateFromConnectionString**.

Może wydawać się oczywiste, że chcesz użyć **IoTHubClient\_CreateFromConnectionString** zamiast tej bardziej pełnej metody inicjowania. Należy jednak pamiętać, że podczas rejestrowania urządzenia w Urzędzie Obsługi Klienta otrzymujesz identyfikator urządzenia i klucz urządzenia (a nie parametry połączenia). Narzędzie SDK *eksploratora urządzeń* wprowadzone w [poprzednim artykule](iot-hub-device-sdk-c-intro.md) używa bibliotek w **zestawie SDK usługi Azure IoT** do utworzenia ciągu połączenia urządzenia z identyfikatora urządzenia, klucza urządzenia i nazwy hosta usługi IoT Hub. Więc **wywołanie IoTHubClient\_\_LL Create** może być korzystne, ponieważ zapisuje krok generowania ciągu połączenia. Użyj dowolnej metody jest wygodna.

## <a name="configuration-options"></a>Opcje konfiguracji

Do tej pory wszystko opisane na temat sposobu działania biblioteki **IoTHubClient** odzwierciedla jego domyślne zachowanie. Istnieje jednak kilka opcji, które można ustawić, aby zmienić sposób działania biblioteki. Jest to realizowane przez wykorzystanie interfejsu API **IoTHubClient\_LL\_SetOption.** Rozważmy następujący przykład:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Istnieje kilka opcji, które są powszechnie używane:

* **SetBatching** (bool) — jeśli **true**, a następnie dane wysyłane do Usługi IoT Hub jest wysyłany w partiach. Jeśli **false**, a następnie wiadomości są wysyłane indywidualnie. Wartość domyślna to **false**. Obsługiwane jest przetwarzanie wsadowe przez amqp / AMQP-WS, a także dodawanie właściwości systemu w komunikatach D2C.

* **Limit czasu** (niepodpisany int) — ta wartość jest reprezentowana w milisekundach. Jeśli wysłanie żądania HTTPS lub otrzymanie odpowiedzi trwa dłużej niż ten czas, limit czasu połączenia.

Opcja wsadowania jest ważna. Domyślnie biblioteka przesyła zdarzenia przychodzące pojedynczo (pojedyncze zdarzenie jest niezależnie od tego, co przekazujesz do **IoTHubClient\_LL\_SendEventAsync).** Jeśli opcja przetwarzania wsadowego jest **true,** biblioteka zbiera tyle zdarzeń, ile może z buforu (do maksymalnego rozmiaru wiadomości, który zaakceptuje Centrum IoT Hub).  Partia zdarzeń jest wysyłana do Centrum IoT w jednym wywołaniu HTTPS (poszczególne zdarzenia są powiązane z tablicą JSON). Włączenie przetwarzania wsadowego zazwyczaj powoduje duży wzrost wydajności, ponieważ zmniejszasz liczbę rund sieciowych. To również znacznie zmniejsza przepustowość, ponieważ wysyłasz jeden zestaw nagłówków HTTPS z partii zdarzeń, a nie zestaw nagłówków dla każdego zdarzenia. Jeśli nie masz określonego powodu, aby zrobić inaczej, zazwyczaj należy włączyć przetwarzanie wsadowe.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano szczegółowo zachowanie biblioteki **IoTHubClient** znalezionej w **sdku urządzenia Usługi Azure IoT dla języka C.** Dzięki tym informacjom należy dobrze zrozumieć możliwości biblioteki **IoTHubClient.** Drugi artykuł w tej serii jest [zestaw SDK urządzenia Usługi Azure IoT dla języka C — serializator](iot-hub-device-sdk-c-serializer.md), który zawiera podobne szczegóły w bibliotece **serializatora.**

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [zestawów SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz [Wdrażanie sztucznej inteligencji na urządzeniach brzegowych za pomocą usługi Azure IoT Edge.](../iot-edge/tutorial-simulate-device-linux.md)
