---
title: Opis bezpośrednich metod usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — użyj bezpośrednich metod do wywoływania kodu na urządzeniach z aplikacji usługi.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 13936a55baed59d5b6257f13f69305a1ce72927a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730401"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Opis i wywoływanie metod bezpośrednich z usługi IoT Hub

Usługa IoT Hub umożliwia wywoływanie metod bezpośrednich na urządzeniach z chmury. Metody bezpośrednie reprezentują interakcję żądanie-odpowiedź z urządzeniem podobnym do wywołania HTTP, ponieważ od razu zakończy się powodzeniem lub niepowodzeniem (po upływie limitu czasu określonego przez użytkownika). Takie podejście jest przydatne w scenariuszach, w których kierunek natychmiastowej akcji różni się w zależności od tego, czy urządzenie było w stanie odpowiedzieć.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Każda metoda urządzenia jest przeznaczona dla jednego urządzenia. [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md) pokazuje, jak zapewnić sposób wywoływania metod bezpośrednich na wielu urządzeniach i planowanie wywołania metody dla odłączonych urządzeń.

Każda osoba z uprawnieniami **do łączenia usługi** w Usłudze IoT Hub może wywołać metodę na urządzeniu.

Metody bezpośrednie są zgodne ze wzorcem żądania-odpowiedzi i są przeznaczone do komunikacji, które wymagają natychmiastowego potwierdzenia ich wyniku. Na przykład interaktywne sterowanie urządzeniem, takie jak włączanie wentylatora.

W razie wątpliwości należy zapoznać się [ze wskazówkami dotyczącymi komunikacji](iot-hub-devguide-c2d-guidance.md) z chmury do urządzenia, korzystając z żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia.

## <a name="method-lifecycle"></a>Cykl życia metody

Metody bezpośrednie są implementowane na urządzeniu i może wymagać zero lub więcej danych wejściowych w ładunku metody poprawnie utworzyć wystąpienia. Metodę bezpośrednią można wywołać za pomocą`{iot hub}/twins/{device id}/methods/`identyfikatora URI skierowanego do usługi ( ). Urządzenie odbiera bezpośrednie metody za pośrednictwem tematu`$iothub/methods/POST/{method name}/`MQTT specyficznego dla `IoThub-methodname` urządzenia `IoThub-status` ( ) lub za pośrednictwem łączy AMQP (właściwości i właściwości aplikacji). 

> [!NOTE]
> Podczas wywoływania metody bezpośredniej na urządzeniu nazwy i wartości właściwości mogą zawierać tylko alfanumeryczne drukarki US-ASCII, z wyjątkiem dowolnego z następujących zestawów:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Metody bezpośrednie są synchroniczne i po upływie limitu czasu zakończyć się powodzeniem lub niepowodzeniem (domyślnie: 30 sekund, z dotym między 5 a 300 sekund). Metody bezpośrednie są przydatne w interaktywnych scenariuszach, w których urządzenie ma działać wtedy i tylko wtedy, gdy urządzenie jest w trybie online i odbiera polecenia. Na przykład włączenie światła z telefonu. W tych scenariuszach chcesz zobaczyć natychmiastowy sukces lub niepowodzenie, dzięki czemu usługa w chmurze może działać na wynik tak szybko, jak to możliwe. Urządzenie może zwrócić niektóre treści wiadomości w wyniku metody, ale nie jest wymagane dla metody, aby to zrobić. Nie ma żadnej gwarancji na kolejność lub semantyki współbieżności na wywołania metody.

Metody bezpośrednie są tylko https od strony chmury i MQTT lub AMQP od strony urządzenia.

Ładunek dla żądań metody i odpowiedzi jest dokument JSON do 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Wywoływanie metody bezpośredniej z aplikacji zaplecza

Teraz wywołać metodę bezpośrednią z aplikacji zaplecza.

### <a name="method-invocation"></a>Wywołanie metody

Bezpośrednie wywołania metod na urządzeniu to wywołania HTTPS, które tworzą następujące elementy:

* Identyfikator *URI żądania* specyficzne dla urządzenia wraz z [wersją interfejsu API:](/rest/api/iothub/service/devicemethod/invokedevicemethod)

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metoda* POST

* *Nagłówki* zawierające autoryzację, identyfikator żądania, typ zawartości i kodowanie zawartości.

* Przezroczysta *treść* JSON w następującym formacie:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Wartość podana `responseTimeoutInSeconds` w żądaniu jest czas, który usługa Usługi IoT Hub musi czekać na zakończenie wykonywania metody bezpośredniej na urządzeniu. Ustaw ten limit czasu jest co najmniej tak długo, jak oczekiwany czas wykonywania metody bezpośredniej przez urządzenie. Jeśli limit czasu nie jest podany, używana jest wartość domyślna 30 sekund. Minimalne i maksymalne `responseTimeoutInSeconds` wartości dla to odpowiednio 5 i 300 sekund.

Wartość podana `connectTimeoutInSeconds` w polu żądania jest czasem po wywołaniu metody bezpośredniej, na którą usługa IoT Hub musi czekać na odłączone urządzenie do trybu online. Wartość domyślna to 0, co oznacza, że urządzenia muszą być już w trybie online po wywołaniu metody bezpośredniej. Maksymalna wartość `connectTimeoutInSeconds` to 300 sekund.


#### <a name="example"></a>Przykład

Zobacz poniżej przykład barebone za pomocą `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Odpowiedź

Aplikacja zaplecza otrzymuje odpowiedź składającą się z następujących elementów:

* *Kod stanu HTTP:*
  * 200 oznacza pomyślne wykonanie metody bezpośredniej;
  * 404 oznacza, że identyfikator urządzenia jest nieprawidłowy lub że urządzenie nie było w trybie online po wywołaniu metody bezpośredniej, a `connectTimeoutInSeconds` następnie (użyj dołączonego komunikatu o błędzie, aby zrozumieć przyczynę);
  * 504 wskazuje limit czasu bramy spowodowany przez urządzenie, które `responseTimeoutInSeconds`nie odpowiada na bezpośrednie wywołanie metody w ramach .

* *Nagłówki* zawierające etag, identyfikator żądania, typ zawartości i kodowanie zawartości.

* Obiekt *JSON* w następującym formacie:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Oba `status` `body` i są dostarczane przez urządzenie i używane do reagowania za pomocą własnego kodu stanu i/lub opisu urządzenia.

### <a name="method-invocation-for-iot-edge-modules"></a>Wywołanie metody dla modułów IoT Edge

Wywoływanie metod bezpośrednich przy użyciu identyfikatora modułu jest obsługiwane w [SDK klienta usługi IoT C# .](https://www.nuget.org/packages/Microsoft.Azure.Devices/)

W tym celu `ServiceClient.InvokeDeviceMethodAsync()` należy użyć metody `deviceId` `moduleId` i przekazać w i jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Obsługa metody bezpośredniej na urządzeniu

Przyjrzyjmy się, jak obsługiwać metodę bezpośrednią na urządzeniu IoT.

### <a name="mqtt"></a>MQTT

Poniższa sekcja dotyczy protokołu MQTT.

#### <a name="method-invocation"></a>Wywołanie metody

Urządzenia otrzymują bezpośrednie żądania metody w `$iothub/methods/POST/{method name}/?$rid={request id}`temacie MQTT: . Liczba subskrypcji na urządzenie jest ograniczona do 5. W związku z tym zaleca się, aby nie subskrybować każdej bezpośredniej metody indywidualnie. Zamiast tego należy rozważyć `$iothub/methods/POST/#` subskrybowanie, a następnie filtrowanie dostarczonych wiadomości na podstawie żądanych nazw metod.

Obiekt odbierany przez urządzenie jest w następującym formacie:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Żądania metody są QoS 0.

#### <a name="response"></a>Odpowiedź

Urządzenie wysyła odpowiedzi `$iothub/methods/res/{status}/?$rid={request id}`do , gdzie:

* Właściwość `status` jest stan dostarczony przez urządzenie wykonania metody.

* Właściwość `$rid` jest identyfikator żądania z wywołania metody odebrane z Usługi IoT Hub.

Treść jest ustawiona przez urządzenie i może mieć dowolny stan.

### <a name="amqp"></a>AMQP

Poniższa sekcja dotyczy protokołu AMQP.

#### <a name="method-invocation"></a>Wywołanie metody

Urządzenie odbiera bezpośrednie żądania metody, tworząc link `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`odbierania na adres .

Komunikat USŁUGI AMQP dociera do łącza odbierania, który reprezentuje żądanie metody. Ten temat zawiera następujące sekcje:

* Właściwość identyfikator korelacji, która zawiera identyfikator żądania, który powinien zostać przekazany z powrotem z odpowiednią odpowiedzią metody.

* Właściwość aplikacji `IoThub-methodname`o nazwie , która zawiera nazwę wywoływanej metody.

* Treść komunikatu AMQP zawierająca ładunek metody jako JSON.

#### <a name="response"></a>Odpowiedź

Urządzenie tworzy łącze wysyłające, aby `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`zwrócić odpowiedź metody na adres .

Odpowiedź metody jest zwracana w łączu wysyłającym i jest uporządkowana w następujący sposób:

* Właściwość identyfikator korelacji, która zawiera identyfikator żądania przekazany w komunikacie żądania metody.

* Właściwość aplikacji `IoThub-status`o nazwie , która zawiera stan metody dostarczonej przez użytkownika.

* Treść komunikatu AMQP zawierająca odpowiedź metody jako JSON.

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które każdy centrum IoT udostępnia dla operacji wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisuje przydziały, które mają zastosowanie i zachowanie ograniczania przepustowości, których można się spodziewać podczas korzystania z usługi IoT Hub.

* [Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań usługi IoT Hub dla bliźniaczych urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) opisuje język zapytań Usługi IoT Hub, którego można użyć do pobierania informacji z Usługi IoT Hub o bliźniaczych bliźniąt urządzeń i zadaniach.

* [Obsługa protokołu MQTT w uężoniać w uiszczaprzestwu IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak korzystać z metod bezpośrednich, możesz być zainteresowany następującym przewodnikiem dla deweloperów usługi IoT Hub:

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, możesz być zainteresowany następującym samouczkiem Centrum IoT Hub:

* [Korzystanie z metod bezpośrednich](quickstart-control-device-node.md)
* [Zarządzanie urządzeniami za pomocą narzędzi usługi Azure IoT dla programu VS Code](iot-hub-device-management-iot-toolkit.md)
