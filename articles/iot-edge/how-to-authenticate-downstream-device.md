---
title: Uwierzytelnianie urządzeń dalszych — Azure IoT Edge | Microsoft Docs
description: Jak uwierzytelniać urządzenia podrzędne lub urządzenia liścia w IoT Hub i przekierowyć ich połączenia za pośrednictwem Azure IoT Edge bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4702682dcd6af68242fd5a34d1fb2e0a9273da36
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482028"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

W przezroczystym scenariuszu bramy urządzenia podrzędne (nazywane czasami urządzeniami liścia lub urządzeniami podrzędnymi) potrzebują tożsamości w IoT Hub jak każde inne urządzenie. W tym artykule opisano opcje uwierzytelniania urządzenia podrzędnego w IoT Hub, a następnie pokazano, jak zadeklarować połączenie bramy.

Istnieją trzy ogólne kroki służące do skonfigurowania pomyślnego połączenia bramy przezroczystej. Ten artykuł obejmuje drugi krok:

1. Skonfiguruj urządzenie bramy jako serwer, aby urządzenia podrzędne łączyły się z nim w bezpieczny sposób. Skonfiguruj bramę do odbierania komunikatów z urządzeń dalszych i przekierowywuj je do odpowiedniego miejsca docelowego. Aby uzyskać te instrukcje, zobacz Configure an IoT Edge device to act as a transparent gateway (Konfigurowanie urządzenia IoT Edge [jako przezroczystej bramy).](how-to-create-transparent-gateway.md)
2. **Utwórz tożsamość urządzenia dla urządzenia podrzędnego, aby można je było uwierzytelnić za pomocą IoT Hub. Skonfiguruj urządzenie podrzędne do wysyłania komunikatów za pośrednictwem urządzenia bramy.**
3. Połącz urządzenie podrzędne z urządzeniem bramy i rozpocznij wysyłanie komunikatów. Aby uzyskać te instrukcje, zobacz Connect a downstream device to an Azure IoT Edge gateway (Łączenie urządzenia podrzędnego [z bramą Azure IoT Edge wirtualnej).](how-to-connect-downstream-device.md)

Urządzenia podrzędne mogą uwierzytelniać się za pomocą usługi IoT Hub przy użyciu jednej z trzech metod: kluczy symetrycznych (czasami nazywanych kluczami dostępu współdzielonego), certyfikatów Z podpisem własnym X.509 lub certyfikatów podpisanych przez urząd certyfikacji X.509. Kroki uwierzytelniania są podobne do kroków używanych do skonfigurowania dowolnego urządzenia spoza usługi IoT Edge za pomocą usługi IoT Hub, z niewielkimi różnicami w celu zadeklarowania relacji bramy.

Automatyczne aprowizowanie urządzeń nadrzędnych za pomocą Azure IoT Hub Device Provisioning Service (DPS) nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w teście Configure an IoT Edge device to act as a transparent gateway (Konfigurowanie urządzenia [IoT Edge, aby działało jako przezroczysta brama.](how-to-create-transparent-gateway.md)

Jeśli używasz uwierzytelniania X.509, wygeneruj certyfikaty dla urządzenia podrzędnego. Mieć ten sam certyfikat głównego urzędu certyfikacji i skrypt generujący certyfikat, który został użyty na użytek przezroczystego artykułu bramy, dostępny do użycia ponownie.

W tym artykule odwołuje się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w **parametrze nazwy hosta** pliku konfiguracji na IoT Edge bramy. Jest on określany w ciągu połączenia urządzenia podrzędnego. Nazwa hosta bramy musi być rozpoznawana jako adres IP przy użyciu systemu DNS lub wpisu pliku hosta na urządzeniu nadrzędnym.

## <a name="register-device-with-iot-hub"></a>Rejestrowanie urządzenia przy użyciu IoT Hub

Wybierz sposób uwierzytelniania urządzenia podrzędnego za pomocą IoT Hub:

* [Uwierzytelnianie za pomocą](#symmetric-key-authentication)klucza IoT Hub tworzy klucz, który należy umieścić na urządzeniu nadrzędnym. Podczas uwierzytelniania urządzenia IoT Hub, czy dwa klucze są zgodne. Nie musisz tworzyć dodatkowych certyfikatów, aby używać uwierzytelniania za pomocą klucza symetrycznego.

  Ta metoda jest szybsza w przypadku testowania bram w scenariuszu dewelopera lub testowego.

* Uwierzytelnianie Z podpisem własnym [X.509:](#x509-self-signed-authentication)czasami nazywane uwierzytelnianiem odcisku palca, ponieważ odcisk palca z certyfikatu X.509 urządzenia jest współużytowany przez IoT Hub.

  Uwierzytelnianie certyfikatu jest zalecane w przypadku urządzeń w scenariuszach produkcyjnych.

* [Uwierzytelnianie z podpisem X.509 urzędu certyfikacji:](#x509-ca-signed-authentication)przekaż certyfikat głównego urzędu certyfikacji do IoT Hub. Gdy urządzenia prezentują swój certyfikat X.509 do uwierzytelniania, IoT Hub sprawdza, czy należy on do łańcucha zaufania podpisanego przez ten sam certyfikat głównego urzędu certyfikacji.

  Uwierzytelnianie certyfikatu jest zalecane w przypadku urządzeń w scenariuszach produkcyjnych.

### <a name="symmetric-key-authentication"></a>Uwierzytelnianie za pomocą klucza zawartości

Uwierzytelnianie za pomocą klucza symetrycznego lub uwierzytelnianie klucza dostępu współdzielonego to najprostszy sposób uwierzytelniania za pomocą IoT Hub. W przypadku uwierzytelniania za pomocą klucza symetrycznego klucz base64 jest skojarzony z identyfikatorem urządzenia IoT w IoT Hub. Ten klucz należy uwzględnić w aplikacjach IoT, aby urządzenie było w stanie go przedstawić podczas połączenia z IoT Hub.

Dodaj nowe urządzenie IoT w centrum IoT hub przy użyciu interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla Visual Studio Code. Należy pamiętać, że urządzenia podrzędne muszą być identyfikowane w IoT Hub jako zwykłe urządzenia IoT, a nie IoT Edge urządzeń.

Podczas tworzenia nowej tożsamości urządzenia podaj następujące informacje:

* Utwórz identyfikator urządzenia.

* Wybierz **pozycję Klucz symetryczny** jako typ uwierzytelniania.

* Wybierz **pozycję Ustaw urządzenie nadrzędne** i wybierz IoT Edge bramy, za pośrednictwem których będzie nawiązywane połączenie to urządzenie podrzędne. Zawsze można później zmienić element nadrzędny.

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem klucza symetrycznego w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Ustawienie urządzenia nadrzędnego było krokiem opcjonalnym dla urządzeń dalszych, które korzystają z uwierzytelniania za pomocą klucza symetrycznego. Jednak począwszy od IoT Edge 1.1.0 każde urządzenie podrzędne musi być przypisane do urządzenia nadrzędnego.
   >
   >Możesz skonfigurować centrum IoT Edge, aby wracało do poprzedniego zachowania, ustawiając zmienną środowiskową **AuthenticationMode** na wartość **CloudAndScope**.

Możesz również użyć rozszerzenia [IoT dla interfejsu wiersza polecenia platformy Azure,](https://github.com/Azure/azure-iot-cli-extension) aby wykonać tę samą operację. W poniższym przykładzie użyto [polecenia az iot hub device-identity,](/cli/azure/iot/hub/device-identity) aby utworzyć nowe urządzenie IoT z uwierzytelnianiem za pomocą klucza symetrycznego i przypisać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Następnie pobierz [i zmodyfikuj ciąg połączenia,](#retrieve-and-modify-connection-string) tak aby urządzenie łączyło się za pośrednictwem bramy.

### <a name="x509-self-signed-authentication"></a>Uwierzytelnianie Z podpisem własnym X.509

W przypadku uwierzytelniania z podpisem własnym X.509, czasami nazywanego uwierzytelnianiem odciskiem palca, należy utworzyć certyfikaty, które mają być umieszczane na urządzeniu nadrzędnym. Te certyfikaty mają odcisk palca, który udostępniasz innym użytkownikom IoT Hub do uwierzytelniania.

1. Przy użyciu certyfikatu urzędu certyfikacji utwórz dwa certyfikaty urządzenia (podstawowy i pomocniczy) dla urządzenia podrzędnego.

   Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X.509, możesz użyć skryptów certyfikatów demonstracyjnych usługi IoT Edge w celu utworzenia certyfikatów urządzenia [podrzędnego.](how-to-create-test-certificates.md#create-downstream-device-certificates) Wykonaj kroki tworzenia certyfikatów z podpisem własnym. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

   Jeśli tworzysz własne certyfikaty, upewnij się, że nazwa podmiotu certyfikatu urządzenia jest ustawiona na identyfikator urządzenia, który jest Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

2. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w interfejsie IoT Hub) z każdego certyfikatu, który jest 40 szesnastkowym ciągiem znaków. Użyj następującego polecenia openssl, aby wyświetlić certyfikat i znaleźć odcisk palca:

   * W systemie Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * W systemie Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Uruchom to polecenie dwa razy, raz dla certyfikatu podstawowego i raz dla certyfikatu pomocniczego. Odciski palców dla obu certyfikatów są zapewniane podczas rejestrowania nowego urządzenia IoT przy użyciu certyfikatów X.509 z podpisem własnym.

3. Przejdź do centrum IoT Hub w Azure Portal i utwórz nową tożsamość urządzenia IoT z następującymi wartościami:

   * Podaj **identyfikator urządzenia,** który odpowiada nazwie podmiotu certyfikatów urządzenia.
   * Wybierz **typ uwierzytelniania X.509 z** podpisem własnym.
   * Wklej ciągi szesnastkowe skopiowane z certyfikatów podstawowych i pomocniczych urządzenia.
   * Wybierz **pozycję Ustaw urządzenie nadrzędne** i wybierz IoT Edge bramy, za pośrednictwem których to urządzenie podrzędne będzie nawiązywać połączenie. Zawsze można później zmienić element nadrzędny.

   ![Tworzenie identyfikatora urządzenia przy użyciu uwierzytelniania Z podpisem własnym X.509 w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Skopiuj zarówno certyfikaty urządzenia podstawowego, jak i pomocniczego oraz ich klucze do dowolnej lokalizacji na urządzeniu nadrzędnym. Przenieś również kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzenia podrzędnego.

   Będziesz odwoływać się do tych plików certyfikatów we wszystkich aplikacjach na urządzeniu nadrzędnym, które łączą się z IoT Hub. Aby przenieść pliki certyfikatów, [Azure Key Vault](../key-vault/index.yml) użyć usługi, na przykład protokołu bezpiecznego kopiowania. [](https://www.ssh.com/ssh/scp/)

5. W zależności od preferowanego języka przejrzyj przykłady dotyczące sposobu, w jaki można odwoływać się do certyfikatów X.509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz również użyć rozszerzenia [IoT dla](https://github.com/Azure/azure-iot-cli-extension) interfejsu wiersza polecenia platformy Azure, aby wykonać tę samą operację tworzenia urządzenia. W poniższym przykładzie użyto [polecenia az iot hub device-identity,](/cli/azure/iot/hub/device-identity) aby utworzyć nowe urządzenie IoT z uwierzytelnianiem z podpisem własnym X.509 i przypisać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Następnie pobierz [i zmodyfikuj ciąg połączenia,](#retrieve-and-modify-connection-string) aby urządzenie łączyło się za pośrednictwem bramy.

### <a name="x509-ca-signed-authentication"></a>Uwierzytelnianie z podpisem urzędu certyfikacji X.509

W przypadku uwierzytelniania podpisanego przez urząd certyfikacji (CA) X.509 potrzebny jest certyfikat głównego urzędu certyfikacji zarejestrowany w IoT Hub, który jest potrzebny do podpisywania certyfikatów dla urządzenia podrzędnego. Każde urządzenie korzystające z certyfikatu, które zostało wydane przez certyfikat głównego urzędu certyfikacji lub dowolny z jego certyfikatów pośrednich, będzie mieć uprawnienia do uwierzytelniania.

Ta sekcja jest oparta na instrukcjach szczegółowo IoT Hub artykule [Set up X.509 security in your Azure IoT hub (Konfigurowanie zabezpieczeń X.509](../iot-hub/iot-hub-security-x509-get-started.md)w centrum Azure IoT Hub).

1. Przy użyciu certyfikatu urzędu certyfikacji utwórz dwa certyfikaty urządzenia (podstawowy i pomocniczy) dla urządzenia podrzędnego.

   Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X.509, możesz użyć skryptów certyfikatów demonstracyjnych usługi IoT Edge, aby utworzyć certyfikaty urządzenia [podrzędnego.](how-to-create-test-certificates.md#create-downstream-device-certificates) Wykonaj kroki tworzenia certyfikatów podpisanych przez urząd certyfikacji. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

2. Postępuj zgodnie z instrukcjami w sekcji Register [X.509 CA certificates to your IoT hub (Rejestrowanie certyfikatów X.509](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) urzędu certyfikacji w centrum IoT Hub) artykułu *Set up X.509 security in your Azure IoT hub (Konfigurowanie zabezpieczeń X.509* w centrum Azure IoT Hub). W tej sekcji wykonasz następujące kroki:

   1. Przekaż certyfikat głównego urzędu certyfikacji. Jeśli używasz certyfikatów demonstracyjnych, główny urząd certyfikacji to **\<path> /certs/azure-iot-test-only.root.ca.cert.pem.**

   2. Sprawdź, czy jesteś właścicielem tego certyfikatu głównego urzędu certyfikacji.

3. Postępuj zgodnie z instrukcjami w sekcji Tworzenie urządzenia [X.509](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) dla centrum IoT w teście Konfigurowanie zabezpieczeń *X.509* w usłudze Azure IoT Hub. W tej sekcji wykonasz następujące kroki:

   1. Dodaj nowe urządzenie. Podaj małą nazwę identyfikatora urządzenia **i** wybierz typ uwierzytelniania **X.509 Urzędu certyfikacji** ze podpisem .

   2. Ustaw urządzenie nadrzędne. Wybierz **pozycję Ustaw urządzenie nadrzędne** i wybierz IoT Edge bramy, które zapewni połączenie z IoT Hub.

4. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który został IoT Hub do tego łańcucha. Użyj tego samego identyfikatora urządzenia z małymi literami, który został nadany tożsamości urządzenia w portalu.

5. Skopiuj certyfikat i klucze urządzenia do dowolnej lokalizacji na urządzeniu nadrzędnym. Przenieś również kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzenia podrzędnego.

   Będziesz odwoływać się do tych plików we wszystkich aplikacjach na urządzeniu nadrzędnym, które łączą się z IoT Hub. Aby przenieść pliki certyfikatów, [Azure Key Vault](../key-vault/index.yml) użyć usługi, na przykład protokołu bezpiecznego kopiowania. [](https://www.ssh.com/ssh/scp/)

6. W zależności od preferowanego języka przejrzyj przykłady dotyczące sposobu, w jaki można odwoływać się do certyfikatów X.509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz również użyć rozszerzenia [IoT dla](https://github.com/Azure/azure-iot-cli-extension) interfejsu wiersza polecenia platformy Azure, aby wykonać tę samą operację tworzenia urządzenia. W poniższym przykładzie użyto [polecenia az iot hub device-identity,](/cli/azure/iot/hub/device-identity) aby utworzyć nowe urządzenie IoT z uwierzytelnianiem podpisanym przez urząd certyfikacji X.509 i przypisać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Następnie pobierz [i zmodyfikuj ciąg połączenia,](#retrieve-and-modify-connection-string) aby urządzenie łączyło się za pośrednictwem bramy.

## <a name="retrieve-and-modify-connection-string"></a>Pobieranie i modyfikowanie parametrów połączenia

Po utworzeniu tożsamości urządzenia IoT w portalu można pobrać jego klucze podstawowe lub pomocnicze. Jeden z tych kluczy musi zostać uwzględniony w parametrów połączenia, których aplikacje używają do komunikowania się z IoT Hub. W przypadku uwierzytelniania za pomocą IoT Hub udostępnia w pełni formowane ciągi połączenia w szczegółach urządzenia dla Twojej wygody. Musisz dodać dodatkowe informacje o urządzeniu bramy do parametrów połączenia.

Parametry połączenia dla urządzeń nadrzędnych wymagają następujących składników:

* Centrum IoT, z które łączy się urządzenie: `Hostname={iothub name}.azure-devices.net`
* Identyfikator urządzenia zarejestrowany w centrum: `DeviceID={device ID}`
* Metoda uwierzytelniania, niezależnie od tego, czy jest to klucz symetryczny, czy certyfikaty X.509
  * W przypadku korzystania z uwierzytelniania za pomocą klucza symetrycznego podaj klucz podstawowy lub pomocniczy: `SharedAccessKey={key}`
  * W przypadku korzystania z uwierzytelniania certyfikatu X.509 podaj flagę: `x509=true`
* Urządzenie bramy, za pośrednictwem których łączy się urządzenie. Podaj **wartość nazwy hosta** z IoT Edge konfiguracji urządzenia bramy: `GatewayHostName={gateway hostname}`

Wszystkie razem pełne ciągi połączenia wyglądają następująco:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Lub:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Dzięki relacji nadrzędny/podrzędny można uprościć ciąg połączenia, wywołując bramę bezpośrednio jako hosta połączenia. Na przykład:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Tych zmodyfikowanych parametrów połączenia użyjesz w następnym artykule z serii Brama przezroczysta.

## <a name="next-steps"></a>Następne kroki

W tym momencie masz urządzenie IoT Edge zarejestrowane w centrum IoT i skonfigurowane jako przezroczysta brama. Masz również urządzenie podrzędne zarejestrowane w centrum IoT i wskazać jego urządzenie bramy.

Następnie należy skonfigurować urządzenie podrzędne tak, aby ufało urządzeniu bramy i nawiązywało z nim bezpieczne połączenie. Przejdź do następnego artykułu z serii przezroczystej bramy Connect a downstream device to an Azure IoT Edge gateway (Łączenie urządzenia podrzędnego [z Azure IoT Edge bramą).](how-to-connect-downstream-device.md)