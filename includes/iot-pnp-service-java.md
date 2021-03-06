---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8d3f35a733a0f78fabc33df857d911ba3cd222f5
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244544"
---
IoT Plug and Play upraszcza IoT, umożliwiając korzystanie z możliwości urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka Java do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest podłączony do rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](/java/azure/jdk/)w obszarze **Obsługa długoterminowa** wybierz pozycję **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonowanie repozytorium zestawu SDK przy użyciu przykładowego kodu

Jeśli zakończysz [Przewodnik Szybki Start: Podłącz przykładową aplikację urządzenia IoT Plug and Play działającą w systemie Windows do IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), repozytorium zostało już sklonowane.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [Microsoft Azure IoT SDK for Java](https://github.com/Azure/azure-iot-sdk-java) GitHub Repository do tej lokalizacji:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Kompilowanie i uruchamianie przykładowego urządzenia

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

W tym przewodniku szybki start użyjesz przykładowego urządzenia z termostatem zapisaną w języku Java jako urządzenie Plug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz okno terminalu i przejdź do folderu lokalnego zawierającego repozytorium Microsoft Azure IoT SDK for Java, które zostało sklonowane z usługi GitHub.

1. To okno terminalu służy jako terminal **urządzenia** . Przejdź do folderu głównego sklonowanego repozytorium. Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

1. Uruchom następujące polecenie, aby skompilować przykładową aplikację urządzenia:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Aby uruchomić przykładową aplikację urządzenia, przejdź do folderu *device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* i uruchom następujące polecenie:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładowego urządzenia w trakcie wykonywania następnych kroków.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W obszarze [Konfigurowanie środowiska dla usługi IoT Plug and Play Przewodniki Szybki Start i samouczki](../articles/iot-pnp/set-up-environment.md) zostały utworzone dwie zmienne środowiskowe w celu skonfigurowania przykładu w celu połączenia z Centrum IoT i urządzeniem:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT, które zostało zapisanie w języku Java, aby można było korzystać z właśnie skonfigurowanego przykładowego urządzenia.

> [!NOTE]
> Ten przykład używa przestrzeni nazw **com. Microsoft. Azure. Sdk. IoT. Service** z **klienta usługi IoT Hub**. Aby dowiedzieć się więcej na temat interfejsów API, w tym Digital bliźniaczych reprezentacji API, zobacz [Przewodnik dla deweloperów usług](../articles/iot-pnp/concepts-developer-guide-service.md).

1. Otwórz inne okno terminalu, które ma być używane jako terminal **usługi** .

1. W sklonowanym repozytorium Java SDK przejdź do folderu *service\iot-Service-samples\pnp-Service-sample\thermostat-Service-Sample* .

1. Aby uruchomić przykładową aplikację usługi, uruchom następujące polecenie:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Pobierz sznurki urządzenia

Poniższy fragment kodu przedstawia sposób pobierania sznurka urządzenia w usłudze:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Aktualizowanie sznurka urządzenia

Poniższy fragment kodu przedstawia sposób używania *poprawki* do aktualizowania właściwości za pomocą sznurka urządzenia:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

Dane wyjściowe urządzenia pokazują, jak urządzenie odpowiada na tę aktualizację właściwości.

### <a name="invoke-a-command"></a>Wywołaj polecenie

Poniższy fragment kodu przedstawia Wywoływanie polecenia na urządzeniu:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

Dane wyjściowe urządzenia pokazują, jak urządzenie odpowiada na to polecenie.
