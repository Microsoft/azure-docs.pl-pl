---
title: Korzystanie z różnych mechanizmów zaświadczania za pomocą zestawu SDK klienta IoT Hub Device Provisioning Service platformy Azure
description: Instrukcje dotyczące platformy Azure — jak korzystać z różnych mechanizmów zaświadczania z zestawem SDK klienta usługi Device Provisioning Service (DPS) na platformie Azure
author: wesmc7777
ms.author: wesmc
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
ms.openlocfilehash: 0a32e2f055b2914fa0008e043e80092ac2da0814
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531512"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Instrukcje dotyczące korzystania z różnych mechanizmów zaświadczania za pomocą zestawu SDK klienta usługi Device Provisioning Service dla języka C

W tym artykule przedstawiono sposób korzystania z różnych [mechanizmów zaświadczania](concepts-service.md#attestation-mechanism) przy użyciu zestawu SDK klienta usługi Device Provisioning Service dla języka C. Istnieje możliwość użycia urządzenia fizycznego lub symulatora. Usługa aprowizowania obsługuje uwierzytelnianie dwóch typów mechanizmów zaświadczania: X.509 i modułu TPM.

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko deweloperskie zgodnie z sekcją „Prepare the development environment” (Przygotowywanie środowiska deweloperskiego) w przewodniku[Create and provision simulated device (Tworzenie i aprowizowanie symulowanego urządzenia)](./quick-create-simulated-device.md).

### <a name="choose-an-attestation-mechanism"></a>Wybieranie mechanizmu zaświadczania

Jako producent urządzenia musisz najpierw wybrać mechanizm zaświadczania oparty na jednym z obsługiwanych typów. Aktualnie [zestaw SDK klienta usługi Device Provisioning Service dla języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) oferuje obsługę następujących mechanizmów zaświadczania: 

- [Moduł TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM to standard ustanowiony dla większości platform urządzeń z systemem Windows, a także dla kilku urządzeń z systemem Linux/Ubuntu. Jako producent urządzenia możesz wybrać ten mechanizm zaświadczania, jeśli na urządzeniach jest uruchomiony jeden z tych systemów operacyjnych i szukasz ustalonego standardu. Używając mikroukładów modułów TPM, możesz tylko rejestrować każde urządzenie indywidualnie w usłudze Device Provisioning. Do celów programistycznych można użyć symulatora modułu TPM na maszynie deweloperskiej z systemem Windows lub Linux.

- [X.509](https://cryptography.io/en/latest/x509/): certyfikaty X.509 można przechowywać w stosunkowo nowszych mikroukładach nazywanych [sprzętowymi modułami zabezpieczeń (HSM)](concepts-service.md#hardware-security-module). W firmie Microsoft trwa również praca nad mikroukładami RIoT i DICE, które implementują certyfikaty X.509. W przypadku mikroukładów X.509 można przeprowadzić zbiorczą rejestrację w portalu. Obsługuje ona również niektóre systemy operacyjne inne niż Windows, takie jak embedOS. Dla celów programistycznych zestaw SDK klienta usługi Device Provisioning obsługuje symulator urządzeń X.509. 

Aby uzyskać więcej informacji, zobacz IoT Hub Device Provisioning Service [mechanizmów zaświadczania](concepts-service.md#attestation-mechanism).

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Włączanie uwierzytelniania dla obsługiwanych mechanizmów zaświadczania

Aby można było zarejestrować urządzenie fizyczne lub symulator w witrynie Azure Portal, należy dla niego włączyć tryb uwierzytelniania zestawu SDK (X.509 lub Moduł TPM). Przejdź do folderu głównego elementu azure-iot-sdk-c. Następnie uruchom odpowiednie polecenie, w zależności od wybranego trybu uwierzytelniania:

### <a name="use-x509-with-simulator"></a>Używanie trybu X.509 z symulatorem

Usługa aprowizacji jest dostarczana z emulatorem aparatu kompozycji tożsamości urządzenia, który generuje certyfikat **X. 509** na potrzeby uwierzytelniania urządzenia. Aby włączyć uwierzytelnianie **X. 509** , uruchom następujące polecenie: 

```
cmake -Ddps_auth_type=x509 ..
```

Informacje na temat sprzętu z aparatem DICE można znaleźć [tutaj](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Używanie trybu X.509 ze sprzętem

Usługa aprowizacji może być używana z opcją **X. 509** na innym sprzęcie. Do ustanowienia połączenia niezbędny jest interfejs między sprzętem i zestawem SDK. Skontaktuj się z producentem modułu HSM, aby uzyskać informacje na temat interfejsu.

### <a name="use-tpm"></a>Używanie modułu TPM

Usługę aprowizowania można połączyć z mikroukładami modułu TPM sprzętu z systemem Windows i Linux przy użyciu tokenu sygnatury dostępu współdzielonego. Uruchom następujące polecenie, aby włączyć uwierzytelnianie przy użyciu modułu TPM:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Używanie modułu TPM z symulatorem

Jeśli nie masz urządzenia z mikroukładami modułu TPM, możesz użyć symulatora w celach deweloperskich w systemie operacyjnym Windows. Aby włączyć uwierzytelnianie przy użyciu modułu TPM i uruchomić symulator modułu TPM, uruchom następujące polecenie:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Kompilowanie zestawu SDK
Skompiluj zestaw SDK przed utworzeniem rejestracji urządzeń.

### <a name="linux"></a>Linux
- Aby skompilować zestaw SDK w systemie Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Aby utworzyć pliki binarne debugowania, dodaj odpowiednią opcję CMake do polecenia generowania projektu, na przykład:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Istnieje wiele [opcji konfiguracji CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) dostępnych podczas tworzenia zestawu SDK. Można na przykład wyłączyć jeden z dostępnych stosów protokołu, dodając argument do polecenia generowania projektu CMake:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Można również skompilować i uruchomić test jednostkowy:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Aby skompilować zestaw SDK w systemie Windows, wykonaj następujące kroki w celu wygenerowania plików projektu:
  - Otwórz „Wiersz polecenia dla deweloperów w programie VS2015”
  - Uruchom następujące polecenia CMake w folderze głównym repozytorium:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    To polecenie powoduje kompilowanie bibliotek x86. Aby kompilować dla wersji x64, zmodyfikuj argument generatora cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Jeśli generowanie projektu zostanie ukończone pomyślnie, w folderze `cmake` powinien pojawić się plik rozwiązania programu Visual Studio (sln). Aby skompilować zestaw SDK:
    - Otwórz plik **cmake\azure_iot_sdks.sln** w programie Visual Studio i skompiluj go **LUB**
    - Uruchom następujące polecenie w wierszu polecenia używanym do generowania plików projektu:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Aby utworzyć pliki binarne debugowania, użyj odpowiedniego argumentu platformy MSBuild: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Istnieje wiele opcji konfiguracji CMake dostępnych podczas tworzenia zestawu SDK. Można na przykład wyłączyć jeden z dostępnych stosów protokołu, dodając argument do polecenia generowania projektu CMake:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Można również tworzyć i uruchamiać testy jednostkowe:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Biblioteki do uwzględnienia
- Następujące biblioteki powinny zostać uwzględnione w zestawie SDK:
    - Usługa aprowizowania: dps_http_transport, dps_client, dps_security_client
    - Zabezpieczenia IoTHub: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Tworzenie wpisu rejestracji urządzenia w usłudze Device Provisioning

### <a name="tpm"></a>Moduł TPM
Jeśli korzystasz z modułu TPM, postępuj zgodnie z instrukcjami z sekcji [„Create and provision a simulated device using IoT Hub Device Provisioning Service” (Tworzenie i aprowizowanie symulowanego urządzenia za pomocą usługi IoT Hub Device Provisioning)](./quick-create-simulated-device.md), aby utworzyć wpis rejestracji urządzenia w usłudze Device Provisioning i symulować pierwsze uruchomienie.

### <a name="x509"></a>X.509

1. Aby zarejestrować urządzenie w usłudze aprowizowania, należy zanotować klucz poręczenia i identyfikator rejestracji każdego urządzenia. Te informacje są wyświetlane w narzędziu aprowizowania dostępnym w zestawie SDK klienta. Uruchom następujące polecenie, aby wydrukować certyfikat głównego urzędu certyfikacji (dla grup rejestracji) i certyfikat liścia (dla indywidualnej rejestracji):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Zaloguj się w witrynie Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoją usługę Device Provisioning.
   - **Rejestracja indywidualna X. 509**: w bloku podsumowania usługi aprowizacji wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **indywidualne rejestracje** i kliknij u góry przycisk **Dodaj** . Wybierz pozycję **X. 509** jako *mechanizm*zaświadczania tożsamości, Przekaż certyfikat liścia zgodnie z wymaganiami bloku. Gdy skończysz, kliknij przycisk **Zapisz**. 
   - **Rejestracja grupy X. 509**: w bloku podsumowania usługi aprowizacji wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Grupowe rejestracje** i kliknij u góry przycisk **Dodaj**. Wybierz pozycję **X. 509** jako *mechanizm*zaświadczania tożsamości, wprowadź nazwę grupy i nazwę certyfikacji, Przekaż certyfikat urzędu certyfikacji/pośredniego zgodnie z wymaganiami bloku. Gdy skończysz, kliknij przycisk **Zapisz**. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Włączanie uwierzytelniania urządzeń przy użyciu niestandardowego mechanizmu zaświadczania (opcjonalnie)

> [!NOTE]
> Ta sekcja dotyczy tylko urządzeń wymagających obsługi niestandardowej platformy lub niestandardowych mechanizmów zaświadczania, które nie są aktualnie obsługiwane przez zestaw SDK klienta usługi Device Provisioning Service dla języka C. Należy również zauważyć, że w ramach zestawu SDK skrót „HSM” jest często używany jako ogólny zamiennik pojęcia „mechanizm zaświadczania”.

Najpierw należy utworzyć repozytorium i bibliotekę na potrzeby niestandardowego mechanizmu zaświadczania:

1. Opracuj bibliotekę, aby uzyskać dostęp do mechanizmu zaświadczania. Ten projekt wymaga utworzenia biblioteki statycznej do użycia przez zestaw Device Provisioning SDK.

2. Zaimplementuj funkcje zdefiniowane w poniższym pliku nagłówka w bibliotece: 

    - W przypadku niestandardowego modułu TPM zaimplementuj funkcje zdefiniowane w obszarze [interfejsu API TPM modułu HSM](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - W przypadku niestandardowego certyfikatu X.509 zaimplementuj funkcje zdefiniowane w obszarze [interfejsu API X509 modułu HSM](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Gdy biblioteka zostanie pomyślnie skompilowana, trzeba będzie ją zintegrować z zestawem SDK klienta usługi Device Provisioning Service przez skonsolidowanie z biblioteką. :

1. Określ niestandardowe repozytorium GitHub i bibliotekę w poleceniu `cmake`:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Otwórz plik rozwiązania Visual Studio skompilowany przez narzędzie CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) i skompiluj je. 

    - Ten proces polega na skompilowaniu biblioteki zestawu SDK.
    - Zestaw SDK spróbuje wykonać konsolidację z niestandardową biblioteką zdefiniowaną w poleceniu `cmake`.

3. Aby zweryfikować, czy niestandardowy mechanizm zaświadczania zaimplementowano poprawnie, uruchom przykładową aplikację „prov_dev_client_ll_sample” w obszarze „Provision_Samples” (`\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`).

## <a name="connecting-to-iot-hub-after-provisioning"></a>Łączenie z usługą IoT Hub po zakończeniu aprowizacji

Po zainicjowaniu obsługi administracyjnej urządzenia przy użyciu usługi aprowizacji ten interfejs API używa określonego trybu uwierzytelniania (**X. 509** lub modułu TPM) do łączenia się z IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
