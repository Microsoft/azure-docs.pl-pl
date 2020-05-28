---
title: Uwierzytelnianie urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Jak uwierzytelniać urządzenia podrzędne lub urządzenia typu liść w celu IoT Hub i kierować ich połączenia za poorednictwem urządzeń bram Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3edd29703f74c7671537fbcf08159dd830e5453c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726230"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub

W przypadku niejawnego scenariusza bramy urządzenia podrzędne (nazywane czasami urządzeniami typu liść lub urządzeniami podrzędnymi) potrzebują tożsamości w IoT Hub jak każde inne urządzenie. W tym artykule opisano opcje uwierzytelniania urządzenia podrzędnego w IoT Hub, a następnie pokazano, jak zadeklarować połączenie bramy.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono drugi krok:

1. Urządzenie bramy musi być w stanie bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikaty z urządzeń podrzędnych i kierować komunikaty do odpowiednich miejsc docelowych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md).
2. **Urządzenie podrzędne musi mieć tożsamość urządzenia, aby można było uwierzytelnić się przy użyciu IoT Hub i wiedzieć o komunikacji za pomocą swojego urządzenia bramy.**
3. Urządzenie podrzędne musi bezpiecznie połączyć się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

Urządzenia podrzędne mogą uwierzytelniać się za pomocą IoT Hub przy użyciu jednej z trzech metod: klucze symetryczne (czasami określane jako klucze dostępu współdzielonego), certyfikaty z podpisem własnym X. 509 lub certyfikaty z certyfikatem X. 509 (CA). Kroki uwierzytelniania są podobne do kroków użytych w celu skonfigurowania dowolnego urządzenia niezwiązanego z usługą IoT-Edge z IoT Hub z małymi różnicami w celu zadeklarować relacji bramy.

W krokach przedstawionych w tym artykule przedstawiono Ręczne inicjowanie obsługi urządzeń, a nie automatyczne Inicjowanie obsługi przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS). Inicjowanie obsługi administracyjnej urządzeń podrzędnych przy użyciu programu DPS nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [sekcji Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md). Jeśli używasz uwierzytelniania X. 509 dla urządzenia podrzędnego, musisz użyć tego samego skryptu generującego ten sam certyfikat, który został skonfigurowany w przezroczystym artykule bramy.

Ten artykuł odnosi się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w parametrze **hostname** pliku config. YAML na urządzeniu bramy IoT Edge. Jest on określany w parametrach połączenia urządzenia podrzędnego. Nazwa hosta bramy musi być rozpoznawalna na adres IP przy użyciu systemu DNS lub wpisu pliku hosta.

## <a name="register-device-symmetric-key"></a>Zarejestruj urządzenie (klucz symetryczny)

Uwierzytelnianie przy użyciu klucza symetrycznego lub uwierzytelnianie klucza dostępu współdzielonego jest najprostszym sposobem na uwierzytelnianie za pomocą IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego klucz Base64 jest skojarzony z IDENTYFIKATORem urządzenia IoT w IoT Hub. Ten klucz jest dołączany do aplikacji IoT, aby urządzenie mógł je przedstawić podczas łączenia się z IoT Hub.

### <a name="create-the-device-identity"></a>Tworzenie tożsamości urządzenia

Dodaj nowe urządzenie IoT w centrum IoT Hub przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla Visual Studio Code. Należy pamiętać, że urządzenia podrzędne muszą być identyfikowane w IoT Hub jako regularne urządzenia IoT, a nie IoT Edge urządzeń.

Podczas tworzenia nowej tożsamości urządzenia podaj następujące informacje:

* Utwórz identyfikator urządzenia.

* Wybierz **klucz symetryczny** jako typ uwierzytelniania.

* Opcjonalnie wybierz **ustawienie urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Ten krok jest opcjonalny w przypadku uwierzytelniania przy użyciu klucza symetrycznego, ale jest zalecane, ponieważ ustawienie urządzenia nadrzędnego umożliwia korzystanie z [funkcji offline](offline-capabilities.md) dla urządzenia podrzędnego. Możesz zawsze aktualizować szczegóły urządzenia, aby później dodać lub zmienić element nadrzędny.

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Aby ukończyć tę samą operację, można użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) . Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem przy użyciu klucza symetrycznego i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure służących do tworzenia i zarządzania urządzeniami nadrzędnymi/podrzędnymi, zobacz zawartość referencyjną polecenia [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) .


Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

## <a name="register-device-x509-self-signed"></a>Rejestrowanie urządzenia (z podpisem własnym X. 509)

W przypadku uwierzytelniania z podpisem własnym X. 509, czasami określanego jako Uwierzytelnianie odciskiem palca, należy utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT. Te certyfikaty mają odcisk palca, które udostępniają IoT Hub na potrzeby uwierzytelniania.

Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X. 509, możesz [utworzyć certyfikaty demonstracyjne do testowania funkcji IoT Edge urządzeń](how-to-create-test-certificates.md). Podczas generowania certyfikatów testów dla urządzenia podrzędnego Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

1. Przy użyciu certyfikatu urzędu certyfikacji Utwórz dwa certyfikaty urządzeń (podstawowe i pomocnicze) dla urządzenia podrzędnego.

   Certyfikat urządzenia musi mieć ustawioną nazwę podmiotu na identyfikator urządzenia, który będzie używany podczas rejestrowania urządzenia IoT w usłudze Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

2. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w interfejsie IoT Hub) z każdego certyfikatu, który jest 40m ciągiem znaków w formacie szesnastkowym. Użyj następującego polecenia OpenSSL, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Uruchom to polecenie dwukrotnie, raz dla certyfikatu podstawowego i jeden raz dla certyfikatu pomocniczego. Podczas rejestrowania nowego urządzenia IoT za pomocą certyfikatów X. 509 z podpisem własnym można podawać odciski palców dla obu certyfikatów.

3. Przejdź do centrum IoT Hub w Azure Portal i Utwórz nową tożsamość urządzenia IoT o następujących wartościach:

   * Podaj **Identyfikator urządzenia** , który jest zgodny z nazwą podmiotu certyfikatów urządzeń.
   * Wybierz pozycję **X. 509 z podpisem własnym** jako typ uwierzytelniania.
   * Wklej ciągi szesnastkowe skopiowane z podstawowych i pomocniczych certyfikatów urządzenia.
   * Wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Urządzenie nadrzędne jest wymagane do uwierzytelniania X. 509 urządzenia podrzędnego.

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem z podpisem własnym X. 509 w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Skopiuj certyfikat i klucze urządzenia do dowolnej lokalizacji na urządzeniu podrzędnym. Należy również przenieść kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Te pliki będą odwoływać się do tych plików w aplikacjach urządzeń typu liść, które łączą się z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

5. W zależności od preferowanego języka zapoznaj się z przykładami, w jaki sposób można odwoływać się do certyfikatów X. 509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem z podpisem własnym X. 509 i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure na potrzeby tworzenia urządzeń, generowania certyfikatów i zarządzania nadrzędnego i podrzędnego, zobacz zawartość referencyjną polecenia [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) .

Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

## <a name="register-device-x509-ca-signed"></a>Rejestrowanie urządzenia (podpisany przez urząd certyfikacji X. 509)

W przypadku uwierzytelniania podpisanego za pomocą certyfikatu X. 509 urząd certyfikacji wymagany jest certyfikat głównego urzędu certyfikacji zarejestrowany w IoT Hub używany do podpisywania certyfikatów dla urządzenia IoT. Uwierzytelnianie będzie możliwe przy użyciu dowolnego urządzenia korzystającego z certyfikatu z certyfikatem głównego urzędu certyfikacji lub dowolnego z jego certyfikatów pośrednich.

Ta sekcja jest oparta na instrukcji przedstawionych w IoT Hub artykule [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Postępuj zgodnie z instrukcjami w tej sekcji, aby dowiedzieć się, które wartości należy użyć do skonfigurowania urządzenia podrzędnego, które nawiązuje połączenie za pomocą bramy.

Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X. 509, możesz [utworzyć certyfikaty demonstracyjne do testowania funkcji IoT Edge urządzeń](how-to-create-test-certificates.md). Podczas generowania certyfikatów testów dla urządzenia podrzędnego Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

1. Postępuj zgodnie z instrukcjami znajdującymi się w sekcji [Rejestrowanie certyfikatów urzędu certyfikacji x. 509 w usłudze IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) tematu *Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności:

   1. Przekaż certyfikat głównego urzędu certyfikacji. W przypadku korzystania z certyfikatów demonstracyjnych główny urząd certyfikacji jest ** \< ścieżką>/certs/Azure-IoT-test-Only.root.ca.CERT.pem**.

   2. Sprawdź, czy jesteś własnym certyfikatem głównego urzędu certyfikacji.

2. Postępuj zgodnie z instrukcjami podanymi w sekcji [Tworzenie urządzenia x. 509 dla Centrum IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *w temacie Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności:

   1. Dodaj nowe urządzenie. Podaj nazwę z małymi literami dla **identyfikatora urządzenia**, a następnie wybierz pozycję typ uwierzytelniania **X. 509 podpisany przez urząd certyfikacji**.
   2. Ustaw urządzenie nadrzędne. W przypadku urządzeń podrzędnych wybierz opcję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, które zapewni połączenie IoT Hub.

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który został przekazany do IoT Hub, aby utworzyć ten łańcuch. Użyj tego samego identyfikatora urządzenia małymi literami, który został przekazany do tożsamości urządzenia w portalu.

4. Skopiuj certyfikat i klucze urządzenia do dowolnej lokalizacji na urządzeniu podrzędnym. Należy również przenieść kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Te pliki będą odwoływać się do tych plików w aplikacjach urządzeń typu liść, które łączą się z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

5. W zależności od preferowanego języka zapoznaj się z przykładami, w jaki sposób można odwoływać się do certyfikatów X. 509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT przy użyciu podpisanego uwierzytelniania urzędu certyfikacji X. 509 i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Aby uzyskać więcej informacji, zobacz Dokumentacja interfejsu wiersza polecenia platformy Azure dla poleceń [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) .

Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

## <a name="retrieve-and-modify-connection-string"></a>Pobieranie i modyfikowanie parametrów połączenia

Po utworzeniu tożsamości urządzenia IoT w portalu można pobrać jej klucze podstawowe lub pomocnicze. Jeden z tych kluczy musi być uwzględniony w parametrach połączenia używanych przez aplikacje do komunikowania się z IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego IoT Hub zapewnia w pełni uformowane parametry połączenia w szczegółach urządzenia dla wygody użytkownika. Należy dodać dodatkowe informacje o urządzeniu bramy do parametrów połączenia.

Parametry połączenia dla urządzeń podrzędnych muszą mieć następujące składniki:

* Centrum IoT, z którym łączy się urządzenie:`Hostname={iothub name}.azure-devices.net`
* Identyfikator urządzenia zarejestrowany w centrum:`DeviceID={device ID}`
* Klucz podstawowy lub pomocniczy:`SharedAccessKey={key}`
* Urządzenie bramy, za pomocą którego urządzenie nawiązuje połączenie. Podaj wartość **hostname** IoT Edge z pliku config. YAML urządzenia bramy:`GatewayHostName={gateway hostname}`

Wszystkie razem, kompletne parametry połączenia wyglądają następująco:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Jeśli została ustanowiona relacja nadrzędny/podrzędny dla tego urządzenia podrzędnego, można uprościć parametry połączenia przez wywołanie bramy bezpośrednio jako hosta połączenia. Relacje nadrzędny/podrzędny są wymagane do uwierzytelniania X. 509, ale opcjonalne do uwierzytelniania przy kluczu symetrycznym. Przykład:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

W tym momencie urządzenie IoT Edge powinno być zarejestrowane i skonfigurowane jako brama. Masz również zarejestrowane urządzenie IoT podrzędnego i wskazujące jego urządzenie bramy. Ostatnim krokiem jest umieszczenie certyfikatów na urządzeniu podrzędnym, aby można było bezpiecznie połączyć się z bramą.

Przejdź do następnego artykułu z serii bramy, [Połącz urządzenie podrzędne z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Wykonanie tego artykułu powinno spowodować, że urządzenie IoT Edge działa jako niewidoczna brama i urządzenie podrzędne zarejestrowane w usłudze IoT Hub. Następnie należy skonfigurować urządzenia podrzędne, aby ufać urządzeniu bramy i bezpiecznie połączyć się z nim. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).
