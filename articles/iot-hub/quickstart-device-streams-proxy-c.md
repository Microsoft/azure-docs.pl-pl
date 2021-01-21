---
title: Szybki Start — strumienie urządzeń z systemem Azure IoT Hub C szybki start dla protokołów SSH i RDP
description: W tym przewodniku szybki start uruchomisz przykładową aplikację w języku C, która działa jako serwer proxy, aby umożliwić obsługę scenariuszy SSH i RDP przez IoT Hub strumieni urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 2305a87b91160b5de90f4cbfbc9418adc50bb92a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624409"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki Start: Włączanie protokołu SSH i RDP przez strumień urządzenia IoT Hub przy użyciu aplikacji serwera proxy języka C (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Usługa Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Aby zapoznać się z omówieniem instalacji, zobacz [stronę Przykładowa lokalny serwer proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym przewodniku szybki start opisano konfigurowanie protokołu tunelowania Secure Shell (SSH) (przy użyciu portu 22) za pośrednictwem strumieni urządzeń. Konfiguracja dla ruchu Remote Desktop Protocol (RDP) jest podobna i wymaga prostej zmiany konfiguracji. Ponieważ strumienie urządzeń są typu Application-and Protocol-niezależny od, można zmodyfikować ten przewodnik Szybki Start, aby pomieścić inne typy ruchu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które zostały utworzone w następujących regionach:

  * Central US
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym [programowaniem pulpitu przy użyciu języka C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Jak to działa

Na poniższej ilustracji przedstawiono sposób, w jaki programy lokalnego serwera proxy dla urządzeń i usług umożliwiają kompleksową łączność między procesami klienta SSH i SSH. W publicznej wersji zapoznawczej zestaw SDK języka C obsługuje strumienie urządzeń tylko po stronie urządzenia. W efekcie w tym przewodniku szybki start przedstawiono instrukcje dotyczące uruchamiania tylko aplikacji lokalnego serwera proxy na urządzeniu. Aby skompilować i uruchomić towarzyszącą aplikację po stronie usług, postępuj zgodnie z instrukcjami w jednym z następujących przewodników szybki start:

* [Strumienie urządzeń SSH/RDP przez IoT Hub przy użyciu serwera proxy C#](./quickstart-device-streams-proxy-csharp.md)
* [Strumienie urządzeń SSH/RDP przez IoT Hub przy użyciu serwera proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Konfiguracja lokalnego serwera proxy](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Lokalny serwer proxy usługi łączy się z Centrum IoT i uruchamia strumień urządzenia do urządzenia docelowego.

2. Lokalny serwer proxy urządzenia wykonuje uzgadnianie inicjacji strumienia i ustanawia kompleksowy tunel przesyłania strumieniowego za pomocą punktu końcowego przesyłania strumieniowego usługi IoT Hub do strony usług.

3. Lokalny serwer proxy urządzenia nawiązuje połączenie z demonem SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji lokalnego serwera proxy".

4. Lokalny serwer proxy usługi czeka na nowe połączenia SSH od użytkownika, nasłuchuje na określonym porcie, w tym przypadku jest to port 2222. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji lokalnego serwera proxy". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między programami klienta SSH i serwera.

> [!NOTE]
> Ruch SSH przesyłany za pośrednictwem strumienia urządzenia jest tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT Hub zamiast bezpośredniego przesyłania między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści wynikające z używania strumieni urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md#benefits). Na rysunku przedstawiono również demona SSH, która jest uruchomiona na tym samym urządzeniu (lub komputerze) co lokalny serwer proxy urządzenia. W tym przewodniku szybki start adres IP demona SSH umożliwia również uruchamianie lokalnego serwera proxy na urządzeniu i demona na różnych komputerach.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start użyjesz [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotuj środowisko programistyczne służące do klonowania i kompilowania [zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki Start.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    *Przed* rozpoczęciem instalacji CMAKE należy pamiętać, że wymagania wstępne programu Visual Studio (Visual Studio i *Programowanie aplikacji klasycznych w języku C++* ) są zainstalowane na komputerze. Po sprawdzeniu wymagań wstępnych i usunięciu można zainstalować system kompilacji CMake.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować repozytorium GitHub [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ta operacja może potrwać kilka minut.

1. Utwórz podkatalog *CMAKE* w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia w katalogu *Azure-IoT-SDK-c* :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z katalogu *CMAKE* , aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W systemie Windows uruchom następujące polecenia w wiersz polecenia dla deweloperów dla programu Visual Studio 2015 lub 2017. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu *CMAKE* .

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) , aby zarejestrować symulowane urządzenie.

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
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

Ta sekcja umożliwia ustanowienie kompleksowego strumienia do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy **iothub_client_c2d_streaming_proxy_sample. c** w folderze `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` i podaj parametry połączenia urządzenia, adres IP/nazwa hosta urządzenia docelowego i port SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompiluj przykład:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Uruchom skompilowany program w urządzeniu:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Zgodnie z opisem w sekcji "jak działa", ustanowienie kompleksowego strumienia do tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (zarówno usługi, jak i urządzenia). W publicznej wersji zapoznawczej zestaw SDK IoT Hub C obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby skompilować i uruchomić lokalny serwer proxy usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników szybki start:

   * [Strumienie urządzeń SSH/RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md)
   * [Strumienie urządzeń SSH/RDP przez IoT Hub przy użyciu Node.js aplikacji serwera proxy](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu obu lokalnych serwerów proxy urządzenia i usługi należy użyć programu klienta SSH i nawiązać połączenie z lokalnym serwerem proxy usługi na porcie 2222 (zamiast demona SSH bezpośrednio).

```cmd/sh
ssh {username}@localhost -p 2222
```

W tym momencie w oknie Logowanie SSH zostanie wyświetlony komunikat z prośbą o wprowadzenie poświadczeń.

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli na lokalnym serwerze proxy urządzenia, który łączy się z demonem SSH w `IP_address:22` :

![Dane wyjściowe lokalnego serwera proxy urządzenia](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli programu klienta SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na którym nasłuchuje lokalny serwer proxy usługi:

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz usługę IoT Hub, zarejestrowano urządzenie, wdrożono program lokalny dla urządzenia i usługi serwera proxy w celu nawiązania strumieniowego urządzenia za pośrednictwem IoT Hub i użył serwerów proxy do tunelowania ruchu SSH.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
