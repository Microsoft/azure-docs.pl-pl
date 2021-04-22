---
title: Szybki start — przesyłanie Azure IoT Hub strumieni urządzeń w języku C# — Szybki start dla protokołu SSH i RDP
description: W tym przewodniku Szybki start uruchamiasz dwie przykładowe aplikacje języka C#, które umożliwiają scenariusze SSH i RDP za pośrednictwem IoT Hub strumienia urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 5bd33a2da6b2f1ae775f088950f14ac4df465fbf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863951"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i RDP przez strumień IoT Hub za pomocą aplikacji serwera proxy języka C# (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje strumienie urządzeń jako funkcję w [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki start obejmuje dwie aplikacje języka C#, które umożliwiają przesyłanie ruchu aplikacji klient-serwer (na przykład Secure Shell [SSH] i Remote Desktop Protocol [RDP] za pośrednictwem strumienia urządzenia ustanowionego za pośrednictwem centrum IoT. Aby uzyskać omówienie konfiguracji, zobacz Przykładowa aplikacja [lokalnego serwera proxy dla protokołu SSH lub RDP.](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

W tym artykule najpierw opisano konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania portu konfiguracji dla protokołu RDP. Strumienie urządzeń są niezależne od aplikacji i protokołów, dlatego ten sam przykład można zmodyfikować w celu uwzględnienia innych typów ruchu aplikacji. Ta modyfikacja zwykle obejmuje tylko zmianę portu komunikacyjnego na port używany przez zamierzony aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Central US
  * Środkowe stany USA — EUAP
  * Southeast Asia
  * Europa Północna

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start są napisane w języku C#. Na komputerze dewelopera zestaw .NET Core SDK 2.1.0 lub nowszy.

    Możesz pobrać plik zestaw .NET Core SDK [dla wielu platform z platformy .NET.](https://dotnet.microsoft.com/download)

    Sprawdź bieżącą wersję języka C# na komputerze dewelopera przy użyciu następującego polecenia:

    ```
    dotnet --version
    ```

* [Pobierz przykłady języka C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)i wyodrębnij archiwum ZIP.

* Prawidłowe konto użytkownika i poświadczenia na urządzeniu (Windows lub Linux) używane do uwierzytelniania użytkownika.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Jak to działa

Na poniższej ilustracji pokazano, jak aplikacje serwera proxy urządzenia lokalnego i usługi lokalnej w tym przykładzie umożliwiają łączność typu end-to-end między klientem SSH i procesami demona SSH. W tym miejscu założono, że demon jest uruchomiony na tym samym urządzeniu co aplikacja serwera proxy urządzenia lokalnego.

![Konfiguracja lokalnej aplikacji serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Aplikacja serwera proxy usługi lokalnej łączy się z centrum IoT i inicjuje strumień urządzenia do urządzenia docelowego.

1. Aplikacja serwera proxy urządzenia lokalnego kończy proces inicjowania strumienia i ustanawia tunel przesyłania strumieniowego typu end-to-end przez punkt końcowy przesyłania strumieniowego centrum IoT po stronie usługi.

1. Aplikacja serwera proxy urządzenia lokalnego łączy się z demonem SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji serwera proxy urządzenia lokalnego".

1. Aplikacja serwera proxy usługi lokalnej czeka na nowe połączenia SSH od użytkownika, nasłuchując na wyznaczonym porcie— w tym przypadku jest to port 2222. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji serwera proxy usługi lokalnej". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między klientem a aplikacją serwera SSH.

> [!NOTE]
> Ruch SSH wysyłany przez strumień urządzenia jest tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego centrum IoT Hub, a nie wysyłany bezpośrednio między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści wynikające z używania strumieni urządzeń usługi IoT Hub.](iot-hub-device-streams-overview.md#benefits)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start użyjesz Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy rejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice w* pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametrów połączenia urządzenia* dla urządzenia, które zostało właśnie zarejestrowane, uruchom następujące polecenia w Cloud Shell:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócone ciągi połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Aby nawiązać połączenie z centrum IoT i ustanowić  strumień urządzenia, potrzebujesz również parametrów połączenia usługi z centrum IoT, aby włączyć aplikację po stronie usługi. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zanotuj zwrócone ciągi połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji zostanie ustanawiany strumień typu end-to-end do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

W lokalnym oknie terminalu przejdź do `device-streams-proxy/device` katalogu w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Utworzone wcześniej ciągi połączenia urządzenia. |
| `targetServiceHostName` | Adres IP, na którym nasłuchuje serwer SSH. Adres byłby taki sam, gdyby był to ten sam adres IP, w którym działa aplikacja serwera proxy urządzenia `localhost` lokalnego. |
| `targetServicePort` | Port używany przez protokół aplikacji (domyślnie dla protokołu SSH będzie to port 22).  |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

W innym lokalnym oknie terminalu przejdź `iot-hub/quickstarts/device-streams-proxy/service` do lokalizacji w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z który będzie łączyć się klient SSH. W tym przykładzie używamy portu 2222, ale można użyć innych dowolnych liczb. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Uruchamianie klienta SSH

Teraz użyj aplikacji klienckiej SSH i połącz się z aplikacją serwera proxy usługi lokalnej na porcie 2222 (zamiast bezpośrednio z demonem SSH).

```
ssh {username}@localhost -p 2222
```

W tym momencie w oknie logowania SSH zostanie wyświetlony monit o wprowadzenie poświadczeń.

Dane wyjściowe konsoli po stronie usługi (aplikacja serwera proxy usługi lokalnej nasłuchuje na porcie 2222):

![Dane wyjściowe aplikacji serwera proxy usługi lokalnej](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Dane wyjściowe konsoli w aplikacji serwera proxy urządzenia lokalnego, która łączy się z demonem SSH o *IP_address:22:*

![Dane wyjściowe aplikacji serwera proxy urządzenia lokalnego](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Dane wyjściowe konsoli aplikacji klienckiej SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na którym nasłuchuje aplikacja serwera proxy usługi lokalnej:

![Dane wyjściowe aplikacji klienckiej SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Konfiguracja protokołu RDP jest podobna do konfiguracji protokołu SSH (opisanej powyżej). Zamiast tego należy użyć docelowego adresu IP protokołu RDP i portu 3389 oraz klienta RDP (zamiast klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego (RDP)

W lokalnym oknie terminalu przejdź do `device-streams-proxy/device` katalogu w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Utworzone wcześniej ciągi połączenia urządzenia. |
| `targetServiceHostName` | Nazwa hosta lub adres IP, na którym działa serwer RDP. Ten adres byłby taki sam, gdyby był to ten sam adres IP, w którym działa aplikacja `localhost` serwera proxy urządzenia lokalnego. |
| `targetServicePort` | Port używany przez protokół aplikacji (domyślnie dla protokołu RDP będzie to port 3389).  |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej (RDP)

W innym lokalnym oknie terminalu przejdź `device-streams-proxy/service` do lokalizacji w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z który będzie łączyć się klient SSH. W tym przykładzie użyto portu 2222, ale można go zmienić na inny. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Uruchamianie klienta RDP

Teraz użyj aplikacji klienckiej RDP i połącz się z aplikacją serwera proxy usługi lokalnej na porcie 2222 (był to wybrany wcześniej dowolny dostępny port).

![Połączenie RDP z aplikacją serwera proxy usługi lokalnej](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, wdrożysz aplikacje serwera proxy device-local i service-local w celu ustanowienia strumienia urządzenia za pośrednictwem centrum IoT i za pomocą aplikacji serwera proxy tunelu ruchu SSH lub RDP. Ten sam paradygmat może obsłużyć inne protokoły klient-serwer, w których serwer działa na urządzeniu (na przykład demon SSH).

Aby dowiedzieć się więcej na temat strumieni urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
