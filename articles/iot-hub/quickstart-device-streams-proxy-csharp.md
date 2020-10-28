---
title: Usługa Azure IoT Hub urządzenia — Przewodnik Szybki Start dla usług SSH i RDP
description: W tym przewodniku szybki start uruchomisz dwie przykładowe aplikacje w języku C#, które umożliwiają scenariusze SSH i RDP w strumieniu urządzenia IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3e53937122b8721aff5db435ac447b686ea16643
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748689"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki Start: Włączanie protokołu SSH i RDP przez strumień urządzenia IoT Hub przy użyciu aplikacji serwera proxy języka C# (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki Start obejmuje dwie aplikacje w języku C#, które umożliwiają wysyłanie ruchu aplikacji klient-serwer (takich jak Secure Shell [SSH] i Remote Desktop Protocol [RDP] w strumieniu urządzenia, który jest ustanowiony za pośrednictwem Centrum IoT. Aby zapoznać się z omówieniem instalacji, zobacz [lokalny serwer proxy przykład dla SSH lub RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym artykule przedstawiono najpierw Instalatora protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania portu Instalatora dla protokołu RDP. Ponieważ strumienie urządzeń są typu Application-i Protocol-niezależny od, ten sam przykład można zmodyfikować, aby pomieścić inne typy ruchu aplikacji. Ta modyfikacja zwykle polega wyłącznie na zmianie portu komunikacyjnego na ten, który jest używany przez zamierzoną aplikację.

## <a name="how-it-works"></a>Jak to działa

Na poniższej ilustracji przedstawiono, w jaki sposób lokalne i lokalne aplikacje serwera proxy w tym przykładzie umożliwiają kompleksową łączność między procesami klienta SSH i demonów SSH. Załóżmy, że demon jest uruchomiony na tym samym urządzeniu co aplikacja lokalnego serwera proxy.

![Konfiguracja lokalnej aplikacji serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Lokalna aplikacja serwera proxy nawiązuje połączenie z usługą IoT Hub i inicjuje strumień urządzenia do urządzenia docelowego.

1. Aplikacja lokalnego serwera proxy na urządzeniu wykonuje uzgadnianie inicjacji strumienia i ustanawia kompleksowy tunel przesyłania strumieniowego za pomocą punktu końcowego przesyłania strumieniowego usługi IoT Hub do strony usług.

1. Lokalna aplikacja serwer proxy łączy się z demonem SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji lokalnego serwera proxy".

1. Aplikacja lokalnego serwera proxy usługi czeka na nowe połączenia SSH od użytkownika, nasłuchuje na określonym porcie, w tym przypadku jest to port 2222. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie usługi lokalnego serwera proxy". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między aplikacją klienta SSH i serwerem.

> [!NOTE]
> Ruch SSH przesyłany za pośrednictwem strumienia urządzenia jest tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT Hub zamiast bezpośredniego przesyłania między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści wynikające z używania strumieni urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które zostały utworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP
  * Southeast Asia
  * Europa Północna

* Dwie przykładowe aplikacje, które są uruchamiane w tym przewodniku Szybki Start, są zapisywane w języku C#. Potrzebujesz zestaw .NET Core SDK 2.1.0 na komputerze deweloperskim lub nowszym.

  Zestaw .NET Core SDK można pobrać [dla wielu platform z platformy .NET](https://www.microsoft.com/net/download/all).

* Sprawdź bieżącą wersję języka C# na komputerze deweloperskim, używając następującego polecenia:

    ```
    dotnet --version
    ```

* Uruchom następujące polecenie, aby dodać rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Pobierz przykłady w języku C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)i Wyodrębnij archiwum zip.

* Prawidłowe konto użytkownika i poświadczenie na urządzeniu (Windows lub Linux) używane do uwierzytelniania użytkownika.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku szybki start użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.
   > * W przypadku nazwy urządzenia, które rejestrujesz, zaleca się użycie *urządzenia* w sposób przedstawiony. Jeśli wybierzesz inną nazwę urządzenia, Użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *Parametry połączenia urządzenia* dla zarejestrowanego urządzenia, uruchom następujące polecenia w Cloud Shell:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Aby połączyć się z Centrum IoT Hub i ustanowić strumień urządzenia, należy również uzyskać *Parametry połączenia usługi* z Centrum IoT Hub, aby włączyć aplikację po stronie usługi. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia usługi do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

Ta sekcja umożliwia ustanowienie kompleksowego strumienia do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

W oknie terminalu lokalnego przejdź do `device-streams-proxy/device` katalogu w folderze niespakowanym projektu. Należy pamiętać o następujących kwestiach:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Parametry połączenia urządzenia utworzone wcześniej. |
| `targetServiceHostName` | Adres IP, na którym nasłuchuje serwer SSH. Adres powinien być adresem `localhost` IP, w którym jest uruchomiona aplikacja lokalnego serwera proxy. |
| `targetServicePort` | Port używany przez protokół aplikacji (w przypadku protokołu SSH domyślnie to port 22).  |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

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

W innym lokalnym oknie terminalu przejdź do `iot-hub/quickstarts/device-streams-proxy/service` folderu niespakowanego projektu. Należy pamiętać o następujących kwestiach:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z którym łączy się klient SSH. W tym przykładzie używamy portu 2222, ale można użyć innych dowolnych numerów. |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

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

Teraz Użyj aplikacji klienta SSH i Połącz się z lokalną aplikacją serwera proxy na porcie 2222 (zamiast demona SSH bezpośrednio).

```
ssh {username}@localhost -p 2222
```

W tym momencie w oknie Logowanie SSH zostanie wyświetlony komunikat z prośbą o wprowadzenie poświadczeń.

Dane wyjściowe konsoli po stronie usługi (aplikacja lokalnego serwera proxy nasłuchuje na porcie 2222):

![Usługa — dane wyjściowe aplikacji lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Dane wyjściowe konsoli na urządzeniu lokalnego serwera proxy, który łączy się z demonem SSH w *IP_address: 22* :

![Dane wyjściowe aplikacji lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Dane wyjściowe konsoli aplikacji klienta SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na którym nasłuchuje aplikacja lokalnego serwera proxy:

![Dane wyjściowe aplikacji klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Konfiguracja protokołu RDP jest podobna do konfiguracji SSH (opisanej powyżej). Zamiast tego należy użyć docelowego adresu IP i portu 3389 protokołu RDP, a następnie użyć klienta RDP (zamiast klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Uruchamianie aplikacji lokalnego serwera proxy (RDP) na urządzeniu

W oknie terminalu lokalnego przejdź do `device-streams-proxy/device` katalogu w folderze niespakowanym projektu. Należy pamiętać o następujących kwestiach:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Parametry połączenia urządzenia utworzone wcześniej. |
| `targetServiceHostName` | Nazwa hosta lub adres IP, na którym działa serwer RDP. Adres powinien być adresem `localhost` IP, w którym jest uruchomiona aplikacja lokalnego serwera proxy. |
| `targetServicePort` | Port używany przez protokół aplikacji (domyślnie dla protokołu RDP to port 3389).  |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Uruchamianie lokalnej aplikacji serwera proxy (RDP)

W innym lokalnym oknie terminalu przejdź do `device-streams-proxy/service` folderu niespakowanego projektu. Należy pamiętać o następujących kwestiach:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z którym łączy się klient SSH. W tym przykładzie użyto portu 2222, ale można go zmienić na inny. |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

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

Teraz należy używać aplikacji klienckiej RDP i nawiązać połączenie z lokalną aplikacją serwera proxy na porcie 2222 (jest to dowolny dostępny port, który został wybrany wcześniej).

![Protokół RDP nawiązuje połączenie z aplikacją lokalnego serwera proxy usługi](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wdrożone lokalnie i lokalne aplikacje serwera proxy, aby nawiązywać strumień urządzeń za pośrednictwem usługi IoT Hub i używały aplikacji serwera proxy do tunelowania ruchu SSH lub RDP. Ten sam model może obsługiwać inne protokoły klient-serwer, w których serwer działa na urządzeniu (na przykład demon SSH).

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
