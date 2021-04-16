---
title: Szybki start — strumienie Azure IoT Hub C — Szybki start dla protokołu SSH i RDP
description: W tym przewodniku Szybki start uruchamiasz przykładową aplikację języka C, która działa jako serwer proxy, aby włączyć scenariusze SSH i RDP IoT Hub strumieni urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c9c09914e30bdeae809924bc4256c773f42abd52
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479943"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i RDP przez strumień IoT Hub przy użyciu aplikacji serwera proxy języka C (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub obecnie obsługuje strumienie urządzeń jako funkcję w [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Omówienie konfiguracji można znaleźć na stronie Przykład [lokalnego serwera proxy.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

W tym przewodniku Szybki start opisano konfigurację tunelowania Secure Shell (SSH) (przy użyciu portu 22) przez strumienie urządzeń. Konfiguracja ruchu Remote Desktop Protocol (RDP) jest podobna i wymaga prostej zmiany konfiguracji. Strumienie urządzeń są niezależne od aplikacji i protokołów, dlatego możesz zmodyfikować ten przewodnik Szybki start, aby uwzględnić inne typy ruchu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Wersja zapoznawcza strumieni urządzeń jest obecnie obsługiwana tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Central US
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Zainstaluj [Visual Studio 2019 z włączonym](https://www.visualstudio.com/vs/) [pakietem roboczego Tworzenie aplikacji klasycznych w](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) języku C++.
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Jak to działa

Na poniższej ilustracji pokazano, w jaki sposób programy serwera proxy urządzenia i usługi lokalnej umożliwiają łączność typu end-to-end między klientem SSH i procesami demona SSH. W publicznej wersji zapoznawczej zestaw SDK języka C obsługuje strumienie urządzeń tylko po stronie urządzenia. W związku z tym ten przewodnik Szybki start zawiera instrukcje uruchamiania tylko aplikacji serwera proxy urządzenia lokalnego. Aby skompilować i uruchomić towarzyszącej aplikacji po stronie usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

* [Połączenia SSH/RDP za pośrednictwem IoT Hub strumieni urządzeń przy użyciu serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP za pośrednictwem IoT Hub strumieni urządzeń przy użyciu serwera proxy NodeJS.](./quickstart-device-streams-proxy-nodejs.md)

![Konfiguracja lokalnego serwera proxy](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Serwer proxy usługi lokalnej łączy się z centrum IoT i uruchamia strumień urządzenia do urządzenia docelowego.

2. Serwer proxy urządzenia lokalnego kończy proces inicjowania strumienia i ustanawia tunel przesyłania strumieniowego typu end-to-end przez punkt końcowy przesyłania strumieniowego centrum IoT po stronie usługi.

3. Serwer proxy urządzenia lokalnego łączy się z demonem SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji serwera proxy urządzenia lokalnego".

4. Serwer proxy usługi lokalnej czeka na nowe połączenia SSH od użytkownika, nasłuchując na wyznaczonym porcie— w tym przypadku jest to port 2222. To ustawienie można skonfigurować zgodnie z opisem w sekcji "Uruchamianie aplikacji serwera proxy urządzenia lokalnego". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między programami klienta i serwera SSH.

> [!NOTE]
> Ruch SSH wysyłany przez strumień urządzenia jest tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego centrum IoT, a nie wysyłany bezpośrednio między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści wynikające z używania strumieni urządzeń usługi IoT Hub.](iot-hub-device-streams-overview.md#benefits) Ponadto na rysunku przedstawiono demona SSH uruchomionego na tym samym urządzeniu (lub maszynie) co serwer proxy urządzenia lokalnego. W tym przewodniku Szybki start podanie adresu IP demona SSH umożliwia uruchamianie serwera proxy urządzenia lokalnego i demona również na różnych maszynach.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start użyjemy zestawu [SDK urządzenia usługi Azure IoT dla języka C.](iot-hub-device-sdk-c-intro.md) Przygotowujesz środowisko deweloperskie używane do klonowania i kompilowania zestawu [SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w usłudze GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start.

1. Pobierz system [kompilacji CMake.](https://cmake.org/download/)

    Przed rozpoczęciem instalacji programu CMake należy Visual Studio zainstalować na komputerze wymagania wstępne programu Visual Studio (tworzenie  aplikacji klasycznych przy użyciu pakietu roboczego *C++).* Po sprawdzeniu wymagań wstępnych i zweryfikowaniu pobierania możesz zainstalować system kompilacji CMake.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować repozytorium GitHub zestawu SDK języka C usługi [Azure IoT:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ta operacja powinna potrwać kilka minut.

1. Utwórz *podkatalog cmake* w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia z katalogu *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z katalogu *cmake,* aby utworzyć wersję zestawu SDK specyficzną dla platformy klienta dewelopera.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W systemie Windows uruchom następujące polecenia w wiersz polecenia dla deweloperów for Visual Studio 2015 lub 2017. W Visual Studio *cmake* zostanie wygenerowane rozwiązanie dla urządzenia symulowanego.

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

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell z rozszerzeniem [IoT,](/cli/azure/ext/azure-iot/iot) aby zarejestrować urządzenie symulowane.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT Hub.
   > * W przypadku nazwy rejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice w* pokazany sposób. Jeśli wybierzesz inną nazwę urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametrów połączenia urządzenia* dla urządzenia, które zostało właśnie zarejestrowane, uruchom następujące polecenia w Cloud Shell:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócone ciągi połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji zostanie ustanawiany strumień typu end-to-end do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy **iothub_client_c2d_streaming_proxy_sample.c** w folderze i podaj ciąg połączenia urządzenia, adres IP/nazwę hosta urządzenia docelowego oraz `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` port SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Skompiluj przykład:

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

Zgodnie z omówieniem w sekcji "Jak to działa" ustanowienie strumienia typu end-to-end w celu tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (zarówno po stronie usługi, jak i urządzenia). W publicznej wersji zapoznawczej zestaw SDK języka C IoT Hub obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby skompilować i uruchomić serwer proxy usługi lokalnej, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

   * [Połączenia SSH/RDP za pośrednictwem IoT Hub strumieni urządzeń przy użyciu aplikacji serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md)
   * [Połączenia SSH/RDP za pośrednictwem strumieni IoT Hub urządzeń przy użyciu Node.js proxy](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu zarówno serwerów proxy urządzenia, jak i lokalnego serwera proxy usługi użyj programu klienckiego SSH i połącz się z serwerem proxy usługi lokalnej na porcie 2222 (zamiast bezpośrednio z demonem SSH).

```cmd/sh
ssh {username}@localhost -p 2222
```

W tym momencie w oknie logowania SSH zostanie wyświetlony monit o wprowadzenie poświadczeń.

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli na serwerze proxy urządzenia lokalnego, który łączy się z demonem SSH w lokalizacji `IP_address:22` :

![Dane wyjściowe serwera proxy urządzenia lokalnego](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli programu klienckiego SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na którym nasłuchuje serwer proxy usługi lokalnej:

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, wdrożysz program serwera proxy urządzenia i usługi lokalnej w celu ustanowienia strumienia urządzenia za pośrednictwem usługi IoT Hub oraz za pomocą serwerów proxy tuneluje ruch SSH.

Aby dowiedzieć się więcej na temat strumieni urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
